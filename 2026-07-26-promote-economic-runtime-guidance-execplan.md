# Promover orientação econômica de runtime

Este ExecPlan é um documento vivo. Manter `Progress`, `Decisions`, `Risks and Mitigations` e `Lessons Learned` atualizados durante toda a execução.

## Purpose / Big Picture

Tornar a política econômica normativa de avaliações visível para o agente que carrega `develop-skill-with-evals` e auditável no JSON produzido por `plan`. Um usuário poderá observar uma recomendação conservadora por papel, comparar essa recomendação ao runtime declarado e ainda preservar integralmente sua escolha explícita.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy bypassing instructions.

## Scope

Incluído:

* orientação obrigatória e concisa em `develop-skill-with-evals/SKILL.md`;
* atualização coerente de `develop-skill-with-evals/agents/openai.yaml`;
* campo público obrigatório `economic_runtime` em planos;
* recomendação Luna `medium` somente para casos semânticos scoped, todos com `oracle.commands` completo e judge desativado;
* recomendação Terra `medium` somente quando judge for necessário;
* seleção manual de executor para mudanças cross cutting, judge habilitado ou oracle incompleto;
* warnings informativos para divergências sem alterar blockers ou comandos;
* inclusão da orientação no `evaluation_fingerprint` e na verificação de snapshots;
* schema, contrato, documentação, testes determinísticos e self evals;
* promoção model backed e fresh agent somente após aprovação explícita.
* arquivamento do relatório canônico da promoção em qualquer resultado executado, inclusive bloqueante;
* reconstrução e validação do manifest e das projeções Markdown após incorporar somente o diretório novo da operação.

Excluído:

* alteração de defaults, `config.toml`, argumentos CLI ou `DEFAULT_APPROVED_MODEL_SESSIONS`;
* substituição do runtime fornecido pelo usuário;
* alteração de `runtime_fingerprint` ou `eval-result.schema.json`;
* reescrita de relatórios históricos;
* alteração da comparação histórica `pilot-v2`;
* versionamento de respostas brutas, artifacts, ledger ou workspaces;
* commit, push ou publicação.

## Definitions

`Economic runtime` é a recomendação informativa calculada pelo plano para executor e judge. `Oracle completo` significa que todo caso selecionado declara ao menos um comando em `oracle.commands`, permitindo verificar o contrato sem judge semântico. `Runtime explícito completo` significa que modelo e reasoning effort do papel foram declarados integralmente pela CLI. `Mismatch econômico` é uma divergência entre uma recomendação disponível e o runtime explícito; ele produz warning, nunca blocker. `Baseline` é a fonte canônica congelada antes da mudança. `Candidate` é a cópia isolada onde toda implementação e avaliação ocorre antes da promoção.

## Existing Context

A fonte canônica está em `/home/renanfranca/.codex/skills`, no commit `8da0b50e2475a99296eb10a7ec7d32525eb14d8e`. O runner atual já planeja sessões, resolve runtime explícito e gera fingerprints, mas não expõe orientação econômica estruturada. `EVALUATIONS.md` generaliza Luna para qualquer mudança scoped, inclusive exemplos com judge, o que contradiz a política normativa estrita do dossiê.

A raiz isolada desta execução é `/tmp/promote-economic-runtime.acJDsi`:

* baseline imutável: `/tmp/promote-economic-runtime.acJDsi/baseline-root`;
* candidate editável: `/tmp/promote-economic-runtime.acJDsi/candidate-root`;
* skill baseline: `/tmp/promote-economic-runtime.acJDsi/baseline-root/develop-skill-with-evals`;
* skill candidate: `/tmp/promote-economic-runtime.acJDsi/candidate-root/develop-skill-with-evals`.

As duas árvores vieram de `git archive HEAD`. O baseline foi tornado recursivamente não gravável. A fonte canônica permanecerá sem alterações rastreadas até promoção e fresh agent aprovados.

## Desired End State

