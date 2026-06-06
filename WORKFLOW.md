# WORKFLOW.md — Auditoria de Consistência do Ecossistema

**Versão:** v1.1 — 2026-06-06
**Status:** ativo
**Responsável:** Victor Leonardo Arimatea Queiroz — Diretor de Transformação Digital
**Repositório:** wkf-auditoria-consistencia (W05)

---

## Seção 1 — Identificação

| Campo | Valor |
|---|---|
| Nome do processo | Auditoria de Consistência do Ecossistema |
| ID | W05 |
| Versão | v1.0 |
| Status | ativo |
| Data de criação | 2026-06-05 |
| Responsável | DTD/SETIS/SES-DF |
| Skill associada | Nenhuma — processo independente da S04 por design |
| Repositório âncora | hub-fonte (sumario.md como fonte de verdade) |
| Frequência recomendada | Abertura de sessão + sessão semanal de roadmapping (W04) |

---

## Seção 2 — Missão e contexto organizacional

### Missão

Verificar sistematicamente se o estado declarado do ecossistema corresponde
ao estado real — percorrendo o grafo completo de dependências em 5 camadas,
classificando cada divergência por severidade e reportando ao mantenedor
antes de qualquer operação.

### Por que este processo existe

O ecossistema DTD/SETIS acumulou 13 erros registrados entre 2026-06-01 e
2026-06-05, a maioria classificados como SEV2 ou SEV3 — dados incorretos
em arquivos de referência central e inconsistências detectáveis apenas por
auditoria. O padrão se repetia porque a única camada de verificação era a
própria S04, verificando o que ela mesma executava.

O exercício de engenharia reversa de 2026-06-05 identificou isso como
**GAP 1 — Ausência de verificação independente**: toda verificação era
feita pelo mesmo agente que executou, no mesmo contexto, imediatamente
após. O viés de confirmação estrutural tornava invisíveis exatamente os
erros mais comuns — omissões e propagações incompletas de versão.

O W05 é a resposta estrutural a esse gap: um processo separado, com lógica
própria, acionado antes de qualquer operação, sem acesso a token e sem
capacidade de alterar repositórios. Sua única saída é um relatório.

### Princípio de design — Separação executor/auditor

O W05 **nunca** solicita token. **Nunca** altera arquivos. **Nunca** é
executado pelo mesmo agente no mesmo fluxo que vai corrigir as divergências.

Se o W05 encontrar divergências, o relatório é apresentado ao mantenedor.
A decisão de corrigir e a execução da correção são responsabilidade de
uma nova operação da S04, com plano e aprovação explícita.

Esta separação é deliberada, permanente e inviolável.

### Quem pode acionar

- O Claude no início de qualquer sessão com o ecossistema
- O Diretor de Transformação Digital sob demanda
- O W04 (workflow de roadmapping) como pré-condição da sessão semanal

---

## Seção 3 — Estado final esperado

Uma auditoria bem-sucedida satisfaz todos os critérios abaixo:

- [ ] sumario.md lido como fonte de verdade — lista completa de repositórios ativos extraída
- [ ] Camada 1 (versões) verificada para todos os repositórios ativos
- [ ] Camada 2 (arquivos obrigatórios) verificada por tipo de repositório
- [ ] Camada 3 (hub-entrada) verificada contra sumario.md
- [ ] Camada 4 (backlogs) verificada para repositórios com versão recente
- [ ] Camada 5 (glossário) verificada nos arquivos operacionais centrais
- [ ] Relatório de auditoria produzido com todas as divergências classificadas por SEV
- [ ] Contagem por severidade apresentada ao mantenedor
- [ ] Log de execução depositado em `execucoes/AAAA-MM-DD-[contexto].md`
- [ ] Nenhuma alteração feita nos repositórios auditados

---

## Seção 4 — Etapas do processo

| # | Etapa | Executor | Tipo | Entrada | Saída |
|---|---|---|---|---|---|
| 0 | Leitura da fonte de verdade | Claude | Automático | sumario.md via web_fetch | Lista de todos os repositórios ativos com versões declaradas |
| 1 | Auditoria Camada 1 — Versões | Claude | Automático | Lista do sumario.md + cabeçalhos dos arquivos principais | Lista de divergências de versão |
| 2 | Auditoria Camada 2 — Arquivos obrigatórios | Claude | Automático | Lista do sumario.md + listagem de arquivos de cada repositório | Lista de arquivos ausentes |
| 3 | Auditoria Camada 3 — hub-entrada | Claude | Automático | sumario.md + README.md e ROADMAP.md do hub-entrada | Lista de repositórios ausentes no hub-entrada |
| 4 | Auditoria Camada 4 — Backlogs | Claude | Automático | sumario.md + backlog-versoes.md de cada repositório | Lista de versões sem entrada de backlog |
| 5 | Auditoria Camada 5 — Glossário | Claude | Semi-automático | Arquivos operacionais centrais + GLOSSARIO.md | Lista de termos candidatos não definidos |
| 6 | Produção do relatório | Claude | Automático | Resultados das Camadas 1–5 | Relatório de auditoria classificado por SEV |
| 7 | Apresentação ao mantenedor | Claude | Manual | Relatório produzido | Decisão do mantenedor: corrigir agora, agendar ou aceitar |
| 8 | Log de execução | Claude | Automático (com token) | Relatório aprovado | Arquivo em `execucoes/` |

