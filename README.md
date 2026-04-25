# 📱 Guía Completa: Programar Apps para Flipper Zero

> **Para la clase de Flipper Zero** — Roger Arana  
> Nivel: Intermedio | Lenguaje: C | SDK: Flipper Zero Firmware

---

## Índice

1. [¿Qué es una FAP?](#1-qué-es-una-fap)
2. [Prerrequisitos del sistema](#2-prerrequisitos-del-sistema)
3. [Instalar ufbt (Micro Flipper Build Tool)](#3-instalar-ufbt)
4. [Estructura del proyecto](#4-estructura-del-proyecto)
5. [El archivo application.fam](#5-el-archivo-applicationfam)
6. [Conceptos clave del SDK](#6-conceptos-clave-del-sdk)
7. [Comandos de compilación](#7-comandos-de-compilación)
8. [Instalar la app en el Flipper](#8-instalar-la-app-en-el-flipper)
9. [Flujo de desarrollo completo](#9-flujo-de-desarrollo-completo)
10. [Depuración y errores comunes](#10-depuración-y-errores-comunes)
11. [Recursos](#11-recursos)

---

## 1. ¿Qué es una FAP?

Una **FAP** (Flipper Application Package) es una aplicación externa compilada para el Flipper Zero. Se diferencia de las apps integradas al firmware en que:

- Se distribuye como un archivo `.fap` (ejecutable del Flipper)
- No requiere recompilar el firmware completo
- Se carga desde la tarjeta SD: `SD:/apps/<Categoría>/mi_app.fap`
- Puede instalarse/actualizarse sin tocar el firmware

### Arquitectura general

```
┌─────────────────────────────────────┐
│           Firmware Flipper          │
│  ┌──────┐ ┌──────┐ ┌────────────┐  │
│  │ GUI  │ │Input │ │Notification│  │  ← APIs del sistema
│  └──┬───┘ └──┬───┘ └─────┬──────┘  │
│     │        │            │         │
│  ┌──▼────────▼────────────▼──────┐  │
│  │         Tu FAP (.fap)         │  │  ← Tu código
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

---

## 2. Prerrequisitos del sistema

### En Windows
```powershell
# Instalar Python 3.8+
winget install Python.Python.3.11

# Instalar Git
winget install Git.Git

# Instalar drivers USB (qFlipper incluye los drivers)
# Descargar qFlipper: https://flipperzero.one/update
```

### En Linux / macOS
```bash
# Python 3.8+ y Git (probablemente ya instalados)
python3 --version  # debe ser >= 3.8
git --version

# macOS: instalar Xcode Command Line Tools si hace falta
xcode-select --install
```

### Verificar instalación
```bash
python3 -m pip install --version  # pip disponible
git --version                      # git disponible
```

---

## 3. Instalar ufbt

**ufbt** (Micro Flipper Build Tool) es la herramienta oficial recomendada. Descarga automáticamente el toolchain (compilador ARM GCC, SDK, etc.).

```bash
# Instalar ufbt via pip
python3 -m pip install ufbt

# Verificar instalación
ufbt --version

# Actualizar el SDK de Flipper (¡siempre hacer esto primero!)
ufbt update
```

> **Nota**: `ufbt update` descarga el compilador ARM y el SDK (~200 MB la primera vez).

### Alternativa: SDK completo

Si quieres compilar el firmware completo o necesitas más control:

```bash
git clone --recursive https://github.com/flipperdevices/flipperzero-firmware.git
cd flipperzero-firmware
./fbt
```

Para esta guía usaremos **ufbt** (más simple).

---

## 4. Estructura del proyecto

Una app mínima requiere dos archivos:

```
futbol_score/
├── application.fam      ← Manifiesto (REQUERIDO)
├── futbol_score.c       ← Código fuente C (REQUERIDO)
└── football.png         ← Icono 10×10 px PNG (opcional)
```

### Estructura multi-archivo (proyectos grandes)

```
mi_app/
├── application.fam
├── mi_app.c             ← Entry point
├── views/
│   ├── main_view.c      ← Pantalla principal
│   └── menu_view.c      ← Menú
├── helpers/
│   └── utils.c          ← Utilidades
└── assets/
    └── icon.png
```

---

## 5. El archivo application.fam

Este archivo Python describe la app al sistema de build:

```python
App(
    appid           = "mi_app_unica",         # ← ID único, sin espacios ni símbolos
    name            = "Mi Aplicación",        # ← Nombre en el menú del Flipper
    apptype         = FlipperAppType.EXTERNAL,# ← Siempre EXTERNAL para FAPs
    entry_point     = "mi_app_main",          # ← Nombre exacto de tu función int32_t
    requires        = [                       # ← Módulos del sistema que usas
        "gui",
        "notification",
        # "storage",   # Para leer/escribir SD
        # "dialogs",   # Para diálogos del sistema
    ],
    stack_size      = 2 * 1024,               # ← Stack en bytes (2KB típico)
    fap_icon        = "icon.png",             # ← PNG 10×10 px
    fap_category    = "Games",               # ← Carpeta en el menú
    fap_version     = (1, 0),                 # ← (mayor, menor)
    fap_description = "Descripción corta",
    fap_author      = "Tu Nombre",
)
```

### Categorías disponibles

| Categoría      | Ubicación en SD              |
|---------------|------------------------------|
| `"Games"`     | `SD:/apps/Games/`            |
| `"Tools"`     | `SD:/apps/Tools/`            |
| `"NFC"`       | `SD:/apps/NFC/`              |
| `"Sub-GHz"`   | `SD:/apps/Sub-GHz/`          |
| `"GPIO"`      | `SD:/apps/GPIO/`             |
| `"Bluetooth"` | `SD:/apps/Bluetooth/`        |

---

## 6. Conceptos clave del SDK

### 6.1 La función de entrada

```c
// Siempre retorna int32_t y recibe void*
// El nombre DEBE coincidir con entry_point en application.fam
int32_t mi_app_main(void* p) {
    UNUSED(p);  // p suele ser NULL

    // Inicializar
    // ...

    // Bloquear hasta que el usuario salga
    view_dispatcher_run(vd);

    // Limpiar y salir
    // ...
    return 0;
}
```

### 6.2 Headers más usados

```c
#include <furi.h>                              // Core: memoria, threads, timers
#include <gui/gui.h>                           // Sistema gráfico
#include <gui/view_dispatcher.h>               // Gestor de vistas
#include <gui/modules/text_input.h>            // Teclado de texto
#include <gui/modules/variable_item_list.h>    // Lista de opciones
#include <gui/modules/submenu.h>               // Submenú
#include <input/input.h>                       // Botones físicos
#include <notification/notification.h>         // LED, vibración, sonido
#include <notification/notification_messages.h>// Secuencias predefinidas
#include <storage/storage.h>                   // Sistema de archivos (SD)
```

### 6.3 Sistema de vistas (View Dispatcher)

```
ViewDispatcher
    ├── View 0 → tu draw_callback + input_callback
    ├── View 1 → TextInput (módulo del SDK)
    └── View 2 → Submenu  (módulo del SDK)
```

```c
// 1. Crear dispatcher
ViewDispatcher* vd = view_dispatcher_alloc();
view_dispatcher_enable_queue(vd);
view_dispatcher_attach_to_gui(vd, gui, ViewDispatcherTypeFullscreen);

// 2. Registrar callbacks globales
view_dispatcher_set_custom_event_callback(vd, mi_custom_cb);
view_dispatcher_set_navigation_event_callback(vd, mi_back_cb);
view_dispatcher_set_event_callback_context(vd, &mi_app);

// 3. Agregar vistas (cada una con un ID único)
view_dispatcher_add_view(vd, 0, mi_vista);

// 4. Mostrar una vista
view_dispatcher_switch_to_view(vd, 0);

// 5. Correr el loop de eventos (bloquea hasta que se detenga)
view_dispatcher_run(vd);
```

### 6.4 Canvas (dibujo en pantalla)

La pantalla es **128×64 píxeles**, monocromática (negro/blanco).

```c
// Callback de dibujo: se llama automáticamente cuando la vista necesita redibujarse
static void mi_draw_cb(Canvas* c, void* ctx) {
    MiApp* a = ctx;

    canvas_clear(c);                    // Limpiar pantalla (blanco)
    canvas_set_color(c, ColorBlack);    // Color de dibujo

    // Fuentes disponibles:
    //   FontPrimary    → mediana, proporcional
    //   FontSecondary  → pequeña (para hints)
    //   FontBigNumbers → números grandes (ideal para marcadores)
    canvas_set_font(c, FontBigNumbers);

    // Texto centrado en coordenada X, Y desde esquina superior izquierda
    canvas_draw_str_aligned(c, 64, 32, AlignCenter, AlignCenter, "TEXTO");

    // Rectángulo sólido (para barras invertidas)
    canvas_draw_box(c, 0, 0, 128, 12);

    // Línea
    canvas_draw_line(c, 0, 12, 128, 12);

    // Marco/borde
    canvas_draw_frame(c, 10, 10, 50, 30);

    // Para texto blanco sobre fondo negro:
    canvas_set_color(c, ColorWhite);
    canvas_draw_str(c, 5, 9, "Texto blanco");
    canvas_set_color(c, ColorBlack);    // Restaurar
}
```

### 6.5 Input (botones físicos)

```c
static bool mi_input_cb(InputEvent* ev, void* ctx) {
    // ev->type: InputTypeShort, InputTypeLong, InputTypeRepeat, InputTypePress, InputTypeRelease
    // ev->key:  InputKeyUp, InputKeyDown, InputKeyLeft, InputKeyRight, InputKeyOk, InputKeyBack

    if(ev->type == InputTypeShort) {
        switch(ev->key) {
        case InputKeyOk:    /* Centro */   break;
        case InputKeyUp:    /* Arriba */   break;
        case InputKeyDown:  /* Abajo */    break;
        case InputKeyLeft:  /* Izquierda */break;
        case InputKeyRight: /* Derecha */  break;
        case InputKeyBack:  /* Atrás */    break;
        }
    }

    // Retornar true = evento consumido (no propagar)
    // Retornar false = dejar que el dispatcher lo maneje (ej: Back sale)
    return true;
}
```

### 6.6 Timers

```c
// Crear timer periódico (ejecuta callback cada X ms)
FuriTimer* tmr = furi_timer_alloc(mi_timer_cb, FuriTimerTypePeriodic, &mi_app);

// Iniciar
furi_timer_start(tmr, 1000);  // Cada 1000ms = 1 segundo

// Detener
furi_timer_stop(tmr);

// Liberar (siempre hacerlo al salir)
furi_timer_free(tmr);

// El callback del timer NO debe tocar la GUI directamente (corre en otro hilo)
// Usa custom events en su lugar:
static void mi_timer_cb(void* ctx) {
    MiApp* a = ctx;
    view_dispatcher_send_custom_event(a->vd, MI_EVENTO_ID);
}

// El custom event SÍ corre en el hilo de la GUI:
static bool mi_custom_cb(void* ctx, uint32_t event_id) {
    MiApp* a = ctx;
    if(event_id == MI_EVENTO_ID) {
        // Aquí sí puedes modificar estado y la GUI se redibujaría
    }
    return true;
}
```

### 6.7 Notificaciones (LED y vibración)

```c
NotificationApp* notif = furi_record_open(RECORD_NOTIFICATION);

// Secuencias predefinidas:
notification_message(notif, &sequence_blink_green_100);   // LED verde
notification_message(notif, &sequence_blink_red_100);     // LED rojo
notification_message(notif, &sequence_blink_blue_100);    // LED azul
notification_message(notif, &sequence_single_vibro);      // Vibración
notification_message(notif, &sequence_double_vibro);      // Doble vibración
notification_message(notif, &sequence_success);           // LED verde + vibro

furi_record_close(RECORD_NOTIFICATION);
```

---

## 7. Comandos de compilación

### Entrar al directorio del proyecto

```bash
cd futbol_score/
```

### Compilar la FAP

```bash
ufbt fap
```

Esto genera: `build/f7-firmware-D/.extapps/futbol_score.fap`

### Compilar + lanzar en Flipper (si está conectado por USB)

```bash
ufbt launch
```

> Compila y copia automáticamente la `.fap` al Flipper, luego la ejecuta.

### Solo compilar (sin lanzar)

```bash
ufbt fap
```

### Limpiar build anterior

```bash
ufbt clean
```

### Actualizar el SDK de Flipper

```bash
ufbt update
```

### Compilar con firmware específico (ej: Unleashed, RogueMaster)

```bash
# Usar firmware Unleashed
ufbt update --channel=dev --index-url=https://up.unleashedflip.com/directory.json
ufbt fap
```

---

## 8. Instalar la app en el Flipper

### Método 1: USB con ufbt (más rápido)

```bash
# Conectar Flipper por USB, luego:
ufbt launch

# Solo copiar (sin ejecutar):
ufbt launch APP_FLIP=0
```

### Método 2: qFlipper (GUI)

1. Abrir qFlipper
2. Navegar a `SD Card → apps → Games`
3. Arrastrar el archivo `.fap` compilado
4. En el Flipper: **Apps → Games → Marcador Futbol**

### Método 3: Tarjeta SD directamente

```bash
# Montar la SD del Flipper (o sacarla físicamente)
cp build/f7-firmware-D/.extapps/futbol_score.fap /ruta/SD/apps/Games/

# La estructura en la SD debe ser:
# SD:/apps/Games/futbol_score.fap
```

### Método 4: Con flipper-application-catalog (distribución pública)

Para publicar la app en el catálogo oficial de Flipper:
- Subir el código a GitHub
- Hacer fork de `github.com/flipperdevices/flipper-application-catalog`
- Crear un PR con el manifiesto de tu app

---

## 9. Flujo de desarrollo completo

```bash
# ── 1. Primera vez ─────────────────────────────────────
pip install ufbt
ufbt update

# ── 2. Crear proyecto ──────────────────────────────────
mkdir mi_app && cd mi_app
# Crear application.fam y mi_app.c

# ── 3. Ciclo de desarrollo ─────────────────────────────
# [Editar código]
ufbt fap          # Compilar
# Si hay errores → corregir y repetir

# ── 4. Probar en el Flipper ────────────────────────────
ufbt launch       # Compilar + copiar + ejecutar

# ── 5. Depurar ─────────────────────────────────────────
# Ver logs en tiempo real:
ufbt cli          # Abre terminal serial con el Flipper
# En la terminal del Flipper:
# > log              ← ver logs
# > log info         ← nivel info

# En el código, usar FURI_LOG_*:
FURI_LOG_I("MiTag", "Valor: %d", variable);  # Info
FURI_LOG_E("MiTag", "Error: %s", msg);       # Error
FURI_LOG_D("MiTag", "Debug: %lu", ts);       # Debug

# ── 6. Distribuir ──────────────────────────────────────
ls build/f7-firmware-D/.extapps/*.fap  # El .fap listo para compartir
```

---

## 10. Depuración y errores comunes

### Error: `furi_check failed`
**Causa**: Un `furi_check(ptr)` falló (malloc retornó NULL, stack overflow).  
**Solución**: Aumentar `stack_size` en `application.fam` (probar `4 * 1024`).

### Error: Pantalla en negro / app se cierra sola
**Causa**: Crash en el draw callback o el timer.  
**Solución**: Ver logs con `ufbt cli` → `log`.

### Error al compilar: `undefined reference to...`
**Causa**: Falta un módulo en `requires` del `application.fam`.  
**Solución**: Agregar el módulo correspondiente:
```python
requires = ["gui", "notification", "storage"]
```

### La app no aparece en el menú del Flipper
**Causa**: El `.fap` está en la carpeta incorrecta de la SD.  
**Solución**: Verificar que la categoría en `application.fam` coincide con la carpeta:
```
fap_category = "Games"  →  SD:/apps/Games/mi_app.fap
```

### Error: `ufbt: command not found`
```bash
# Agregar ufbt al PATH:
python3 -m pip install --user ufbt
export PATH="$HOME/.local/bin:$PATH"   # Linux/macOS
# En Windows: reiniciar terminal después de pip install
```

---

## 11. Recursos

| Recurso | URL |
|---------|-----|
| Firmware oficial (fuente) | `github.com/flipperdevices/flipperzero-firmware` |
| ufbt | `github.com/flipperdevices/flipperzero-ufbt` |
| Catálogo de apps | `github.com/flipperdevices/flipper-application-catalog` |
| Apps de ejemplo | `github.com/flipperdevices/flipperzero-firmware/tree/dev/applications/examples` |
| Documentación API | `developer.flipper.net` |
| qFlipper (GUI) | `flipperzero.one/update` |
| Comunidad Discord | `discord.gg/flipper` |
| Unleashed Firmware | `github.com/Flipper-XFW/Xtreme-Firmware` |

---

## Cheatsheet rápido

```bash
ufbt update          # Actualizar SDK
ufbt fap             # Compilar .fap
ufbt launch          # Compilar + copiar + ejecutar en Flipper
ufbt clean           # Limpiar archivos de build
ufbt cli             # Terminal serial con Flipper
```

```c
// Módulos más usados
furi_record_open(RECORD_GUI)           // Acceder al sistema de GUI
furi_record_open(RECORD_NOTIFICATION)  // Acceder a notificaciones
furi_record_open(RECORD_STORAGE)       // Acceder a almacenamiento

// Timer
furi_timer_alloc(cb, FuriTimerTypePeriodic, ctx)
furi_timer_start(tmr, ms)
furi_timer_stop(tmr)
furi_timer_free(tmr)

// Canvas
canvas_clear(c)
canvas_set_font(c, FontBigNumbers)
canvas_draw_str_aligned(c, x, y, AlignCenter, AlignTop, "texto")
canvas_draw_box(c, x, y, w, h)

// Notificaciones
notification_message(notif, &sequence_blink_green_100)
notification_message(notif, &sequence_single_vibro)

// Log
FURI_LOG_I("Tag", "mensaje: %d", valor)
```

---

*Guía generada para la clase de Flipper Zero — uso educativo*
