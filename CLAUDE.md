# Espejo interior

Herramienta de reflexión visual: la persona escribe sus pensamientos y los ve flotar como luces de color sobre fondo negro, conectados entre sí, con sonido generativo. Funciona como un espejo para mirar la propia cabeza desde afuera, y Claude ayuda a pensar con preguntas, sin decidir por la persona.

Público objetivo en evaluación: uso personal y, sobre todo, psicólogos y coaches que lo usen con pacientes (en sesión o como tarea entre sesiones).

Idioma de la interfaz: español rioplatense (voseo). Mantenerlo en todo texto nuevo.

## Estado actual

Todo vive en un solo archivo, `index.html` (HTML + CSS + JS, sin dependencias ni build). Se abre directo en el navegador.

Para qué sirve (decirlo siempre así): para cuando tenés la cabeza llena y todo da vueltas mezclado. Sacás lo que te da vueltas, ves qué es lo que más pesa, pensás una pregunta y soltás algo.

Funciones:
- **Soltar pensamientos** con tipo (idea, duda, emoción, recuerdo) y peso del 1 al 5.
- **Visual en canvas 2D**: cada tipo tiene su color y su zona; tamaño y órbitas según el peso; los pensamientos viejos se hunden y apagan un poco; el mouse genera corrientes.
- **Conexiones**: arrastrar un pensamiento sobre otro los une con una curva animada con pulsos de luz; los del mismo tipo se unen solos con hilos punteados.
- **Foco**: al pasar el mouse, el pensamiento crece, sus lazos tiemblan y el resto se atenúa. Tocar abre una tarjeta; doble clic lo deja ir.
- **Sonido generativo (Web Audio)**: dron de fondo que cambia según lo que pesa (ideas = más brillante, dudas = batido tenso, recuerdos = más cálido); cada pensamiento es una voz con paneo según su posición; cada conexión suma una armonía. Opcional, con volumen.
- **¿Y ahora?** (sin IA, funciona en la web pública): aparece al soltar 3 pensamientos y guía en tres pasos. *Mirá*: qué pesa más (mayor peso; desempata por conexiones y antigüedad), cuántos hay de cada tipo, qué está en el centro. *Pensá*: una pregunta del banco `PREG` según el tipo de lo que más pesa (más `RESERVA`); la respuesta nace unida a ese pensamiento. *Soltá*: elegir algo para dejar ir, o "Hoy no". Es el momento de valor de la versión pública: sin esto, escribir queda en el aire.
- **Leer mi espejo**: Claude devuelve una lectura breve que termina con una pregunta.
- **Explorar una duda**: Claude hace preguntas socráticas de a una; cada respuesta nace como pensamiento nuevo unido al anterior, y Claude la clasifica (tipo, peso, versión corta). "Dame perspectivas" ofrece tres miradas sin decidir.
- **Guardado** en `localStorage` del navegador (solo ese dispositivo).
- Pantalla de entrada ("con sonido" / "en silencio"), que no se repite tras una recarga en la misma sesión.
- Enlaces arriba a la izquierda: **Qué es · privacidad · ayuda** (panel con explicación, aviso de privacidad, "no es terapia" y líneas de ayuda), **Guardar imagen** (PNG del canvas con el nombre y el link, para compartir) y **Compartir** (abre un post en X con el link; nunca incluye lo que escribió la persona).

## Publicación

- Web pública: https://mateosassaroli2001-rgb.github.io/espejo-interior/ (GitHub Pages, rama `main`, carpeta raíz). Cada push a `main` la actualiza en uno o dos minutos.
- `og.png` (1200×630) es la imagen que muestran X y otras redes al pegar el link. Si cambia la URL, actualizar las etiquetas `og:` y `twitter:` y `URL_PUBLICA` en `index.html`.
- Medición anónima con GoatCounter (`espejointerior.goatcounter.com`, sin cookies). Solo se mandan nombres de eventos con `medir()`, **nunca el texto de los pensamientos**. Eventos: `guia-abrir`, `guia-otra-pregunta`, `guia-responder`, `guia-soltar`, `guia-fin`, `entrar-con-sonido`, `entrar-en-silencio`, `soltar`, `unir`, `vaciar`, `vuelve-con-pensamientos` (volvió con pensamientos guardados), `guardar-imagen`, `compartir`, `acerca`.
- Por ahora es gratis, para medir si la gente lo usa. Si se cobra más adelante, la idea es un pago único con Lemon Squeezy (pagos internacionales) y la IA detrás de un backend con límite de uso por persona.

## Importante: la IA hoy no funciona fuera de claude.ai

Las funciones de Claude usan `window.claude.use('sample')`, que solo existe cuando la página se abre como artifact dentro de claude.ai. Abierta como archivo suelto o en otro hosting, esos botones se ocultan solos y el resto funciona igual.

Para que funcione de forma independiente hace falta un backend chico que llame a la API de Anthropic. **Nunca poner la API key en el navegador.** Los prompts actuales están en el código (buscar `const prompt=` en `index.html`) y sirven de punto de partida.

## Reglas de cuidado (no negociables)

- Es una herramienta de reflexión, no terapia. No prometer efectos terapéuticos (tampoco del sonido).
- Claude no da consejos ni decide: pregunta y ofrece perspectivas.
- Si algo sugiere riesgo de hacerse daño, se dejan de lado las preguntas y se responde con calidez, invitando a buscar ayuda ya. Recursos en Argentina: Línea de Salud Mental 0800-999-0091 (nacional, gratuita, 24 h); Centro de Asistencia al Suicida, 135 desde CABA y GBA o (011) 5275-1135 desde todo el país; emergencias 911. Otros países: findahelpline.com. Verificados en octubre de 2026; revisarlos de vez en cuando.
- Los pensamientos son datos sensibles (de salud, si lo usan profesionales). Tratar privacidad y almacenamiento con seriedad.

## Decisiones de diseño

- Fondo negro absoluto; todo el color vive en los pensamientos y sus lazos. Interfaz de vidrio oscuro discreta.
- Colores por tipo: idea `#a094ff`, duda `#ff985c`, emoción `#ff68a8`, recuerdo `#56e8ba`.
- Tipografías: Instrument Serif (pensamientos, títulos) y Manrope (interfaz), desde Google Fonts.
- Respetar `prefers-reduced-motion`.
- Cuidado con el orden de inicialización: el script es un solo IIFE con `const`/`let`; nada que corra al cargar (como `cargar()`/`resumen()`) puede usar algo declarado más abajo. Antes `resumen()` usaba `NOM` antes de definirse y la página se cortaba entera al volver con pensamientos guardados.
- No usar `<form>` ni botones `type="submit"`: dentro de claude.ai la página corre en un iframe sandbox sin `allow-forms`, y el navegador bloquea el envío (Soltar y Enter dejan de andar). Las acciones van con `onclick` y un `keydown` de Enter (ver `alEnter` en `index.html`).

## Próximos pasos posibles

1. Pasar a una estructura con archivos separados (HTML, CSS, JS) o a un framework, si el proyecto crece.
2. Backend que llame a la API de Anthropic para Leer, Explorar y Perspectivas.
3. Cuentas y guardado en la nube (sincronizar entre dispositivos, historial).
4. Modo profesional: el psicólogo o coach ve la evolución del mapa del paciente, controla si la IA está activa, modo sesión compartida.
5. Exportar la "constelación" como imagen para compartir.
