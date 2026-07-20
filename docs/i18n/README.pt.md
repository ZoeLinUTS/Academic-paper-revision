# Skill de revisão de artigos acadêmicos

<!-- badges -->
[![stars](https://img.shields.io/github/stars/ZoeLinUTS/Academic-paper-revision?style=flat)](https://github.com/ZoeLinUTS/Academic-paper-revision/stargazers)
[![last commit](https://img.shields.io/github/last-commit/ZoeLinUTS/Academic-paper-revision)](https://github.com/ZoeLinUTS/Academic-paper-revision/commits)
[![license](https://img.shields.io/github/license/ZoeLinUTS/Academic-paper-revision)](../../LICENSE)
[![sponsor](https://img.shields.io/badge/sponsor-%E2%9D%A4-ff69b4)](https://github.com/sponsors/ZoeLinUTS)

<!-- i18n language switcher -->
[English](../../README.md) | [中文](README.zh-CN.md) | [日本語](README.ja.md) | [한국어](README.ko.md) | [Español](README.es.md) | [Français](README.fr.md) | [Deutsch](README.de.md) | **Português**
<!-- As traduções são mantidas pela comunidade; o inglês é a fonte da verdade e pode estar à frente. -->

---

Um **skill do Claude** reutilizável que transforma um rascunho de artigo de pesquisa tosco ou de
estilo estudantil (LaTeX) em uma submissão que se lê como um artigo *aceito* em um **evento-alvo
específico**. Ele é *evento primeiro*: primeiro fixa as regras do alvo, estuda os artigos aceitos
do evento e então reescreve — em vez de aplicar conselhos genéricos de "boa escrita" que muitas
vezes são errados para um dado evento.

## ✅ Experimente online agora — sem instalação, sem chave de API

Uma versão gratuita hospedada, **somente revisão**, deste skill roda em
**[zoelin.dev/tools/paper-revision](https://zoelin.dev/tools/paper-revision)**:
envie um PDF ou uma fonte LaTeX, escolha seu evento-alvo, e receba o briefing de
orientação mais a lista de achados ordenada por gravidade direto no seu navegador
— um modelo hospedado gratuito está incluído, ou traga sua própria chave de API
para uma saída de qualidade superior. Nunca edita nem armazena seu arquivo; para
o modo de **revisão completa** (onde o Claude realmente reescreve seu `.tex`),
use o skill diretamente com o Claude Code como descrito abaixo.

## O que há neste repositório

```
SKILL.md                            O skill em si (instruções que o Claude segue)
references/venue-conventions.md     Convenções de eventos (família ACL, LNCS/AJCAI, conf. de ML)
docs/USER-GUIDE.md                  Uso passo a passo, os dois modos, perguntas frequentes
docs/i18n/                          READMEs traduzidos (seletor de idioma acima)
examples/student-draft/             Rascunho sintético abaixo do limite + .bib + gabarito
examples/over-length/               Rascunho sintético acima do limite (exercita corte)
```

## O que o skill faz

- **Convenções evento primeiro.** Antes de tocar na prosa, carrega as regras do alvo (limite de
  páginas, seções obrigatórias, anonimato, modelo). Sabe, por exemplo, que na família ACL as seções
  Limitations/Ethics são *gratuitas* (não contam para o limite) e que o LNCS dobra o trabalho futuro
  na conclusão.
- **Tom de artigo aceito.** Remove o andaime RQ/RO em favor de contribuições em prosa, usa títulos
  em sintagmas nominais declarativos e reorganiza o trabalho relacionado por tema em vez de por data.
- **Formalização equilibrada.** Adiciona equações numeradas e floats `algorithm` onde o método está
  pouco formalizado, e protege cada fórmula contra má interpretação.
- **Correções de estrutura e referências cruzadas.** Conecta figuras não referenciadas, resolve
  `\ref`/`\cite` pendentes e adiciona pacotes de preâmbulo ausentes.
- **Ajuste de páginas.** Corta o corpo principal ao limite por meio de edições que preservam o
  conteúdo (fusão de parágrafos, mover um exemplo trabalhado para um apêndice).
- **Novidade e posicionamento.** Sob solicitação, reconstrói a linhagem da linha de trabalho, agrupa
  o trabalho anterior por tema, nomeia o diferenciador e dá uma lista franca dos prováveis ataques
  dos revisores.

## Duas formas de usar

1. **Revisão completa (edita seu LaTeX).** Você dá ao Claude a fonte compilável (`.tex` + `.bib` +
   arquivos de classe/estilo + `figures/`, ou um zip de exportação do Overleaf) e ele faz edições
   cirúrgicas, compensa qualquer comprimento adicionado e devolve um zip de upload reconstruído.
   **Ele nunca inventa números de resultados.**
2. **Somente revisão (sem edições).** O Claude lê o rascunho e devolve *apenas panorama e
   comentários*: tema/escopo da pesquisa, contribuição central, novidade franca, problemas e
   sugestões concretas — sem alterar nenhum arquivo. Esse é o modo que o
   [aplicativo web hospedado](https://zoelin.dev/tools/paper-revision) expõe.

Ambos os modos funcionam em **qualquer idioma**: faça suas perguntas e receba o panorama, os
comentários e os resumos em chinês, espanhol, árabe ou qualquer outro idioma. Apenas a *conversa*
segue seu idioma; o manuscrito permanece no idioma exigido pelo evento (geralmente inglês).

Veja [`docs/USER-GUIDE.md`](../USER-GUIDE.md) para o passo a passo detalhado.

## Início rápido

Usando o skill com o Claude (por exemplo, Claude Code) em um repositório que contém este skill:

1. Aponte o Claude para a fonte do seu artigo (ou o exemplo abaixo) e nomeie seu **evento-alvo**.
2. Peça a ele *"revise isto para <evento>"* (modo completo) ou *"apenas revise este rascunho e me
   dê um panorama e comentários, não edite nada"* (modo somente revisão).
3. O Claude primeiro devolve um **briefing de orientação** e uma **lista de achados** e então — no
   modo completo — faz as edições e diz exatamente o que recompilar e verificar.

Experimente nos rascunhos sintéticos incluídos (ambos **fictícios**: autores, conjuntos de dados e
números inventados, sem PI; cada um com um gabarito `EXPECTED-FINDINGS.md`):

```
examples/student-draft/main.tex   abaixo do limite; problemas clássicos de rascunho estudantil
examples/over-length/main.tex     acima do limite de páginas; exercita o corte
```

- **`student-draft`** está semeado com andaime RQ1–RQ3, títulos genéricos, trabalho relacionado
  cronológico, zero equações e um `\ref` para uma figura sem `\label`. Está *abaixo* do limite, então
  exercita adicionar seções gratuitas.
- **`over-length`** mira o limite de artigo curto da ACL (4 páginas) mas ocupa ~5-6 páginas; é de
  resto polido, então exercita o **passo de corte** (fundir parágrafos redundantes, mover um exemplo
  trabalhado para um apêndice).

## Uma nota sobre as entradas

O modo de revisão completa precisa da **fonte LaTeX**, não de um PDF: você não pode fazer edições
cirúrgicas em um PDF, e reconstruir a fonte arrisca a formatação e os números do autor. O modo
somente revisão pode trabalhar a partir de um PDF porque apenas lê e comenta.
