# 🛠️ CarbonHunter: Sensor de CO₂ con ESP32-S3 y SCD41

**CarbonHunter** es un sistema basado en ESPHome para medir **CO₂, temperatura y humedad** usando un **ESP32-S3** y un sensor **SCD41**, con integración completa en **Home Assistant**.

---

## 📡 Conexión del hardware

**Placa:** ESP32-S3 DevKitC  
**Sensor:** SCD41 (I2C)  

| **SCD41** | **ESP32-S3** | **Color sugerido** |
|-----------|--------------|--------------------|
| **VDD**   | **3.3 V**    | Rojo               |
| **GND**   | **GND**      | Negro              |
| **SDA**   | **GPIO8**    | Verde              |
| **SCL**   | **GPIO9**    | Amarillo           |

🔧 **Notas importantes:**
- Usa **GPIO8** y **GPIO9** para el bus I2C.
- Alimenta el sensor a **3.3 V**.
- El módulo SCD41 incluye **resistencias pull-up integradas**.

---

## ⚙️ Configuración de ESPHome

Este es el archivo **`carbonhunter.yaml`**:

```yaml
esphome:
  name: carbonhunter2
  platform: esp32
  board: esp32-s3-devkitc-1

wifi:
  ssid: "Camelot"
  password: "8K[Z7x~C\"NW:AsagrRKb"

api:

ota:
  platform: esphome
  password: ""

logger:
  level: DEBUG

i2c:
  sda: 8
  scl: 9
  scan: true

sensor:
  - platform: scd4x
    update_interval: 5s
    co2:
      name: "CarbonHunter CO2"
      unit_of_measurement: "ppm"
    temperature:
      name: "CarbonHunter Temperatura"
      unit_of_measurement: "°C"
    humidity:
      name: "CarbonHunter Humedad"
      unit_of_measurement: "%"

```

---

## 📲 Instalación y despliegue

1. Conecta el ESP32-S3 al PC.
2. Abre una terminal y ejecuta:

```bash
esphome run carbonhunter.yaml --device COM7
```

3. Una vez flasheado, el dispositivo se conectará automáticamente a Home Assistant.

---

## 🔍 Diagnóstico y resolución de problemas

### 🧪 Verificar comunicación I2C

Si el sensor no responde, usa:

```bash
esphome logs carbonhunter.yaml --device COM7
```

**Resultado esperado:**
```
[I][i2c.arduino:XXX]: Found i2c device at address 0x62
```

### ⚠️ Errores comunes

| **Error**                                          | **Causa**                          | **Solución**                                |
|----------------------------------------------------|------------------------------------|--------------------------------------------|
| `No I2C devices found`                             | Pines incorrectos                  | Usa **GPIO8 (SDA)** y **GPIO9 (SCL)**.      |
| `Timeout reading from I2C`                         | Sensor no alimentado               | Verifica conexión **VDD a 3.3 V**.          |
| `Wi-Fi no conecta`                                 | Contraseña incorrecta              | Revisa **SSID y password**.                 |
| El sensor muestra 400 ppm constante                | Calibración inicial                | Déjalo encendido **30 min en aire limpio**. |
| LED integrado no responde                          | Pin incorrecto                     | Cambiar **GPIO2** por **GPIO13**.           |

---

## 💡 Notas adicionales

- **Calibración inicial:** El SCD41 necesita entre **5 y 30 minutos** la primera vez.  
- **Puertos I2C:** **Siempre usa GPIO8 (SDA) y GPIO9 (SCL)**.  
- **Integración Home Assistant:** Añádelo desde **Ajustes → Integraciones → ESPHome** usando la IP del dispositivo.  

🎯 **CarbonHunter está diseñado para facilitar el control de calidad del aire con una configuración optimizada y eficiente.**

