Prototipo de una plataforma de monitoreo de actividad laboral. Es una maqueta de una sola página, sin build ni
servidor: `index.html` se abre con doble clic y funciona.

Hecho como tarea escolar. Datos meramente ilustrativos y adjustables.
## Qué es real y qué está simulado

El sensor de arriba **mide de verdad** a quien abre la página, con las APIs que el
navegador sí expone:

| Señal | API | Real |
|---|---|---|
| Clics | `pointerdown` | sí |
| Enlaces abiertos | `click` sobre `a[href]` | sí |
| Cambios de ventana | `blur` / `focus` / `visibilitychange` | sí |
| Recorrido del cursor | `pointermove` | sí |
| Sitios y aplicaciones por persona | — | simulado |
| Bloqueo de sitios | — | simulado |

## El índice

```
índice = 0.65 · clics + 0.35 · enlaces abiertos
```

Los clics se cuentan sobre una ventana de 10 s y los enlaces sobre una de 60 s. El
resultado se normaliza a 0–100 y se suaviza con la muestra anterior. Sin eventos
por 6 s marca *actividad baja*; por 20 s, *sin señal*.

El recorrido del cursor quedó como dato secundario: medía movimiento, no intención.

## El límite técnico que importa

Una página web **no puede** leer las otras pestañas del navegador, ver las ventanas
del sistema ni bloquear un sitio. Por sí sola sólo detecta que perdió el foco.

Leer dominios y aplicar bloqueos exige una **extensión de navegador** (permisos
`tabs` y `webRequest`) o un **agente instalado en el sistema operativo**. Por eso
ningún producto de esta categoría funciona con sólo abrir una página. En esta demo
la línea de tiempo, los destinos y los bloqueos son datos inventados; el resto no.

## Notas de diseño

- Tema claro en tonos azules, inspirado en Oracle Cloud.
- El asistente responde con un guion fijo por coincidencia de palabras. No llama a
  ningún modelo: cero costo, funciona sin internet y no se cae en una presentación.

## Antes de usar algo así de verdad

El monitoreo laboral requiere aviso previo, consentimiento por escrito y medición
sólo dentro de la jornada. La lista de sitios bloqueados debe estar publicada.
Y conviene que cada persona vea su propio panel: un tablero que sólo ve la jefatura
se percibe como vigilancia; uno que la persona medida también ve, como una métrica.

## Correrlo

```bash
open index.html          # no necesita nada más
python3 -m http.server   # o con un servidor local
```

## Hacer tu propia versión

Este repo es un **template**. En GitHub, el botón **"Use this template" → Create a
new repository** te crea una copia independiente, tuya, con su propio historial —
no un fork atado a este.

Si prefieres empezar desde cero sin GitHub de por medio:

```bash
git clone https://github.com/deepocket/pulso-telemetria.git mi-version
cd mi-version
rm -rf .git && git init -b main     # historial limpio, tuyo
```

Todo vive en `index.html`. No hay dependencias, ni build, ni `npm install`: se
edita el archivo y se recarga el navegador.

### Dónde tocar qué

| Qué quieres cambiar | Dónde | Qué es |
|---|---|---|
| Colores, tipografías, radios | `:root` (línea ~6) | todos los tokens del tema |
| Las personas del equipo | `TEAM` | nombres, roles, clics y enlaces del día |
| Peso del índice | `TOPE_CLICS`, `TOPE_LINKS`, `indice()` | la fórmula `0.65·clics + 0.35·enlaces` |
| Forma de la jornada | `SHAPE` | pesos por hora; los clics se reparten con ellos |
| Apps y sitios | `APP_CAT`, `SEQ` | categoría de cada app y la secuencia del día |
| Sitios bloqueados | `BLOQUEADOS`, `INTENTOS` | las reglas y los intentos registrados |
| Respuestas del chat | `GUION`, `FALLBACK`, `SUGS` | pares `[regex, función]`, en orden de prioridad |

Dos cosas que conviene no romper:

- **`SEQ` debe sumar 600 minutos por persona** (09:00–19:00). Los destinos y la
  mezcla se calculan desde ahí, así que si no suma, las tres vistas se
  contradicen entre sí.
- En `GUION` **gana la primera regex que coincide**. Las reglas de nombre propio
  van primero a propósito: si no, "¿qué tal Diego?" contestaría el saludo.

### Si le cambias los colores

Las tres categorías (Trabajo / Comunicación / Personal) están validadas para
daltonismo sobre fondo claro. Si las cambias, vale la pena revisar que sigan
distinguiéndose: contraste ≥ 3:1 contra el fondo y separación suficiente entre
cada par, incluyendo deuteranopía y protanopía. Cuatro categorías no pasan en una
línea de tiempo donde cualquier par puede quedar contiguo — por eso son tres más
un neutro con textura.
