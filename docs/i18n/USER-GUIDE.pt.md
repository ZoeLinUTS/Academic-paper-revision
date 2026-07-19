# Guia do usuário

> [English](../USER-GUIDE.md) | [中文](USER-GUIDE.zh-CN.md) | [日本語](USER-GUIDE.ja.md) | [한국어](USER-GUIDE.ko.md) | [Español](USER-GUIDE.es.md) | [Français](USER-GUIDE.fr.md) | [Deutsch](USER-GUIDE.de.md) | **Português**

Como usar o skill `academic-paper-revision`, o que fornecer, o que ele retorna e como os dois modos
de operação diferem.

## 1. Antes de começar: o que ter pronto

| Modo | Entrada que você fornece | O que o Claude faz |
| --- | --- | --- |
| **Revisão completa** | A **fonte LaTeX**: `.tex` principal, `references.bib`, a classe/estilo do evento (`llncs.cls` + `*.bst`, ou `acl.sty`, …) e `figures/`. Uma **exportação de projeto do Overleaf (zip)** é ideal. | Faz edições cirúrgicas na fonte, compensa o comprimento adicionado e devolve um zip de upload reconstruído. |
| **Somente revisão** | Um **PDF** (ou a fonte) basta. | Apenas lê e comenta — **nenhum arquivo é alterado**. |

O modo de revisão completa precisa da fonte porque você não pode editar cirurgicamente um PDF, e
reconstruir a fonte a partir de um PDF arrisca perder sua formatação e seus números. Um `main.pdf`
renderizado é uma entrada *secundária* útil para verificar propriedades de renderização (número de
páginas, posicionamento de floats), mas nunca é suficiente sozinho.

## 2. Diga ao Claude seu evento-alvo

Isso é decisivo. A mesma edição pode ser correta para um evento e uma rejeição direta para outro.
Nomeie o evento explicitamente (ACL, NAACL, EMNLP, AJCAI, NeurIPS, …). Se as regras exatas do evento
não forem conhecidas, o Claude consultará a chamada de trabalhos atual antes de editar. Se um nome de
arquivo diz um evento mas suas notas dizem outro, ele apontará a discrepância e perguntará — nunca
presume.

## 3. Escolha um modo e peça

**Revisão completa** — exemplos de prompts:
- "Revise isto para EMNLP para que se leia como um artigo aceito."
- "Corte isto para o limite de páginas da AJCAI e remova o andaime RQ."
- "Adicione uma equação/algoritmo para o método e conserte as referências cruzadas."

**Somente revisão** — exemplos de prompts:
- "Apenas revise este rascunho — me dê um panorama e comentários, não edite nada."
- "Avalie a novidade e me diga as prováveis objeções dos revisores."
- "Qual é o escopo e os principais problemas aqui? Como difere do trabalho anterior?"

### Idioma de comunicação — qualquer idioma

Você pode trabalhar em **qualquer idioma** e obter seus resultados nele. O Claude perguntará (ou
inferirá) em que idioma você quer a *conversa* — chinês, espanhol, árabe ou qualquer outro — e entrega
cada panorama, comentário, achado e resumo nesse idioma. Isso é independente do manuscrito: **o artigo
permanece em inglês** (ou o idioma exigido pelo evento); apenas a conversa segue o idioma escolhido.

## 4. Como é a saída

Ambos os modos começam com o mesmo memorando estilo revisor:

1. **Briefing de orientação** — *O que é o rascunho* · *Contribuição central* · *Novidade (franca,
   com marcadores de ataque de revisores)* · *Estado de completude*. Termina confirmando o evento e
   perguntando qual escopo você quer.
2. **Lista de achados** — uma linha por problema, agrupada e ordenada por severidade, cada uma nomeando
   a correção. Linhas de exemplo:
   - `[venue]` Alvo = ACL (ARR): corpo de 8 páginas, Limitations/Ethics grátis, duplo-cego.
   - `[structure]` Andaime RQ1–RQ3 explícito → converter em contribuições em prosa.
   - `[formalization]` Zero equações embora a regra de pontuação seja central → adicionar 2–3 eqs.
   - `[rigor]` O ganho principal não tem teste de significância / partição única → marcar como fraqueza principal.
   - `[bug]` `\ref{fig:pipeline}` aponta para uma figura sem `\label` → adicionar o rótulo.
   - `[cross-ref]` O trabalho relacionado é uma lista cronológica → reagrupar por temas.
   - `[page-fit]` +0,5 página a mais → emparelhado com os movimentos `[trim]` exatos que o compensam
     (fundir parágrafos redundantes, mover um exemplo trabalhado para um apêndice).

**Somente revisão para aqui** (mais sugestões concretas e uma ressalva honesta). Nenhum arquivo é
tocado.

**A revisão completa continua:**

3. **Resumo de mudanças** (após editar) — o que mudou e *por quê*, em ordem: convenções do evento
   aplicadas · enquadramento/estrutura · formalização adicionada · figuras · correções de
   compilação/referências · ajuste de páginas. Depois **o que você deve fazer**: recompilar (p. ex. no
   Overleaf), verificar os itens de renderização nomeados (número de páginas, um glifo/float
   específico) e a próxima alavanca concreta se uma verificação falhar. Ele sinaliza tudo o que não
   pôde verificar localmente e qualquer regra do evento não confirmada.
4. **Uma ressalva honesta** — o maior risco perante os revisores, dito com clareza.

## 4b. Um exemplo trabalhado (entrada → saída)

Concretamente, é assim que uma execução sobre o `examples/student-draft/` incluído se parece.

**Entrada esperada** — a fonte LaTeX (não um PDF), mais um evento nomeado:

