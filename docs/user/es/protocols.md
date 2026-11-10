# Protocolos

| Protocolo | Puerto por defecto | Se abre como |
| --- | --- | --- |
| SSH | 22 | Terminal |
| SFTP | 22 | Terminal con el explorador de ficheros abierto |
| Telnet | 23 | Terminal |
| Serie | (puerto COM o `/dev/tty…`) | Terminal |
| RDP | 3389 | Escritorio remoto |
| VNC | 5900 | Escritorio remoto |

## SSH

- **Autenticación**: contraseña, clave privada (con frase de paso) o tu agente SSH (el agente de OpenSSH o Pageant en Windows, `SSH_AUTH_SOCK` en macOS y Linux). *Ajustes → Claves SSH* crea e importa claves, y *Copiar clave pública*, en el formulario del servidor, deja la clave pública en el portapapeles, lista para `~/.ssh/authorized_keys`.
- **Las claves de host** se comprueban contra `~/.ssh/known_hosts`. Si una clave cambia, SynapTerm te avisa antes de conectar: acéptala solo si sabes por qué (por ejemplo, un servidor reinstalado).
- **Keepalive y tiempo de espera** están en *Ajustes → SSH y shell*.
- **Servidores puente (ProxyJump)**: en los ajustes de un servidor, *Servidor puente* elige otro servidor guardado a través del que conectarse (ese puede tener a su vez su propio servidor puente, hasta 5 seguidos). La conexión con el destino va cifrada de extremo a extremo; cada servidor usa sus propias credenciales y su clave de host, y SynapTerm las pide por separado. La terminal, el explorador de ficheros, los túneles, el editor, la barra de recursos y *Abrir en VS Code* (que escribe `ProxyJump` en `~/.ssh/config`) funcionan a través de él, y al importar `~/.ssh/config` el `ProxyJump` se enlaza con el servidor guardado. El servidor puente tiene que permitir el reenvío TCP (`AllowTcpForwarding`); si no lo permite, SynapTerm lo dice.
- **Rutas de acceso** (cuando el destino solo es accesible desde un shell de otro servidor, por ejemplo `ssh alias` y después `sudo su -` o `rootsh`): en los ajustes de un servidor, *Cómo llegar a este servidor → Ruta de acceso* elige el servidor puente y enumera los pasos: *Esperar* un texto (una expresión regular, con tiempo de espera), *Enviar* un texto (`{host}`, `{user}`, `{port}` y `{alias}` se sustituyen por los de este servidor) o *Enviar secreto* (la contraseña guardada de este servidor, la del puente, o una que se pide cada vez para códigos de un solo uso). Los secretos salen del llavero del sistema y nunca se guardan en la ruta. SynapTerm se conecta al puente y ejecuta los pasos en la terminal; si uno falla, dice qué paso y qué ha recibido. *● Grabar ruta*, en la barra de pestañas, graba lo que escribes mientras entras a mano y crea un servidor con esos pasos (las contraseñas que escribes no se graban).

## Ficheros (SFTP)

**Ficheros (SFTP)**, en la barra de pestañas (**Ctrl+Shift+B**), abre un explorador de ficheros sobre la misma conexión: navegar, subir ficheros o carpetas (también arrastrándolos encima), descargar (clic derecho; el doble clic abre un fichero en el editor), cambiar de nombre, eliminar y crear carpetas. *Sincronizar* (⇄) copia una carpeta local al servidor o al revés, y muestra lo que va a cambiar antes de hacerlo.

## Recursos del servidor

Las pestañas SSH muestran abajo una barra de una línea con el estado del servidor: host, tiempo encendido, carga, CPU (con una pequeña gráfica), memoria usada/total, el disco más lleno y el tráfico de red.

- Haz clic en un elemento para ver su tarjeta de detalles; puede haber varias abiertas a la vez, que comparten el espacio sobre la barra (Escape las cierra): **Discos** (cada sistema de ficheros con tamaño, usado, libre, % e inodos), **CPU** (por núcleo, carga media y los procesos que más CPU o memoria usan), **Memoria** (como `free -m`) y **Red** (tráfico por interfaz).
- Los elementos se ponen amarillos o rojos al pasar los niveles de aviso y crítico (80 % y 90 % por defecto; la CPU usa la media de las últimas muestras, así que un pico corto no cuenta). La primera vez que un disco pasa del nivel crítico recibes una notificación.
- No se instala nada en el servidor: un canal SSH aparte ejecuta órdenes de solo lectura (`/proc`, `df`, `ps`; `sysctl` y `vm_stat` en macOS y FreeBSD; PowerShell en Windows) cada 3 segundos. Nunca aparecen en la terminal ni en su historial. La lista de procesos solo se lee mientras los detalles de la CPU están abiertos.
- *Ajustes → Monitor de recursos* lo activa o desactiva para todos los servidores, y fija el intervalo de actualización y los niveles. Cada servidor puede cambiarlo en sus ajustes (*Monitor de recursos*). En equipos de red y otros sistemas que no reconoce, la barra simplemente no aparece.

