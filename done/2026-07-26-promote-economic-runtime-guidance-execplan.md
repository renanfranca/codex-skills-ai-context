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
* alinhamento obrigatório entre prompt público e oracle oculto;
* equivalência lexical controlada quando a redação pode variar;
* novo caso determinístico `self-evolution-oracle-contract`;
* ausência explícita de judge semântico para o lembrete de redação.

Excluído:

* alteração de defaults, `config.toml`, argumentos CLI ou `DEFAULT_APPROVED_MODEL_SESSIONS`;
* alteração do parser, schemas, defaults ou testes para fazer `plan` aceitar autorização operacional prospectiva;
* substituição do runtime fornecido pelo usuário;
* alteração de `runtime_fingerprint` ou `eval-result.schema.json`;
* reescrita de relatórios históricos;
* alteração da comparação histórica `pilot-v2`;
* versionamento de respostas brutas, artifacts, ledger ou workspaces;
* commit, push ou publicação.

## Definitions

`Economic runtime` é a recomendação informativa calculada pelo plano para executor e judge. `Oracle completo` significa que todo caso selecionado declara ao menos um comando em `oracle.commands`, permitindo verificar o contrato sem judge semântico. `Runtime explícito completo` significa que modelo e reasoning effort do papel foram declarados integralmente pela CLI. `Mismatch econômico` é uma divergência entre uma recomendação disponível e o runtime explícito; ele produz warning, nunca blocker. `Baseline` é a fonte canônica congelada antes da mudança. `Candidate` é a cópia isolada onde toda implementação e avaliação ocorre antes da promoção. `Equivalência lexical controlada` é a aceitação mecânica de variações textuais que preservam todos os conceitos obrigatórios, sem aceitar paráfrases livres e sem relaxar verificações estruturais exatas.

## Existing Context

A fonte canônica está em `/home/renanfranca/.codex/skills`, no commit `8da0b50e2475a99296eb10a7ec7d32525eb14d8e`. O runner atual já planeja sessões, resolve runtime explícito e gera fingerprints, mas não expõe orientação econômica estruturada. `EVALUATIONS.md` generaliza Luna para qualquer mudança scoped, inclusive exemplos com judge, o que contradiz a política normativa estrita do dossiê.

A raiz isolada desta execução é `/tmp/promote-economic-runtime.acJDsi`:

* baseline imutável: `/tmp/promote-economic-runtime.acJDsi/baseline-root`;
* candidate editável: `/tmp/promote-economic-runtime.acJDsi/candidate-root`;
* skill baseline: `/tmp/promote-economic-runtime.acJDsi/baseline-root/develop-skill-with-evals`;
* skill candidate: `/tmp/promote-economic-runtime.acJDsi/candidate-root/develop-skill-with-evals`.

As duas árvores vieram de `git archive HEAD`. O baseline foi tornado recursivamente não gravável. A fonte canônica permanecerá sem alterações rastreadas até promoção e fresh agent aprovados.

Na quarta promoção, o executor preservou a skill instalada, o baseline e o candidate e inseriu `Explicitly redact personal email addresses from fixtures.`. O oracle rejeitou somente porque exigia literalmente `Redact personal email addresses from fixtures.`. A operação consumiu 11 sessões e o ledger passou a acumular 15 sessões.

Após a correção determinística do oracle, uma primeira tentativa de planejar a quinta promoção passou incorretamente `--approved-model-sessions 16` ao subcomando `plan`. Esse argumento pertence somente a `validate-change`, então o parser rejeitou o comando antes de qualquer operação. A tentativa inválida não iniciou sessões, não criou artifacts, não reservou orçamento e não criou nem modificou o ledger.

## Desired End State

Todo plano válido contém `economic_runtime` com `policy_version: 1`, um dos modos `zero-session`, `scoped-complete-oracle` ou `manual-selection`, recomendações opcionais por papel, estado de correspondência com runtime explícito e razões auditáveis.

Planos static e deterministic recomendam zero sessão. Um plano scoped recebe Luna `medium` somente quando todos os casos selecionados são semânticos, têm `oracle.commands` e desativam judge. Qualquer judge necessário recebe Terra `medium`, mas força seleção manual do executor. Cross cutting e oracle incompleto também exigem seleção manual do executor. Divergências geram warnings sem novos blockers, sem mudar comandos e sem sobrescrever valores explícitos.

