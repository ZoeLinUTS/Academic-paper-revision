# Guía del usuario

> [English](../USER-GUIDE.md) | [中文](USER-GUIDE.zh-CN.md) | [日本語](USER-GUIDE.ja.md) | [한국어](USER-GUIDE.ko.md) | **Español** | [Français](USER-GUIDE.fr.md) | [Deutsch](USER-GUIDE.de.md) | [Português](USER-GUIDE.pt.md)

Cómo usar el skill `academic-paper-revision`, qué darle, qué devuelve y en qué se diferencian los
dos modos de operación.

## 1. Antes de empezar: qué tener listo

| Modo | Entrada que proporcionas | Qué hace Claude |
| --- | --- | --- |
| **Revisión completa** | La **fuente LaTeX**: `.tex` principal, `references.bib`, la clase/estilo del congreso (`llncs.cls` + `*.bst`, o `acl.sty`, …) y `figures/`. Una **exportación de proyecto de Overleaf (zip)** es ideal. | Hace ediciones quirúrgicas a la fuente, compensa la longitud añadida y devuelve un zip de subida reconstruido. |
| **Solo revisión** | Un **PDF** (o la fuente) basta. | Solo lee y comenta — **no cambia ningún archivo**. |

El modo de revisión completa necesita la fuente porque no puedes editar quirúrgicamente un PDF, y
reconstruir la fuente desde un PDF arriesga perder tu formato y tus números. Un `main.pdf`
renderizado es una entrada *secundaria* útil para comprobar propiedades de renderizado (número de
páginas, colocación de flotantes), pero nunca es suficiente por sí solo.

## 2. Dile a Claude tu congreso destino

Esto es crucial. La misma edición puede ser correcta para un congreso y un rechazo directo para
otro. Nombra el congreso explícitamente (ACL, NAACL, EMNLP, AJCAI, NeurIPS, …). Si no se conocen
las reglas exactas del congreso, Claude buscará la convocatoria actual antes de editar. Si un
nombre de archivo dice un congreso pero tus notas dicen otro, señalará la discrepancia y preguntará
— nunca supone.

## 3. Elige un modo y pide

**Revisión completa** — ejemplos de prompts:
- "Revisa esto para EMNLP para que se lea como un artículo aceptado."
- "Recorta esto al límite de páginas de AJCAI y quita el andamiaje RQ."
- "Añade una ecuación/algoritmo para el método y corrige las referencias cruzadas."

**Solo revisión** — ejemplos de prompts:
- "Solo revisa este borrador — dame un panorama y comentarios, no edites nada."
- "Evalúa la novedad y dime las probables objeciones de los revisores."
- "¿Cuál es el alcance y los principales problemas aquí? ¿En qué se diferencia del trabajo previo?"

### Idioma de comunicación — cualquier idioma

Puedes trabajar en **cualquier idioma** y obtener tus resultados en él. Claude preguntará (o
inferirá) en qué idioma quieres la *conversación* — chino, español, árabe o cualquier otro — y
entrega cada panorama, comentario, hallazgo y resumen en ese idioma. Esto es independiente del
manuscrito: **el artículo permanece en inglés** (o el idioma exigido por el congreso); solo la
conversación sigue tu idioma elegido.

## 4. Cómo se ve la salida

Ambos modos empiezan con el mismo memorándum estilo revisor:

1. **Sesión de orientación** — *Qué es el borrador* · *Contribución central* · *Novedad (franca,
   con viñetas de ataque de revisores)* · *Estado de completitud*. Termina confirmando el congreso
   y preguntando qué alcance quieres.
2. **Lista de hallazgos** — una línea por problema, agrupada y ordenada por severidad, cada una
   nombrando la corrección. Filas de ejemplo:
   - `[venue]` Destino = ACL (ARR): cuerpo de 8 páginas, Limitations/Ethics gratis, doble ciego.
   - `[structure]` Andamiaje RQ1–RQ3 explícito → convertir en contribuciones en prosa.
   - `[formalization]` Cero ecuaciones aunque la regla de puntuación es central → añadir 2–3 ecs.
   - `[rigor]` La mejora destacada no tiene prueba de significancia / partición única → marcar como debilidad principal.
   - `[bug]` `\ref{fig:pipeline}` apunta a una figura sin `\label` → añadir la etiqueta.
   - `[cross-ref]` El trabajo relacionado es una lista cronológica → reagrupar por temas.
   - `[page-fit]` +0.5 página de más → emparejado con los movimientos `[trim]` exactos que lo compensan
     (fusionar párrafos redundantes, mover un ejemplo desarrollado a un apéndice).

