# 🛠️ CarbonHunter: Sensor de CO₂ con ESP32-S3 y SCD41

**CarbonHunter** es un sistema basado en ESPHome para medir **CO₂, temperatura y humedad** usando un **ESP32-S3** y un sensor **SCD41**, con integración completa en **Home Assistant**.

---

Este es el sensor que he usado:


## 📡 Conexión del hardware

**Placa:** ESP32-S3 DevKitC  
![PlacaESP32-S3](https://github.com/user-attachments/assets/42622657-db03-40b7-9780-662f0f9d436f)

**Sensor:** SCD41 (I2C)  
![SensorSCD41](https://github.com/user-attachments/assets/1e9cc918-5c90-4bf2-b115-dc7bbb817aa9)



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
  ssid: "TuWifi"
  password: "LaClaveDeTuWifi"

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
    update_interval: 30s
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

### 🖥️ Preparar la placa

Para flashear el firmware en la **ESP32-S3**, sigue estos pasos:

1. Con la placa **Desconectada**.
2. **Mantén pulsado el botón BOOT** de la placa.
3. **Mientras mantienes BOOT pulsado**, contecta la placa al usb y después de 5 segundos suéltalo.
4. Inicia la carga del firmware con el siguiente comando:

```bash
esphome run carbonhunter.yaml --device COM7
```

6. Una vez completada la carga, **reinicia la placa** sin mantener pulsado el botón **BOOT** para que arranque normalmente.

7. El dispositivo se conectará automáticamente a Home Assistant.

---

## 🔍 Diagnóstico y resolución de problemas

### 🧪 Verificar comunicación I2C

Para ver a que puerto está conectado:

```bash
mode
```

Si el sensor no responde, usa:

```bash
esphome logs carbonhunter.yaml --device COMCONECTADO
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

## ⚠️ Consideraciones sobre el `update_interval`

Ajustar el `update_interval` del sensor SCD41 a **1 segundo** puede generar problemas importantes debido a las características del sensor.  

### 🚨 **Riesgos de usar 1s**:

- **Mayor desgaste del sensor**:  
  El SCD41 está diseñado para realizar lecturas periódicas, no constantes.  
  Forzarlo a medir cada segundo puede **acortar su vida útil**.  

- **Aumento del consumo de energía**:  
  El sensor utiliza un **láser de infrarrojos** para medir el CO₂, lo que aumenta significativamente el consumo si se actualiza cada segundo.  

- **Inexactitud por falta de estabilización**:  
  El sensor necesita **tiempo para estabilizarse** entre mediciones. Con actualizaciones muy frecuentes, las lecturas pueden ser erráticas.  

- **Saturación de Home Assistant**:  
  Actualizar cada segundo genera una carga innecesaria de datos, ralentizando el sistema.  

### ✅ **Recomendación**:
- **Mínimo seguro**: **5 segundos**.  
- **Óptimo**: **30 segundos** para obtener datos estables y precisos.  
---

## 💡 Notas adicionales

- **Calibración inicial:** El SCD41 necesita entre **5 y 30 minutos** la primera vez.  
- **Puertos I2C:** **Siempre usa GPIO8 (SDA) y GPIO9 (SCL)**.  
- **Integración Home Assistant:** Añádelo desde **Ajustes → Integraciones → ESPHome** usando la IP del dispositivo.  

🎯 **CarbonHunter está diseñado para facilitar el control de calidad del aire con una configuración optimizada y eficiente.**

