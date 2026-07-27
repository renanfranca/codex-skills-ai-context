# Reestruturar o cookbook do Codex CLI

Este ExecPlan é um documento vivo. Manter `Progress`, `Decisions`, `Risks and Mitigations` e `Lessons Learned` atualizados durante toda a execução.

## Purpose / Big Picture

Reescrever `CODEX_CLI.md` como guia operacional para quem usa as skills deste repositório. O leitor deve conseguir preparar o Codex CLI, descobrir e invocar uma skill, escolher entre a interface interativa e `codex exec`, selecionar o workflow correto e, somente depois, encontrar as operações avançadas de criação, avaliação, promoção, archive e comparação.

O resultado será observável em quatro jornadas manuais: um novo usuário encontra e invoca uma skill; um usuário escolhe corretamente a interface; um mantenedor planeja e executa uma promoção; e um mantenedor encontra relatórios sem confundir pricing estimado com cobrança ou relatórios comparativos com defaults de runtime.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy bypassing instructions.

## Scope

Incluído:

* reestruturar somente `CODEX_CLI.md` como cookbook operacional;
* preservar o título `# Using Skills with Codex CLI`;
* preservar os anchors `run-skill-evaluations`, `plan-proportional-gates-first`, `validate-the-planned-change`, `persist-evidence-with-dated-pricing` e `safety-and-troubleshooting`;
* auditar afirmações contra o CLI instalado `codex-cli 0.145.0`, as interfaces locais, as suites, metadados, archive, pricing e relatórios existentes;
* confrontar afirmações de produto com a documentação oficial atual de comandos, modo não interativo, skills e modelos GPT-5.6;
* cobrir todas as skills ativas por padrões de tarefa, com links para `README.md`, `SKILL.md` e `agents/openai.yaml` como fontes canônicas;
* manter o loop que descobre todas as suites;
* manter comandos executáveis de planejamento, promoção, persistência e inspeção;
* classificar esta mudança documental como `static`.

Excluído:

* alterar `AGENTS.md`, `README.md`, `EVALUATIONS.md`, qualquer `SKILL.md`, referências, runner, schemas, suites, metadados ou relatórios;
* introduzir APIs, schemas, flags ou comportamento;
* executar evals com modelos ou reconstruir o archive;
* stage, commit, push ou publicação;
* modificar ou remover mudanças, relatórios, caches e operações preexistentes no worktree.

## Definitions

`Cookbook` é um guia orientado a tarefas com decisões e comandos copiáveis. Ele não substitui a explicação conceitual de `EVALUATIONS.md` nem os contratos normativos da skill.

`Discovery` é o mecanismo pelo qual o Codex encontra skills em locais documentados e apresenta seus nomes e descrições para seleção.

`TUI` é a interface interativa de terminal iniciada por `codex`. Ela mantém uma conversa e pode solicitar aprovação humana durante a sessão.

`codex exec` é o modo não interativo. Ele recebe uma solicitação completa, envia progresso a standard error, entrega a resposta final em standard output e não deve depender de uma nova resposta humana durante a execução.

`Seleção explícita` ocorre quando a solicitação nomeia `$skill-name`. `Seleção implícita` ocorre quando a descrição de uma skill corresponde à tarefa e o Codex a escolhe sem o nome explícito.

`Promoção` é o workflow do runner que prova a mudança com baseline RED, três candidate GREEN estáveis e regressão proporcional ao impacto.

`Operação exploratória` é uma interface útil para diagnóstico, compatibilidade ou inspeção que não substitui o workflow de promoção.

`Archive` é o conjunto auditado de relatórios canônicos JSON, projeções Markdown, manifests e comparações sob `evaluation-reports/`.

`Pricing reference` é uma tabela datada usada para estimar custo de API em relatórios. Ela não representa cobrança observada e não define o runtime padrão do runner.

## Existing Context

A fonte está em `/home/renanfranca/.codex/skills`. A base solicitada para esta revisão é o commit `5ec46884a197cb70d137517abf04992e730db848`, com assunto `docs(develop-skill-with-evals): restructure evaluation guide`.

