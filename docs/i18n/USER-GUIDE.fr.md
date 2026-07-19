# Guide de l'utilisateur

> [English](../USER-GUIDE.md) | [中文](USER-GUIDE.zh-CN.md) | [日本語](USER-GUIDE.ja.md) | [한국어](USER-GUIDE.ko.md) | [Español](USER-GUIDE.es.md) | **Français** | [Deutsch](USER-GUIDE.de.md) | [Português](USER-GUIDE.pt.md)

Comment utiliser le skill `academic-paper-revision`, que lui donner, ce qu'il renvoie, et en quoi
les deux modes de fonctionnement diffèrent.

## 1. Avant de commencer : ce qu'il faut préparer

| Mode | Entrée que vous fournissez | Ce que fait Claude |
| --- | --- | --- |
| **Révision complète** | La **source LaTeX** : le `.tex` principal, `references.bib`, la classe/style du lieu (`llncs.cls` + `*.bst`, ou `acl.sty`, …) et `figures/`. Un **export de projet Overleaf (zip)** est idéal. | Effectue des modifications chirurgicales à la source, compense la longueur ajoutée et renvoie un zip de dépôt reconstruit. |
| **Relecture seule** | Un **PDF** (ou la source) suffit. | Lit et commente seulement — **aucun fichier n'est modifié**. |

Le mode révision complète a besoin de la source car on ne peut pas éditer chirurgicalement un PDF,
et reconstruire la source depuis un PDF risque de perdre votre mise en forme et vos chiffres. Un
`main.pdf` rendu est une entrée *secondaire* utile pour vérifier les propriétés de rendu (nombre de
pages, placement des flottants) mais n'est jamais suffisant à lui seul.

## 2. Indiquez à Claude votre lieu cible

C'est déterminant. La même modification peut être correcte pour un lieu et un rejet direct pour un
autre. Nommez le lieu explicitement (ACL, NAACL, EMNLP, AJCAI, NeurIPS, …). Si les règles exactes du
lieu ne sont pas connues, Claude consultera l'appel à contributions courant avant d'éditer. Si un nom
de fichier indique un lieu mais que vos notes en indiquent un autre, il signalera l'écart et posera
la question — il ne suppose jamais.

## 3. Choisissez un mode et demandez

**Révision complète** — exemples de prompts :
- « Révise ceci pour EMNLP pour que ça se lise comme un article accepté. »
- « Réduis ceci à la limite de pages d'AJCAI et retire l'échafaudage RQ. »
- « Ajoute une équation/un algorithme pour la méthode et corrige les références croisées. »

**Relecture seule** — exemples de prompts :
- « Relis simplement ce brouillon — donne-moi un aperçu et des commentaires, n'édite rien. »
- « Évalue la nouveauté et dis-moi les objections probables des relecteurs. »
- « Quel est le périmètre et les principaux problèmes ici ? En quoi est-ce différent des travaux antérieurs ? »

### Langue de communication — n'importe quelle langue

Vous pouvez travailler dans **n'importe quelle langue** et obtenir vos résultats dans celle-ci.
Claude demandera (ou déduira) dans quelle langue vous voulez la *conversation* — chinois, espagnol,
arabe ou autre — et livre chaque aperçu, commentaire, constat et résumé dans cette langue. C'est
indépendant du manuscrit : **l'article reste en anglais** (ou la langue exigée par le lieu) ; seule
la conversation suit la langue que vous avez choisie.

## 4. À quoi ressemble la sortie

Les deux modes commencent par le même mémo de style relecteur :