O oracle de `self-evolution-candidate` aceita uma única frase curta que contenha, sem diferenciar maiúsculas, uma flexão simples de `redact`, `personal`, `email address` e `fixture`, com singular ou plural. Ele rejeita negações, conceitos ausentes, duas inserções e qualquer outra alteração. O prompt público declara essa liberdade lexical. O contrato geral proíbe exigir texto literal oculto quando o prompt público não exige a mesma literalidade.

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

### Milestone 5: Corrigir e provar o oracle

#### Goal

Aceitar variações textuais controladas sem reduzir as garantias de isolamento nem acrescentar sessões de judge.

#### Changes

* Atualizar `evals/cases/self-evolution-candidate/prompt.md` para declarar os conceitos obrigatórios e permitir variação de redação.
* Reescrever `evals/cases/self-evolution-candidate/oracle/check_self_evolution.py` para exigir baseline idêntico à skill instalada, exatamente uma inserção curta, conceitos completos, ausência de negação e nenhuma alteração adicional.
* Acrescentar a `SKILL.md` e `references/eval-contract.md` que texto literal só pode ser exigido quando o prompt público também o exigir.
* Adicionar o caso determinístico `self-evolution-oracle-contract`, que executa o oracle sobre workspaces sintéticos positivos e negativos.
* Adicionar o caso ao `evals/suite.json` sem habilitar judge em nenhum dos dois casos.

#### Validation

* Command: `PYTHONDONTWRITEBYTECODE=1 python3 /tmp/promote-economic-runtime.acJDsi/candidate-root/develop-skill-with-evals/scripts/run_skill_evals.py validate-change --skill /tmp/promote-economic-runtime.acJDsi/candidate-root/develop-skill-with-evals --baseline /tmp/promote-economic-runtime.acJDsi/baseline-root/develop-skill-with-evals --impact deterministic --case self-evolution-oracle-contract --approved-model-sessions 0 --report-dir /tmp/promote-economic-runtime.acJDsi/self-evolution-oracle-reports --artifacts-dir /tmp/promote-economic-runtime.acJDsi/self-evolution-oracle-artifacts --progress`
* Command: `PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover -s develop-skill-with-evals/scripts/tests -v`
* Command: `PYTHONDONTWRITEBYTECODE=1 python3 /home/renanfranca/.codex/skills/.system/skill-creator/scripts/quick_validate.py /tmp/promote-economic-runtime.acJDsi/candidate-root/develop-skill-with-evals`
* Command: validar os três schemas JSON com `python3 -m json.tool`.
* Command: executar o planejamento prévio abaixo com o limite operacional padrão de 8 e o último teto cumulativo aprovado de 20:

      PYTHONDONTWRITEBYTECODE=1 python3 \
        /tmp/promote-economic-runtime.acJDsi/candidate-root/develop-skill-with-evals/scripts/run_skill_evals.py \
        plan \
        --skill /tmp/promote-economic-runtime.acJDsi/candidate-root/develop-skill-with-evals \
        --baseline /tmp/promote-economic-runtime.acJDsi/baseline-root/develop-skill-with-evals \
        --impact cross-cutting \
        --case economic-runtime-guidance \
        --case cost-efficient-runtime-contract \
        --case nested-codex-outer-sandbox-contract \
        --case self-evolution-oracle-contract \
        --workflow promotion \
        --model gpt-5.6-sol \
        --reasoning-effort medium \
        --judge-model gpt-5.6-terra \
        --judge-reasoning-effort medium \
        --campaign-ledger /tmp/promote-economic-runtime.acJDsi/promotion-ledger.json \
        --approved-cumulative-model-sessions 20

* Expected result: baseline `FAIL`, três candidate `PASS`, assinatura estável e zero sessões; suíte, estrutura e schemas verdes.
* Expected result: o planejamento prévio sai com código 0, não cria nem modifica ledger, workspace, relatório ou artifact e informa máximo operacional 16, limite operacional vigente 8, consumo anterior 15, teto cumulativo vigente 20, projeção cumulativa 31, blockers de orçamento operacional e cumulativo insuficientes e `evaluation_fingerprint` `f638cf2708a2af49f1195746eac9d1a30345d9e55abff53b90080617940dbb92`.

