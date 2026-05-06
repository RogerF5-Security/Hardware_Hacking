content = """<div align="center">
  <h1>🛡️ Fundamentos de Hardware Hacking</h1>
  <p><b>Auditoría de Seguridad Física, Análisis de Señales y Explotación Controlada</b></p>
  
  [![Licencia MIT](https://img.shields.io/badge/Licencia-MIT-blue.svg)](LICENSE)
  [![Enfoque](https://img.shields.io/badge/Enfoque-Offensive%20Security-red.svg)]()
  [![Estado](https://img.shields.io/badge/Estado-Activo-success.svg)]()
</div>

---

Este repositorio centraliza los recursos, presentaciones y documentación técnica del curso **"Fundamentos de Hardware Hacking"**. El material está diseñado con un rigor técnico avanzado, orientado exclusivamente a la auditoría de seguridad en dispositivos físicos, validación de controles de acceso y ejecución de vectores de ataque físico (Physical Pentesting) en entornos corporativos autorizados.

## 📑 Índice

1. [Acerca del Proyecto](#-acerca-del-proyecto)
2. [Contenido del Curso](#-contenido-del-curso)
3. [Herramientas y Dispositivos de Auditoría](#-herramientas-y-dispositivos-de-auditoría)
4. [Proyectos Web y Emuladores](#-proyectos-web-y-emuladores)
5. [Referencias y Recursos Externos](#-referencias-y-recursos-externos)
6. [Marco Legal y Ético](#-marco-legal-y-ético)

---

## 🎯 Acerca del Proyecto

Desarrollado y estructurado para la formación y estandarización de pruebas de intrusión física. Este repositorio contiene las bases teóricas y las pruebas de concepto (PoC) documentadas para el despliegue de payloads, inyección de pulsaciones (HID) y clonación/emulación de credenciales RFID/NFC, así como la interacción con protocolos de radiofrecuencia (Sub-GHz). 

---

## 📂 Contenido del Curso

El material didáctico principal se divide en módulos secuenciales, abarcando desde los principios eléctricos hasta la explotación avanzada.

| Archivo | Descripción del Módulo |
| :--- | :--- |
| 📄 [`Clase01.pdf`](./Clase01.pdf) | **Introducción al Hardware Hacking:** Conceptos base de electrónica orientada al pentesting, reconocimiento de componentes e interfaces de depuración (UART, JTAG, SPI). |
| 📄 [`Clase02.pdf`](./Clase02.pdf) | **Análisis de Protocolos y Señales:** Auditoría de comunicaciones inalámbricas, interceptación de tráfico y manipulación de datos en tránsito. |
| 📄 [`Clase3.pdf`](./Clase3.pdf) | **Vectores de Ataque Físico:** Técnicas de bypass de controles físicos, inyección de código mediante dispositivos HID y escalada de privilegios local. |
| 📄 [`Clase04.pdf`](./Clase04.pdf) | **Explotación y Post-Explotación Controlada:** Integración de herramientas, exfiltración de datos y elaboración de reportes técnicos de vulnerabilidades físicas. |
| 📘 [`GUIA_FLIPPER_APP.md`](./GUIA_FLIPPER_APP.md) | **Desarrollo y Payloads para Flipper Zero:** Documentación técnica sobre despliegue de payloads personalizados y automatización de validaciones de seguridad inalámbrica. |

---

## 🧰 Herramientas y Dispositivos de Auditoría

El curso y los recursos del repositorio profundizan en el uso de hardware especializado para pruebas ofensivas:

* **[Flipper Zero](https://flipperzero.one/):** Empleado para la auditoría de control de acceso, clonación de tarjetas RFID/NFC, emulación iButton y análisis de controles remotos (Sub-GHz).
* **[M5StickC Plus 2](https://shop.m5stack.com/):** Utilizado para la automatización de ataques WiFi (Deauth, Evil Twin) y Bluetooth (BLE), así como el desarrollo de herramientas portátiles personalizadas mediante MicroPython/C++.
* **[USB Rubber Ducky (BadUSB)](https://shop.hak5.org/):** Despliegue de scripts en DuckyScript para pruebas de inyección HID (Human Interface Device), validando políticas de seguridad en estaciones de trabajo y sistemas bloqueados.

---

## 🌐 Proyectos Web y Emuladores

Como parte del ecosistema de este repositorio, se han desarrollado e integrado las siguientes plataformas web para facilitar la visualización y emulación de los entornos de prueba:

> **🖥️ [Sitio Oficial del Curso - Hardware Hacking](https://rogerf5-security.github.io/Hardware_Hacking/)** > Portal de acceso rápido a los materiales estáticos, alojado mediante GitHub Pages (`index.html`). Sirve como índice principal para la visualización web de los conceptos impartidos.

> **🕹️ [Simulador Didáctico Flipper Zero](https://rogerf5-security.github.io/Flipper-Zero-Emulador-Didactico-Web/)** > Una herramienta web interactiva diseñada para emular y comprender el funcionamiento de la interfaz y módulos del Flipper Zero sin necesidad de contar con el hardware físico. Ideal para preparar payloads antes del despliegue en campo.

---

## 📚 Referencias y Recursos Externos

Para profundizar en la creación de herramientas, scripts y comprensión de los vectores físicos, se recomienda la siguiente documentación oficial:

1. **Documentación Flipper Zero:** [docs.flipper.net](https://docs.flipper.net/) - Guías oficiales de firmware, GPIO y desarrollo de aplicaciones (.fap).
2. **Hak5 Payload Hub:** [github.com/hak5/usbrubberducky-payloads](https://github.com/hak5/usbrubberducky-payloads) - Repositorio central de PoCs en DuckyScript para inyección HID.
3. **M5Stack Docs (M5StickC Plus 2):** [docs.m5stack.com](https://docs.m5stack.com/) - Referencia de hardware y librerías para ESP32-PICO-V3-02.
4. **Awesome Hardware Hacking:** [github.com/mottosson/awesome-hardware-hacking](https://github.com/mottosson/awesome-hardware-hacking) - Lista curada de recursos y herramientas.

---

## ⚖️ Marco Legal y Ético

> **⚠️ Advertencia Profesional:** Todo el contenido, esquemas, técnicas de bypassing y payloads documentados en este repositorio están estrictamente destinados a **auditorías de seguridad profesionales y entornos de prueba controlados**. Toda interacción técnica descrita aquí asume la existencia de autorización legal explícita por parte de los propietarios de la infraestructura auditada.

<div align="center">
  <i>Desarrollado y mantenido por <b>Roger F5</b> | Ingeniero Especializado en Ciberseguridad Ofensiva</i>
</div>