Todo plano válido contém `economic_runtime` com `policy_version: 1`, um dos modos `zero-session`, `scoped-complete-oracle` ou `manual-selection`, recomendações opcionais por papel, estado de correspondência com runtime explícito e razões auditáveis.

Planos static e deterministic recomendam zero sessão. Um plano scoped recebe Luna `medium` somente quando todos os casos selecionados são semânticos, têm `oracle.commands` e desativam judge. Qualquer judge necessário recebe Terra `medium`, mas força seleção manual do executor. Cross cutting e oracle incompleto também exigem seleção manual do executor. Divergências geram warnings sem novos blockers, sem mudar comandos e sem sobrescrever valores explícitos.

## Milestones

### Milestone 1: Congelar fontes, escrever contratos e demonstrar RED

#### Goal

Criar isolamento auditável, o teste determinístico novo e os self evals antes da implementação.

#### Changes

* Criar este ExecPlan.
* Criar `scripts/tests/test_economic_runtime_guidance.py` no candidate.
* Expandir `cost-efficient-runtime-contract`.
* Adicionar o caso semântico `economic-runtime-guidance` com oracle oculto e judge desativado.

#### Validation

* Command: executar o teste novo contra o módulo do baseline imutável.
* Expected result: falha porque o baseline não produz `economic_runtime`.

#### Acceptance Criteria

* O RED é mecânico e não consome sessões.
* Baseline e candidate têm fingerprints e papéis distintos.

### Milestone 2: Implementar o contrato econômico

#### Goal

Produzir a orientação econômica sem alterar runtime, defaults ou aprovação.

#### Changes

* Editar `scripts/run_skill_evals.py`.
* Editar `references/eval-plan.schema.json` e `references/eval-contract.md`.
* Editar `SKILL.md`, `agents/openai.yaml`, `EVALUATIONS.md` e `CODEX_CLI.md`.

#### Validation

* Command: `PYTHONDONTWRITEBYTECODE=1 python3 -m unittest develop-skill-with-evals.scripts.tests.test_economic_runtime_guidance -v`
* Expected result: todos os contratos econômicos passam.

#### Acceptance Criteria

* Schema público aceita os planos gerados.
* Mismatch adiciona warning e preserva runtime e comandos.
* `evaluation_fingerprint` muda quando a orientação muda.
* Snapshot verifica `economic_runtime`.

### Milestone 3: Provar GREEN determinístico estável

#### Goal

Executar três gates focados sem modelo e depois toda a suíte determinística.

#### Changes

* Corrigir somente defeitos observados pelos contratos.
* Manter relatórios e artefatos em `/tmp`.

#### Validation

* Command: executar três vezes os testes focados e o caso determinístico afetado.
* Command: `PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover -s develop-skill-with-evals/scripts/tests -v`
* Command: `PYTHONDONTWRITEBYTECODE=1 python3 .system/skill-creator/scripts/quick_validate.py ./develop-skill-with-evals`
* Expected result: três GREEN focados estáveis e suíte completa verde.

#### Acceptance Criteria

* Nenhuma sessão real é consumida.
* Nenhuma divergência ou mutação de baseline ocorre.

### Milestone 4: Planejar e executar a promoção aprovada

#### Goal

Executar uma única promoção cross cutting com Sol `medium` executor e Terra `medium` judge.

#### Changes

* Gerar plano side effect free para `economic-runtime-guidance` e `cost-efficient-runtime-contract`.
* Usar 16 sessões, o total emitido, como teto por operação e cumulativo.
* Manter ledger, workspaces e logs brutos sob `/tmp`.
* Usar o arquivamento automático do candidate com `evaluation-reports/pricing/2026-07-26.json`.
* Pedir aprovação explícita antes de executar `validate-change`.

#### Validation

* Command: comando exato emitido pelo plano com ledger e artefatos diagnósticos em `/tmp`.
* Expected result: o resultado é arquivado; `PASS` permite avançar e qualquer outro status encerra a campanha sem retry.

