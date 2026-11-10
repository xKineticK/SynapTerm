# Preguntas frecuentes y problemas

## Conexiones

**«Connection refused» o «timed out».** La pestaña dice qué ha fallado (la resolución del nombre, la red o el puerto) y qué revisar. Comprueba que el host y el puerto son correctos, que el servicio está en marcha y que ningún cortafuegos lo bloquea. *Probar conexión*, en el formulario del servidor, hace las mismas comprobaciones sin enviar credenciales.

**«Host key has CHANGED».** El servidor presenta una clave distinta de la guardada en `~/.ssh/known_hosts`. Si el servidor se ha reinstalado, acepta la clave nueva; si no, no te conectes y pregunta a quien lo administra. Las claves de confianza están en *Ajustes → Hosts conocidos*.

**Me pide la contraseña cada vez.** Marca *Guardar la contraseña en el llavero del sistema* cuando te la pida, o ponla en el formulario del servidor (*Autenticación → Contraseña*).

**Se ha cortado la conexión.** Pulsa **Intro** en la terminal para reconectar. Las conexiones inactivas se mantienen con los keepalive de SSH (*Ajustes → SSH y shell*), pero algunos cortafuegos las cierran igualmente.

## Escritorios remotos

**RDP: el equipo remoto escribe caracteres equivocados.** Windows fija la distribución del teclado al crear la sesión remota. Cierra la sesión remota una vez (Inicio → Cerrar sesión) y vuelve a conectar.

**VNC: «the server only offers … authentication».** El servidor exige un método que SynapTerm todavía no admite (TLS, Apple ID, cuentas de RealVNC). Activa en el servidor la autenticación clásica con contraseña VNC.

**VNC: rechaza la contraseña, pero es correcta.** Las contraseñas VNC solo usan los 8 primeros caracteres, y algunos servidores bloquean los intentos durante un rato tras varios fallos.

## Datos y privacidad

**¿Dónde están mis datos?** Windows: `%APPDATA%\com.synapterm.desktop`; macOS: `~/Library/Application Support/com.synapterm.desktop`; Linux: `~/.local/share/com.synapterm.desktop`. Los registros están en su carpeta `logs` (*Ajustes → Seguridad y privacidad* muestra la ruta).

**«Server list recovered» al arrancar.** El fichero de la base de datos estaba dañado (por ejemplo, tras un cierre inesperado o con el disco lleno). SynapTerm ha guardado el fichero dañado junto al nuevo (`session_manager.corrupt-….sqlite`) y ha empezado con la lista vacía: recupera tu última exportación desde *Ajustes → Importar y exportar*.

**«Database locked» al arrancar.** La base de datos está cifrada y su clave no está en el llavero de este equipo (un equipo nuevo, o se ha reiniciado el llavero). Desbloquéala con tu **clave de recuperación** (`KSK1-…`).

**¿SynapTerm envía algo?** Ni telemetría ni informes de errores. Solo si activas *Buscar actualizaciones al arrancar SynapTerm* pregunta al servidor de actualizaciones de SynapTerm por la última versión, enviando tu licencia, la versión instalada y la plataforma; no se envía nada de tus servidores.

## Actualizaciones

*Ajustes → Acerca de y actualizaciones → Buscar actualizaciones ahora*. Las actualizaciones llegan de GitHub Releases y se comprueban con la clave de firma de SynapTerm antes de instalarse. SynapTerm se reinicia para terminar, lo que cierra las sesiones abiertas.

## Informar de un problema o sugerir una idea

Pulsa **Comentarios** a la derecha de la barra de acciones (también en *Ajustes → Acerca de y actualizaciones → Informar de un problema*). Elige *Informar de un problema* o *Sugerir una idea*, descríbelo y pulsa *Enviar* (o Ctrl+Intro). Llega al equipo de SynapTerm con la versión de la app y tu sistema. El email es opcional y solo se usa para responderte.

El registro de la aplicación solo se adjunta si marcas *Adjuntar el registro de la aplicación*: son las últimas 200 líneas, sin contraseñas, y *Ver exactamente qué se envía* lo muestra antes de enviarlo. Los informes en los que trabajamos se publican como [incidencias en GitHub](https://github.com/xKineticK/SynapTerm/issues), nunca con tu email. También puedes abrir tú una incidencia allí; nunca pegues en ella contraseñas, claves privadas ni claves de recuperación.
