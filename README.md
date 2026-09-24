# RTMP Web

Aplicación web local para recibir la señal de una DJI Osmo Pocket 3 y verla en el navegador.

## Idea

La Osmo publica por RTMP en el ordenador. Un único proceso SRS recibe la señal y genera HLS. La aplicación web muestra ese HLS y también muestra la URL RTMP para usarla en OBS/VLC.

```text
DJI Osmo Pocket 3 ── RTMP ──> SRS :1935 ── HLS ──> RTMP Web :17890
                                      └── RTMP playback ──> OBS/VLC
```

No hay transcodificación: la señal conserva los codecs enviados por la cámara. Esta versión está pensada para una red local de confianza y no implementa autenticación.

## Requisitos

- Go 1.23 o superior.
- SRS 6.x instalado o copiado en `data/runtime/srs/srs`.
- La Osmo y el ordenador en la misma LAN.
- Windows 10/11 o macOS.

## Ejecutar

```bash
go run .
```

Abre automáticamente:

```text
http://127.0.0.1:17890
```

La aplicación abre los puertos RTMP 1935 y HLS interno 8080. El firewall solo necesita permitir el puerto TCP 1935 en la red privada.

## Configurar la Osmo

Usa los datos que aparecen en la web:

- Servidor: `rtmp://IP_DEL_PC:1935/live`
- Stream key: la clave mostrada en la interfaz
- Vídeo: H.264
- Resolución: 1920×1080
- FPS: 60
- Audio: AAC
- Bitrate: 12–16 Mbps
- GOP/keyframe: 1–2 segundos

## Ver la señal

- **Navegador:** la aplicación web usa el HLS local.
- **OBS:** añade una fuente **VLC Video Source** y pega la URL RTMP de reproducción mostrada en la web.
- **VLC:** también puedes abrir la URL HLS o RTMP directamente.

## Instalar SRS

Descarga un binario SRS 6.x compatible con tu sistema desde [la página de releases de SRS](https://github.com/ossrs/srs/releases).

### macOS

Compila SRS 6.x y copia el binario a:

```text
data/runtime/srs/srs
```

### Windows

Copia `srs.exe` a:

```text
data\runtime\srs\srs.exe
```

También puedes indicar otra ruta al arrancar:

```bash
go run . --srs /ruta/completa/a/srs
```

## Compilar

```bash
go build -o rtmp-web .
```

En Windows:

```powershell
go build -o rtmp-web.exe .
```

## Configuración avanzada

Se puede cambiar el directorio de datos y los puertos desde la línea de comandos:

```bash
go run . --data-dir ./data --rtmp-port 1935 --hls-port 8080 --web-port 17890
```