Antes desta execução, o worktree já estava sujo. Havia modificações rastreadas em `CODEX_CLI.md`, `EVALUATIONS.md`, arquivos de `develop-skill-with-evals/` e manifests de `evaluation-reports/`; também havia `_temporary/`, caches Python, novos casos, testes e operações de avaliação não rastreados. A mudança preexistente em `CODEX_CLI.md` acrescentava orientação sobre `codex doctor --json`, permissões externas do runner, economic runtime e seleção manual de Sol e Terra. Esse conteúdo pertence ao usuário e deve ser preservado semanticamente durante a reescrita.

`CODEX_CLI.md` tinha 559 linhas e seguia esta progressão: preparação; TUI versus modo não interativo; operações de avaliação; criação e melhoria de skills; um prompt separado para cada skill; troubleshooting. A informação é extensa e útil, mas o catálogo de prompts fragmenta tarefas semelhantes e a manutenção avançada aparece antes de um mapa claro dos workflows.

`README.md` é o catálogo público das skills ativas e desabilitadas. `EVALUATIONS.md` é a explicação conceitual do sistema de avaliação. `develop-skill-with-evals/SKILL.md`, suas referências e scripts são as fontes normativas do comportamento local.

O CLI instalado informou `codex-cli 0.145.0`. `codex --help` confirmou TUI, `-C`, sandbox e políticas de aprovação. `codex exec --help` confirmou `--ephemeral`, `--json`, `-o/--output-last-message` e o comportamento de prompt. `codex resume --help` e `codex exec resume --help` confirmaram caminhos separados de retomada interativa e não interativa. `codex login status` informou autenticação por ChatGPT.

O manual oficial do Codex foi atualizado pelo helper de `openai-docs` em `/tmp/openai-docs-cache/codex-manual.md`, com outline em `/tmp/openai-docs-cache/codex-manual.outline.md`. Páginas oficiais específicas serão usadas para verificar comandos, modo não interativo, discovery de skills e guidance dos modelos GPT-5.6.

## Desired End State

`CODEX_CLI.md` segue esta ordem:

1. preparação, autenticação e discovery;
2. escolha entre TUI e `codex exec`;
3. seleção explícita e implícita;
4. mapa de workflows por tarefa;
5. criação e evolução de skills;
6. avaliações, promoção e operações exploratórias;
7. archive, pricing e comparação;
8. revisão final, segurança e troubleshooting.

O caminho principal é curto e orientado a quem usa skills. A manutenção avançada continua completa, mas aparece depois. Todas as skills ativas são cobertas por poucos padrões de tarefa e por links para fontes canônicas, sem um prompt quase idêntico para cada skill.

O guia explica apenas os recursos do CLI realmente usados no repositório: discovery, TUI, sandbox, `codex exec`, `--ephemeral`, `--json`, `-o`, standard output, standard error e retomada de sessões. Outros recursos são encaminhados à referência oficial.

As explicações de RED, GREEN, gates, fingerprints e statuses aparecem uma vez em forma operacional e apontam para `EVALUATIONS.md` para o modelo conceitual. `run`, `verify-change` e `stability` são identificados como operações exploratórias ou de compatibilidade, não como o caminho normal de promoção.

Luna, Terra e Sol aparecem somente em exemplos vinculados ao snapshot datado e aos relatórios. O texto declara que não são defaults do runner e que a comparação `pilot-v2` é direcional e não qualificou nenhum modelo como padrão.

## Milestones

### Milestone 1: Auditar fontes e interfaces

#### Goal

Estabelecer um inventário factual antes da reescrita.

#### Changes

* Ler `CODEX_CLI.md`, `README.md`, `EVALUATIONS.md` e as fontes locais citadas pelo cookbook.
* Confirmar o CLI instalado e os helps de `codex`, `codex exec`, retomada, login e doctor.
* Confirmar todos os helps do runner e dos scripts de renderização, comparação e archive.
* Inventariar suites, skills ativas, `agents/openai.yaml`, pricing, archive config e relatórios `pilot-v2`.
* Verificar as afirmações de produto nas fontes oficiais atuais.

#### Validation

* Command: `codex --version && codex --help && codex exec --help && codex resume --help && codex exec resume --help`
* Expected result: todas as opções documentadas existem no CLI `0.145.0`.
* Command: executar `--help` no runner e nos scripts de renderização, comparação e archive descobertos no repositório.
* Expected result: os nomes de operações, argumentos e semântica resumida do cookbook correspondem às interfaces reais.