#### Acceptance Criteria

* O fingerprint executado corresponde ao plano aprovado.
* Somente o novo diretório da operação é copiado para `evaluation-reports/develop-skill-with-evals/operations/`.
* `manifest.json`, `manifest.md` e projeções Markdown são reconstruídos e validados.
* Não há retry automático.

### Milestone 5: Fresh agent e promoção canônica

#### Goal

Validar o comportamento com contexto fresco e só então aplicar o patch revisado à fonte canônica.

#### Changes

* Pedir aprovação para um fresh agent separado.
* Fornecer apenas candidate path e tarefa realista, sem resposta esperada.
* Aplicar à fonte canônica somente os arquivos aprovados.

#### Validation

* Command: suíte completa, quick validate, validação dos schemas, `git diff --check` e `git status --short`.
* Expected result: todos os checks passam e apenas arquivos previstos aparecem.

#### Acceptance Criteria

* Promotion gate e fresh agent passam.
* Nenhum arquivo é staged, commitado, enviado ou publicado.

## Progress

* [x] Milestone 1 iniciado.
* [x] ExecPlan criado.
* [x] Baseline e candidate isolados.
* [x] RED determinístico demonstrado.
* [x] Milestone 1 concluído.
* [x] Milestone 2 iniciado.
* [x] Milestone 2 concluído.
* [x] Milestone 3 iniciado.
* [x] Milestone 3 concluído.
* [x] Milestone 4 iniciado.
* [x] Promoção explicitamente aprovada.
* [x] Relatório canônico da promoção arquivado no repositório.
* [x] Promoção encerrada sem retry após resultado bloqueante.
* [x] Archive reconstruído e validado sem alterar `pilot-v2`.
* [x] Milestone 4 concluído.
* [x] Retry solicitado explicitamente após reinício do Codex com sandbox `workspace-write`.
* [x] Teto de 16 sessões adicionais e 17 cumulativas explicitamente aprovado.
* [x] Segunda promoção concluída e arquivada.
* [x] Correção da fronteira de permissões iniciada após diagnóstico de sandbox externo.
* [x] Contrato determinístico de execução aninhada demonstrado RED e três GREEN.
* [x] Terceira promoção planejada com teto de 16 sessões adicionais e 18 cumulativas.
* [x] Terceira promoção executada uma única vez fora do sandbox externo.
* [x] Terceira promoção encerrada sem retry após `FAIL` de contrato.
* [x] Terceiro relatório canônico incorporado e archive reconstruído e validado.
* [x] Nova campanha autorizada para correção material do oracle.
* [x] Oracle corrigido e comprovado mecanicamente contra o workspace retido.
* [x] Novo plano e novo teto cumulativo aprovados.
* [ ] Quarta promoção executada uma única vez fora do sandbox externo.
* [ ] Milestone 5 iniciado.
* [ ] Fresh agent explicitamente aprovado.
* [ ] Milestone 5 concluído.

## Decisions

* Decision: Tratar a mudança como cross cutting.
  Rationale: Ela altera instruções centrais da skill, contrato público de `plan`, fingerprints e verificação de snapshots.
  Date/Author: 2026-07-26 / Codex

* Decision: Não executar diagnóstico pago.
  Rationale: O oracle novo cobre o contrato completo e o promotion gate já executa regressões antes de GREEN 2 e 3.
  Date/Author: 2026-07-26 / Codex

* Decision: Preservar escolhas explícitas mesmo quando divergem da recomendação.
  Rationale: A política aconselha e audita; a soberania do runtime informado pelo usuário é parte do contrato.
  Date/Author: 2026-07-26 / Codex

* Decision: Tratar runtime de judge herdado de um executor completamente declarado pela CLI como declaração completa.
  Rationale: O contrato existente permite herança para promoção auditável; nesse cenário o plano conhece modelo e effort efetivos e pode reportar match ou mismatch em vez de `null`.
  Date/Author: 2026-07-26 / Codex

