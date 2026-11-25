# Copia portable para Windows

La copia portable es SynapTerm en una carpeta: descomprímela donde puedas escribir (una carpeta tuya o un USB) y abre `SynapTerm.exe`. No se instala nada, no se escribe en el registro ni en el Administrador de credenciales de Windows, y todos tus datos se quedan en la carpeta, junto al programa. Se actualiza sola en esa misma carpeta.

Descarga *Portable (.zip)* desde [synapterm.com/es/download](https://synapterm.com/es/download) o desde la página de tu invitación. Necesita Windows 10/11 x64 con el runtime Edge WebView2 (viene con Windows 11; en Windows 10 suele venir con Edge).

## Qué hay en la carpeta

| Elemento | Qué es |
| --- | --- |
| `SynapTerm.exe` | La app. |
| `portable` | El marcador que hace portable esta copia. Déjalo junto a `SynapTerm.exe`. |
| `bin\synapterm.exe` | El comando `synapterm` que usan los agentes de IA. |
| `bin\python\` | Python 3.14 con pip, para tus scripts (ver abajo). |
| `data\` | Todo lo tuyo: servidores, ajustes, scripts, logs, tu bóveda y los datos del navegador de la app (`data\webview`). Lo crea SynapTerm. |

Para mover la copia, mueve la carpeta entera. Para hacer una copia de seguridad, copia la carpeta entera (con SynapTerm cerrado). Para quitarla de un PC, borra la carpeta.

El programa aún no está firmado: la primera vez que abras una copia descomprimida con el Explorador, SmartScreen puede avisar. Elige *Más información → Ejecutar de todas formas*.

## La contraseña maestra

La primera vez, SynapTerm te pide crear una **contraseña maestra** (8 caracteres como mínimo). Tus contraseñas, claves de API y licencia se guardan en `data\vault.json`, cifradas con una clave que sale de ella (Argon2id). Después te enseña una **clave de recuperación** (`KSK1-…`): guárdala en un gestor de contraseñas u otro sitio seguro, **no** en esta carpeta. Sin la contraseña ni la clave de recuperación nadie puede abrir tus datos, ni siquiera tú.

- SynapTerm pide la contraseña cada vez que arranca (la primera pantalla sale en el idioma de Windows: tu idioma elegido está dentro de la bóveda).
- **¿La olvidaste?** En la pantalla de desbloqueo, *He olvidado la contraseña…* te deja pegar la clave de recuperación y elegir otra contraseña. Tus datos se quedan.
- **Cámbiala** en *Ajustes → Seguridad y privacidad → Cambiar la contraseña maestra…* (pide la actual). *Mostrar la clave de recuperación…* también pide la contraseña.
- **Empezar de cero…** (en la pantalla de desbloqueo) aparta tus datos cifrados en `data\` (como `vault.lost-…` y `session_manager.lost-…`, sin borrarlos) y arranca vacío. Solo la clave de recuperación puede volver a abrir esos ficheros. Tendrás que activar la licencia otra vez (no gasta otro dispositivo).
- Si la carpeta es de solo lectura, SynapTerm no puede guardar sus datos y no arranca: copia la carpeta a una unidad donde puedas escribir.

## Recordar en este equipo

En la pantalla de desbloqueo (y al crear la bóveda o recuperarla), *Recordar en este equipo* hace que SynapTerm se abra sin contraseña **para tu usuario de Windows en este equipo**. La clave se guarda en `data\remembered.json`, protegida por Windows (DPAPI) para tu usuario: otro usuario, otro equipo o una copia de la carpeta en otro sitio siguen pidiendo la contraseña. El fichero no nombra el equipo ni el usuario; cada entrada lleva un identificador interno de la clave DPAPI de tu usuario de Windows, que cambia cada pocos meses.

- Quien pueda entrar en tu cuenta de Windows en ese equipo abre la copia sin contraseña. **No lo uses en un equipo compartido o prestado.**
- *Ajustes → Seguridad y privacidad → Olvidar en este equipo* lo quita en este equipo.
- Recuperar con la clave de recuperación o empezar de cero lo olvidan en **todos** los equipos. Cambiar la contraseña no: usa *Olvidar* en cada equipo, o recupera, si quieres que los demás vuelvan a pedirla.

## Licencia

Actívala en *Ajustes → Licencia* como siempre. En la copia portable la licencia se guarda dentro de la bóveda, y la copia cuenta como **un dispositivo**, la conectes al PC que la conectes. Tras *Empezar de cero* hay que activarla otra vez.

## Actualizaciones

Como en la versión instalada (*Ajustes → Acerca de y actualizaciones*): SynapTerm descarga el zip nuevo, comprueba su firma, se cierra, sustituye `SynapTerm.exe`, `bin\synapterm.exe` y `bin\python\` en esta carpeta y se vuelve a abrir. `data\` no se toca. Si hay otra ventana de la misma copia abierta, te pide que la cierres antes. Si hay un script o un `python.exe` de esta copia en marcha, la actualización para los scripts; un `python.exe` que hayas abierto a mano hace que la actualización falle y deja la carpeta como estaba: ciérralo y vuelve a intentarlo. Si la carpeta es de solo lectura, ofrece descargar el zip para que sustituyas tú los ficheros (conserva `data\`).

## Python y pip

Tus scripts usan el **Python que viene en la copia** (`bin\python`, Python 3.14 de python.org con pip 26.3), así que funcionan en cualquier PC, tenga Python o no. *Ajustes → Automatización → Comprobar* dice cuál se usa. `import synapterm` funciona sin configurar nada.

- **Tu propio Python**: escribe su ruta en *Ajustes → Automatización*. Una ruta relativa con `\` (por ejemplo `tools\python\python.exe`) está dentro de la carpeta de la copia, así que también viaja en el USB. Si esa ruta no responde, los scripts usan el embebido.
- **Instalar paquetes**: en una terminal local de SynapTerm, `python` y `pip` son los de la copia: escribe `python -m pip install <paquete>`. Los paquetes van a `data\python\py314\` y sobreviven a las actualizaciones. La caché de pip está en `data\python\pip-cache`. pip solo habla con PyPI cuando lo ejecutas.
- Desde cualquier otra consola, usa la ruta completa: `<carpeta>\bin\python\python.exe -m pip install <paquete>` (los paquetes también van a `data\`).
- Si cambia la letra de la unidad (el USB en otro PC), `python -m <herramienta>` sigue funcionando, pero los lanzadores `.exe` que crea pip en `data\python\py314\Scripts` dejan de hacerlo: usa `python -m`.
- Los paquetes sin wheel para Windows necesitan un compilador de C, que la mayoría de los PC no tienen.
- Cuando una actualización de SynapTerm trae una **versión menor nueva de Python** (3.14 → 3.15), las notas de la versión lo dicen y hay que **volver a instalar los paquetes** (van a `data\python\py315`; puedes borrar `py314`). Una versión de corrección (3.14.N → 3.14.M) los conserva.
- No vienen: `venv`, `ensurepip` ni `tkinter` (con IDLE).
- Una variable `PIP_PREFIX` o `PIP_CACHE_DIR` definida en el PC gana a las de la copia, y entonces los paquetes o la caché van donde diga.
- La licencia de Python (PSF) está en `bin\python\LICENSE.txt`.

## Qué se escribe fuera de la carpeta

Al arrancar, SynapTerm no escribe nada fuera de su carpeta, salvo ficheros temporales en `%TEMP%` y lo que haga el propio WebView2. Estas cosas, que pides tú, sí escriben en el PC:

- Las descargas (SFTP, Zmodem, S3) van a la carpeta que elijas (*Descargas* por defecto).
- *Editar con…* trabaja con copias temporales en `%TEMP%`.
- *Abrir en VS Code* añade el servidor a `%USERPROFILE%\.ssh\config`.
- Los logs de sesión van a la carpeta que elijas.
- Cuando confías en la clave de un servidor SSH, se recuerda en `%USERPROFILE%\.ssh\known_hosts` de ese PC, como hace OpenSSH.
- Si el PC tiene una variable de entorno `WEBVIEW2_USER_DATA_FOLDER`, WebView2 guarda ahí los datos del navegador de la app en vez de en `data\webview`.