#### Acceptance Criteria

* Toda afirmação operacional tem uma fonte oficial ou local identificável.
* Nenhum comando do novo documento depende de opção inexistente.

### Milestone 2: Reescrever o caminho principal

#### Goal

Levar um usuário da instalação até a seleção correta de uma skill e de uma interface antes de apresentar manutenção avançada.

#### Changes

* Editar `/home/renanfranca/.codex/skills/CODEX_CLI.md`.
* Preservar o título e reorganizar preparação, discovery, autenticação, TUI, `codex exec`, seleção explícita e seleção implícita.
* Explicar standard output, standard error, `--json`, `-o`, `--ephemeral` e retomada somente no contexto em que são úteis.
* Criar um mapa compacto de workflows por tarefa que cubra todas as skills ativas.
* Remover o catálogo de um prompt por skill e substituí lo por poucos padrões de solicitação orientados a tarefas.

#### Validation

* Command: `sed -n '1,240p' CODEX_CLI.md`
* Expected result: as jornadas de novo usuário e escolha de interface são completas antes das operações avançadas.
* Command: comparar o mapa de workflows com o catálogo de skills ativas em `README.md`.
* Expected result: cada skill ativa aparece no mapa ou em um padrão de tarefa claramente correspondente.

#### Acceptance Criteria

* Um novo usuário consegue descobrir e invocar uma skill sem ler a seção de avaliações.
* O documento explica quando persistir ou não uma sessão e como retomar cada tipo.

### Milestone 3: Reorganizar manutenção e avaliações

#### Goal

Manter receitas avançadas completas sem duplicar o guia conceitual.

#### Changes

* Colocar criação e evolução de skills antes da referência detalhada do runner.
* Preservar os anchors obrigatórios.
* Resumir RED, GREEN, gates, fingerprints e statuses e linkar `EVALUATIONS.md`.
* Manter `plan`, diagnóstico opcional e `validate-change` como caminho de promoção.
* Manter o loop de descoberta de todas as suites.
* Classificar `run`, `verify-change` e `stability` como operações exploratórias ou de compatibilidade.
* Preservar a orientação preexistente sobre `codex doctor --json`, permissões externas e sandbox interno.

#### Validation

* Command: `rg -n '^#{1,4} |run-skill-evaluations|plan-proportional-gates-first|validate-the-planned-change|persist-evidence-with-dated-pricing|safety-and-troubleshooting' CODEX_CLI.md`
* Expected result: a ordem solicitada e todos os anchors permanecem presentes.
* Command: inspecionar os comandos do runner em blocos de código.
* Expected result: planejamento e promoção são o caminho recomendado; interfaces auxiliares são rotuladas corretamente.

#### Acceptance Criteria

* Um mantenedor consegue copiar o plano e a promoção sem consultar a implementação do runner.
* O cookbook não tenta redefinir os contratos normativos.

### Milestone 4: Organizar archive, pricing e comparação

#### Goal

Permitir inspeção e comparação de evidência sem criar inferências econômicas ou defaults falsos.

#### Changes

* Reunir persistência, rendering, archive, pricing e comparação em uma seção coerente.
* Vincular exemplos de Luna, Terra e Sol ao snapshot e aos relatórios datados.
* Declarar que os modelos não são defaults do runner.
* Declarar que `actual_charge` é falso e que estimativas de API não são cobrança observada.
* Declarar que `pilot-v2` é evidência direcional e não selecionou um default.

#### Validation

* Command: confrontar exemplos com pricing, archive config, manifests e relatórios `pilot-v2`.
* Expected result: caminhos, modelos e ressalvas correspondem aos artefatos reais.

#### Acceptance Criteria

* Um leitor não consegue interpretar razoavelmente pricing como cobrança ou comparação como seleção automática de runtime.

### Milestone 5: Validar documentação e preservar o worktree

#### Goal

Provar que o novo cookbook é navegável, factual e limitado aos arquivos autorizados.

#### Changes