1. **Briefing d'orientation** — *Ce qu'est le brouillon* · *Contribution centrale* · *Nouveauté
   (franche, avec des puces d'attaque de relecteurs)* · *État d'achèvement*. Se termine en confirmant
   le lieu et en demandant le périmètre voulu.
2. **Liste de constats** — une ligne par problème, groupée et triée par gravité, chacune nommant la
   correction. Exemples de lignes :
   - `[venue]` Cible = ACL (ARR) : corps de 8 pages, Limitations/Ethics gratuits, double aveugle.
   - `[structure]` Échafaudage RQ1–RQ3 explicite → convertir en contributions en prose.
   - `[formalization]` Zéro équation alors que la règle de score est centrale → ajouter 2–3 éq.
   - `[rigor]` Le gain phare n'a pas de test de significativité / partition unique → signaler comme faiblesse majeure.
   - `[bug]` `\ref{fig:pipeline}` cible une figure sans `\label` → ajouter l'étiquette.
   - `[cross-ref]` L'état de l'art est une liste chronologique → regrouper par thèmes.
   - `[page-fit]` +0,5 page en trop → apparié aux mouvements `[trim]` exacts qui le compensent
     (fusionner les paragraphes redondants, déplacer un exemple détaillé en annexe).

**La relecture seule s'arrête ici** (plus des suggestions concrètes et une mise en garde honnête).
Aucun fichier n'est touché.

**La révision complète continue :**

3. **Résumé des changements** (après édition) — ce qui a changé et *pourquoi*, dans l'ordre :
   conventions du lieu appliquées · cadrage/structure · formalisation ajoutée · figures · corrections
   de compilation/références · ajustement de pagination. Puis **ce que vous devez faire** : recompiler
   (p. ex. sur Overleaf), vérifier les éléments de rendu nommés (nombre de pages, un glyphe/flottant
   précis) et le prochain levier concret si une vérification échoue. Il signale tout ce qu'il n'a pas
   pu vérifier localement et toute règle de lieu non confirmée.
4. **Une mise en garde honnête** — le plus grand risque côté relecteur, dit clairement.

## 4b. Un exemple détaillé (entrée → sortie)

Concrètement, voici à quoi ressemble une exécution sur le `examples/student-draft/` fourni.

**Entrée attendue** — la source LaTeX (pas un PDF), plus un lieu nommé :

```
examples/student-draft/
  main.tex          # brouillon classe article : RQ1–RQ3, titres génériques,
                    #   zéro équation, \ref{fig:pipeline} vers une figure sans étiquette
  references.bib    # fourni mais jamais cité avec \cite
```

> Prompt : *« Relis ceci pour **ACL (ARR)**, puis révise-le pour que ça se lise comme un article accepté. »*

**Sortie d'exemple — le briefing + la liste de constats que Claude renvoie d'abord :**

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

**La relecture seule** s'arrête là (plus quelques suggestions concrètes et une mise en garde
honnête). **La révision complète** effectue ensuite les modifications et renvoie un résumé des
changements vous indiquant exactement quoi recompiler et vérifier. Notez toute exécution par rapport
au `EXPECTED-FINDINGS.md` de l'exemple.

## 5. L'aller-retour Overleaf (mode complet)

1. Dans Overleaf : **Menu → Download → Source** (un zip).
2. Donnez le zip à Claude (ou la source décompressée).
3. Claude édite la source et reconstruit un zip de dépôt contenant uniquement les entrées de
   compilation (`.tex`, `.bib`, classe/style, `figures/`) — il exclut `main.pdf`, les README et les
   fichiers git.
4. Re-téléversez le zip sur Overleaf et recompilez.
5. Vérifiez les éléments de rendu que Claude a nommés (le nombre de pages est une propriété de rendu,
   donc confirmable seulement après compilation).

## 6. Garanties d'intégrité de la recherche

- **N'invente jamais de chiffres.** Les emplacements de résultats ne sont remplis qu'à partir
  d'exécutions réelles et sauvegardées que vous fournissez. Si les résultats n'existent pas encore,
  ils restent des emplacements.
- **Préserve votre contenu technique.** Les modifications sont chirurgicales ; le contenu
  intellectuel reste, seule la présentation change.
- **Aucune action partagée non sollicitée.** Il ne fera pas de commit, de push ni n'enverra quoi que
  ce soit sans que vous le demandiez.

## 7. Essayez-le sur les exemples

