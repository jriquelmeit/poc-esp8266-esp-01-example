# ESP8266 ESP-01 / ESP-01S Wi-Fi PoC

PoC (Prueba de Concepto) para conectar un módulo **ESP8266 ESP-01 / ESP-01S** a una red Wi-Fi de 2.4 GHz y monitorear telemetría y diagnósticos vía puerto serial.

Desarrollado para **CLion** y compilado con **PlatformIO**.

---

## 🔬 Hardware y Diagnósticos Confirmados

A través de las pruebas de memoria y diagnóstico del chip, se validó el hardware real:
- **Microcontrolador:** ESP8266EX (80 MHz CPU, 80 KB RAM).
- **Memoria Flash:** **1 MB (1048576 bytes / 8 Mbit)** (comprobado vía `ESP.getFlashChipRealSize()`).
- **Programador USB:** ESP Prog v1.0 IoTMcu (conversor serie CH340).

---

## ⚠️ Hallazgo Crítico de Configuración: Modo Flash `dout`

Por defecto, la definición `esp01_1m` en PlatformIO compila en modo **`qio`** (Quad I/O SPI). Sin embargo, el módulo ESP-01 estándar solo tiene enrutadas las pistas para modo Dual SPI.

> **Problema:** Si se graba con `qio`, el proceso de escritura termina sin errores, pero la ROM del chip no puede leer la memoria y se queda **congelado al arrancar**.
> 
> **Solución:** En `platformio.ini` es mandatorio configurar:
> ```ini
> board_build.flash_mode = dout
> ```

---

## 🔌 Proceso de Flasheo (ESP Prog v1.0 IoTMcu)

### 1. Modo Flasheo (Subida de Firmware)
1. **Desconecta** el programador USB de la computadora.
2. Coloca un **puente (jumper o cable) entre el pin `GPIO0` y `GND`**.
3. Conecta el adaptador al puerto USB (el módulo arrancará en modo bootloader/descarga).
4. Ejecuta la subida de firmware:
   - En CLion: Selecciona el target **`PLATFORMIO_UPLOAD`** y pulsa **Run**.
   - O en terminal:
     ```bash
     pio run -t upload
     ```

### 2. Modo Ejecución / Monitor Serial
1. **Retira el puente entre `GPIO0` y `GND`** (si permanece conectado, el ESP no ejecutará el programa).
2. **Reconecta** el USB (o pulsa el botón de reset de la plaqueta).
3. Abre el monitor serial a **115200 baudios**:
   - En terminal o terminal integrado de CLion:
     ```bash
     pio device monitor
     ```

> **Tip para CLion:** Si ejecutas el target `PLATFORMIO_MONITOR` directamente con el botón Run de CLion, entra a *Edit Configurations...* y activa la casilla ☑ **"Emulate terminal in the output console"** para evitar el error de terminal no interactivo.

---

## 🛠️ Configuración de Red Wi-Fi

1. Copia la plantilla de configuración:
   ```bash
   cp include/config.h.example include/config.h
   ```
2. Edita `include/config.h` con los datos de tu red:
   ```cpp
   #define WIFI_SSID "Tu_Red_WiFi_2.4G"
   #define WIFI_PASSWORD "Tu_Password"
   ```
   *(Este archivo se encuentra en `.gitignore` para proteger tus credenciales).*

---

## 💻 Comandos PlatformIO CLI

* **Compilar:**
  ```bash
  pio run
  ```
* **Subir Firmware (con GPIO0 a GND):**
  ```bash
  pio run -t upload
  ```
* **Monitor Serial (115200 baud):**
  ```bash
  pio device monitor
  ```
* **Limpiar compilación:**
  ```bash
  pio run -t clean
  ```

---

## 📁 Estructura del Proyecto

```text
├── CMakeLists.txt              # Configuración y targets para CLion
├── platformio.ini              # Configuración de entorno (board=esp01_1m, flash_mode=dout)
├── include/
│   ├── config.h.example        # Plantilla pública de configuración Wi-Fi
│   └── config.h                # Credenciales locales (ignorado en Git)
├── src/
│   └── main.cpp                # Diagnóstico, conexión Wi-Fi y latido de LED
├── .gitignore                  # Exclusiones de Git (.pio, config.h, etc.)
└── README.md                   # Documentación técnica
```
