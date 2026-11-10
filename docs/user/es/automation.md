# Automatización: scripts y lotes

SynapTerm puede ejecutar **scripts de Python** que escriben en tus sesiones, esperan su salida, se conectan a tus servidores guardados y copian ficheros, en un servidor o en muchos a la vez. Los scripts nunca ven tus contraseñas, frases de paso ni claves: usan las conexiones que SynapTerm abre por ellos.

- [Antes de empezar](#antes-de-empezar)
- [La biblioteca de scripts](#la-biblioteca-de-scripts)
- [Ejecutar un script](#ejecutar-un-script)
- [Ejecutar en varios servidores](#ejecutar-en-varios-servidores)
- [Grabar una macro](#grabar-una-macro)
- [El módulo `synapterm` (API v1)](#el-módulo-synapterm-api-v1)
- [Ejemplos](#ejemplos)
- [Seguridad](#seguridad)

## Antes de empezar

Los scripts usan el **Python instalado en tu equipo** (3.9 o posterior). Abre *Ajustes → Automatización* y pulsa **Comprobar**: SynapTerm busca `py -3`, `python` y `python3`. Si tu Python está en otro sitio, escribe su ruta completa en el campo. Si no lo tienes, instálalo desde [python.org](https://www.python.org/downloads/) (en Windows, marca *Add python.exe to PATH*).

En la misma sección:

- **Tiempo máximo de ejecución**: un script que sigue en marcha pasado este tiempo se para, junto con las sesiones que abrió (30 minutos por defecto).
- **Servidores a la vez**: en cuántos servidores se ejecuta un lote al mismo tiempo (5 por defecto).
- **Abrir la consola de scripts**: *Solo si falla* (la opción por defecto) deja la terminal despejada: un script que se ejecuta en una pestaña se ve en la propia terminal, y la consola se abre sola únicamente si el script falla o se queda sin tiempo. *Siempre* la abre en cada ejecución; *Nunca* la deja cerrada (el aviso de error tiene **Ver la consola**). Los lotes siempre la abren.
- **Mostrar registro**: abre el registro de automatización (ver [Seguridad](#seguridad)).

## La biblioteca de scripts

Solo se pueden ejecutar los scripts de tu biblioteca. Ábrela con el icono de **Scripts** de la barra lateral (o pulsa **Alt+Y**).

- **Nuevo script** crea un fichero `.py` a partir de una plantilla que explica lo básico.
- **Añadir .py** copia a la biblioteca un script que ya tengas.
- **⋯ → Mostrar en la carpeta** abre la carpeta de la biblioteca, para editar el script con tu editor. Los cambios se aplican la siguiente vez que se ejecuta.
- Bajo el nombre de cada script se ve la primera línea de su docstring (o su primer comentario).

## Ejecutar un script

- **▶**, junto a un script, lo ejecuta en la **pestaña activa** (`app.session` en el script). También puede ejecutarse sin ninguna pestaña abierta: entonces `app.session` es `None` y el script abre sus propias conexiones.
- **⋯ → Ejecutar con argumentos…** le pasa argumentos (`app.args`). Usa comillas para un argumento con espacios.
- **⋯ → Botón y atajo…** añade un botón ▶ del script a la barra de botones de debajo de la terminal y le da su propio atajo de teclado.
- En la paleta de comandos (**Ctrl+Shift+K**), escribe `>` y parte del nombre del script.

Lo que escribe un script y lo que responde el servidor se ve en la **terminal**. Lo que escribe con `print()`, y sus errores, van a la **consola de scripts**, debajo de la terminal: una pestaña por ejecución, con su salida en directo y su estado (*En marcha*, *Terminado*, *Error*, *Parado*, *Tiempo agotado*). Por defecto la consola solo se abre cuando un script falla o se queda sin tiempo (*Ajustes → Automatización → Abrir la consola de scripts*); ábrela cuando quieras con **Consola**, al pie del panel *Scripts*. **Parar**, en la consola, termina un script y todo lo que haya puesto en marcha.

Mientras un script se ejecuta, su pestaña muestra **■**: haz clic para parar los scripts de esa pestaña, o pulsa **Ctrl+Shift+X** (cámbialo en *Ajustes → Atajos de teclado*). Los scripts que se ejecutan sin pestaña muestran **N scripts en marcha sin pestaña** al final de la barra de pestañas: haz clic para pararlos. Al parar un script también se cierran las sesiones que abrió.

No uses `input()` para preguntar algo: en su lugar muestra un diálogo de SynapTerm (igual que `app.prompt()`).

## Ejecutar en varios servidores

Un **lote** ejecuta un comando guardado o un script en muchos servidores SSH:

- Para un **comando guardado**: haz clic derecho en su botón de la barra de botones, o abre el *Gestor de comandos* (**Alt+J**), elige el comando y pulsa *Ejecutar en servidores…*.
- Para un **script**: **⋯ → Ejecutar en servidores…**

Elige los servidores por carpeta, etiqueta, texto o uno a uno, decide cuántos se ejecutan a la vez, pulsa **Vista previa** para ver la lista de destinos y después **Ejecutar**. Cada servidor tiene su propia conexión sin pestaña, con su contraseña guardada, su clave o el agente SSH, que se cierra al terminar. Las variables de un comando (`{{host}}`, `{{alias}}`…) se rellenan para cada servidor, y las `{{?preguntas}}` se hacen una sola vez para todos.

La consola muestra una fila por servidor: **OK**, **Error** (con el motivo: el servidor está apagado, la contraseña no está guardada, el comando ha fallado…) o **Tiempo agotado**, cuánto ha tardado y su salida (haz clic en la fila). **Exportar CSV** y **Exportar JSON** guardan el informe.

Un comando de un lote se ejecuta en el shell del servidor (bash, sh, zsh…). Su código de salida decide si es OK o Error. Para equipos de red (switches, routers) usa un script, que puede esperar al prompt propio del equipo.

## Grabar una macro

El panel **Macros** de la barra lateral (**Alt+U**) graba lo que escribes en la pestaña activa: pulsa *Grabar una macro*, trabaja como siempre, después *Parar y guardar* y ponle un nombre. Se guarda como un script de tu biblioteca que espera los mismos prompts y escribe las mismas líneas, así que puedes editarlo como cualquier otro script. Las contraseñas y los códigos que escribes sin eco nunca se guardan: la macro te los pide al ejecutarse. El panel lista las macros grabadas; ▶ ejecuta una en la pestaña activa.

## El módulo `synapterm` (API v1)

SynapTerm pone el módulo `synapterm` a disposición de tus scripts. Los patrones son expresiones regulares (como las de `re` de Python, sin look-around); `^` y `$` coinciden con el principio y el final de cada línea, y `(?i)` no distingue mayúsculas de minúsculas.

```python
from synapterm import app
```

### `app`

| | |
| --- | --- |
| `app.session` | La sesión (pestaña) desde la que se lanzó el script, o `None`. |
| `app.args` | Lista de argumentos del script. |
| `app.servers(folder=None, tag=None, query=None)` | Servidores guardados, como objetos `Server` (`id`, `alias`, `host`, `port`, `username`, `protocol`, `folder`, `tags`). `folder` coincide con el nombre o la ruta de una carpeta e incluye sus subcarpetas; `query` busca en el alias, el host y el usuario. |
| `app.connect(server, tab=False, timeout=60)` | Se conecta a un servidor guardado (alias o id) y devuelve una `Session`. Sin pestaña usa la contraseña guardada, la clave o el agente y nunca pregunta nada: si la contraseña no está guardada o la clave del host todavía no es de confianza, lanza un error que lo dice. `tab=True` abre una pestaña normal, que puede pedir lo que falte. Las sesiones sin pestaña se cierran cuando termina el script. |
| `app.sftp(session)` | Transferencia de ficheros por una sesión SSH (`Sftp`). |
| `app.message(text, kind="info")` | Muestra un aviso en SynapTerm (`info`, `success` o `error`). |
| `app.prompt(question, secret=False, default="")` | Pregunta al usuario y devuelve la respuesta. Lanza `KeyboardInterrupt` si cancela. |

### `Session`

Una sesión guarda una **posición de lectura**: `wait_for`, `read_until` y `read` solo miran lo que ha llegado después de lo último que devolvieron (o después del último `send`). Una sesión de `connect()` empieza en su primera salida (el banner y el primer prompt); `app.session` empieza en lo que llega después de arrancar el script.

| | |
| --- | --- |
| `send(text)` | Escribe `text` (añade `\r` para Intro). |
| `sendline(text="")` | Escribe `text` y pulsa Intro. |
| `wait_for(pattern, timeout=30)` | Espera a `pattern` y devuelve un `Match` (`text`, `groups`, `group(n)`). Lanza `TimeoutError`, o `ConnectionError` si la sesión se cierra antes. |
| `read_until(pattern, timeout=30)` | Todo lo escrito hasta `pattern` (incluido). |
| `read()` | Lo que ha llegado desde la última lectura, sin esperar. |
| `close()` | Cierra una sesión abierta sin pestaña. |
| `id`, `headless` | Su id, y si no tiene pestaña. |

### `Sftp`

| | |
| --- | --- |
| `upload(local_path, remote_dir)` | Copia un fichero o carpeta local en `remote_dir`. |
| `download(remote_path, local_dir=".")` | Copia un fichero o carpeta remota en `local_dir`. |
| `list(remote_dir=None)` | Entradas de una carpeta remota (por defecto, la carpeta personal): `name`, `path`, `kind`, `size`… |

Los errores que informa SynapTerm se lanzan como `synapterm.SynapTermError` (o `TimeoutError` / `ConnectionError`).

## Ejemplos

### Revisar los discos de todos los servidores con una etiqueta

Ejecútalo desde la biblioteca con argumentos, por ejemplo `web 85` (etiqueta y nivel de aviso).

```python
"""Disk usage of every server with a tag (arguments: tag, warning %)."""
from synapterm import app

tag = app.args[0] if app.args else "web"
limit = int(app.args[1]) if len(app.args) > 1 else 85
PROMPT = r"[$#] ?$"

for server in app.servers(tag=tag):
    try:
        s = app.connect(server.alias)
        s.wait_for(PROMPT, timeout=20)
        s.sendline("df -P / | tail -1")
        m = s.wait_for(r"(\d+)% /\s*$", timeout=20)
        used = int(m.group(1))
        print(f"{server.alias:<20} {used:>3}%{'  <- check' if used >= limit else ''}")
        s.close()
    except Exception as error:
        print(f"{server.alias:<20} error: {error}")
```

La misma comprobación como **lote** de un comando guardado, sin script: guarda `df -P / | tail -1` como comando y ejecútalo en los servidores de la etiqueta.

### Copia de seguridad de la configuración de los switches

Guarda la configuración en ejecución de cada servidor con la etiqueta `switch` en una carpeta de tu equipo (argumento: la carpeta). Ajusta los comandos y el prompt a tus equipos.

```python
"""Save the running config of the switches (argument: local folder)."""
import datetime
import os
from synapterm import app

folder = app.args[0] if app.args else os.path.expanduser("~/switch-backups")
os.makedirs(folder, exist_ok=True)
stamp = datetime.date.today().isoformat()
PROMPT = r"[>#]\s*$"

for switch in app.servers(tag="switch"):
    try:
        s = app.connect(switch.alias)
        s.wait_for(PROMPT, timeout=30)
        s.sendline("terminal length 0")
        s.wait_for(PROMPT)
        s.sendline("show running-config")
        config = s.read_until(PROMPT, timeout=120)
        path = os.path.join(folder, f"{switch.alias}-{stamp}.cfg")
        with open(path, "w", encoding="utf-8") as f:
            f.write(config)
        print(f"{switch.alias}: saved {path}")
        s.close()
    except Exception as error:
        print(f"{switch.alias}: {error}")

app.message(f"Switch backups in {folder}", kind="success")
```

## Seguridad

- Solo se ejecutan los scripts de tu biblioteca. El plan gratuito guarda hasta 8 scripts y 8 macros; SynapTerm Pro (o la beta) no tiene límite y añade la ejecución de un comando o un script en varios servidores a la vez.
- Un script solo puede usar la sesión desde la que se lanzó y las sesiones que abre él mismo.
- La API no tiene forma de leer las contraseñas guardadas, las frases de paso, las claves privadas ni la clave que cifra tu lista de servidores.
- La ejecución en varios servidores siempre muestra antes la lista de destinos.
- Cada ejecución tiene un tiempo límite y se puede parar; las sesiones que abrió se cierran al terminar, aunque falle.
- Cada ejecución de un script y cada lote se anotan en `automation-audit.log` (*Ajustes → Automatización → Mostrar registro*): el script o el comando, dónde se ejecutó y cómo terminó; nunca su salida ni los valores de sus argumentos.