**Solo revisión se detiene aquí** (más sugerencias concretas y una advertencia honesta). No se
toca ningún archivo.

**La revisión completa continúa:**

3. **Resumen de cambios** (tras editar) — qué cambió y *por qué*, en orden: convenciones del
   congreso aplicadas · encuadre/estructura · formalización añadida · figuras · correcciones de
   compilación/referencias · ajuste de páginas. Luego **lo que debes hacer**: recompilar (p. ej. en
   Overleaf), verificar los elementos de renderizado nombrados (número de páginas, un glifo/flotante
   específico) y la siguiente palanca concreta si una comprobación falla. Señala todo lo que no pudo
   verificar localmente y cualquier regla del congreso sin confirmar.
4. **Una advertencia honesta** — el mayor riesgo ante revisores, dicho con claridad.

## 4b. Un ejemplo trabajado (entrada → salida)

Concretamente, así se ve una ejecución sobre el `examples/student-draft/` incluido.

**Entrada esperada** — la fuente LaTeX (no un PDF), más un congreso nombrado:

```
examples/student-draft/
  main.tex          # borrador clase article: RQ1–RQ3, títulos genéricos,
                    #   cero ecuaciones, \ref{fig:pipeline} a una figura sin etiqueta
  references.bib    # provisto pero nunca citado con \cite
```

> Prompt: *"Revisa esto para **ACL (ARR)**, luego revísalo para que se lea como un artículo aceptado."*

**Salida de ejemplo — la sesión de orientación + lista de hallazgos que Claude devuelve primero:**

```
Orientation briefing
  What it is   : a sentiment classifier that keeps punctuation as tokens; 3 sections.
  Contribution : punctuation-aware tokenization improves accuracy on movie reviews.
  Novelty      : thin as written — the idea is plausible but reads as one ablation.
                 Reviewer attack: "this is a preprocessing tweak, where's the analysis?"
  Completeness : results present but no significance test; one figure renders "??".

Findings (severity-ordered)
  [venue]         Target = ACL (ARR): 8-page body, Limitations/Ethics free, double-blind.
  [structure]     Explicit RQ1–RQ3 scaffolding → convert to prose contributions.
  [formalization] Zero equations though the scoring rule is central → add 2–3 eqs.
  [rigor]         Headline gain has no significance test / single split → top weakness.
  [bug]           \ref{fig:pipeline} targets a figure with no \label → add the label.
  [cross-ref]     Related work is a dated list → regroup thematically.
  [venue]         No Limitations section — ACL requires it and it is page-free → add.
```

**Solo revisión** se detiene ahí (más un par de sugerencias concretas y una advertencia honesta).
**La revisión completa** luego hace las ediciones y devuelve un resumen de cambios que te dice
exactamente qué recompilar y verificar. Califica cualquier ejecución contra el `EXPECTED-FINDINGS.md`
de la muestra.

## 5. El ida y vuelta con Overleaf (modo completo)

1. En Overleaf: **Menu → Download → Source** (un zip).
2. Dale a Claude el zip (o la fuente descomprimida).
3. Claude edita la fuente y reconstruye un zip de subida que contiene solo las entradas de
   compilación (`.tex`, `.bib`, clase/estilo, `figures/`) — excluye `main.pdf`, READMEs y archivos de git.
4. Vuelve a subir el zip a Overleaf y recompila.
5. Verifica los elementos de renderizado que Claude nombró (el número de páginas es una propiedad de
   renderizado, así que solo se confirma tras compilar).

## 6. Garantías de integridad de la investigación

- **Nunca inventa números.** Los marcadores de resultados solo se rellenan con ejecuciones reales y
  guardadas que tú proporciones. Si los resultados aún no existen, quedan como marcadores.
- **Preserva tu contenido técnico.** Las ediciones son quirúrgicas; el contenido intelectual
  permanece, solo cambia la presentación.
