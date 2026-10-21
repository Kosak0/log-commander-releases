# Log Commander

**Panel en tiempo real para el `Game.log` de Star Citizen** · **Real-time dashboard for Star Citizen's `Game.log`**

[Descargas / Downloads](../../releases/latest) · [Español](#español) · [English](#english)

---

# Español

Log Commander lee tu `Game.log` mientras juegas y lo convierte en un panel web con estilo de cabina: eventos legibles, datos del servidor, tu ubicación, tu party y herramientas para revisar sesiones pasadas. Funciona en **Windows 10/11** y se ha probado con Star Citizen **4.10.2**.

> El código fuente no se publica aquí; esta página solo contiene el programa (`LogCommander.exe`) y sus notas de versión.

## Guía rápida

1. **Descarga** `LogCommander.exe` desde [Releases](../../releases/latest). Cada versión incluye también `LogCommander.exe.sha256`; si quieres comprobar el archivo, ejecuta `certutil -hashfile LogCommander.exe SHA256` y compara el resultado.
2. **Guárdalo en su propia carpeta**, por ejemplo `C:\Tools\LogCommander\`. Debe ser una carpeta donde puedas escribir: el programa guarda ahí su configuración (`logcommander_config.json`) y se actualiza a sí mismo en esa misma ruta.
3. **Ábrelo con doble clic.** Windows SmartScreen puede avisar de que el programa no es conocido, porque el exe no está firmado: pulsa *Más información → Ejecutar de todas formas*. Se abre una ventana de consola; **déjala abierta mientras lo uses**.
4. **Elige la instalación del juego.** Busca Star Citizen en todos tus discos (LIVE, PTU, EPTU). Si hay varias, la consola te deja elegir. Si no encuentra ninguna, te pide la ruta. Para que funcione, el juego debe haberse lanzado al menos una vez (así existe el `Game.log`).
5. **Abre el panel en el navegador:** <http://localhost:3111>. La consola muestra la dirección, pero no abre el navegador sola: **mantén `Ctrl` y haz clic en la dirección** que aparece en la consola y se abrirá en tu navegador predeterminado (en Windows Terminal; si tu consola no lo permite, copia la dirección y pégala en el navegador). El puerto se puede cambiar en *Settings → General → Web Server Port* (hay que reiniciar).
6. **Juega.** El panel sigue el log en vivo. Si abres el programa con el juego ya en marcha, repite todo el log de la sesión y se pone al día, así que puedes abrirlo en cualquier momento.
7. **Para cerrarlo,** pulsa `Ctrl+C` en la consola o cierra su ventana.

### Actualizar

- Al arrancar, el programa **comprueba si hay una versión nueva** (una sola petición a GitHub, sin enviar datos tuyos) y **nunca instala nada sin preguntarte**.
- Desde la **1.2.2**, si hay una versión nueva la consola lo avisa antes de cargar el panel: `[I]` instalar ahora, `[L]` luego (opción por defecto a los 20 segundos) o `[S]` saltar esa versión. Si no hay una consola interactiva, no te bloquea.
- En el panel web aparece también un aviso arriba, con los botones *Install now*, *Later* y *Skip this version*.
- Para comprobarlo cuando quieras: *Settings → About → Check for updates*.
- Al instalar, el programa descarga el archivo nuevo, **verifica su SHA-256**, lo sustituye y se reinicia. Guarda tu versión anterior como `LogCommander.exe.bak` y **conserva tu configuración**.
- Las versiones **1.2.0 y 1.2.1** tienen un fallo en el reinicio: pueden mostrar `Update failed: [WinError 87]` aunque el archivo ya se haya actualizado. No pasa nada: cierra el programa y ábrelo de nuevo. A partir de la 1.2.2 se reinicia solo.
- Puedes desactivar la comprobación en *Settings → General → Check for new versions at startup*.

### Acceder desde otro dispositivo de tu red local

Por defecto el panel solo responde en el propio PC (`127.0.0.1`). Para verlo desde un móvil, una tablet u otro PC de tu casa:

1. En el panel: *Settings → General → **Allow network access (listen on 0.0.0.0)***. Confirma el aviso.
2. **Reinicia** Log Commander (cierra la consola y ábrelo otra vez).
3. Averigua la IP de tu PC: abre una consola de Windows y escribe `ipconfig`; busca la *Dirección IPv4* (por ejemplo `192.168.1.20`).
4. Desde el otro dispositivo, abre `http://192.168.1.20:3111` (cambia la IP y el puerto por los tuyos).
5. Si Windows te pregunta por el **Firewall** al abrirlo, permite el acceso en **redes privadas**.

> ⚠️ Con esta opción, **cualquier persona de tu red puede usar el panel sin contraseña**: ver tus logs, exportarlos y abrir los históricos. Actívala solo en redes de confianza y desactívala cuando no la necesites. El panel está pensado para pantallas de ordenador; en móvil se ve, pero no está optimizado.

## Qué hace Log Commander

### Cabecera

Cinco tarjetas que se ajustan al ancho de la ventana (si no caben, pasan a una segunda fila):

- **Server:** región e instancia (`US East #100`), dirección IP:puerto y tiempo conectado. Pasa el ratón por el nombre para ver el *shard* completo.
- **Location:** dónde estás, con el lugar en grande y debajo el planeta o luna (**Planet**) y el sistema (**System**), y la zona concreta (**Area**) cuando se conoce, por ejemplo un hangar. Es una estimación a partir de las pistas que aún escribe el juego.
- **Party:** si estás en una party, muestra el líder (**Leader**), el número de miembros (**Members**, una estimación, `~2`) y el tiempo en party (**Time**); el *tooltip* lista los miembros que han entrado mientras el programa miraba. Si no, pone *Not in a party*.
- **Log:** ruta del `Game.log` y líneas leídas.
- **Client:** si el juego está en marcha y su PID.

### Eventos y filtros

Debajo de la cabecera está el **Event Summary**, con cada evento de tu jugador y un contador por tipo. Cada tipo tiene su botón de filtro: pulsa para ocultar o mostrar; **Invert** invierte la selección; **Clear** vacía la lista; **Reprocess** repite el log desde el principio (útil si algo no cuadra). Tipos:

- **Missions:** contratos aceptados, completados, fallidos o compartidos; objetivos; recompensas (con clase/tamaño/grado de componentes y marca `[BP]` de planos); reputación por contrato.
- **Travel:** *Ship requested to hangar*, con la nave (fabricante y modelo, por ejemplo `Anvil ASGARD`), el hangar y el lugar. El nombre de la nave se aprende de otras líneas del log y de logs anteriores; si no se conoce, simplemente no aparece.
- **Location:** cambios de ubicación, con el lugar primero: `Baijini Point (ArcCorp · Stanton)`.
- **Status:** incapacitación y avisos de emergencias.
- **Party:** crear, unirse, salir, líder nuevo, disolución, invitaciones caducadas.
- **Crew:** gente que entra o sale del canal de comunicación de una nave. No tiene que ver con la party.
- **Alerts:** salir del servidor, perder la conexión (con el motivo), tiempos de espera de los servicios del juego y cierre del juego.

> Desde la 4.10 el juego ya no escribe en el log las muertes, kills ni destrucciones de naves, así que esos datos no se pueden mostrar. Todo lo que ves viene de líneas que el juego sigue escribiendo.

### Raw Log Feed

El log en bruto, en vivo, con *Auto-scroll*, *Wrap Text* y *Clear*.

### Iconos de la cabecera

- **Selector de versión** (`LIVE 4.10.2`): cambia entre instalaciones.
- **Historical Logs:** lista los logs anteriores del juego (`logbackups`). Puedes **analizar** o **exportar a HTML** cualquiera, **comparar dos** logs lado a lado, o descargar una **copia anonimizada** (sin IPs, ids de sesión ni de cuenta, ni tu apodo) para compartirla.
- **Session summary:** duración, contratos, reputación ganada, objetivos, recompensas, incapacitaciones, alertas de conexión, lugares visitados, naves pedidas y una línea de tiempo con dónde ocurrió cada hecho. Puedes **guardarlo como imagen** o descargar el log anonimizado.
- **Export Current Log to HTML:** informe del log actual.
- **Conexión (wifi):** estado de la conexión del panel con el programa.
- **Tema:** oscuro o claro (también en *Settings → General → Theme*).
- **About** y **Settings** (*Games*: instalaciones y rutas personalizadas; *General*: tema, puerto, comprobación de actualizaciones, aviso sonoro y acceso por red).

### Aviso sonoro

Opcional (*Settings → General*): suena cuando te incapacitan o te desconectan. Se guarda solo en ese navegador, y algunos navegadores piden un clic en la página antes de permitir el sonido.

### Análisis desde la línea de comandos

Sin abrir el panel, puedes generar un informe HTML de cualquier log:

```
LogCommander.exe analyze "C:\Ruta\Game.log" --output informe.html --format simple
```

## Privacidad y seguridad

- Lo único que sale a internet es la **comprobación de actualizaciones** (a GitHub, al arrancar), que solo envía un *User-Agent*. Todo lo demás se queda en tu PC.
- El panel rechaza peticiones con un `Host` u `Origin` ajeno, así que páginas web abiertas en tu navegador no pueden hablar con él (salvo que actives el acceso por red).
- La actualización solo descarga de GitHub y comprueba el SHA-256 antes de instalar.

## Solución de problemas

- **«Game.log not found»:** inicia Star Citizen al menos una vez, o añade tu carpeta en *Settings → Games → Add Custom Path*.
- **No carga <http://localhost:3111>:** comprueba que la consola sigue abierta y que el puerto no está en uso; puedes cambiarlo en *Settings → General*.
- **SmartScreen o el antivirus avisan:** el exe no está firmado; es normal en programas pequeños sin certificado.
- **La party o el servidor no se corresponden con lo que ves:** pulsa **Reprocess**. El programa solo puede reflejar lo que el juego escribe en el log.
- **Los eventos no salen en versiones del juego muy anteriores a la 4.10:** el programa está hecho para el formato de log actual.

---

# English

Log Commander follows your `Game.log` while you play and turns it into a cockpit-style web dashboard: readable events, server info, your location, your party and tools to review past sessions. It runs on **Windows 10/11** and was tested with Star Citizen **4.10.2**.

> The source code is not published here; this page only holds the program (`LogCommander.exe`) and its release notes.

## Quick start

1. **Download** `LogCommander.exe` from [Releases](../../releases/latest). Each version also ships `LogCommander.exe.sha256`; to verify the file, run `certutil -hashfile LogCommander.exe SHA256` and compare the result.
2. **Put it in its own folder**, for example `C:\Tools\LogCommander\`. It must be somewhere you can write to: the program keeps its settings (`logcommander_config.json`) there and updates itself in place.
3. **Double-click it.** Windows SmartScreen may warn that the app is unknown, because the exe is not code-signed: click *More info → Run anyway*. A console window opens; **keep it open while you use the app**.
4. **Pick your game install.** It searches every drive for Star Citizen (LIVE, PTU, EPTU). If you have several, the console lets you choose. If none is found, it asks for the path. The game must have been launched at least once, so that `Game.log` exists.
5. **Open the dashboard in your browser:** <http://localhost:3111>. The console prints the address but does not open the browser by itself: **hold `Ctrl` and click the address** shown in the console and it opens in your default browser (in Windows Terminal; if your console does not allow it, copy the address and paste it into the browser). You can change the port in *Settings → General → Web Server Port* (needs a restart).
6. **Play.** The dashboard follows the log live. If you start the program while the game is already running, it replays the whole session log and catches up, so you can open it at any time.
7. **To quit,** press `Ctrl+C` in the console or close its window.

### Updating

- At startup the program **checks for a newer version** (one request to GitHub, sending none of your data) and **never installs anything without asking you**.
- From **1.2.2** on, if there is a newer version the console says so before the dashboard loads: `[I]` install now, `[L]` later (the default after 20 seconds) or `[S]` skip that version. It never blocks a launch without an interactive console.
- The web dashboard also shows a banner at the top, with *Install now*, *Later* and *Skip this version*.
- To check on demand: *Settings → About → Check for updates*.
- When installing, the program downloads the new file, **verifies its SHA-256**, swaps it in and restarts. It keeps your previous version as `LogCommander.exe.bak` and **keeps your settings**.
- Versions **1.2.0 and 1.2.1** have a restart bug: they may show `Update failed: [WinError 87]` even though the file was already updated. Nothing is wrong: close the program and open it again. From 1.2.2 on it restarts by itself.
- You can turn the check off in *Settings → General → Check for new versions at startup*.

### Opening it from another device on your local network

By default the dashboard only answers on the PC itself (`127.0.0.1`). To see it from a phone, tablet or another PC at home:

1. In the dashboard: *Settings → General → **Allow network access (listen on 0.0.0.0)***. Confirm the warning.
2. **Restart** Log Commander (close the console and open it again).
3. Find your PC's IP: open a Windows console and type `ipconfig`; look for the *IPv4 Address* (for example `192.168.1.20`).
4. On the other device, open `http://192.168.1.20:3111` (use your own IP and port).
5. If Windows asks about the **Firewall**, allow access on **private networks**.

> ⚠️ With this option **anyone on your network can use the dashboard without a password**: see your logs, export them and open past ones. Only enable it on networks you trust and turn it off when you do not need it. The dashboard is designed for desktop screens; on a phone it works but is not optimised.

## What Log Commander does

### Header

Five cards that size themselves to the window (when they do not fit, they wrap to a second row):

- **Server:** region and instance (`US East #100`), IP:port address and time connected. Hover the name to see the full *shard*.
- **Location:** where you are, with the place in large type and below it the planet or moon (**Planet**), the system (**System**) and the specific area (**Area**) when known, such as a hangar. It is an estimate from the clues the game still writes.
- **Party:** when you are in a party it shows the leader (**Leader**), the number of members (**Members**, an estimate, `~2`) and time in the party (**Time**); the tooltip lists the members who joined while the program was watching. Otherwise it says *Not in a party*.
- **Log:** the `Game.log` path and lines read.
- **Client:** whether the game is running, and its PID.

### Events and filters

Below the header, **Event Summary** lists every event of your player with a counter per type. Each type has a filter button: click to hide or show; **Invert** inverts the selection; **Clear** empties the list; **Reprocess** replays the log from the start (handy if something looks off). Types:

- **Missions:** contracts accepted, completed, failed or shared; objectives; rewards (with component class/size/grade and `[BP]` blueprint marks); reputation per contract.
- **Travel:** *Ship requested to hangar*, with the ship (maker and model, for example `Anvil ASGARD`), the hangar and the place. The ship's name is learned from other log lines and from earlier logs; if it is not known, it is simply left out.
- **Location:** location changes, place first: `Baijini Point (ArcCorp · Stanton)`.
- **Status:** incapacitation and emergency-services notices.
- **Party:** creating, joining, leaving, a new leader, disbanding, expired invites.
- **Crew:** people joining or leaving a ship's communication channel. Not related to the party.
- **Alerts:** leaving the server, losing the connection (with the reason), game service timeouts and the game closing.

> Since 4.10 the game no longer writes deaths, kills or ship destruction to the log, so those cannot be shown. Everything you see comes from lines the game still writes.

### Raw Log Feed

The raw log, live, with *Auto-scroll*, *Wrap Text* and *Clear*.

### Header icons

- **Version selector** (`LIVE 4.10.2`): switch between installs.
- **Historical Logs:** lists the game's previous logs (`logbackups`). You can **analyse** or **export to HTML** any of them, **compare two** logs side by side, or download an **anonymised copy** (no IPs, session or account ids, or your nickname) to share.
- **Session summary:** duration, contracts, reputation earned, objectives, rewards, incapacitations, connection alerts, places visited, ships requested and a timeline showing where each moment happened. You can **save it as an image** or download the anonymised log.
- **Export Current Log to HTML:** a report of the current log.
- **Connection (wifi):** the dashboard's connection status with the program.
- **Theme:** dark or light (also in *Settings → General → Theme*).
- **About** and **Settings** (*Games*: installs and custom paths; *General*: theme, port, update checks, sound alert and network access).

### Sound alert

Optional (*Settings → General*): it sounds when you are incapacitated or disconnected. It is saved in that browser only, and some browsers need one click on the page before they allow sound.

### Command-line analysis

Without opening the dashboard, you can build an HTML report of any log:

```
LogCommander.exe analyze "C:\Path\Game.log" --output report.html --format simple
```

## Privacy and security

- The only thing that goes to the internet is the **update check** (to GitHub, at startup), which sends only a *User-Agent*. Everything else stays on your PC.
- The dashboard rejects requests with a foreign `Host` or `Origin`, so web pages open in your browser cannot talk to it (unless you enable network access).
- Updating only downloads from GitHub and checks the SHA-256 before installing.

## Troubleshooting

- **"Game.log not found":** launch Star Citizen at least once, or add your folder in *Settings → Games → Add Custom Path*.
- **<http://localhost:3111> does not load:** check that the console is still open and that the port is not in use; you can change it in *Settings → General*.
- **SmartScreen or your antivirus warns:** the exe is not code-signed; that is normal for small programs without a certificate.
- **Party or server do not match what you see:** click **Reprocess**. The program can only reflect what the game writes to the log.
- **Events are missing on game versions well before 4.10:** the program is built for the current log format.

---

## Créditos / Credits

- Inspirado en **StarLogs**, de [Ozy311](https://github.com/Ozy311/StarLogs). / Inspired by **StarLogs** by [Ozy311](https://github.com/Ozy311/StarLogs).
- Datos de componentes y contratos derivados del paquete de idioma de la comunidad [ExoAE/ScCompLangPack](https://github.com/ExoAE/ScCompLangPack). Textos del juego © Cloud Imperium Games. / Component and contract data derived from the community language pack [ExoAE/ScCompLangPack](https://github.com/ExoAE/ScCompLangPack). Game text © Cloud Imperium Games.
- Proyecto de fans, no afiliado a Cloud Imperium Games ni respaldado por ellos. / A fan project, not affiliated with or endorsed by Cloud Imperium Games.
- Desarrollado por **Kosak0**. / Developed by **Kosak0**.
