# Réplica de Sensei: pruebas de caja negra en chatbots reales

Réplica de la pregunta de investigación RQ2 de:

> de Lara, J., del Pozzo, A., Guerra, E., & Sánchez Cuadrado, J. (2026). *Automated end-to-end testing for conversational agents*. Journal of Systems and Software, 233, 112685. https://doi.org/10.1016/j.jss.2025.112685

RQ2: *"Is Sensei effective to find defects in conversational agents in the wild?"*

Autor de la réplica: Sebastian Stefano Saldarriaga Mendoza, Universidad de Lima, curso Propuesta de Investigación 2026-2.

## Qué se replica

Se usa la **misma herramienta**: el código original de [Sensei](https://github.com/sensei-chat/sensei), con licencia MIT.

También se usan los mismos perfiles de usuario, las mismas reglas del [paquete de replicación de los autores](https://github.com/satori-chatbots/user-simulator-evaluation) y los mismos chatbots de la Tabla 6 del paper. Cada chatbot se prueba con 2 personalidades: normal y directa.

| Chatbot | Cómo se replica | Conversaciones |
|---|---|---|
| Julie (Amtrak) | En vivo: Sensei conversa con el chatbot real | 32 |
| Serviceform | En vivo | 12 |
| Catalina | Hoy responde HTTP 403. Se aplican las reglas a las conversaciones originales de los autores | 14 |
| SAIC | Igual que Catalina (403) | 14 |

## Qué se mide

Se mide lo mismo que en las Tablas 6 y 7 del paper:

- **Fallas por tipo:**
  - C: caída.
  - G: objetivo no cumplido.
  - T: timeout.
  - L: bucle.
  - GR1: fuera de tema.
  - GR2: repite respuestas.
  - GR3: no responde en el idioma del usuario.
  - GR4: botones o enlaces vacíos.
- **Tiempo de respuesta del chatbot:** promedio, mínimo y máximo, registrado por Sensei en cada turno.

## Resultados

| Chatbot | Conv. | Fallas paper | Fallas réplica | Tiempo prom. paper | Tiempo prom. réplica |
|---|---|---|---|---|---|
| Julie (en vivo) | 32 | 53 (G 8, GR2 11, GR3 15, GR4 19) | 47 (G 14, GR1 1, GR2 12, GR3 10, GR4 10) | 1,23 s | 0,76 s |
| Serviceform (en vivo) | 12 | 9 (G 1, GR1 1, GR2 5, GR3 2) | 3 (GR1 3) | 5,48 s | 2,44 s |
| Catalina (análisis reproducido) | 14 | 20 (C 3, G 7, GR2 10) | 20 (idéntico) | — | — |
| SAIC (análisis reproducido) | 14 | 22 (C 2, G 8, T 1, GR2 11) | 24 (+2 GR1) | — | — |

Julie y Serviceform se ejecutaron en vivo entre el 15 y el 17 de noviembre de 2026 (la cuota gratuita de Gemini obligó a repartir la ejecución en varios días; el Paso 11 restaura lo hecho antes).

Los tiempos del paper se calcularon a partir de las conversaciones originales de los autores. La regla `chatbot_fills_all_slots` no se cuenta porque no está en la Tabla 6.

## Diferencias con el paper

1. **Modelo:** el paper usó gpt-4o y gpt-4o-mini. La réplica usa Gemini 3.5 Flash-Lite, que es gratuito, a través de su API compatible con OpenAI. El modelo afecta al usuario simulado y a las reglas que juzga el LLM (`only_talks_about`, idioma).
2. **Fecha:** el paper ejecutó las pruebas en 2024 y la réplica en noviembre de 2026. Los chatbots cambiaron: hoy responden más rápido.
3. **Acceso:** Catalina y SAIC (plataforma 1MillionBot) devuelven HTTP 403 ("You don't have permissions to perform this action"), porque la clave incluida en el conector de Sensei ya no es válida.
4. **Bug corregido en Sensei:** cuando el usuario inicia la conversación (`is_starter: False`), `sensei-chat.py` enviaba su primer mensaje dos veces al chatbot. Se corrigió agregando un `continue`.

   Es necesario que el usuario hable primero porque los conectores de Julie y Serviceform no implementan el saludo inicial del chatbot.
5. **Otros ajustes:** reintentos ante el límite del plan gratuito (429), un nombre de prueba único por perfil y ajustes de formato en algunos perfiles. Ninguno cambia la lógica de evaluación.

## Cómo ejecutarlo

1. Abre `Prototipo.ipynb` en Google Colab.
2. Crea una clave gratuita de Gemini en https://aistudio.google.com/apikey.
3. Guarda la clave en Colab: icono 🔑 *Secrets*, con el nombre `TESIS`.
4. Ejecuta los pasos en orden; cada uno explica qué hacer y qué deberías ver.
   - La cuota gratuita (~500 llamadas/día) no alcanza para todo en un día. El notebook indica cómo dividir la ejecución y cómo restaurar resultados con el zip (Paso 11).
   - El Paso 12 es una demo de 1 conversación real.

## Archivos

- `Prototipo.ipynb`: el notebook de la réplica.
- `resultados_replica_sensei.zip`: conversaciones generadas, CSV de Sensei-check y perfiles usados.

## Créditos

- Sensei: © Universidad Autónoma de Madrid y Universidad de Murcia, licencia MIT.
- Perfiles, reglas y resultados originales: repositorio `satori-chatbots/user-simulator-evaluation`.
