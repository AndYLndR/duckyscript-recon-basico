# Mitigación y Defensa

## 🛡️ Técnicas defensivas

| Vector | Mitigación |
|--------|-----------|
| BadUSB (HID) | GPO/Intune bloqueando clases USB no autorizadas |
| PowerShell ofuscado | Constrained Language Mode + AMSI |
| `-EncodedCommand` | Script Block Logging (Event ID 4104) |
| Exfiltración a Discord | Bloqueo de `discord.com/api/webhooks` a nivel de proxy/DNS |
| Ventana oculta | Detección heurística de `powershell -WindowStyle Hidden` |
| Persistencia | ASR rules de Microsoft Defender |

## 🔍 Indicadores de Compromiso (IoC)

- `powershell.exe` con `-WindowStyle Hidden -EncodedCommand`.
- Procesos hijos de `explorer.exe` sin interacción del usuario.
- Conexiones salientes a `discord.com` desde equipos no autorizados.
- Ráfagas de tecleo a velocidad sobrehumana (detección HID).
- Cadena Base64 con patrón UTF-16LE (`JAB` inicial típico).

## 👥 Concienciación

- No conectar USB desconocidos (found USB = drop USB).
- Bloqueadores físicos de puertos.
- Formación frente a phishing físico / tailgating.

## 🧰 Herramientas

- Microsoft Defender for Endpoint
- Sysmon + reglas Sigma
- USBGuard (Linux) / GPO (Windows)
- Velociraptor / Wazuh / Wireshark (para hunting)