Deux brouillons synthétiques sont inclus (auteurs, jeux de données et chiffres fictifs — sans PI),
chacun avec un corrigé `EXPECTED-FINDINGS.md` pour noter une exécution :

- **`examples/student-draft/main.tex`** — *sous* la limite, truffé des problèmes classiques :
  échafaudage RQ1–RQ3, titres génériques (« Approach », « Experiment 1 »), état de l'art
  chronologique, zéro équation alors que la règle de score est centrale, et un `\ref{fig:pipeline}`
  dont la figure n'a pas de `\label` (rend `??`). Pas de test de significativité non plus — le
  constat `[rigor]`.
- **`examples/over-length/main.tex`** — vise la limite d'article court ACL (4 pages) mais fait ~5-6
  pages. Il est par ailleurs soigné, donc il exerce l'**étape d'élagage** : fusionner deux paragraphes
  d'introduction redondants, resserrer un état de l'art répétitif et déplacer un long exemple détaillé
  en annexe.

Exécutez n'importe quel mode sur n'importe quel brouillon pour voir le briefing, la liste de constats
et — en mode complet — les modifications.

## 8. FAQ

**Puis-je ne donner qu'un PDF ?** Pour la relecture seule, oui. Pour la révision complète, non —
demandez à Claude et il réclamera la source LaTeX.

**Va-t-il traduire mon article dans ma langue de chat ?** Non. Le manuscrit reste dans la langue du
lieu ; seule la conversation suit la langue que vous avez choisie.

**Fonctionne-t-il pour des lieux non-ACL ?** Oui. `references/venue-conventions.md` couvre la famille
ACL, Springer/LNCS/AJCAI et les conférences ML ; pour tout autre, Claude lit d'abord l'appel à
contributions courant et consigne ce qu'il apprend.

## 9. L'utiliser avec d'autres modèles d'IA (Codex, DeepSeek, Gemini, …)

Le skill n'est que des **instructions Markdown indépendantes du modèle** — seul le front-matter YAML
en tête de `SKILL.md` (le bloc `name:`/`description:` entre les lignes `---`) est une métadonnée de
chargement automatique propre à Claude. Tout ce qui est en dessous est un prompt ordinaire que tout
modèle capable peut suivre. Pour l'utiliser ailleurs :

1. **Copiez le corps de `SKILL.md`** — tout ce qui suit le `---` de fermeture — dans le **prompt
   système** de l'autre outil (ou collez-le comme premier message). Vous pouvez retirer le front-matter
   YAML ; il ne compte que pour la découverte automatique des skills de Claude.
2. **Joignez le fichier de référence** `references/venue-conventions.md` en contexte (collez-le ou
   ajoutez-le au panneau fichiers/contexte de l'outil) pour que le modèle ait les règles du lieu.
3. **Donnez-lui l'article de la même façon** — la source `.tex` pour la révision complète, ou un PDF
   pour la relecture seule — et nommez le lieu cible.

Notes par outil :

- **OpenAI Codex / ChatGPT, DeepSeek, Gemini, modèles locaux :** collez le corps de `SKILL.md` comme
  prompt système/développeur et le fichier de conventions en contexte, puis procédez comme ci-dessus.
  Aucun changement de format nécessaire — ce sont toutes des instructions en prose.
- **Cursor / Windsurf / Continue et agents d'IDE similaires :** mettez le corps de `SKILL.md` dans le
  fichier de règles du projet que l'outil lit (p. ex. `.cursorrules`, `AGENTS.md`) pour un chargement
  automatique, et gardez `references/` dans le dépôt.
- **Ce qui peut différer :** l'appel d'outils et l'édition de fichiers passent par le mécanisme propre
  de chaque hôte, donc un modèle sans accès en écriture au dépôt peut quand même faire de la **relecture
  seule** (il lit et commente) mais a besoin d'un hôte capable d'éditer pour la **révision complète**.
  La règle d'intégrité de la recherche — *ne jamais inventer de chiffres de résultats* — est énoncée
  dans le prompt lui-même, donc elle se transmet à tout modèle qui suit les instructions.