```
examples/student-draft/
  main.tex          # rascunho classe article: RQ1–RQ3, títulos genéricos,
                    #   zero equações, \ref{fig:pipeline} para uma figura sem rótulo
  references.bib    # fornecido mas nunca citado com \cite
```

> Prompt: *"Revise isto para **ACL (ARR)**, depois reescreva para que se leia como um artigo aceito."*

**Saída de exemplo — o briefing + lista de achados que o Claude retorna primeiro:**

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

**Somente revisão** para aí (mais algumas sugestões concretas e uma ressalva honesta). **A revisão
completa** então faz as edições e devolve um resumo de mudanças dizendo exatamente o que recompilar e
verificar. Avalie qualquer execução contra o `EXPECTED-FINDINGS.md` da amostra.

## 5. O vai e volta com o Overleaf (modo completo)

1. No Overleaf: **Menu → Download → Source** (um zip).
2. Dê o zip ao Claude (ou a fonte descompactada).
3. O Claude edita a fonte e reconstrói um zip de upload contendo apenas as entradas de compilação
   (`.tex`, `.bib`, classe/estilo, `figures/`) — ele exclui `main.pdf`, READMEs e arquivos de git.
4. Reenvie o zip ao Overleaf e recompile.
5. Verifique os itens de renderização que o Claude nomeou (o número de páginas é uma propriedade de
   renderização, então só é confirmado após compilar).

## 6. Garantias de integridade da pesquisa

- **Nunca inventa números.** Marcadores de resultados só são preenchidos a partir de execuções reais e
  salvas que você fornecer. Se os resultados ainda não existem, permanecem como marcadores.
- **Preserva seu conteúdo técnico.** As edições são cirúrgicas; o conteúdo intelectual permanece,
  apenas a apresentação muda.
- **Nenhuma ação compartilhada não solicitada.** Ele não fará commit, push nem enviará nada sem você
  pedir.

## 7. Experimente nas amostras

Dois rascunhos sintéticos estão incluídos (autores, conjuntos de dados e números fictícios — sem PI),
cada um com um gabarito `EXPECTED-FINDINGS.md` contra o qual você pode avaliar uma execução:

- **`examples/student-draft/main.tex`** — *abaixo* do limite, semeado com os problemas clássicos:
  andaime RQ1–RQ3, títulos genéricos ("Approach", "Experiment 1"), seção de trabalho relacionado
  cronológica, zero equações embora a regra de pontuação seja central, e um `\ref{fig:pipeline}` cuja
  figura não tem `\label` (renderiza `??`). Também não tem teste de significância — o achado `[rigor]`.
- **`examples/over-length/main.tex`** — mira o limite de artigo curto da ACL (4 páginas) mas ocupa ~5-6
  páginas. É de resto polido, então exercita o **passo de corte**: fundir dois parágrafos de introdução
  redundantes, apertar um trabalho relacionado repetitivo e mover um exemplo trabalhado longo para um
  apêndice.

Execute qualquer modo em qualquer rascunho para ver o briefing, a lista de achados e — no modo completo
— as edições.

## 8. Perguntas frequentes

**Posso dar apenas um PDF?** Para somente revisão, sim. Para revisão completa, não — pergunte ao Claude
e ele solicitará a fonte LaTeX.

**Ele traduzirá meu artigo para o idioma do chat?** Não. O manuscrito permanece no idioma do evento;
apenas a conversa segue o idioma escolhido.

**Funciona para eventos que não sejam ACL?** Sim. `references/venue-conventions.md` cobre a família
ACL, Springer/LNCS/AJCAI e conferências de ML; para qualquer outro o Claude lê a CFP atual primeiro e
registra o que aprende.

## 9. Usá-lo com outros modelos de IA (Codex, DeepSeek, Gemini, …)

O skill são apenas **instruções Markdown independentes do modelo** — apenas o front-matter YAML no
topo de `SKILL.md` (o bloco `name:`/`description:` entre as linhas `---`) é metadado de autocarga
específico do Claude. Tudo abaixo é um prompt comum que qualquer modelo capaz pode seguir. Para usá-lo
em outro lugar:

1. **Copie o corpo de `SKILL.md`** — tudo *depois* do `---` de fechamento — para o **prompt de sistema**
   da outra ferramenta (ou cole como primeira mensagem). Você pode descartar o front-matter YAML; ele
   só importa para a autodescoberta de skills do Claude.
2. **Anexe o arquivo de referência** `references/venue-conventions.md` como contexto (cole ou adicione
   ao painel de arquivos/contexto da ferramenta) para que o modelo tenha as regras do evento.
3. **Dê o artigo do mesmo jeito** — a fonte `.tex` para revisão completa, ou um PDF para somente
   revisão — e nomeie o evento-alvo.

Notas por ferramenta:

- **OpenAI Codex / ChatGPT, DeepSeek, Gemini, modelos locais:** cole o corpo de `SKILL.md` como prompt
  de sistema/desenvolvedor e o arquivo de convenções como contexto, depois proceda como acima. Nenhuma
  mudança de formato é necessária — são todas instruções em prosa.
- **Cursor / Windsurf / Continue e agentes de IDE similares:** ponha o corpo de `SKILL.md` no arquivo
  de regras do projeto que a ferramenta lê (p. ex. `.cursorrules`, `AGENTS.md`) para que carregue
  automaticamente, e mantenha `references/` no repositório.
- **O que pode diferir:** a chamada de ferramentas e a edição de arquivos acontecem pelo mecanismo
  próprio de cada host, então um modelo sem acesso de escrita ao repositório ainda pode fazer **somente
  revisão** (só lê e comenta) mas precisa de um host com capacidade de edição para a **revisão
  completa**. A regra de integridade da pesquisa — *nunca inventar números de resultados* — está no
  próprio prompt, então se transfere para qualquer modelo que siga as instruções.