* Validar links Markdown locais e anchors.
* Inspecionar headings e primeiras ocorrências de termos operacionais.
* Executar `git diff --check -- CODEX_CLI.md`.
* Revisar o diff limitado a `CODEX_CLI.md` e a este ExecPlan.
* Comparar o status final com o estado preexistente.
* Registrar resultados, decisões e aprendizados neste ExecPlan.

#### Validation

* Command: validador local de links Markdown e anchors para `CODEX_CLI.md`.
* Expected result: nenhum link local ou anchor quebrado.
* Command: `git diff --check -- CODEX_CLI.md`
* Expected result: saída vazia e exit code `0`.
* Command: `git status --short`
* Expected result: somente `CODEX_CLI.md` e este ExecPlan foram modificados por esta execução; os demais itens correspondem ao estado preexistente.

#### Acceptance Criteria

* As quatro jornadas manuais são completas.
* Relatórios, caches e operações preexistentes permanecem intactos.
* Nenhuma avaliação model backed nem rebuild do archive foi executado.

## Progress

* [x] Milestone 1 iniciado.
* [x] Milestone 1 concluído.
* [x] Milestone 2 iniciado.
* [x] Milestone 2 concluído.
* [x] Milestone 3 iniciado.
* [x] Milestone 3 concluído.
* [x] Milestone 4 iniciado.
* [x] Milestone 4 concluído.
* [x] Milestone 5 iniciado.
* [x] Milestone 5 concluído.

## Decisions

* Decision: tratar `5ec4688` como base documental solicitada, mesmo com mudanças posteriores e um worktree sujo.
  Rationale: o usuário fixou essa base e pediu explicitamente que o estado preexistente fosse registrado e preservado.
  Date/Author: 2026-07-27 / Codex

* Decision: preservar semanticamente as mudanças preexistentes em `CODEX_CLI.md` durante a reescrita.
  Rationale: elas pertencem ao usuário e documentam contratos recentes do runner e do ambiente que continuam relevantes.
  Date/Author: 2026-07-27 / Codex

* Decision: usar documentação oficial para comportamento do produto e arquivos locais para comportamento específico do repositório.
  Rationale: essa separação evita atribuir convenções locais ao Codex CLI ou documentar memória do produto como contrato.
  Date/Author: 2026-07-27 / Codex

* Decision: usar headings Markdown, sem anchors HTML duplicados, para preservar os cinco slugs consumidos por outros guias.
  Rationale: os headings existentes já geram exatamente os anchors requeridos e evitam IDs duplicados no documento renderizado.
  Date/Author: 2026-07-27 / Codex

## Risks and Mitigations

* Risk: sobrescrever mudanças preexistentes no cookbook.
  Mitigation: conservar o diff inicial, auditar cada trecho modificado e revisar o diff final contra `5ec4688` e contra o estado observado no início.

* Risk: outro processo alterar o index durante a execução.
  Mitigation: registrar o status inicial e final, não executar stage ou unstage e limitar a revisão final do conteúdo produzido a `CODEX_CLI.md` e ao ExecPlan.

* Risk: o documento descrever opções ou modelos que mudaram.
  Mitigation: validar o CLI instalado, fontes oficiais atuais e artefatos datados; qualificar snapshots e evitar chamar exemplos de defaults.

* Risk: remover anchors consumidos por `README.md` ou `EVALUATIONS.md`.
  Mitigation: preservar headings explícitos e validar links e slugs ao final.

* Risk: confundir cookbook com contrato normativo.
  Mitigation: manter explicações operacionais breves e apontar para `EVALUATIONS.md`, `SKILL.md`, referências e schemas.

* Risk: comandos de validação alterarem archive ou consumirem sessões.
  Mitigation: executar somente operações de help, leitura e validação estática; não executar evals model backed nem comandos de rebuild.

## Validation Strategy

1. Validar as interfaces de CLI e scripts por `--help`.
2. Auditar inventários e artefatos locais somente por leitura.
3. Reescrever o documento em marcos e revisar sua estrutura.
4. Validar links locais, anchors, headings, termos e whitespace.
5. Exercitar manualmente as quatro jornadas contra o texto final.
6. Confirmar no status e diff que o trabalho permaneceu no escopo.

Resultados:

