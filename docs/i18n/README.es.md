# Skill de revisión de artículos académicos

<!-- badges -->
[![stars](https://img.shields.io/github/stars/ZoeLinUTS/Academic-paper-revision?style=flat)](https://github.com/ZoeLinUTS/Academic-paper-revision/stargazers)
[![last commit](https://img.shields.io/github/last-commit/ZoeLinUTS/Academic-paper-revision)](https://github.com/ZoeLinUTS/Academic-paper-revision/commits)
[![license](https://img.shields.io/github/license/ZoeLinUTS/Academic-paper-revision)](../../LICENSE)
[![sponsor](https://img.shields.io/badge/sponsor-%E2%9D%A4-ff69b4)](https://github.com/sponsors/ZoeLinUTS)

<!-- i18n language switcher -->
[English](../../README.md) | [中文](README.zh-CN.md) | [日本語](README.ja.md) | [한국어](README.ko.md) | **Español** | [Français](README.fr.md) | [Deutsch](README.de.md) | [Português](README.pt.md)
<!-- Las traducciones las mantiene la comunidad; el inglés es la fuente de verdad y puede ir por delante. -->

---

Un **skill de Claude** reutilizable que convierte un borrador de artículo de investigación
tosco o de estilo estudiantil (LaTeX) en un envío que se lee como un artículo *aceptado* en
un **congreso destino específico**. Es *primero el congreso*: primero fija las reglas del
destino, estudia los artículos aceptados del congreso y luego reescribe, en lugar de aplicar
consejos genéricos de "buena redacción" que a menudo son incorrectos para un congreso dado.

## ✅ Pruébalo en línea ahora — sin instalación, sin clave de API

Una versión gratuita alojada, **solo revisión**, de este skill funciona en
**[zoelin.dev/tools/paper-revision](https://zoelin.dev/tools/paper-revision)**:
sube un PDF o una fuente LaTeX, elige tu congreso destino y recibe la sesión de
orientación más la lista de hallazgos ordenada por severidad directamente en tu
navegador — incluye un modelo alojado gratuito, o usa tu propia clave de API
para una salida de mayor calidad. Nunca edita ni almacena tu archivo; para el
modo de **revisión completa** (donde Claude reescribe tu `.tex`), usa el skill
directamente con Claude Code como se describe más abajo.

## Qué hay en este repositorio

```
SKILL.md                            El skill en sí (instrucciones que Claude sigue)
references/venue-conventions.md     Convenciones de congresos (familia ACL, LNCS/AJCAI, conf. ML)
docs/USER-GUIDE.md                  Uso paso a paso, los dos modos, preguntas frecuentes
docs/i18n/                          READMEs traducidos (selector de idioma arriba)
examples/student-draft/             Borrador sintético por debajo del límite + .bib + clave
examples/over-length/               Borrador sintético por encima del límite (recorte)
```

## Qué hace el skill

- **Convenciones primero el congreso.** Antes de tocar la prosa, carga las reglas del destino
  (límite de páginas, secciones obligatorias, anonimato, plantilla). Sabe, p. ej., que en la
  familia ACL las secciones Limitations/Ethics son *gratis* (no cuentan para el límite) y que
  LNCS pliega el trabajo futuro en la conclusión.
- **Tono de artículo aceptado.** Elimina el andamiaje RQ/RO en favor de contribuciones en prosa,
  usa títulos con sintagmas nominales declarativos y reorganiza el trabajo relacionado por tema
  en lugar de por fecha.
- **Formalización equilibrada.** Añade ecuaciones numeradas y flotantes `algorithm` donde el
  método está poco formalizado, y protege cada fórmula contra malas interpretaciones.
- **Correcciones de estructura y referencias cruzadas.** Conecta figuras sin referenciar,
  resuelve `\ref`/`\cite` colgantes y añade paquetes del preámbulo que falten.
- **Ajuste de páginas.** Recorta el cuerpo principal al límite mediante ediciones que preservan
  el contenido (fusión de párrafos, mover un ejemplo desarrollado a un apéndice).
- **Novedad y posicionamiento.** Si se pide, reconstruye el linaje de la línea de trabajo,
  agrupa el trabajo previo por temas, nombra el diferenciador y da una lista franca de los
  probables ataques de los revisores.

## Dos formas de usarlo

1. **Revisión completa (edita tu LaTeX).** Le das a Claude la fuente compilable (`.tex` + `.bib`
   + archivos de clase/estilo + `figures/`, o un zip de exportación de Overleaf) y hace ediciones
   quirúrgicas, compensa cualquier longitud añadida y devuelve un zip de subida reconstruido.
   **Nunca inventa números de resultados.**
2. **Solo revisión (sin ediciones).** Claude lee el borrador y devuelve *solo panorama y
   comentarios*: tema/alcance de la investigación, contribución central, novedad franca,
   problemas y sugerencias concretas, sin cambiar ningún archivo. Este es el modo que expone
   la [aplicación web alojada](https://zoelin.dev/tools/paper-revision).

Ambos modos funcionan en **cualquier idioma**: haz tus preguntas y recibe el panorama, los
comentarios y los resúmenes en chino, español, árabe o cualquier otro idioma. Solo la
*conversación* sigue tu idioma; el manuscrito permanece en el idioma exigido por el congreso
(normalmente inglés).

Consulta [`docs/USER-GUIDE.md`](../USER-GUIDE.md) para el recorrido detallado.

## Inicio rápido

Usando el skill con Claude (p. ej. Claude Code) en un repositorio que contiene este skill:

1. Apunta Claude a la fuente de tu artículo (o al ejemplo de abajo) y nombra tu **congreso destino**.
2. Pídele *"revísalo para <congreso>"* (modo completo) o *"solo revisa este borrador y dame un
   panorama y comentarios, no edites nada"* (modo solo revisión).
3. Claude devuelve primero una **sesión de orientación** y una **lista de hallazgos**, y luego
   —en modo completo— hace las ediciones y te dice exactamente qué recompilar y verificar.

Pruébalo con los borradores sintéticos incluidos (ambos **ficticios**: autores, conjuntos de
datos y números inventados, sin PI; cada uno con una clave de respuestas `EXPECTED-FINDINGS.md`):

```
examples/student-draft/main.tex   por debajo del límite; problemas clásicos de borrador estudiantil
examples/over-length/main.tex     por encima del límite de páginas; ejercita el recorte
```

- **`student-draft`** está sembrado con andamiaje RQ1–RQ3, títulos genéricos, trabajo relacionado
  cronológico, cero ecuaciones y un `\ref` a una figura sin `\label`. Está *por debajo* del límite,
  así que ejercita añadir secciones gratuitas.
- **`over-length`** apunta al límite de artículo corto de ACL (4 páginas) pero ocupa ~5-6 páginas;
  por lo demás está pulido, así que ejercita el **paso de recorte** (fusionar párrafos redundantes,
  mover un ejemplo desarrollado a un apéndice).

## Una nota sobre las entradas

El modo de revisión completa necesita la **fuente LaTeX**, no un PDF: no puedes hacer ediciones
quirúrgicas a un PDF, y reconstruir la fuente arriesga el formato y los números del autor. El modo
solo revisión puede trabajar desde un PDF porque únicamente lee y comenta.