---

### Etapa 0 — Leitura da fonte de verdade

**Instrução:**
Ler o `sumario.md` do hub-fonte via web_fetch:
```
GET https://raw.githubusercontent.com/victorarimatea/hub-fonte/main/sumario.md
```

> ⚠️ **ALERTA DE CACHE — leitura da S04 obrigatoriamente via API:**
> O `raw.githubusercontent.com` possui cache de CDN com delay indeterminado.
> Leituras da `skl-github-orquestracao/SKILL.md` via raw podem retornar versões
> desatualizadas sem aviso. Para garantir leitura da versão atual da S04,
> use **sempre** a API GitHub:
> `GET https://api.github.com/repos/victorarimatea/skl-github-orquestracao/contents/SKILL.md`
> (decodificar campo `content` de base64). Este protocolo se aplica a qualquer
> arquivo de instrução crítico lido no início de sessão.

Extrair e registrar internamente:
- Lista completa de repositórios ativos com ID, nome, versão declarada e tipo (M/S/D/W/A/P)
- Repositórios planejados (não auditar — apenas registrar existência)

**Fonte de verdade:** o `sumario.md` é o árbitro. Se um repositório não está
no `sumario.md`, não existe para fins de auditoria.

---

### Etapa 1 — Auditoria Camada 1: Versões

**O que verificar:** para cada repositório ativo, a versão declarada no
`sumario.md` bate com o cabeçalho do arquivo principal do repositório.

**Arquivo principal por tipo:**

| Tipo | Arquivo principal | Campo a verificar |
|---|---|---|
| M (Matriz) | Arquivo alterado mais recentemente | `**Versão:**` |
| S (Skill) | `SKILL.md` | `**Versão:**` |
| D (Documento) | `README.md` | `**Versão:**` |
| W (Workflow) | `WORKFLOW.md` | `**Versão:**` |
| A (Agenda) | `README.md` | `**Versão:**` |
| P (Projeto) | `README.md` | `**Versão:**` |

**Também verificar:** a versão no `CONTEXTO.md` bate com o `sumario.md`
para cada repositório listado em ambos.

**Severidade de divergência:** SEV2

---

### Etapa 2 — Auditoria Camada 2: Arquivos obrigatórios

**O que verificar:** para cada repositório ativo, os arquivos obrigatórios
definidos na `nomenclatura.md` existem na raiz.

**Arquivos obrigatórios por tipo:**

| Tipo | Obrigatórios |
|---|---|
| Todos | `README.md`, `INDICE.md`, `backlog-versoes.md` |
| S (Skill) | + `SKILL.md` |
| W (Workflow) | + `WORKFLOW.md`, pasta `execucoes/` |
| P (Projeto) | + `stakeholders.md` |
| M01 | + `sumario.md`, `nomenclatura.md`, `CONTEXTO.md`, `GLOSSARIO.md`, `ONBOARDING.md` |

**Como verificar:** listar arquivos na raiz via API GitHub:
```
GET https://api.github.com/repos/victorarimatea/{repo}/contents/
```

**Severidade de divergência:** SEV3

---

### Etapa 3 — Auditoria Camada 3: hub-entrada

**O que verificar:** todo repositório listado como ativo no `sumario.md`
aparece nos dois arquivos do hub-entrada.

**Verificações:**
1. `README.md` do hub-entrada — repositório presente na tabela da seção correta?
2. `ROADMAP.md` do hub-entrada — repositório marcado como ✅ concluído?

**Como verificar:** ler os dois arquivos e cruzar com a lista do sumario.md.

**Severidade de divergência:** SEV2

---

### Etapa 4 — Auditoria Camada 4: Backlogs

**O que verificar:** repositórios com versão ≥ v1.1 têm entrada no
`backlog-versoes.md` correspondente à versão atual declarada.

**Como verificar:** para cada repositório com versão > v1.0, ler o
`backlog-versoes.md` e verificar se existe entrada com o número de versão
atual. Aceitar os dois padrões de cabeçalho: `## vX.Y` e `### vX.Y`.

**Escopo:** focar nos repositórios alterados nas últimas 2 semanas
(versão mais recente) para não tornar a auditoria excessivamente longa.

**Severidade de divergência:** SEV3

---

### Etapa 5 — Auditoria Camada 5: Glossário

**O que verificar:** os arquivos operacionais centrais não contêm termos
técnicos relevantes ausentes do `GLOSSARIO.md`.