Depois de o usuário autorizar explicitamente 16 sessões adicionais e elevar o teto cumulativo para 31, executar `codex doctor --json` fora do sandbox externo e exigir `overallStatus: ok`. Em seguida, solicitar aprovação externa separada para o comando completo e executar `validate-change` uma única vez com `--approved-model-sessions 16`, `--campaign-ledger /tmp/promote-economic-runtime.acJDsi/promotion-ledger.json`, `--approved-cumulative-model-sessions 31`, artifacts novos em `/tmp/promote-economic-runtime.acJDsi/promotion-fifth-artifacts`, Sol `medium` como executor e Terra `medium` como judge. Arquivar qualquer resultado e parar sem retry se o status não for `PASS`.

#### Acceptance Criteria

* A frase produzida na quarta promoção passa.
* Alterações extras e lembretes incompletos ou negados falham.
* Nenhum judge ou sessão real é usado no desenvolvimento da correção.
* O baseline imutável permanece sem modificação.

### Milestone 6: Fresh agent e promoção canônica

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
* [x] Quarta promoção executada uma única vez fora do sandbox externo.
* [x] Quarta promoção encerrada sem retry após `FAIL` de regressão.
* [x] Quarto relatório canônico incorporado e archive validado.
* [x] Milestone 5 iniciado.
* [x] Predicado lexical e contrato determinístico implementados.
* [x] Baseline RED e três candidate GREEN demonstrados sem modelo.
* [x] Planejamento prévio corrigido executado sem efeitos colaterais.
* [x] Nova promoção aprovada.
* [x] Preflight externo concluído com `overallStatus: ok`.
* [x] Nova promoção executada com resultado `PASS`.
* [x] Novo relatório canônico incorporado e archive validado.
* [x] Nova promoção concluída e arquivada.
* [x] Milestone 5 concluído.
* [x] Milestone 6 iniciado.
* [x] Fresh agent explicitamente aprovado.
* [x] Fresh agent concluído e revisado.
* [x] Patch candidato promovido à fonte canônica com merge preservador de `EVALUATIONS.md`.
* [x] Validação final concluída.
* [x] Milestone 6 concluído.

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

* Decision: Encerrar a quarta promoção no primeiro `FAIL` de regressão.
  Rationale: `economic-runtime-guidance` e as primeiras cinco regressões passaram, mas `self-evolution-candidate` falhou no oracle. O contrato exige parar antes das regressões restantes e das repetições dois e três, sem retry inalterado.
  Date/Author: 2026-07-27 / Codex

* Decision: Validar significado limitado mecanicamente, sem judge e sem aceitar paráfrases livres.
  Rationale: Os conceitos obrigatórios e os limites estruturais são totalmente observáveis por código; um judge acrescentaria custo e variabilidade sem cobrir garantia adicional.
  Date/Author: 2026-07-27 / Codex

* Decision: Não selecionar `self-evolution-candidate` como caso afetado.
  Rationale: O baseline já realiza o comportamento solicitado e poderia produzir `INVALID_RED`. A correção será observada pelo novo caso determinístico, enquanto o caso semântico permanece como regressão única.
  Date/Author: 2026-07-27 / Codex

* Decision: Fixar a quinta promoção em até 16 sessões adicionais e 31 cumulativas, condicionada a nova aprovação explícita.
  Rationale: O plano calcula 13 sessões de executor e 3 de judge, enquanto o ledger registra 15 sessões consumidas e nenhuma reserva ativa.
  Date/Author: 2026-07-27 / Codex

* Decision: Planejamento não concede autorização de custo.
  Rationale: `plan` usa o limite operacional padrão de 8 e lê o ledger com o último teto cumulativo aprovado, 20. Os novos limites de 16 sessões adicionais e 31 cumulativas só podem ser passados a `validate-change` depois da autorização explícita do usuário.
  Date/Author: 2026-07-27 / Codex

* Decision: Registrar “autorizo” como autorização explícita para a quinta promoção.
  Rationale: A solicitação imediatamente anterior identificou unicamente os dois limites pendentes, até 16 sessões adicionais e teto cumulativo de 31, e informou que a promoção não seria executada sem essa autorização. A aprovação de custo não concede aprovação externa de shell, que continua sendo solicitada separadamente para o comando completo.
  Date/Author: 2026-07-27 / Codex