- **Ninguna acción compartida no solicitada.** No hará commit, push ni enviará nada sin que lo pidas.

## 7. Pruébalo en las muestras

Se incluyen dos borradores sintéticos (autores, conjuntos de datos y números ficticios — sin PI),
cada uno con una clave de respuestas `EXPECTED-FINDINGS.md` contra la que puedes calificar una
ejecución:

- **`examples/student-draft/main.tex`** — *por debajo* del límite, sembrado con los problemas
  clásicos: andamiaje RQ1–RQ3, títulos genéricos ("Approach", "Experiment 1"), sección de trabajo
  relacionado cronológica, cero ecuaciones aunque la regla de puntuación es central, y un
  `\ref{fig:pipeline}` cuya figura no tiene `\label` (renderiza `??`). Tampoco tiene prueba de
  significancia — el hallazgo `[rigor]`.
- **`examples/over-length/main.tex`** — apunta al límite de artículo corto de ACL (4 páginas) pero
  ocupa ~5-6 páginas. Por lo demás está pulido, así que ejercita el **paso de recorte**: fusionar dos
  párrafos de introducción redundantes, ajustar un trabajo relacionado repetitivo y mover un ejemplo
  desarrollado largo a un apéndice.

Ejecuta cualquier modo sobre cualquier borrador para ver la sesión, la lista de hallazgos y — en modo
completo — las ediciones.

## 8. Preguntas frecuentes

**¿Puedo darle solo un PDF?** Para solo revisión, sí. Para revisión completa, no — pídeselo a Claude
y solicitará la fuente LaTeX.

**¿Traducirá mi artículo a mi idioma de chat?** No. El manuscrito permanece en el idioma del
congreso; solo la conversación sigue tu idioma elegido.

**¿Funciona para congresos que no sean ACL?** Sí. `references/venue-conventions.md` cubre la familia
ACL, Springer/LNCS/AJCAI y congresos de ML; para cualquier otro Claude lee la convocatoria actual
primero y registra lo que aprende.

## 9. Usarlo con otros modelos de IA (Codex, DeepSeek, Gemini, …)

El skill son solo **instrucciones Markdown independientes del modelo** — solo el front-matter YAML
al inicio de `SKILL.md` (el bloque `name:`/`description:` entre las líneas `---`) es metadato de
autocarga específico de Claude. Todo lo de abajo es un prompt corriente que cualquier modelo capaz
puede seguir. Para usarlo en otro lado:

1. **Copia el cuerpo de `SKILL.md`** — todo lo que está *después* del `---` de cierre — en el
   **prompt de sistema** de la otra herramienta (o pégalo como primer mensaje). Puedes descartar el
   front-matter YAML; solo importa para el autodescubrimiento de skills de Claude.
2. **Adjunta el archivo de referencia** `references/venue-conventions.md` como contexto (pégalo o
   añádelo al panel de archivos/contexto de la herramienta) para que el modelo tenga las reglas del
   congreso.
3. **Dale el artículo del mismo modo** — la fuente `.tex` para revisión completa, o un PDF para solo
   revisión — y nombra el congreso destino.

Notas por herramienta:

- **OpenAI Codex / ChatGPT, DeepSeek, Gemini, modelos locales:** pega el cuerpo de `SKILL.md` como
  prompt de sistema/desarrollador y el archivo de convenciones como contexto, luego procede como
  arriba. No se necesitan cambios de formato — todo son instrucciones en prosa.
- **Cursor / Windsurf / Continue y agentes de IDE similares:** pon el cuerpo de `SKILL.md` en el
  archivo de reglas del proyecto que la herramienta lee (p. ej. `.cursorrules`, `AGENTS.md`) para que
  se cargue automáticamente, y mantén `references/` en el repositorio.
- **Lo que puede diferir:** la invocación de herramientas y la edición de archivos ocurren mediante
  el propio mecanismo de cada host, así que un modelo sin acceso de escritura al repositorio aún
  puede hacer **solo revisión** (solo lee y comenta) pero necesita un host con capacidad de edición
  para la **revisión completa**. La regla de integridad de la investigación — *nunca inventar números
  de resultados* — está en el propio prompt, así que se transfiere a cualquier modelo que siga las
  instrucciones.