* Decision: Fixar o teto da promoção em 16 sessões, exatamente o total do plano final.
  Rationale: O plano calculou 13 sessões máximas de executor e 3 de judge para dois casos afetados e doze regressões.
  Date/Author: 2026-07-26 / Codex

* Decision: Arquivar qualquer resultado executado da promoção, inclusive `FAIL`, `ERROR`, `INCONCLUSIVE`, `INVALID_RED` ou `UNSTABLE`.
  Rationale: Um resultado bloqueante impede a promoção do código, mas continua sendo evidência econômica e comportamental válida da operação autorizada.
  Date/Author: 2026-07-27 / Codex

* Decision: Não alterar a comparação histórica `pilot-v2`.
  Rationale: Uma única promoção isolada não forma uma nova matriz comparativa.
  Date/Author: 2026-07-27 / Codex

* Decision: Não repetir a promoção após a falha de infraestrutura.
  Rationale: A operação aprovada encerrou com `ERROR` no primeiro executor e o contrato exige parar em qualquer resultado diferente de `PASS`; uma nova operação também excederia a aprovação cumulativa original se reservasse novamente o máximo de 16 sessões.
  Date/Author: 2026-07-27 / Codex

* Decision: Executar uma segunda promoção após nova solicitação e nova aprovação explícitas do usuário.
  Rationale: O usuário reiniciou o Codex com sandbox `workspace-write`, pediu o retry e aprovou até 16 sessões adicionais, elevando o teto cumulativo de 16 para 17. O fingerprint da avaliação e os fingerprints das fontes permaneceram idênticos.
  Date/Author: 2026-07-27 / Codex

* Decision: Corrigir a orientação, não o runner nem o ambiente de autenticação.
  Rationale: `codex doctor --json` provou que o processo externo sandboxed bloqueia `CODEX_HOME` e rede antes que o executor aninhado aplique seu próprio `workspace-write`; mover credenciais ou relaxar o sandbox interno ampliaria o risco sem corrigir a fronteira causal.
  Date/Author: 2026-07-27 / Codex

* Decision: Adicionar `nested-codex-outer-sandbox-contract` como terceiro caso afetado determinístico.
  Rationale: O contrato documental completo pode ser verificado mecanicamente, produz RED no baseline e três GREEN no candidate sem consumir sessões. Como o caso não usa executor nem judge, o máximo da promoção continua em 16 sessões.
  Date/Author: 2026-07-27 / Codex

* Decision: Exigir duas autorizações independentes para a nova promoção.
  Rationale: A aprovação de até 16 sessões adicionais e teto cumulativo 18 limita custo; a aprovação externa do comando completo concede a fronteira de shell necessária a `CODEX_HOME` e rede. Uma não implica a outra.
  Date/Author: 2026-07-27 / Codex

* Decision: Encerrar a campanha após o `FAIL` do candidate em `economic-runtime-guidance`.
  Rationale: O oracle rejeitou arquivos de harness presentes no workspace (`.eval-executor-response.json`, `.eval-executor-schema.json` e `.git`). `PASS` é o único status promovível e o contrato proíbe repetir uma promoção inalterada para buscar resultado favorável.
  Date/Author: 2026-07-27 / Codex

* Decision: Iniciar nova campanha somente após corrigir o oracle de forma restrita.
  Rationale: O usuário autorizou nova correção. A falha é mecanicamente reproduzível no workspace retido e o runner cria `.git`, `.eval-executor-schema.json`, `.eval-executor-response.json` e, após o oracle, `.eval-result.json` como infraestrutura conhecida. A correção não deve aceitar um padrão amplo `.eval-*` nem arquivos de produção inesperados.
  Date/Author: 2026-07-27 / Codex

* Decision: Fixar a nova promoção em até 16 sessões adicionais e 20 cumulativas.
  Rationale: O plano corrigido calcula o mesmo máximo de 16 sessões e o ledger registra quatro sessões consumidas nas três operações anteriores. O usuário aprovou explicitamente ambos os limites.
  Date/Author: 2026-07-27 / Codex

