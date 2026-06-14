# wkf-auditoria-consistencia

**Tipo:** Workflow (W05)
**Versão:** v1.3 — 2026-06-14
**Status:** ativo
**Repositório:** https://github.com/victorarimatea/wkf-auditoria-consistencia
**Mantenedor:** victorarimatea
**Skill associada:** nenhuma — processo independente da S04 por design

> Workflow de Auditoria de Consistência do ecossistema DTD/SETIS.
> Verifica se o estado declarado corresponde ao estado real.
> Não executa operações — detecta, classifica e reporta.

---

## O que é este workflow

O W05 responde a uma única pergunta: **o estado declarado do ecossistema
corresponde ao estado real?**

Ele percorre o grafo completo de dependências do ecossistema em 5 camadas —
versões, arquivos obrigatórios, consistência do hub-entrada, backlogs e
glossário — e produz um relatório de auditoria com todas as divergências
classificadas por severidade (SEV1–SEV4).

## Por que é independente da S04

A S04 executa operações e verifica o que ela mesma fez. O W05 verifica
independentemente — sem o viés de confirmação do executor. Este é o
princípio de Separação de Responsabilidades identificado no exercício
de engenharia reversa de 2026-06-05 como GAP 1 estrutural do ecossistema.

## Quando acionar

- **Abertura de sessão** — antes de qualquer missão, como primeira ação
- **Sob demanda** — quando houver suspeita de drift acumulado
- **Sessão semanal de roadmapping** — como pré-condição do W04

## Resultado esperado

Um relatório de auditoria com:
- Lista de divergências encontradas, classificadas por SEV
- Contagem por severidade
- Recomendação de ação para cada divergência

Se nenhuma divergência for encontrada: ecossistema consistente confirmado.

---

## Estrutura do repositório

```
wkf-auditoria-consistencia/
├── README.md              — este arquivo
├── INDICE.md              — mapa de arquivos
├── WORKFLOW.md            — processo completo em 8 seções
├── backlog-versoes.md     — histórico de versões
└── execucoes/             — logs de cada auditoria executada
```

---

*Mantido por Victor Leonardo Arimatea Queiroz*
*Diretor de Transformação Digital — DTD/SETIS/SES-DF*