## Editar ficheros y VS Code

- **Editor integrado**: haz doble clic en un fichero del explorador (o *Abrir en el editor*) para abrirlo en un panel de editor junto a la terminal. Es el editor de VS Code (Monaco): resaltado de sintaxis, buscar y reemplazar (**Ctrl+F** / **Ctrl+H**), varios cursores (**Alt+clic**, **Ctrl+D**) y los atajos habituales de VS Code. Se carga la primera vez que abres un fichero.
  - **Ctrl+S** guarda por SFTP manteniendo la codificación (UTF-8, UTF-8 con BOM, UTF-16, Windows-1252), los finales de línea (LF o CRLF, se ven en la barra de estado; haz clic para cambiarlos) y los permisos y el propietario del fichero.
  - Si el fichero ha cambiado en el servidor desde que lo abriste, SynapTerm pregunta antes de sobrescribirlo (o carga la versión del servidor).
  - Un ● en la pestaña del fichero marca cambios sin guardar; al cerrarla, al cerrar su panel o la pestaña SSH, pregunta antes. **Ctrl+W** cierra el fichero actual; la × a la derecha de las pestañas de ficheros cierra el panel del editor, que también se cierra solo con su último fichero. La terminal y su conexión siguen abiertas (la × de la pestaña de arriba, la que muestra la conexión, lo cierra todo).
  - Los ficheros binarios y los de más de 5 MB no se abren: SynapTerm ofrece descargarlos.
  - El panel del editor se divide (**Ctrl+Shift+E** / **Ctrl+Shift+O**) y se redimensiona como cualquier otro panel; las dos mitades pueden mostrar el mismo fichero.

- **Modo IDE** (**IDE** en la barra de pestañas, **Ctrl+Shift+D**) convierte una pestaña SSH en un pequeño IDE: el explorador de ficheros a la izquierda, los editores en el centro y la terminal abajo (arrastra los bordes para cambiar su tamaño). Cada pestaña lo recuerda; vuelve a pulsarlo para recuperar la vista normal de la terminal.
  - *Seguir a la terminal*, arriba del explorador: **Seguir cd** mueve el explorador cada vez que la terminal cambia de carpeta; **Carpeta fija** lo deja donde está, e *Ir a la carpeta de la terminal* salta allí una vez. La elección se recuerda por servidor.
  - **Ctrl+P** (*Ir a un fichero*) lista los ficheros bajo la carpeta del explorador (con `find` en el servidor) y los filtra mientras escribes, como en VS Code; **Intro** abre el fichero en el editor.
  - **Ctrl+Shift+F** (o la pestaña *Buscar* de la barra lateral) busca texto en esos ficheros (con `grep` en el servidor): distinguir mayúsculas, palabra completa y expresiones regulares; los resultados se agrupan por fichero, y al hacer clic en uno se abre el fichero en esa línea. Las búsquedas se ejecutan en segundo plano en su propio canal SSH (la terminal se puede seguir usando) y se pueden cancelar. Se saltan carpetas como `.git` y `node_modules`, y solo se muestran las 2000 primeras coincidencias (20 000 ficheros para Ctrl+P).
  - Estos dos atajos funcionan con el foco fuera de la terminal; dentro de ella, **Ctrl+P** y **Ctrl+Shift+F** siguen haciendo lo de siempre (comando anterior en el shell, buscar en la terminal).
  - La carpeta de la terminal la informa el shell (OSC 7). Muchos servidores no la envían: *Folder not reported — enable…* añade unas líneas a `~/.bashrc` o `~/.zshrc` (solo si aceptas) y funciona desde el siguiente shell (reconecta o ejecuta `source ~/.bashrc`).