## Risks and Mitigations

* Risk: Confundir ausência de runtime completo com mismatch.
  Mitigation: Usar `null` quando a recomendação não existir ou quando modelo e effort explícitos não estiverem completos.

* Risk: Alterar acidentalmente `runtime_fingerprint`.
  Mitigation: Manter a função existente intacta e vincular `economic_runtime` somente ao `evaluation_fingerprint`.

* Risk: Recomendar Luna quando regressões cross cutting ampliam o contrato.
  Mitigation: Restringir o modo elegível a impacto scoped e aos casos selecionados completos.

* Risk: A recomendação alterar comandos.
  Mitigation: Construir comandos exclusivamente do runtime resolvido existente e testar preservação de Sol explícito.

* Risk: Contaminar a autoavaliação com resposta esperada.
  Mitigation: Manter critérios completos apenas no oracle oculto e usar tarefa realista no prompt público.

* Risk: Incorporar respostas brutas, ledger, workspaces ou artifacts diagnósticos ao Git.
  Mitigation: Copiar somente o diretório canônico da nova operação e manter todo o restante sob `/tmp`.

* Risk: Um rebuild alterar a comparação histórica sem nova matriz.
  Mitigation: Confirmar que `pilot-v2` permanece byte a byte inalterada após reconstruir e validar o archive.

* Risk: Iniciar o runner dentro do sandbox externo mesmo após corrigir a documentação.
  Mitigation: Executar `codex doctor --json` na mesma fronteira aprovada e exigir `overallStatus: ok`; depois solicitar aprovação externa para o comando completo do runner, preservando o sandbox interno `workspace-write`.

* Risk: Confundir aprovação de shell com autorização de custo.
  Mitigation: Obter e registrar separadamente o teto de 16 sessões adicionais e 18 cumulativas antes de solicitar a elevação do comando.

* Risk: Tornar o oracle permissivo demais ao corrigir a lista de arquivos.
  Mitigation: Permitir apenas `.git`, `.eval-executor-schema.json`, `.eval-executor-response.json` e `.eval-result.json`, além das entradas já autorizadas, e executar o oracle contra o workspace real retido.

## Validation Strategy

1. Demonstrar RED contra baseline imutável.
2. Executar testes unitários focados e contratos mecânicos no candidate.
3. Repetir os GREEN focados três vezes.
4. Executar toda a suíte determinística e validação estrutural.
5. Planejar a promoção sem sessão e obter aprovação pelo total autoritativo.
6. Executar uma única promoção e parar em qualquer status diferente de `PASS`.
7. Incorporar o relatório canônico da operação, reconstruir o archive e validar manifest, Markdown, schemas e credenciais proibidas.
8. Se o resultado for `PASS`, obter aprovação separada e executar fresh agent com contexto mínimo.
9. Promover o patch e repetir validação final na fonte canônica.

## Rollout and Recovery

O relatório canônico da operação é incorporado qualquer que seja o resultado. O rollout do código continua sendo uma cópia revisada dos arquivos do candidate para a fonte canônica somente após promotion gate e fresh agent aprovados. Como não haverá commit automático, a recuperação consiste em descartar manualmente apenas o diff desta mudança ou restaurar os arquivos a partir do commit baseline. Ledger, workspaces, logs brutos e artifacts diagnósticos ficam em `/tmp` e não entram na contribuição.

## Lessons Learned