* `codex --version` confirmou `codex-cli 0.145.0`; os helps de `codex`, `exec`, retomada, login e doctor confirmaram as opções documentadas.
* Os helps de `run`, `verify-change`, `stability`, `plan`, `probe-change`, `validate-change`, rendering, comparação e archive confirmaram todas as interfaces citadas.
* `codex doctor --json` retornou `overallStatus: ok` no limite externo usado para a auditoria.
* A documentação oficial confirmou discovery em `.agents/skills`, seleção explícita e implícita, TUI, modo não interativo, sandbox, `--ephemeral`, JSONL, standard output, standard error, `-o` e retomada.
* O validador local conferiu todos os links Markdown e anchors de `CODEX_CLI.md`, `README.md` e `EVALUATIONS.md` sem falhas.
* `git diff --check -- CODEX_CLI.md` retornou exit code `0` e nenhuma saída.
* Os cinco headings requeridos geram exatamente os anchors consumidos pelos outros guias.
* O mapa por tarefa cobre as oito skills ativas do catálogo e linka `SKILL.md` e `agents/openai.yaml`.
* A jornada de novo usuário termina em discovery, `/skills` e invocação explícita antes de qualquer detalhe de eval.
* A jornada de escolha de interface distingue follow ups e aprovação no TUI de execução previamente delimitada em `codex exec`, além de separar sessões efêmeras das retomáveis.
* A jornada de promoção apresenta `plan`, diagnóstico opcional e `validate-change`, com runtime, orçamento, blockers e resultado.
* A jornada de relatório diferencia JSON canônico, Markdown derivado, pricing com `actual_charge: false` e comparação direcional com todos os modelos não qualificados.
* O status final mostra uma mudança concorrente de index: arquivos que estavam unstaged ou não rastreados no início passaram a aparecer staged, incluindo `EVALUATIONS.md`, fontes do runner, novos casos e relatórios. Esta execução não chamou `git add`, commit ou outro comando de stage e deixou esse estado externo intacto. `CODEX_CLI.md` permanece unstaged e este ExecPlan permanece sob `_temporary/`.
* Nenhum relatório, operação, suite ou fonte normativa foi editado intencionalmente por esta execução. Como os caches Python já eram não rastreados, `git status` não consegue provar identidade de bytes ou timestamps; os comandos de help não executaram evals, mas imports Python podem atualizar um `.pyc` compatível.

## Rollout and Recovery

A mudança é documental e estática, sem rollout de runtime. A revisão pode ser adotada diretamente após validação. Se precisar ser recuperada, reverta somente `CODEX_CLI.md` e este ExecPlan para o estado anterior à execução; não toque nas demais mudanças preexistentes do worktree. Nenhum commit, push ou publicação será feito nesta execução.

## Lessons Learned

* O CLI `0.145.0` separa explicitamente `codex resume` de `codex exec resume`; o cookbook deve preservar essa distinção.
* `--ephemeral` impede persistência da sessão, portanto um exemplo efêmero não pode ser apresentado como retomável.
* A autenticação atual foi verificada como ChatGPT, mas o cookbook deve ensinar `codex login status` sem pressupor um método único de login.
* O `pilot-v2` contém 18 observações, mas nenhum dos três modelos satisfaz o critério de qualificação; ele não sustenta um default do runner.
* O runner mantém `run`, `verify-change` e `stability` por inspeção e compatibilidade, enquanto `plan` mais `validate-change` integra fingerprints, orçamento e regressão proporcional para promoção.
* O help e a documentação oficial concordam que `codex exec` envia progresso a standard error, mantém a resposta final em standard output e transforma standard output em JSONL com `--json`.
* A configuração global atual usa Sol, mas isso não contradiz o contrato local: o runner de promoção exige runtime explícito e não transforma o default do Codex em default de avaliação.
* Para uma auditoria futura que precise provar caches intocados, executar todos os scripts Python com `PYTHONDONTWRITEBYTECODE=1` e registrar hashes dos arquivos não rastreados antes da primeira importação.
* `git status` pode mudar por atividade concorrente no mesmo worktree. A ausência de comandos de mutação de index nesta execução permite afirmar que o stage observado ao final não foi produzido por este trabalho, mas não identifica qual processo o produziu.