- **Abrir en VS Code** (en la barra de pestañas, o *Abrir en VS Code (Remote - SSH)* sobre una carpeta o un fichero del explorador) abre el servidor en [VS Code](https://code.visualstudio.com/) con la extensión *Remote - SSH* de Microsoft, en la carpeta de la terminal (o del explorador). Si falta la extensión, SynapTerm ofrece instalarla.
  - Añade el servidor a `~/.ssh/config`, dentro de un bloque marcado *SynapTerm* al principio del fichero (host `synapterm-<nombre>`). Tus propias entradas nunca se cambian; no edites dentro del bloque, porque SynapTerm lo reescribe.
  - Con una clave, VS Code usa la misma clave. Con el agente SSH, VS Code usa el agente del sistema (en Windows, el servicio *OpenSSH Authentication Agent*, no Pageant). Con contraseña, VS Code te la pide: no puede leer las contraseñas que SynapTerm guarda en el llavero del sistema.
  - La carpeta de la terminal se conoce cuando el shell la informa (OSC 7); si no, SynapTerm usa la carpeta del explorador o tu carpeta personal.
- **Editar con VS Code / Editar con el editor del sistema** (clic derecho en un fichero del explorador) descarga una copia privada y la abre en ese editor. Cada vez que guardas, SynapTerm la sube y muestra una notificación; se mantienen los permisos y el propietario del fichero. Si el fichero ha cambiado en el servidor desde que lo abriste, SynapTerm pregunta antes de sobrescribirlo. Al cerrar la pestaña SSH deja de vigilar el fichero y borra la copia. El editor del sistema es el Bloc de notas en Windows, el editor de texto por defecto en macOS y `xdg-open` en Linux. Los ficheros de más de 10 MB no se pueden editar (descárgalos).

## Túneles (redirección de puertos)

**Túneles** (**Ctrl+Shift+L**) en una pestaña SSH:

| Tipo | Equivale a | Uso |
| --- | --- | --- |
| Local | `ssh -L 8080:db:5432` | Llegar en `localhost` a un servicio que ve el servidor (una base de datos) |
| Remoto | `ssh -R 9000:localhost:3000` | Que el servidor llegue a un servicio de tu equipo |
| SOCKS5 | `ssh -D 1080` | Navegar a través del servidor |

Los túneles se pueden guardar en el servidor y arrancar solos al conectar.

## Telnet

Para equipos de red sin SSH. Telnet lo envía todo, contraseñas incluidas, **en claro**. *Iniciar sesión automáticamente* responde a las peticiones de usuario y contraseña con las credenciales guardadas.

## Consola serie

Elige el puerto (SynapTerm lista los que encuentra), la velocidad (de 9600 a 115200 y más), los bits de datos, la paridad, los bits de parada y el control de flujo. **Enviar BREAK**, en la barra de pestañas, está para los equipos que lo necesitan (la recuperación de contraseña en equipos de red, por ejemplo).

## RDP (Escritorio remoto de Windows)

- Se conecta con la contraseña guardada o la pide. Usa `DOMINIO\usuario` o el campo *Dominio* para cuentas de dominio.
- La primera conexión muestra la huella del certificado (los servidores RDP suelen tener certificados autofirmados). SynapTerm la recuerda y avisa si cambia.
- La resolución sigue el tamaño de la pestaña, o es una fija elegida en el formulario del servidor.
- En la sesión remota se usa **tu distribución de teclado**. Alt+Tab, la tecla Windows, Alt+F4 y Ctrl+Esc van al equipo remoto mientras tiene el foco. **Ctrl+Alt+Fin** envía Ctrl+Alt+Supr.
- **Portapapeles**: copiar y pegar texto en los dos sentidos (se puede desactivar por servidor).
- **Ficheros**: con el servidor OpenSSH activado en Windows, *Ficheros (SFTP)* navega por los ficheros del equipo remoto por SSH.
- Los comandos guardados se escriben en la ventana remota que tiene el foco.

## VNC

- Funciona con TightVNC, UltraVNC, TigerVNC, x11vnc, Compartir pantalla de macOS (con «Los usuarios de VNC pueden controlar la pantalla con la contraseña» activado) y con la mayoría de servidores VNC.
- Seguridad: ninguna o contraseña VNC. Solo cuentan los **8 primeros caracteres** de una contraseña VNC. Otros métodos (TLS/VeNCrypt, inicio de sesión con Apple ID) todavía no se admiten: activa la contraseña VNC en el servidor.
- VNC no cifra la pantalla ni el teclado. Úsalo en una red de confianza, por una VPN o a través de un túnel SSH.
- Opciones por servidor: *Solo ver*, *Compartir el escritorio con otros visores* y *Compartir el portapapeles* (texto; los caracteres fuera de Latin-1 llegan como `?`).
- La imagen se reduce para caber en la pestaña. Ctrl+Alt+Fin envía Ctrl+Alt+Supr.
