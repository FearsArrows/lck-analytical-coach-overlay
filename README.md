# 🤖 MASTER CORE ANALYTICS (MASTER OS)
### Entrenador Analítico de la LCK y Panel de Control Cyberpunk (Superposición de LoL Impulsada por IA Local)

[![Python](https://shields.io)](https://python.org)
[![Next.js](https://shields.io)](https://nextjs.org)
[![Electron](https://shields.io)](https://electronjs.org)
[![Ollama](https://shields.io)](https://ollama.com)

**MASTER CORE ANALYTICS** es una plataforma analítica de escritorio de nivel profesional y alto rendimiento para *League of Legends*. Diseñada bajo el patrón de arquitectura **Sidecar Process**, combina un motor de telemetría unificado y procesamiento analítico en Python con una interfaz de usuario reactiva basada en Next.js 16 (React 19) empaquetada en Electron 33.

Optimiza el consumo de hardware aislando la computación pesada en subprocesos independientes, integrando un **Coach de Voz local con Inteligencia Artificial** que analiza y corrige las decisiones macro y micro del jugador en tiempo real mediante la monitorización pasiva de sockets y visión artificial de baja latencia.

---

## 🔒 NOTA SOBRE EL CÓDIGO FUENTE (PROPIEDAD INTELECTUAL)

El código base de producción se mantiene estrictamente en un **repositorio privado** para proteger la propiedad intelectual del software, salvaguardar los algoritmos propietarios de análisis y evitar infringciones con los términos de servicio de plataformas externas (*Riot Games API / Vanguard*).

Este repositorio actúa exclusivamente como **vitrina de portafolio técnico, documentación de ingeniería de alto nivel y demostración de arquitectura**.

---

## 🎨 DEMOSTRACIÓN VISUAL Y DE INTERFAZ


| 🖥️ Dashboard Principal (Lobby) | 👥 Sincronización Local (Enlace Dúo) |
|:---:|:---:|
| ![Dashboard Overview](./caps/dashboard-lobby.png) | ![Duo Link Interface](./caps/dashboard-duo.png) |

| 🧠 Panel de Desafíos Avanzados | 📊 Gráficas de Telemetría Histórica |
|:---:|:---:|
| ![Daily Challenges](./caps/dashboard-retos.png) | ![Stats Panel](./caps/stats-charts.png) |


---

## 🧬 ARQUITECTURA TÉCNICA DEL SISTEMA

La plataforma elimina los fallos de renderizado y errores de escalado en pantallas Ultra-Wide fijando las coordenadas del contenedor a una resolución exacta de `1200x750` píxeles. El flujo de datos se divide en tres capas desacopladas que operan en paralelo mediante una red de eventos locales:

### 🎮 1. Frontend de Última Generación (Capa Desktop)
- **Stack**: Next.js 16 (App Router), React 19 y Tailwind CSS, ejecutados bajo la shell de Electron 33.
- **Diseño**: Estética cyberpunk basada en un tema de color Obsidiana (`#0a0b12` – `#030406`) con acentos de alta fidelidad en Verde Neón (`#00ffcc`) y Magenta (`#ff0055`).
- **Flujo de Navegación**: Estructurado en pestañas dinámicas no bloqueantes (Panel, Retos, Entrenador, Dúo, Ajustes) con persistencia de sesión a través de `localStorage` (`mca_session_token`).

### 🔌 2. Backend Sidecar Unificado (`api_server.py`)
El backend centraliza los servicios web en una única instancia local headless que evita la sobrecarga de puertos cruzados:
- **Host y Puerto**: `http://127.0.0.1:8765` con Middleware CORS habilitado de forma global.
- **Capa REST HTTP**: Expone controladores dedicados para el ciclo de vida del usuario, gestión de retos y el puente de datos distribuido.
- **Capa WebSocket**: Canal de telemetría bidireccional asíncrono (`/ws`) con un *Heartbeat* constante de 20 segundos y un temporizador de reconexión automática en el cliente configurado a 2500 ms.

### 🧠 3. Motor de Inteligencia Artificial Local (`asistente_voz.py`)
- **Core LLM**: Integración nativa con **Ollama** (modelo `llama3`) ejecutado localmente de forma privada.
- **Audio Aislado**: Motor de síntesis de voz (`pyttsx3`) desacoplado en subprocesos independientes dentro del hilo *daemon* `TTS-Worker`, erradicando bloqueos críticos (*deadlocks*) del bucle principal.
- **Procesamiento de Voz (STT)**: Entrada continua configurada en el hilo *daemon* `Mic-Coach` empleando `SpeechRecognition` (Google API con localización `es-MX`), activada por la palabra clave única: `"Coach"`.

---

## 🗄️ MODELO DE DATOS CRIPTOGRÁFICO (`master_core.db`)

El sistema utiliza una base de datos relacional local **SQLite3** con altos estándares de seguridad para el manejo de la sesión local:
* **Security**: Las contraseñas se almacenan procesadas mediante **PBKDF2-HMAC-SHA256** utilizando 120.000 iteraciones de hash y una sal (*salt*) de 32 bytes.
* **Gestión de Sesiones**: Generación de identificadores criptográficos opacos mediante tokens seguros urlsafe de 48 bytes.
* **Tablas de la Arquitectura**:
* `usuarios` / `sesiones`: Registro, autenticación y expiración ISO UTC de perfiles.
* `configuracion`: Parámetros de la interfaz del usuario serializados en formato JSON.
* `historial_partidas`: Almacenamiento estadístico de rendimiento (oro, campeones, victorias/derrotas).
* `retos_activos` / `pool_retos`: Tabla de control y pre-semillado de desafíos analíticos limitados a un máximo de 3 activos en simultáneo.
    
## 📊 FUNCIONALIDADES CLAVE IMPLEMENTADAS

### 🔌 Inyecciones LCU Avanzadas & Rescate Automatizado
A través del descubrimiento dinámico del archivo `lockfile` de Riot Games, la plataforma inyecta de forma automatizada la página de runas óptima (6 perks de matriz + 3 shard) en el segundo **T-5** antes del cierre del reclutamiento (`/lol-perks/v1/pages/`). En salas personalizadas o la Herramienta de Práctica, ejecuta una rutina de rescate basada en *polling* continuo (`/current-champion`) para mitigar strings vacíos.

### 👁️ Microvisión Computacional del Minimapa
El script `conector_juego.py` captura por porcentajes la región exacta de la pantalla correspondiente al mapa de juego (Left: 78%, Top: 72%, Width: 20%, Height: 25%) usando la librería de bajo consumo `mss`. La matriz de píxeles BGR `uint8` se transforma a escala de grises y pasa por un **filtro de detección de bordes Canny** (umbrales 50 y 150) con un intervalo mínimo de 2500 ms. La densidad de bordes resultante se procesa matemáticamente para estimar aglomeraciones de unidades enemigas y alertas de visión en el mapa.

### 🚨 Motor de Castigo e Inflexibilidad en Armado de Objetos
El hilo *daemon* `Coach-Brain` monitoriza los cambios en los arrays de inventario a través de peticiones HTTP locales hacia el Live Client Data API (puerto 2999) con un tiempo de espera estricto de 650 ms. Al procesar el evento de un nuevo ítem, contrasta el identificador con el estilo de juego preestablecido del campeón (burst, dps, tanque, soporte). Si la compra es ineficiente o prematura (ej. retrasos en el tempo por ítems de escalado tardío), el Coach de Voz emite un mensaje firme de reprensión lingüística exigiendo su corrección inmediata en la tienda.

### 👥 Sincronización Distribuida en Red Local (Enlace Dúo)
El sistema expone de forma externa el endpoint `GET /api/duo-link`. Al ser consumido por un dispositivo alterno (como un teléfono o laptop dentro de la misma red Wi-Fi), sirve un tablero en vivo que, durante la fase de selección de campeones (frecuencia de 700 ms por ciclo), recomienda de forma predictiva sinergias, counters y composiciones de alta eficiencia basadas en las estadísticas históricas de la base de datos.

### 🗣️ Cronodisparadores Tácticos de Voz (Zero-English Wall)
El asistente procesa el estado de la partida cada 1000 ms y activa alertas orales automatizadas a una velocidad de **140 palabras por minuto** (con un rango hermético de 135-145 PPM), con restricción estricta de voces femeninas y sanitización absoluta contra anglicismos:
* **Segundo 5–12**: Consejo de compra del objeto inicial (*primer_item*).
* **Segundo 15–22**: Deducción de la ruta a ciegas del jungla enemigo basado en el meta (*ruta_jg*).
* **Segundo 50–58**: Directrices micro para el control de la oleada a nivel 1.
* **Minutos 2:15 y 3:20**: Picos de poder de niveles 2 y 3 respectivamente.
* **Alerta de Economía**: Disparo vocal cuando el oro acumulado supera los `>2300g` para forzar un reset de tempo.
* **Predicción de Combate**: Evaluación matricial de oro e inventarios del equipo <20 segundos antes del spawn de objetivos mayores (Dragón, Barón, Larvas) para dictaminar si el combate es viable o debe declinarse.

---

## 📈 POOL DE RETOS DISPONIBLES (Muestra del Pre-Semillado)
La plataforma inyecta de forma automatizada desafíos base y dinámicos para mitigar el *tilt* del invocador, calculando métricas a través de WebSockets:
* **Retos Base**: `cs_85` (Métricas de farmeo óptimo), `kp_55` (Participación en asesinatos), `muertes_3` (Supervivencia extrema), `vision_1_5` (Puntuación de visión por minuto).
* **Retos Dinámicos**: Generación inteligente en tiempo de ejecución (`dyn_cs_`, `dyn_vis_`, `dyn_die_`) basándose en las debilidades detectadas en el historial de partidas del usuario.

---

## ⚙️ ESPECIFICACIONES DE ORQUESTACIÓN (BUCLES DE REFRESCO)
Al ejecutarse el punto de entrada principal (`main.py`), la aplicación levanta **6 hilos asíncronos en segundo plano** (*Daemon Threads*) que controlan el rendimiento global del sistema:
1. `API-8765`: Servidor unificado HTTP + WebSocket de comunicación local.
2. `Dash-Refresh`: Sincronización del perfil y analíticas del invocador con la LCU cada 8000 ms.
3. `Coach-Brain`: Máquina de estados principal del cerebro analítico (Muestreo: 700 ms en Selección de campeones / 1000 ms dentro de partida / 2000 ms en Lobby).
4. `Auth-Sync`: Validación constante de la sesión del usuario local cada 5000 ms.
5. `TTS-Worker`: Cola secuencial no bloqueante para el sintetizador de voz.
6. `Mic-Coach`: Escucha e interceptación de peticiones por micrófono bajo la palabra gatillo.

---

## 🖼️ CAPTURAS ADICIONALES DEL DESARROLLO


| 🛠️ Consola de Eventos del Sidecar |
|:---:|
| ![Logs Console](./caps/console-logs.png) |

---
*MASTER OS es un desarrollo de ingeniería privado con fines de análisis competitivo de alto rendimiento. Las marcas registradas y nombres de personajes pertenecen a Riot Games Inc.*
