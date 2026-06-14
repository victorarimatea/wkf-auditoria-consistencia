## v1.3 — 2026-06-14

**Tipo de alteração:** Melhoria (OP-W)
**Autorizado por:** Victor Leonardo Arimatea Queiroz
**Status do workflow:** ativo
**Execuções afetadas:** nenhuma
**Skills afetadas:** nenhuma (alinha o W05 à doutrina já canônica no PROTOCOLO-SESSAO.md)

**Exposição de motivos:** Propagação da doutrina de dois tokens ao W05,
concluindo o item I1 do ROADMAP e fechando o SEV3 de acesso herdado. O W05
ainda carregava o modelo de acesso anterior ("API vs raw" + alerta de cache)
e uma contradição interna: a Seção 2 declarava "sem acesso a token" enquanto
a Etapa 8 previa log "com token ativo". Sob a doutrina, o auditor opera
exclusivamente com o token de leitura ampla e nunca recebe o token de edição.

### Alterações realizadas
- `WORKFLOW.md` v1.2 -> v1.3:
  - Seção 2 (Princípio de design): auditor opera só sob token de leitura;
    nunca recebe token de edição; correção é responsabilidade da sessão
    executora. "sem acesso a token" -> "sem token de edição"
  - Etapa 0: instrução e box reescritos de "API/raw + cache" para a doutrina
    de dois tokens (token de leitura ampla; raw aposentado como canal de sessão)
  - Etapa 8 (decisão do mantenedor, Opção A, 2026-06-14): log depositado pela
    sessão executora (S04, token de edição), nunca pelo auditor; depósito
    autônomo opcional quando a auditoria é avulsa
  - Seção 4 (tabela) e Seção 7: web_fetch -> API com token de leitura
  - Cabeçalho e Seção 1 (tabela): v1.2 -> v1.3

---

## v1.2 — 2026-06-13 (correção pós-reauditoria)

**Tipo de alteração:** Correção (OP-E) — mesma versão v1.2
**Autorizado por:** Victor Leonardo Arimatea Queiroz
**Detectado por:** reauditoria W05 independente de 2026-06-13

**Exposição de motivos:** A reauditoria de fechamento (2026-06-13) — executada
em chat separado, sem token — detectou uma divergência interna no próprio W05:
o cabeçalho do WORKFLOW.md declarava v1.2, mas a tabela da Seção 1 ainda
registrava v1.0 (versão de criação, nunca propagada). Mesma classe das
correções D3 (W03) e da correção análoga do W06. Divergência pré-existente,
não introduzida por esta sessão — exposta pela varredura geral. Nota: o
instrumento de auditoria detectou uma inconsistência em si mesmo, o que valida
a independência do processo.

### Alterações realizadas
- `WORKFLOW.md`: tabela da Seção 1, campo Versão v1.0 → v1.2 (alinhamento ao
  cabeçalho, que já estava correto)

---

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