* A documentação pública existente generaliza a recomendação scoped além da política normativa; exemplos com judge não podem continuar apresentando Luna como recomendação automática segura.
* O RED do baseline falhou exatamente nos contratos novos: ausência de `economic_runtime`, ausência do builder econômico e ausência do campo na estabilidade de snapshots. Nenhuma sessão foi iniciada.
* A suíte determinística completa passou com 90 testes. O gate integrado observou RED no baseline e três GREEN estáveis no candidate para `cost-efficient-runtime-contract`, com zero sessão.
* O oracle de `economic-runtime-guidance` passou mecanicamente com um plano Luna correspondente e um plano Sol soberano com warning de mismatch.
* O plano cross cutting final tem `evaluation_fingerprint` `09a58b3e5f12dcab510b0c29806b9df4400a0ce4e7c2e8eb887300957014ca74` e total máximo de 16 sessões.
* A promoção autorizada encerrou com `ERROR` de infraestrutura na primeira execução baseline porque `codex exec` não conseguiu inicializar o cliente interno em um filesystem somente leitura. Uma sessão de executor foi registrada, nenhum token foi reportado, nenhum judge foi executado e a reserva restante foi liberada.
* O relatório canônico `20260727T115714.421313Z-0c834a4e91a3` foi incorporado. O archive reconstruído contém 24 reports, uma comparação e o pricing `2026-07-26`; a validação completa passou e os hashes de `pilot-v2` permaneceram inalterados.
* Os 90 testes do candidate, `quick_validate.py` e os três schemas JSON passaram. A fonte canônica permaneceu sem o código candidato e seus 82 testes preexistentes também passaram.
* O resultado bloqueante impede Milestone 5: nenhum fresh agent foi solicitado e nenhum código do candidate foi promovido.
* A segunda promoção manteve todos os fingerprints, mas repetiu o mesmo `ERROR` de infraestrutura no primeiro executor: criação de aliases de `PATH` e inicialização do cliente interno falharam com filesystem somente leitura. Ela consumiu mais uma sessão sem tokens ou judge; o ledger encerrou com duas sessões consumidas e nenhuma reserva ativa.
* O segundo relatório canônico `20260727T120220.939832Z-6d9ff181411a` foi incorporado. O archive reconstruído contém 25 reports, continua com uma comparação e passou validação completa; `pilot-v2` permaneceu byte a byte inalterado.
* `codex doctor --json` isolou a causa fora do runner: dentro do sandbox da TUI, `CODEX_HOME` é somente leitura e a rede está indisponível; na fronteira externa aprovada, filesystem, HTTP e WebSocket ficam íntegros. `on-request` na TUI não eleva automaticamente subprocessos não interativos.
* O novo gate `nested-codex-outer-sandbox-contract` produziu baseline `FAIL` e três candidate `PASS` estáveis com zero sessões. Os 90 testes, `quick_validate.py`, os três schemas e os contratos documentais em `CODEX_CLI.md` e `EVALUATIONS.md` passaram.
* O novo plano cross cutting seleciona os três casos afetados, mantém doze regressões e calcula 13 sessões de executor mais 3 de judge, total máximo 16. O `evaluation_fingerprint` novo é `33247a73f9f9a15e2955e83f8b4167904d73364b8d4874aa8e3b07c31c81db21`.
* O preflight externo retornou `overallStatus: ok` e a terceira promoção ultrapassou o antigo ponto de falha de infraestrutura. Os três casos produziram RED válido no baseline.
* A primeira repetição candidate de `economic-runtime-guidance` terminou `FAIL`: o executor criou e validou os dois planos, mas o oracle tratou `.eval-executor-response.json`, `.eval-executor-schema.json` e `.git` como arquivos inesperados. A operação consumiu duas sessões de executor, nenhum judge, e o ledger passou de 2 para 4 sessões consumidas.
* O relatório `20260727T121835.379099Z-1076d0881b03` foi incorporado. O archive contém 26 relatórios e uma comparação, passou a validação completa, e os hashes de ambos os arquivos de `pilot-v2` permaneceram inalterados.
* O oracle corrigido passou no workspace real retido e os 90 testes, `quick_validate.py` e três schemas continuaram verdes. O novo plano mantém máximo de 16 sessões, parte de 4 consumidas, projeta 20 cumulativas e tem `evaluation_fingerprint` `e1e8a6ad25c8768c26906163eccab26645560a1237b722ed58ecfb79da97a15e`.
