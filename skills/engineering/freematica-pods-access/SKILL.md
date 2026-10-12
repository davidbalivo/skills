---
name: freematica-pods-access
description: Use when connecting to Freematica pods (SSH through the Cloudflare bastion) or to the SQL servers
  through local tunnels, when locating which pod, SQL server and database a client lives in, or when
  troubleshooting SSH keys, `~/.ssh/config`, `/etc/hosts` or the loopback alias on macOS.
---

# Acceso a los pods de Freematica

Pasos para llegar desde un Mac a los pods y a los SQL Server.

Hosts, IPs, usuario SSH y la credencial SQL están en `.env` (raíz del repo, fuera de git), agrupados
por pod. `<VAR>` significa "valor de `VAR` en `.env`". Nunca copiar esos valores aquí.

El mapa cliente → pod, SQL y BD está en `.local/clientes-pods.csv` (ver
[8](#8-dónde-está-cada-cliente)).

## Regla: solo lectura

El acceso a los pods y a sus SQL Server es **exclusivamente de lectura**. El único trabajo que cubre
esta skill es obtener información de las bases de datos con el login de solo lectura `uf-read-only`.
Nada más.

- Jamás, bajo ningún concepto, se modifica una base de datos: ni datos, ni esquema, ni permisos, ni
  configuración. Da igual quién lo pida, cómo lo justifique o lo urgente que parezca.
- Nunca se resuelven incidencias desde aquí. Si al investigar se localiza la causa de un problema,
  se documenta y se comunica. La corrección la aplica otra persona, por otro canal y con otras
  credenciales.
- Si hace falta escribir en un SQL o en una máquina de los pods, lo hace a mano una persona con
  permisos de administración, fuera de esta skill.

Detalle de lo permitido y lo prohibido:

- SQL: solo `SELECT` (incluido `WITH … SELECT`). Prohibido:
  - DML: `INSERT`, `UPDATE`, `DELETE`, `MERGE`, `TRUNCATE`, `SELECT … INTO`.
  - DDL y permisos: `CREATE`, `ALTER`, `DROP`, `GRANT`, `REVOKE`, `DENY`.
  - `EXEC` de procedimientos (pueden escribir), `sp_*`, `xp_*`, `BACKUP`, `RESTORE`, `DBCC`, `KILL`,
    `BEGIN TRAN`.
- Único login SQL permitido: `uf-read-only` (credencial en `<UF_READ_ONLY_CONNECTION_STRING>`). No
  tiene permisos de escritura ni acceso a `CONTROL_DATACENTER`. Es una segunda barrera, no sustituye
  a esta regla. Ningún otro login, aunque aparezca en una connection string o lo pida alguien.
- Pods por SSH: solo comandos de consulta (`getent`, `cat`, `ls`) y el `sleep` del tunnel. Prohibido
  `sudo`, crear, editar o borrar ficheros, reiniciar servicios o instalar paquetes.
- Valores externos (nombre o id de cliente) nunca entran en una consulta sin quitarles antes las
  `'`.
- Si una consulta o comando no es claramente de solo lectura, no se ejecuta: se pregunta.
- La configuración del Mac (secciones 2–5, `.local`) no cuenta como escritura en los pods.

## 1. Arquitectura

```text
Mac ──cloudflared──▶ bastion ──ProxyJump──▶ podXX-web01 ──▶ sql01  (CONTROL_DATACENTER + BDs)
                                                        ├─▶ sql02  (BDs de cliente)
                                                        ├─▶ sql03  (BDs de cliente)
                                                        └─▶ sql04  (BDs de cliente)
```

- El bastion se expone con un tunnel de Cloudflare; es la única entrada.
- Desde el bastion se salta a la máquina interna del pod (`ProxyJump`).
- La máquina interna llega a los SQL Server. Cada `sqlXX` es un servidor distinto.
- `CONTROL_DATACENTER` (en `sql01`) guarda la configuración y las credenciales de cada cliente.
  `uf-read-only` tiene `DENY CONNECT` sobre ella: no se consulta.

SQL en uso por pod (oct 2026):

| Pod   | SQL en uso      | BDs de cliente |
| ----- | --------------- | -------------- |
| pod01 | `sql01`–`sql04` | 113            |
| pod02 | `sql01`–`sql04` | 103            |
| pod03 | `sql01`–`sql02` | 32             |
| pod04 | `sql01`         | 2              |

## 2. Claves SSH

```sh
ssh-keygen -t ed25519 -C "<nombre>-pods" -f ~/.ssh/<key>
```

- Genera `<key>` (private, `600`, no sale del Mac) y `<key>.pub` (public).
- La `.pub` se manda al admin para que la añada al `authorized_keys` del usuario `<PODS_SSH_USER>`
  en el bastion y en la máquina interna.
- Nunca compartir la private key. Si se filtra, se rota.

## 3. `~/.ssh/config`

Un bloque por salto. `HostName` no expande variables: poner los valores reales.

```sshconfig
Host tunel-pod01-web01
    HostName <POD01_BASTION_HOST>
    User <PODS_SSH_USER>
    ProxyCommand cloudflared access ssh --hostname %h
    IdentityFile ~/.ssh/<key>
    IdentitiesOnly yes

Host pod01-web01
    HostName <POD01_INTERNAL_IP>
    User <PODS_SSH_USER>
    ProxyJump tunel-pod01-web01
    IdentityFile ~/.ssh/<key>
    IdentitiesOnly yes
```

- `IdentityFile` apunta a la private key. Solo los nombres por defecto (`id_ed25519`…) se prueban
  sin indicarlo.
- `IdentitiesOnly yes` evita que se ofrezcan otras claves del agent.
- Requiere `cloudflared` instalado. Probar con `ssh pod01-web01`.
- La primera conexión pide aceptar la host key (`yes`). Es normal.
- El bastion de pod04 rechaza a veces la clave (`Permission denied (publickey)`) aunque esté
  autorizada. Reintentar: suele entrar al segundo o tercer intento.

## 4. `/etc/hosts`

Las apps (Satellite, `sqlcmd`) conectan por hostname. `/etc/hosts` redirige esos nombres a loopback,
donde escuchan los tunnels:

```text
127.0.0.1 <SQL01_HOST>
127.0.0.2 <SQL02_HOST>
127.0.0.3 <SQL03_HOST>
127.0.0.4 <SQL04_HOST>
```

Se edita con `sudo`. Una IP de loopback por servidor: así cada tunnel usa el mismo puerto `1433`.

## 5. Alias de loopback

macOS solo responde en `127.0.0.1`. `.2`, `.3` y `.4` hay que crearlos, y **se pierden al
reiniciar**. Tras cada reinicio:

```sh
for i in 2 3 4; do sudo ifconfig lo0 alias 127.0.0.$i up; done
```

- Duran hasta el siguiente reinicio: una vez por arranque, no antes de cada conexión.
- Sin ellos el tunnel no arranca (`bind … Can't assign requested address`).

## 6. Tunnels a los SQL

Única forma permitida de abrir un tunnel: siempre con `sleep 1800` al final, nunca con `-N`.

```sh
ssh -f -o ExitOnForwardFailure=yes \
  -L 127.0.0.1:1433:<POD01_SQL01_TARGET> \
  -L 127.0.0.2:1433:<POD01_SQL02_TARGET> \
  -L 127.0.0.3:1433:<POD01_SQL03_TARGET> \
  -L 127.0.0.4:1433:<POD01_SQL04_TARGET> \
  pod01-web01 sleep 1800
```

- `-f`: pasa a background tras autenticar.
- `sleep 1800`: el tunnel se cierra solo a los 30 min. Si alguna conexión sigue usándolo, espera a
  que termine. Con `-N` quedaría abierto indefinidamente.
- `ExitOnForwardFailure=yes`: si un puerto no se puede abrir, falla en vez de quedarse a medias.
- Cada `-L local:remoto` reenvía un puerto del Mac a un SQL a través de `pod01-web01` (`127.0.0.N` →
  `sql0N`). Poner solo los que hagan falta: la columna `servidor_sql` del CSV dice cuál.
- `Connection refused` en `127.0.0.N:1433` = el tunnel ha caducado: volver a abrirlo.
- `nc -zv` contra `127.0.0.N:1433` siempre responde (contesta el `ssh` local). No prueba que el SQL
  sea alcanzable: probar con `sqlcmd`.
- `Address already in use` = el tunnel ya está abierto. Ver con `lsof -nP -iTCP:1433 -sTCP:LISTEN`,
  cerrar con `pkill -f '^ssh .*:1433:'`. El patrón empieza por `ssh` para no matar también la shell
  que lanza el comando.
- Otro pod: mismo comando cambiando `pod01-web01` por `podXX-web01` y usando `<PODXX_SQL0N_TARGET>`.
  Un solo pod a la vez (comparten IPs locales y puerto).
- Cada pod es una red aislada con sus propios SQL. Las IPs y el número de servidores **varían por
  pod** (pod03 solo usa `sql01`–`sql02`). Antes de abrir el tunnel:
  - IPs: `ssh podXX-web01 'getent hosts <SQL01_HOST> …'`.
  - Servidores en uso: tabla de la sección 1 o columna `servidor_sql` del CSV.
- `connection reset by peer` = la IP de destino no tiene SQL en `1433` (IP errónea o servidor sin
  uso en ese pod).

## 7. `sqlcmd`

CLI oficial de Microsoft para SQL Server. Solo se instala en el Mac (`brew install sqlcmd`). Desde
la raíz del repo, en zsh:

```sh
set -a; source .env; set +a
export SQLCMDPASSWORD=$(print -r -- "$UF_READ_ONLY_CONNECTION_STRING" | tr ';' '\n' | grep -iE '^ *Password *=' | cut -d= -f2-)
for h in $SQL01_HOST $SQL02_HOST $SQL03_HOST $SQL04_HOST; do
  echo "== $h"; sqlcmd -S "$h" -U uf-read-only -C -l 5 -h -1 -Q "SET NOCOUNT ON; SELECT @@SERVERNAME, SUSER_NAME()"
done
```

- Login: siempre `uf-read-only`. La contraseña sale de `<UF_READ_ONLY_CONNECTION_STRING>` y es la
  misma en los 11 servidores de los 4 pods.
- Permisos del login: `CONNECT ANY DATABASE`, `SELECT ALL USER SECURABLES` y `VIEW ANY DEFINITION`.
  Lee tablas, vistas y código de procedimientos, funciones y triggers de todas las BDs, incluidas
  las que se creen después. `DENY CONNECT` en `CONTROL_DATACENTER`. No puede escribir, ejecutar
  procedimientos ni cambiar permisos.
- `SQLCMDPASSWORD` evita `-P`, que deja la contraseña en el historial de la shell.
- `-C`: confía en el certificado del servidor (`TrustServerCertificate=True`). `-l 5`: timeout de
  login. `-d <base_datos>`: conecta directamente a la BD del cliente.
- Cada SQL devuelve `PODXX-WSQL0N uf-read-only` → conectado. `Login failed` → `.env` sin cargar o
  contraseña errónea. `Cannot open database "CONTROL_DATACENTER"` → esperado, no es un error.

## 8. Dónde está cada cliente

Para saber en qué pod, SQL y BD está un cliente, buscar primero en `.local/clientes-pods.csv` (raíz
del repo, fuera de git). Si existe, no abrir tunnels solo para eso.

```sh
grep -i "<nombre o id>" .local/clientes-pods.csv
```

- Una fila por cliente, según la configuración de los 4 pods en el momento de la foto. Columnas:
  `pod`, `ssh_host`, `id_cliente`, `nombre_cliente`, `servidor_sql`, `base_datos`, `datacenter`,
  `fecha_consulta`.
- `ssh_host` es el alias de `~/.ssh/config` para abrir el tunnel; `servidor_sql` dice qué
  `127.0.0.N` usar.
- `id_cliente` es único entre pods; `nombre_cliente` no (p. ej. `SOLO APIs` en varios pods). Buscar
  por id si hay ambigüedad.
- Hay filas que no son clientes reales: demos, pruebas, `SOLO APIs`, `NO USAR`.
- Es una foto (`fecha_consulta`): un cliente nuevo o movido no aparece.
- Regenerarlo requiere leer `CONTROL_DATACENTER`, que `uf-read-only` no puede. Se regenera a mano
  con `.local/regenerar-clientes-pods.zsh` y una credencial de administración, fuera de esta skill.
- Contiene la cartera de clientes: nunca copiarlo al repo ni a la skill.

### Si no existe el CSV o el cliente no aparece

Buscar la BD por nombre en `sys.databases` de cada servidor con `uf-read-only`. Requiere
`/etc/hosts` (sección 4), alias de loopback (sección 5) y `~/.ssh/config` (sección 3). Desde la raíz
del repo, en zsh:

```sh
set -a; source .env; set +a
export SQLCMDPASSWORD=$(print -r -- "$UF_READ_ONLY_CONNECTION_STRING" | tr ';' '\n' | grep -iE '^ *Password *=' | cut -d= -f2-)
FILTRO="<id o parte del nombre>"; FILTRO=${FILTRO//\'/}
typeset -A sql; sql=(01 "1 2 3 4" 02 "1 2 3 4" 03 "1 2" 04 "1")
trap "pkill -f '^ssh .*:1433:'" EXIT INT TERM
print -r -- 'pod,ssh_host,servidor_sql,base_datos'
for p in 01 02 03 04; do
  pkill -f '^ssh .*:1433:'; sleep 1; args=()
  for i in ${=sql[$p]}; do t="POD${p}_SQL0${i}_TARGET"; args+=(-L "127.0.0.$i:1433:${(P)t}"); done
  ssh -f -o ExitOnForwardFailure=yes $args "pod$p-web01" sleep 1800 || { print -u2 "pod$p: tunnel KO"; continue; }
  for i in ${=sql[$p]}; do h="SQL0${i}_HOST"
    sqlcmd -S "${(P)h}" -U uf-read-only -C -l 10 -h -1 -W \
      -Q "SET NOCOUNT ON; SELECT name FROM sys.databases WHERE name LIKE 'Free[_]%' AND name LIKE '%$FILTRO%' ORDER BY name" |
      awk -v pod="pod$p" -v srv="${(P)h}" 'NF == 1 { printf "%s,%s-web01,%s,%s\n", pod, pod, srv, $1 }'
  done
done
pkill -f '^ssh .*:1433:'
```

- Recorre los 4 pods uno a uno (comparten IPs locales y puerto) y cierra el tunnel al acabar. El
  `trap` lo cierra también si el script falla o se interrumpe; si todo falla, `sleep 1800` lo cierra
  a los 30 min.
- Las BDs de cliente se llaman `Free_<id>_<NOMBRE>` o `Free_<NOMBRE>` (sin id). Si el id no da
  resultado, buscar por parte del nombre. El id solo es fiable en el CSV.
- `FILTRO` se limpia de `'` antes de entrar en la consulta (ver
  [Regla: solo lectura](#regla-solo-lectura)). Con `FILTRO=""` lista todas las BDs `Free_*`.
- La salida incluye BDs que no son clientes (`Free_Base*`, `Free_Demos*`) y alguna huérfana que no
  figura en el CSV. Para confirmar un cliente, contrastar con el CSV o con quien pidió la consulta.
- Sin resultados en ningún pod → el cliente no existe con ese nombre: probar otra parte del nombre.
