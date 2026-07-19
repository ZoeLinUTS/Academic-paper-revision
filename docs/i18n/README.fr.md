# Skill de révision d'articles académiques

<!-- badges -->
[![stars](https://img.shields.io/github/stars/ZoeLinUTS/Academic-paper-revision?style=flat)](https://github.com/ZoeLinUTS/Academic-paper-revision/stargazers)
[![last commit](https://img.shields.io/github/last-commit/ZoeLinUTS/Academic-paper-revision)](https://github.com/ZoeLinUTS/Academic-paper-revision/commits)
[![license](https://img.shields.io/github/license/ZoeLinUTS/Academic-paper-revision)](../../LICENSE)
[![sponsor](https://img.shields.io/badge/sponsor-%E2%9D%A4-ff69b4)](https://github.com/sponsors/ZoeLinUTS)

<!-- i18n language switcher -->
[English](../../README.md) | [中文](README.zh-CN.md) | [日本語](README.ja.md) | [한국어](README.ko.md) | [Español](README.es.md) | **Français** | [Deutsch](README.de.md) | [Português](README.pt.md)
<!-- Les traductions sont maintenues par la communauté ; l'anglais fait foi et peut être en avance. -->

---

Un **skill Claude** réutilisable qui transforme un brouillon d'article de recherche brut ou de
style étudiant (LaTeX) en une soumission qui se lit comme un article *accepté* à un **lieu de
publication cible précis**. Il est *centré sur le lieu* : il fixe d'abord les règles de la cible,
étudie les articles acceptés du lieu, puis réécrit — au lieu d'appliquer des conseils génériques
de « bonne écriture » souvent inadaptés à un lieu donné.

> **Statut :** le skill et un brouillon d'exemple synthétique sont prêts à l'emploi dès
> aujourd'hui. Un relecteur de PDF en lecture seule hébergé (Vercel / GitHub Pages) figure sur la
> [feuille de route](#feuille-de-route).

## Contenu de ce dépôt

```
SKILL.md                            Le skill lui-même (instructions que Claude suit)
references/venue-conventions.md     Conventions des lieux (famille ACL, LNCS/AJCAI, conf. ML)
docs/USER-GUIDE.md                  Utilisation pas à pas, les deux modes, FAQ
docs/i18n/                          README traduits (sélecteur de langue ci-dessus)
examples/student-draft/             Brouillon synthétique sous la limite + .bib + corrigé
examples/over-length/               Brouillon synthétique au-dessus de la limite (élagage)
```

## Ce que fait le skill

- **Conventions centrées sur le lieu.** Avant de toucher au texte, il charge les règles de la cible
  (limite de pages, sections obligatoires, anonymat, gabarit). Il sait par exemple que dans la
  famille ACL les sections Limitations/Ethics sont *gratuites* (ne comptent pas dans la limite) et
  que LNCS intègre les travaux futurs dans la conclusion.
- **Ton d'article accepté.** Supprime l'échafaudage RQ/RO au profit de contributions en prose,
  utilise des titres en syntagmes nominaux déclaratifs et réorganise l'état de l'art par thème
  plutôt que par date.
- **Formalisation équilibrée.** Ajoute des équations numérotées et des flottants `algorithm` là où
  la méthode est peu formalisée, et prémunit chaque formule contre les mauvaises interprétations.
- **Corrections de structure et de références croisées.** Relie les figures non référencées, résout
  les `\ref`/`\cite` en suspens et ajoute les paquets manquants du préambule.
- **Ajustement de pagination.** Réduit le corps principal à la limite par des modifications
  préservant le contenu (fusion de paragraphes, déplacement d'un exemple détaillé en annexe).
- **Nouveauté et positionnement.** Sur demande, reconstruit la lignée du domaine, regroupe les
  travaux antérieurs par thème, nomme l'élément différenciateur et donne une liste franche des
  attaques probables des relecteurs.

## Deux façons de l'utiliser

1. **Révision complète (édite votre LaTeX).** Vous donnez à Claude la source compilable (`.tex` +
   `.bib` + fichiers de classe/style + `figures/`, ou un zip d'export Overleaf) et il effectue des
   modifications chirurgicales, compense toute longueur ajoutée et vous rend un zip de dépôt
   reconstruit. **Il n'invente jamais de chiffres de résultats.**
2. **Relecture seule (sans édition).** Claude lit le brouillon et renvoie *un aperçu et des
   commentaires uniquement* : sujet/portée de la recherche, contribution centrale, nouveauté
   franche, problèmes et suggestions concrètes, sans modifier aucun fichier. C'est le mode que
   l'application web prévue exposera.

Les deux modes fonctionnent dans **n'importe quelle langue** : posez vos questions et recevez
l'aperçu, les commentaires et les résumés en chinois, espagnol, arabe ou toute autre langue. Seule
la *conversation* suit votre langue ; le manuscrit reste dans la langue exigée par le lieu
(généralement l'anglais).

Voir [`docs/USER-GUIDE.md`](../USER-GUIDE.md) pour le guide détaillé.

## Démarrage rapide

En utilisant le skill avec Claude (p. ex. Claude Code) dans un dépôt contenant ce skill :

1. Orientez Claude vers la source de votre article (ou l'exemple ci-dessous) et nommez votre
   **lieu cible**.
2. Demandez-lui *« révise ceci pour <lieu> »* (mode complet) ou *« relis simplement ce brouillon et
   donne-moi un aperçu et des commentaires, ne modifie rien »* (mode relecture seule).
3. Claude renvoie d'abord un **briefing d'orientation** et une **liste de constats**, puis — en
   mode complet — effectue les modifications et vous dit exactement quoi recompiler et vérifier.

Essayez-le sur les brouillons synthétiques inclus (les deux sont **fictifs** : auteurs, jeux de
données et chiffres inventés, sans PI ; chacun avec un corrigé `EXPECTED-FINDINGS.md`) :

```
examples/student-draft/main.tex   sous la limite ; problèmes classiques de brouillon étudiant
examples/over-length/main.tex     au-dessus de la limite de pages ; exercice d'élagage
```

- **`student-draft`** est truffé d'échafaudage RQ1–RQ3, de titres génériques, d'un état de l'art
  chronologique, de zéro équation et d'un `\ref` vers une figure sans `\label`. Il est *sous* la
  limite, donc il exerce l'ajout de sections gratuites.
- **`over-length`** vise la limite d'article court ACL (4 pages) mais fait ~5-6 pages ; il est par
  ailleurs soigné, donc il exerce l'**étape d'élagage** (fusion de paragraphes redondants,
  déplacement d'un exemple détaillé en annexe).

## Feuille de route

Un **relecteur de PDF en lecture seule** hébergé : l'utilisateur téléverse un brouillon PDF et
reçoit un aperçu et des commentaires — sujet et portée de la recherche, problèmes traités,
contribution centrale, lecture franche de la nouveauté et suggestions d'amélioration — **sans
aucune édition LaTeX**. Prévu comme un petit front-end web (Vercel ou GitHub Pages) au-dessus du
mode relecture seule décrit ci-dessus. C'est une tâche distincte suivie dans le même dépôt ; la
logique d'analyse vit déjà dans le skill.

## Une note sur les entrées

Le mode révision complète a besoin de la **source LaTeX**, pas d'un PDF : on ne peut pas éditer
chirurgicalement un PDF, et reconstruire la source risque de dégrader la mise en forme et les
chiffres de l'auteur. Le mode relecture seule peut travailler à partir d'un PDF car il ne fait que
lire et commenter.
