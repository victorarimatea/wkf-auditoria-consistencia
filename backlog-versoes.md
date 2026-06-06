# Backlog de Versões — wkf-auditoria-consistencia

## v1.2 — 2026-06-06

**Tipo de alteração:** Melhoria
**Autorizado por:** victorarimatea
**Exposição de motivos:** A Camada 1 do W05 estava gerando falsos positivos
ao comparar versões entre documentos de um mesmo repositório. O princípio
correto é que cada documento versiona a si mesmo — versões diferentes entre
CONTEXTO.md, sumario.md e README.md do mesmo repositório não são inconsistência.
O W05 deve verificar rastreabilidade (versão declarada + entrada no backlog),
não uniformidade de versões. A única exceção é o README.md, que deve declarar
a versão do repositório como unidade alinhada ao sumario.md.

**Causa raiz:** critério de verificação mal especificado na Camada 1 desde
a criação do W05. Identificado após auditoria de fechamento de sessão em
2026-06-06 retornar falso positivo sobre hub-fonte.

### Alterações realizadas
- `WORKFLOW.md` v1.1 → v1.2: Camada 1 atualizada com princípio de
  versionamento independente e critério correto de verificação

---

## v1.1 — 2026-06-06

**Tipo de alteração:** Melhoria
**Autorizado por:** victorarimatea
**Status do workflow:** ativo
**Exposição de motivos:** Adição de alerta explícito na Etapa 0 sobre o risco
de cache do raw.githubusercontent.com ao ler arquivos de instrução críticos
(especialmente a S04). O problema foi identificado na auditoria de abertura de
sessão de 2026-06-06: a leitura da S04 via raw retornou v2.1 enquanto a versão
real no repositório era v2.7. A causa raiz é o delay de CDN do raw, que pode
ser indeterminado. A solução é protocolar a leitura via API GitHub para arquivos
de instrução críticos.

### Alterações realizadas
- Etapa 0 do `WORKFLOW.md`: adição de bloco de alerta ⚠️ especificando que
  a S04 e outros arquivos de instrução críticos devem ser lidos via API GitHub,
  não via raw.githubusercontent.com

---

## v1.0 — 2026-06-05

**Tipo de alteração:** Criação
**Autorizado por:** Victor Leonardo Arimatea Queiroz — Diretor de Transformação Digital
**Status do workflow:** ativo
**Execuções afetadas:** nenhuma
**Skills afetadas:** nenhuma

**Exposição de motivos:** O ecossistema DTD/SETIS não possuía mecanismo de
auditoria independente. A S04 verificava o que ela mesma executava —
vulnerabilidade estrutural identificada como GAP 1 no exercício de engenharia
reversa de 2026-06-05. O W05 nasce como processo genuinamente separado:
não executa operações, não usa token, não altera repositórios. Apenas audita,
detecta, classifica por severidade SEV1–SEV4 e reporta ao mantenedor.
O design de separação executor/auditor é deliberado e permanente.

### Conteúdo inicial (v1.0)
- `README.md` — apresentação e contexto
- `INDICE.md` — mapa de navegação
- `WORKFLOW.md` — processo completo em 8 seções, 5 camadas de auditoria,
  lógica de verificação por tipo de repositório, formato de relatório
- `execucoes/` — pasta para logs de auditoria