* Decision: Avançar ao fresh agent somente com nova aprovação e novo teto cumulativo.
  Rationale: A quinta promoção passou e consumiu integralmente as 16 sessões autorizadas, levando o ledger ao teto de 31. O fresh agent é um gate separado do Milestone 6 e não está coberto pela autorização de custo nem pela aprovação externa concedidas à promoção.
  Date/Author: 2026-07-27 / Codex

* Decision: Limitar o fresh agent a uma tarefa isolada e side effect free de planejamento.
  Rationale: A autorização cobre uma sessão separada de agente, não sessões aninhadas do runner. Uma fixture genérica nova sob `/tmp` permite observar seleção econômica, preservação de runtime explícito e ausência de efeitos colaterais sem fornecer diagnóstico, resposta esperada ou artifacts das avaliações anteriores.
  Date/Author: 2026-07-27 / Codex

* Decision: Fazer merge preservador em `EVALUATIONS.md` e promover mecanicamente os demais arquivos.
  Rationale: O baseline foi congelado no commit `8da0b50`, mas o `HEAD` canônico avançou para `5ec4688` com uma reestruturação ampla do guia. Todos os outros destinos continuavam iguais ao baseline. Sobrescrever `EVALUATIONS.md` perderia trabalho commitado, então somente os conceitos novos foram incorporados à estrutura atual.
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
  Mitigation: Obter e registrar separadamente o teto de 16 sessões adicionais e 31 cumulativas antes de solicitar a elevação do comando.

* Risk: Tornar o oracle permissivo demais ao corrigir a lista de arquivos.
  Mitigation: Permitir apenas `.git`, `.eval-executor-schema.json`, `.eval-executor-response.json` e `.eval-result.json`, além das entradas já autorizadas, e executar o oracle contra o workspace real retido.

* Risk: Aceitar texto semanticamente incorreto apenas porque contém palavras esperadas.
  Mitigation: Exigir inserção única, conceitos completos, ausência de negação e nenhuma alteração adicional.

* Risk: Tornar o caso semântico afetado e obter `INVALID_RED`.
  Mitigation: Observar a correção pelo caso determinístico e manter `self-evolution-candidate` como regressão.

## Validation Strategy

