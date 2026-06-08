# 🤖 LCK Analytical Coach & Cyberpunk Dashboard (AI-Driven LoL Overlay)

¡Bienvenido! Este es el repositorio de vitrina de mi proyecto más ambicioso: un asistente de voz y panel de analítica avanzada para League of Legends en tiempo real, diseñado para superar las capacidades de herramientas comerciales como Mobalytics.

> 🔒 **Nota sobre el código fuente**: El código base de producción se mantiene en un repositorio privado para proteger la propiedad intelectual y evitar infracciones de los términos de servicio de plataformas externas (Riot Games API / Vanguard). Este repositorio actúa exclusivamente como portafolio técnico y demostración de arquitectura.

---

## 🚀 Descripción del Proyecto
La aplicación es un cliente de escritorio híbrido de alto rendimiento. Combina un frontend web de última generación (estilo eSports Cyberpunk) con un backend robusto en Python. El sistema actúa como un "Coach analítico de la LCK", comunicándose con el usuario de forma bidireccional por voz en español latinoamericano, guiándolo segundo a segundo en micro y macrogestión dentro de la partida.

---

## 🛠️ Arquitectura Técnica y Stack Tecnológico

El software implementa un patrón de **Sidecar Process** y comunicación por eventos de alta velocidad:

*   **Frontend Premium (UI/UX)**: Desarrollado con **Next.js, React y Tailwind CSS**, encapsulado en una ventana nativa mediante **Electron**. Implementa efectos de *glassmorphic*, anillos de carga dinámicos y gráficos vectoriales.
*   **Backend & Telemetría**: Escrito en **Python (PyQt5 / WebSockets)**. Se encarga de la orquestación del micrófono, el procesamiento local de lenguaje y los puentes de red.
*   **Inteligencia Artificial Local**: Integración nativa con **Ollama (Modelo Llama3)** ejecutado de forma 100% local en la máquina del usuario (sin latencia de servidores en la nube ni costos de API).
*   **Audio no Bloqueante**: Motor de voz síncrono basado en subprocesos independientes de `pyttsx3` para evitar bloqueos del hilo gráfico principal de la app.
*   **Detección de Voz Bidireccional**: Procesamiento de lenguaje natural mediante la librería `SpeechRecognition`, permitiendo al usuario hablar en vivo con la IA durante la partida (Activación por palabra clave: *"Coach"*).

---

## 🔌 Conexiones de Datos e Inyecciones Avanzadas

El núcleo de la aplicación interactúa directamente con los procesos locales del juego:
1.  **Riot LCU API (League Client Update)**: El script detecta de forma dinámica las credenciales del cliente leyendo el archivo secreto `lockfile`. Rastreará la fase de selección de campeones y, mediante peticiones `PUT` al endpoint `/lol-perks/v1/pages/`, realiza una **inyección automatizada de runas óptimas** en el segundo T-5 antes del cierre de la sala.
2.  **Practice Tool Bypass**: Implementa un polling de rescate mediante `/current-champion` para asegurar la inyección de runas dinámicas (Electrocutar / Cadencia Letal) incluso si el cliente oculta la sesión de draft en salas de práctica.
3.  **Live Client Data Bridge**: Transmisión de estadísticas vivas (oro, tiempo, campeones) a través de un servidor local de **WebSockets (Puerto 8765)** que actualiza el dashboard web a milisegundos de latencia.

---

## 📊 Funcionalidades Clave Implementadas

*   **Radar Pentagonal de Rendimiento**: Gráfico analítico dinámico de 5 ejes vectorizados que mide en tiempo real el desempeño macro del jugador.
*   **Cronodisparadores Tácticos de Voz**: Mensajes automatizados y pausados (velocidad 150) que indican:
    *   Compra óptima del primer ítem en la fuente (Sanitizado a Español LATAM).
    *   Predicción de pathing a ciegas del jungla enemigo (sin leash) basado en el meta actual.
    *   Directrices de microgestión de oleadas para los Niveles 1, 2 y 3 en la fase de líneas.
    *   Alertas de exceso de oro (>2300g) sugiriendo resets de tempo para ítems situacionales.
*   **Debriefing Crudo Post-Game**: Análisis crítico al finalizar la partida señalando sin filtros los peores errores en micro y macro.

---

## 📸 Demostración Visual
<img width="1231" height="775" alt="{CF1E1A87-2548-4E88-B563-349746502E2F}" src="https://github.com/user-attachments/assets/925c42d8-db66-4bd4-8d5f-73700191327b" />