**Arquivos a varrer:** `sumario.md`, `CONTEXTO.md`, `nomenclatura.md`,
`SKILL.md` da S04 (cabeçalho e seções de regras).

**Como verificar:** leitura humana/semi-automática — identificar substantivos
técnicos específicos do ecossistema e verificar presença no `GLOSSARIO.md`.

**Nota:** esta camada é semi-automática por natureza — não há forma de
automatizar completamente a identificação de termos relevantes sem falsos
positivos. O agente faz a varredura e apresenta candidatos ao mantenedor.

**Severidade de divergência:** SEV4

---

### Etapa 6 — Produção do relatório

**Formato obrigatório do relatório de auditoria:**

```
RELATÓRIO DE AUDITORIA — ECOSSISTEMA DTD/SETIS
Data: AAAA-MM-DD
Acionado por: [abertura de sessão | sob demanda | pré-W04]
Repositórios auditados: N

RESUMO
  SEV1: N divergências
  SEV2: N divergências
  SEV3: N divergências
  SEV4: N divergências
  Total: N divergências

[Se zero divergências:]
  ✅ ECOSSISTEMA CONSISTENTE — nenhuma divergência encontrada.

[Se divergências:]
DIVERGÊNCIAS ENCONTRADAS

SEV2 — Alto
  · [Repositório] — [arquivo] — [descrição da divergência]
  · ...

SEV3 — Médio
  · [Repositório] — [arquivo] — [descrição da divergência]
  · ...

SEV4 — Baixo
  · ...

RECOMENDAÇÃO
  [Ação sugerida para cada divergência ou grupo de divergências]
  [Estimativa de esforço: quantos arquivos afetados, quais repositórios]
```

---

### Etapa 7 — Apresentação ao mantenedor e decisão

Apresentar o relatório e aguardar uma das três decisões:

- **Corrigir agora:** iniciar operação S04 com plano de correção
- **Agendar:** registrar divergências na staging.md (Seção A) e corrigir na próxima sessão disponível
- **Aceitar:** divergência conhecida e tolerada — registrar como aceita no log de execução

---

### Etapa 8 — Log de execução (quando com token ativo)

**Localização:** `execucoes/AAAA-MM-DD-[contexto].md`

**Conteúdo obrigatório:**
- Data, hora e gatilho de acionamento
- Número de repositórios auditados
- Resumo de divergências por camada e severidade
- Decisão do mantenedor para cada divergência
- Status: `Completo` / `Parcial` / `Interrompido`

---

## Seção 5 — Skills e subprocessos consumidos

| Instrumento | Papel no W05 |
|---|---|
| S04 `skl-github-orquestracao` | Não consumida durante a auditoria — acionada separadamente para corrigir divergências encontradas |
| `hub-fonte/sumario.md` | Fonte de verdade primária — base de toda auditoria |
| `hub-fonte/nomenclatura.md` | Define arquivos obrigatórios por tipo de repositório |
| `hub-fonte/GLOSSARIO.md` | Referência para Camada 5 |
| `hub-entrada/README.md` | Verificado na Camada 3 |
| `hub-entrada/ROADMAP.md` | Verificado na Camada 3 |

---

## Seção 6 — Histórico de problemas

*Nenhum problema registrado — workflow em versão inicial.*

---

## Seção 7 — Roadmap de automação

| Etapa | Estado atual | Condição para automação |
|---|---|---|
| 0 — Leitura sumario.md | Manual (web_fetch) | Já automatizável |
| 1 — Versões | Semi-automático | Automatizável via script Python com API GitHub |
| 2 — Arquivos obrigatórios | Semi-automático | Automatizável via listagem da API |
| 3 — hub-entrada | Semi-automático | Automatizável via comparação de listas |
| 4 — Backlogs | Semi-automático | Automatizável com regex de versão |
| 5 — Glossário | Manual | Requer NLP ou curadoria humana — automação parcial possível |
| 6 — Relatório | Manual | Automatizável após Etapas 1–4 automatizadas |
| 8 — Log | Manual (requer token) | Automatizável em sessões autenticadas |

**Visão de médio prazo:** script Python completo que executa Camadas 1–4
automaticamente e produz o relatório sem intervenção humana, acionável
no início de qualquer sessão com uma única instrução.

---

## Seção 8 — Referências e dependências

| Referência | Relevância |
|---|---|
| `hub-fonte/sumario.md` | Fonte de verdade primária — define o escopo da auditoria |
| `hub-fonte/nomenclatura.md` | Define arquivos obrigatórios por tipo (Camada 2) |
| `hub-aprendizagem/capitulos/cap-01-engenharia-reversa.md` | Origem intelectual do W05 — diagnóstico que motivou sua criação |
| S04 `skl-github-orquestracao` | Instrumento de correção após auditoria — nunca substitui o W05 |
| Escala SEV1–SEV4 (ITIL/ISO 20000) | Padrão de classificação de divergências |
| Defense in Depth (SRE/NIST) | Princípio arquitetural que justifica a separação executor/auditor |