1. Demonstrar RED contra baseline imutável.
2. Executar testes unitários focados e contratos mecânicos no candidate.
3. Repetir os GREEN focados três vezes.
4. Executar toda a suíte determinística e validação estrutural.
5. Planejar a promoção sem sessão e obter aprovação pelo total autoritativo.
6. Executar uma única promoção e parar em qualquer status diferente de `PASS`.
7. Incorporar o relatório canônico da operação, reconstruir o archive e validar manifest, Markdown, schemas e credenciais proibidas.
8. Planejar a nova promoção cross cutting com `economic-runtime-guidance`, `cost-efficient-runtime-contract`, `nested-codex-outer-sandbox-contract` e `self-evolution-oracle-contract` como afetados. Confirmar máximo de 16 sessões, 15 já consumidas e teto cumulativo de 31.
9. Obter autorização explícita para os totais emitidos, executar `codex doctor --json` fora do sandbox externo e exigir `overallStatus: ok`.
10. Solicitar aprovação externa separada para o comando completo de `validate-change`, preservar `workspace-write` internamente, executar uma única promoção, arquivar qualquer resultado e parar sem retry em qualquer estado diferente de `PASS`.
11. Se o resultado for `PASS`, obter aprovação separada e executar fresh agent com contexto mínimo.
12. Promover o patch e repetir validação final na fonte canônica.

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
* A quarta promoção passou nos três casos afetados na primeira repetição e nas regressões `load-skill-creator-before-scaffold`, `eval-before-behavior`, `reject-passing-baseline`, `non-behavioral-no-artificial-red` e `full-regression-gate`. Ela parou em `self-evolution-candidate`, cujo oracle informou que o candidate não continha o lembrete exatamente uma vez. A operação consumiu 11 sessões e elevou o ledger de 4 para 15.
* O prompt público de `self-evolution-candidate` pede apenas um lembrete com o significado desejado, enquanto o oracle oculto exige a frase exata `Redact personal email addresses from fixtures.`. O executor escreveu `Explicitly redact personal email addresses from fixtures.`, que satisfaz semanticamente o prompt mas não o contrato oculto literal. Corrigir essa inconsistência requer mudar o prompt ou o oracle, gerar novo fingerprint e abrir outra campanha; não é um retry válido.
* O relatório `20260727T123055.036794Z-1963f50be95e` foi incorporado. O archive contém 27 relatórios, passou a validação completa e preservou os hashes de `pilot-v2`.
* Oracles literais ocultos produzem falsos negativos quando o prompt permite variação; a rigidez deve recair sobre efeitos observáveis e limites estruturais.
* O primeiro GREEN expôs um erro na expressão de singular e plural de `email address`; após corrigir o predicado para `address(?:es)?`, o gate integrado produziu baseline `FAIL` e três candidate `PASS` estáveis com zero sessões.
* A suíte completa do candidate passou com 90 testes, `quick_validate.py` aceitou a skill e os três schemas JSON são válidos. O oracle corrigido também passou diretamente no workspace retido da quarta promoção com `Explicitly redact personal email addresses from fixtures.`.
* O plano cross cutting novo seleciona quatro casos afetados, mantém `self-evolution-candidate` como regressão única e calcula 13 sessões de executor mais 3 de judge, total máximo 16. O ledger contém 15 sessões consumidas, a projeção cumulativa é 31 e o novo `evaluation_fingerprint` é `f638cf2708a2af49f1195746eac9d1a30345d9e55abff53b90080617940dbb92`.
* Flags chamadas `approved-*` representam autorizações já concedidas e nunca devem receber valores prospectivos antes da aprovação explícita do usuário. No planejamento prévio, novos limites necessários devem aparecer como blockers, não como autorizações antecipadas.
* O planejamento prévio corrigido saiu com código 0 e confirmou máximo operacional 16, limite operacional 8, consumo anterior 15, teto cumulativo 20, projeção 31, os dois blockers esperados e fingerprint `f638cf2708a2af49f1195746eac9d1a30345d9e55abff53b90080617940dbb92`. Antes e depois, o ledger manteve checksum `1f2059bbb50692e6913b711939c4a9142ef2f4940c5b346b208031885e74aebb`, tamanho 727 bytes e o mesmo horário de modificação; nenhuma entrada de primeiro nível foi criada ou alterada na raiz isolada.
* Após a autorização de custo, `codex doctor --json` foi executado fora do sandbox externo e retornou `overallStatus: ok`. Autenticação ChatGPT, alcance HTTP e handshake WebSocket estavam íntegros antes da quinta promoção.
* A quinta promoção executou uma única vez e terminou `PASS`. Todos os quatro casos afetados produziram RED no baseline e três GREEN estáveis no candidate; as doze regressões também passaram, inclusive `self-evolution-candidate`, que havia bloqueado a quarta promoção. A operação consumiu o máximo planejado de 16 sessões, 13 de executor e 3 de judge, e elevou o ledger de 15 para 31 sessões consumidas.
* O relatório canônico `20260727T132448.821094Z-1fcd91faa06a`, com digest `26c0f53f77375d0ec6d655f2ae7b010f39b1d9fa5e4d60641442287f4118109b`, foi incorporado. O archive reconstruído contém 28 relatórios e uma comparação e passou a validação completa. Os hashes de `pilot-v2` permaneceram `6312727bb984bd33d313b5ccb1d68710ce79986f6ff08a801a5b4cac72ebcd26` para Markdown e `42d13e663f4c7c6ca5ce65525168367f29e55e810308dba533608535722cbef2` para JSON.
* O fresh agent recebeu contexto vazio, a skill candidata e uma tarefa isolada de planejamento scoped. Ele produziu dois planos válidos sem sessões aninhadas: Luna `medium` como recomendação econômica e Sol `medium` preservado com warning não bloqueante. Ambos selecionaram somente `format-note`, planejaram quatro sessões, não apresentaram blockers e não criaram workspace, ledger, relatório, cache ou artifact de modelo. A revisão independente contra `eval-plan.schema.json` passou.
* Antes da promoção canônica, `EVALUATIONS.md` divergia do baseline porque o `HEAD` avançou de `8da0b50` para `5ec4688` com uma reestruturação do guia. Um merge manual preservou a nova organização e incorporou orientação econômica, preflight externo e aprovações independentes. Todos os demais arquivos promovidos correspondem byte a byte ao candidate aprovado.
* No estado canônico final, 90 testes passaram em 11,059 segundos, `quick_validate.py` aceitou a skill, os três schemas JSON são válidos, o archive com 28 relatórios e uma comparação passou, e `git diff --check` não encontrou erros. Nenhum arquivo foi staged, commitado, enviado ou publicado.
