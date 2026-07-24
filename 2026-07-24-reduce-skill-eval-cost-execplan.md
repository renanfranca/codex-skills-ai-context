# Reduzir o custo real das avaliações de skills

Este ExecPlan é um documento vivo. Manter `Progress`, `Decisions`, `Risks and Mitigations` e `Lessons Learned` atualizados durante toda a execução.

## Purpose / Big Picture

Reduzir sessões e tokens sem diminuir a evidência necessária para promoção. A implementação será incremental em uma candidata isolada, com validação determinística após cada milestone e nenhuma sessão de modelo até os novos controles de custo estarem funcionando.

O validador semântico antigo não será executado. Ele servirá somente como baseline para demonstrar RED determinístico nos contratos novos.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy bypassing instructions.

## Scope

Incluído:

* `probe-change` para diagnóstico completo sem capacidade de promoção.
* Regressões antes das repetições GREEN 2 e 3.
* Fingerprints de prompts, fixtures, oráculos, fontes e runtime.
* Telemetria estruturada de tokens usando `codex exec --json`.
* Orçamento cumulativo de sessões por campanha.
* Oráculos mecânicos ocultos para cinco casos hoje julgados redundantemente.
* Schemas, testes, documentação, metadata e self evaluations.
* Um diagnóstico com o runner otimizado, um gate final e um fresh agent.

Excluído:

* Cache ou reutilização de resultados anteriores.
* Modelos diferentes por fase ou por caso.
* Otimização baseada em preços ainda desconhecidos.
* Alterações em `.system/`.
* Commit, push ou publicação.
* Alterações na mudança existente em `.codex/rules/python.rules`.

## Definitions

`Baseline` é a cópia imutável do skill atual no commit `3a358b16`. `Candidate` é a única árvore onde a implementação será realizada antes da promoção. `Diagnostic workflow` é uma execução que coleta todos os problemas de contrato, mas nunca autoriza promoção. `Promotion workflow` exige RED, GREEN, estabilidade e regressões para promoção. `Hidden oracle` é um checker armazenado fora da fixture pública e invisível ao executor. `Campaign ledger` é um arquivo explícito em `/tmp` que registra sessões planejadas, reservadas e consumidas durante toda a campanha. `Evaluation fingerprint` é o hash de todos os inputs capazes de alterar uma avaliação, não apenas de `case.json`.

## Existing Context

O runner atual executa baseline, três candidatas e somente depois as regressões. Cinco gates do processo anterior pagaram novamente as três candidatas antes de encontrar uma regressão problemática.

O processo completo consumiu 92 sessões. O gate final consumiu 21. Trinta e nove executores recuperáveis reportaram aproximadamente 2,27 milhões de tokens.

A fonte canônica é `/home/renanfranca/.codex/skills`, com o skill em `/home/renanfranca/.codex/skills/develop-skill-with-evals`. `HEAD` e o commit baseline são o mesmo commit: `3a358b16`, `feat(develop-skill-with-evals): enforce auditable eval runtime`.

A raiz isolada desta execução é `/tmp/cost-efficient-skill-evals.nmKMhH`. As árvores são:

* `/tmp/cost-efficient-skill-evals.nmKMhH/baseline-source`
* `/tmp/cost-efficient-skill-evals.nmKMhH/baseline-eval`
* `/tmp/cost-efficient-skill-evals.nmKMhH/candidate`

As três derivaram do mesmo `git archive` do commit `3a358b16`, restrito a `develop-skill-with-evals`, `AGENTS.md`, `README.md`, `CODEX_CLI.md` e `EVALUATIONS.md`. `baseline-source` foi tornada recursivamente não gravável. A fonte canônica permanecerá intocada até a promoção final.

A árvore contém arquivos não rastreados em `_temporary/` e caches Python. `.codex/rules/python.rules` é uma alteração ignorada já existente, com SHA 256 inicial `131bace21ec670a146faba07e7b6ff8cb428087770cb47a847159545d2717554`. Ela está fora do escopo e não será alterada.

## Desired End State

### Interfaces públicas

Adicionar:

    probe-change
    plan --workflow diagnostic|promotion
    --campaign-ledger <path>
    --approved-cumulative-model-sessions <n>

`promotion` será o valor padrão compatível de `plan --workflow`.

Adicionar `oracle.commands` ao manifesto dos casos. Preservar `manifest_fingerprint` e adicionar `case_fingerprints`, `evaluation_fingerprint` e `source_fingerprints`.

Adicionar aos relatórios `promotion_eligible`, `failure_category`, `usage` e `campaign`.

Preservar os cinco comandos existentes e manifests sem oráculos.

### Comportamento

`probe-change` executa cada observação uma vez, continua após falhas de contrato e produz `promotion_eligible: false`. Falhas de infraestrutura, autenticação, quota ou subprocesso interrompem imediatamente o diagnóstico.

`validate-change` executa:

1. Baseline dos casos afetados.
2. Primeira candidata dos casos afetados.
3. Regressões.
4. Candidatas 2 e 3.

O plano máximo esperado após migração é 12 sessões para diagnóstico e 14 sessões para promoção. O JSON emitido pelo plano é autoritativo.

Cinco casos mantêm executor real, mas deixam de usar juiz porque oráculos ocultos cobrem integralmente o contrato.

O ledger bloqueia antes de modelos quando consumo acumulado mais máximo planejado exceder a aprovação. Tokens ausentes permanecem `null` com `usage.complete: false`; nunca são registrados como zero.

## Milestones

### Milestone 1: Criar o ExecPlan e isolar a fonte

Criar a raiz isolada com `baseline-source`, `baseline-eval` e `candidate`, tornar `baseline-source` não gravável e registrar o caminho literal neste documento.

Validação sem modelo:

    PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover \
      -s develop-skill-with-evals/scripts/tests -v

    python3 .system/skill-creator/scripts/quick_validate.py \
      ./develop-skill-with-evals

    git diff --check
    git status --short

Aceitação: 36 testes atuais passam; baseline e candidata derivam da mesma fonte; `.codex/rules/python.rules` permanece intacta; nenhum subprocesso Codex é iniciado.

### Milestone 2: Escrever contratos novos e demonstrar RED

Adicionar primeiro em `baseline-eval` e `candidate`:

* `develop-skill-with-evals/scripts/tests/test_cost_efficient_workflow.py`
* Caso determinístico `cost-efficient-runtime-contract`
* Fixtures com fake Codex emitindo eventos JSONL de uso.

Cobrir parsing e help, planos diagnostic e promotion, nova ordem de execução, coleta de múltiplas falhas no diagnóstico, interrupção em falha de infraestrutura, fingerprints sensíveis a prompt, fixture, oráculo e fonte, isolamento do oráculo, uso completo e incompleto, ledger cumulativo, bloqueio sem efeitos, atualização após falha e compatibilidade.

Executar os testes novos contra o runner de `baseline-eval`. Eles devem falhar apenas pela ausência das capacidades novas, enquanto os testes anteriores continuam verdes. Nenhum modelo será chamado.

### Milestone 3: Implementar o núcleo do runner

Alterar na candidata:

* `develop-skill-with-evals/scripts/run_skill_evals.py`
* `develop-skill-with-evals/references/eval-plan.schema.json`
* `develop-skill-with-evals/references/eval-result.schema.json`

Implementar `plan --workflow`, `probe-change`, a nova ordem de `validate-change`, classificação de falhas, fingerprints completos, captura de JSONL com `codex exec --json`, uso agregado e detalhado, ledger explícito com lock e escrita atômica, reserva conservadora e comparação de fingerprints antes do primeiro modelo.

Após cada grupo:

    PYTHONDONTWRITEBYTECODE=1 RUN_SKILL_EVALS_SCRIPT=<candidate-runner> \
      python3 -m unittest discover -s <candidate-tests> -v

    python3 .system/skill-creator/scripts/quick_validate.py <candidate-skill>

Aceitação: todos os testes determinísticos passam; baseline demonstra RED e candidata demonstra três GREENs no caso determinístico; plano, bloqueio e ledger não criam workspaces nem modelos indevidos; nenhuma validação semântica foi executada.

### Milestone 4: Introduzir oráculos ocultos e alinhar a política

Adicionar `oracle/` sem copiá lo para o workspace do executor.

Migrar:

* `explicit-runtime-promotion-workflow`
* `impact-gate-selection`
* `load-skill-creator-first`
* `eval-before-behavior`
* `self-evolution-candidate`

Separar o recorder público de `eval-before-behavior` do verificador oculto. Remover os juízes somente depois de cada oráculo cobrir todos os critérios anteriores.

Atualizar o caso principal para observar plano diagnostic, `probe-change`, plano promotion e `validate-change`.

Atualizar:

* `develop-skill-with-evals/SKILL.md`
* `develop-skill-with-evals/references/eval-contract.md`
* `develop-skill-with-evals/agents/openai.yaml`
* `README.md`
* `CODEX_CLI.md`
* `EVALUATIONS.md`
* `AGENTS.md`

Validar os oráculos contra os artefatos preservados do processo anterior, sem invocar modelos.

Aceitação: o executor não consegue listar ou ler nenhum oráculo; os cinco oráculos detectam os defeitos registrados no dossiê; os cinco casos continuam usando executor real; o máximo esperado passa de 22 para 14 sessões de promoção; os três casos semanticamente menos estruturados mantêm juiz.

### Milestone 5: Executar somente os gates otimizados

Primeiro executar uma validação determinística de custo zero com a candidata.

Depois gerar o plano diagnostic. O valor emitido é autoritativo; o esperado é 12 sessões. Pausar para aprovação explícita e executar `probe-change` uma única vez com ledger cumulativo.

Se o diagnóstico encontrar problemas, atualizar imediatamente este ExecPlan, corrigir com testes e replay determinístico, usar `run --case` somente quando evidência mecânica for insuficiente e após nova aprovação, e não repetir o diagnóstico completo automaticamente.

Quando o diagnóstico estiver resolvido, gerar o plano promotion. O esperado é 14 sessões e consumo acumulado máximo de 26 para diagnóstico mais promoção. Executar um único `validate-change` final com a candidata e o mesmo ledger, após aprovação.

Aceitação: `probe-change` registra `promotion_eligible: false`; o relatório inclui sessões e tokens de executor e juiz; o gate final produz RED, três GREENs estáveis e regressões válidas; o consumo acumulado permanece dentro da aprovação; o validador semântico antigo nunca é executado.

### Milestone 6: Fresh agent, revisão e promoção

Depois do gate final, solicitar aprovação separada para um fresh agent. Ele receberá apenas a candidata e uma tarefa realista em `/tmp`, sem diagnóstico, critérios ocultos ou conclusões anteriores.

Após o fresh agent:

1. Comparar a fonte canônica com `baseline-source`.
2. Parar se qualquer arquivo canônico em escopo tiver mudado.
3. Revisar o diff candidato.
4. Aplicar somente patches por arquivo.
5. Confirmar equivalência byte a byte entre candidata e promoção.
6. Executar validações determinísticas canônicas.
7. Não repetir gates de modelo depois da promoção byte equivalente.

## Progress

* [x] Instruções de `implement-execplan`, `develop-skill-with-evals`, `skill-creator` e contrato de avaliações carregadas.
* [x] ExecPlan salvo e raiz isolada criada.
* [x] Baseline imutável e candidata confirmadas.
* [x] Validações iniciais do milestone 1 concluídas: 36 testes, `quick_validate.py` e `git diff --check` passaram.
* [x] Contratos novos adicionados antes da implementação.
* [x] RED determinístico demonstrado contra o runner antigo: oito testes novos falharam, enquanto os 36 anteriores permaneceram verdes.
* [x] Núcleo do runner implementado e GREEN: 44 testes passam e o caso integrado produziu baseline `FAIL` e três candidatas `PASS` com zero sessões.
* [x] Oráculos ocultos migrados e replayados contra formas mínimas dos defeitos preservados no dossiê; replay byte a byte ficou impossível porque as raízes originais sumiram.
* [x] Documentação, schemas e metadata alinhados.
* [x] Validação determinística final concluída: 47 testes, validação estrutural, JSON e gate integrado com zero sessões.
* [x] Plano diagnostic autoritativo gerado: 12 sessões máximas, sendo 9 de executor e 3 de juiz.
* [x] Diagnóstico otimizado aprovado e executado uma única vez: 12 sessões consumidas; o caso principal encontrou uma falha contratual no próprio oráculo, sem falha de infraestrutura.
* [x] Achados do diagnóstico corrigidos e replayados deterministicamente, sem repetir o diagnóstico completo: 48 testes verdes, replay direto do workspace preservado e gate integrado de custo zero passaram.
* [x] Plano promotion autoritativo gerado: 14 sessões máximas e 26 cumulativas com o diagnóstico.
* [x] Primeira tentativa do gate final aprovada e executada; parou na regressão determinística `runner-progress-output` após 11 das 14 sessões planejadas.
* [x] Fixture de progresso corrigida e coberta por teste integrado de custo zero; 49 testes passam.
* [x] Novo plano promotion gerado após a correção: 14 sessões máximas, 23 já consumidas e projeção cumulativa de 37.
* [x] Segunda tentativa do gate final executada por completo: todos os resultados individuais passaram, mas a estabilidade rejeitou uma alteração opcional de `sample-skill/SKILL.md` presente somente em GREEN 2.
* [x] Fronteira de arquivos do caso principal corrigida; 50 testes, validação estrutural, schemas e verificações de integridade passam.
* [x] Terceiro plano promotion gerado: 14 sessões máximas, 37 já consumidas e projeção cumulativa de 51.
* [x] Terceira e última execução autorizada iniciada; interrompida imediatamente por falha de infraestrutura no segundo juiz após oito sessões.
* [x] Novo plano autorizado após a falha externa: 14 sessões máximas, 45 consumidas e projeção cumulativa de 59.
* [x] Quarta tentativa executada; parou após três sessões porque `load-skill-creator-first` gravou `creation-evidence.json` dentro do skill em vez da raiz do workspace.
* [x] Localização de `creation-evidence.json` tornada inequívoca; 51 testes e validações estruturais passam.
* [x] Novo plano promotion gerado: 14 sessões máximas, 48 consumidas e projeção cumulativa de 62.
* [x] Gate final otimizado concluído com PASS: baseline RED, três GREENs estáveis e dez regressões PASS.
* [x] Fresh agent aprovado e concluído com baseline RED, três GREENs estáveis e zero sessões aninhadas.
* [x] Patch promovido por arquivo, equivalência byte a byte confirmada e validação canônica concluída.

## Decisions

* Decision: Usar um único ExecPlan com milestones incrementais, não uma implementação monolítica.
  Rationale: As mudanças compartilham o runner e os schemas, mas precisam de checkpoints independentes.
  Date/Author: 2026-07-24 / User e Codex

* Decision: Não executar o validador semântico antigo.
  Rationale: Ele não observa os contratos novos e repete a ordem responsável pelo maior desperdício.
  Date/Author: 2026-07-24 / User e Codex

* Decision: Validar continuamente sem modelo antes do primeiro gate otimizado.
  Rationale: Evita implementação extensa sem testes e evita pagar pelo runner caro durante o desenvolvimento.
  Date/Author: 2026-07-24 / User e Codex

* Decision: Adiar cache e runtime por fase.
  Rationale: Cache pode reutilizar evidência envelhecida e modelos diferentes exigem dados de custo e qualidade ainda inexistentes.
  Date/Author: 2026-07-24 / User

* Decision: Derivar as três árvores diretamente do commit `3a358b16`.
  Rationale: `HEAD` coincide com esse commit e o arquivo ignorado `.codex/rules/python.rules` não pode entrar acidentalmente na candidata.
  Date/Author: 2026-07-24 / Codex

* Decision: Não repetir o diagnóstico completo depois da falha do oráculo principal.
  Rationale: O relatório e o workspace retido identificaram deterministicamente a causa; o plano exige correção com testes e replay local antes de qualquer nova sessão.
  Date/Author: 2026-07-24 / Codex

* Decision: Não consumir automaticamente as três sessões restantes da primeira aprovação de promoção.
  Rationale: O gate terminou e liberou sua reserva; qualquer nova operação exige novo plano e nova aprovação explícita, mesmo quando a falha anterior foi determinística.
  Date/Author: 2026-07-24 / Codex

* Decision: Tornar explícita a fronteira de arquivos do caso principal.
  Rationale: A tarefa observa uma alteração isolada no renderer; documentação opcional produziu assinaturas diferentes apesar de três resultados funcionais PASS. O prompt e o contrato mecânico devem exigir somente `sample-skill/scripts/render.py`.
  Date/Author: 2026-07-24 / Codex

* Decision: Não repetir automaticamente a terceira execução após a falha do juiz.
  Rationale: Ela foi aprovada explicitamente como a terceira e última execução; nova tentativa requer nova direção do usuário, mesmo sendo uma falha externa.
  Date/Author: 2026-07-24 / Codex

* Decision: Promover somente os 30 arquivos modificados ou adicionados e remover três checkers públicos por patch explícito.
  Rationale: Isso preserva caches e mudanças preexistentes, evita copiar a árvore inteira e permite confirmar equivalência exata com a candidata revisada.
  Date/Author: 2026-07-24 / Codex

## Risks and Mitigations

* Risk: O runner novo valida a si próprio.
  Mitigation: Baseline imutável, RED público contra o runner antigo, testes determinísticos, oráculos ocultos, replay histórico e fresh agent.

* Risk: Oráculos mecânicos ensinam a resposta ao executor.
  Mitigation: Armazená los fora da fixture pública e nunca copiar seus arquivos para o workspace.

* Risk: Regressões são executadas antes de confirmar estabilidade completa.
  Mitigation: A evidência final continua exigindo três GREENs; somente a ordem muda.

* Risk: `codex exec --json` muda de formato.
  Mitigation: Parser tolerante, `usage.complete`, campos anuláveis e testes com eventos desconhecidos.

* Risk: O diagnóstico continua depois de uma indisponibilidade externa.
  Mitigation: Interromper em subprocesso não iniciado, autenticação, quota ou outra falha de infraestrutura.

* Risk: Ledger corrompido ou atualizado concorrentemente.
  Mitigation: Lock de arquivo, escrita atômica, schema próprio e bloqueio conservador diante de inconsistência.

* Risk: Os valores esperados de 12 e 14 sessões mudam.
  Mitigation: O JSON emitido pelo plano é autoritativo; qualquer diferença exige atualizar este ExecPlan antes da aprovação.

* Risk: Mudanças preexistentes do usuário entram na promoção.
  Mitigation: O isolamento deriva do commit, o hash da regra ignorada foi registrado e a promoção será feita apenas por patches de arquivos revisados.

* Risk: Os diretórios originais de artefatos do processo anterior já foram removidos de `/tmp`.
  Mitigation: Usar somente o dossiê autossuficiente preservado como fonte de fatos, construir fixtures mínimas que reproduzam os defeitos documentados e distinguir explicitamente esse replay reconstruído de um replay byte a byte.

## Validation Strategy

1. Testes novos contra baseline para RED, sem modelos.
2. Testes unitários completos e validação estrutural após cada milestone.
3. Caso determinístico integrado com zero sessões.
4. Replay dos artefatos antigos contra os novos oráculos.
5. Um diagnóstico otimizado com orçamento explícito.
6. Um gate final otimizado.
7. Um fresh agent externo, contabilizado separadamente.
8. Validação canônica final sem repetir modelos.

Comandos finais:

    PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover \
      -s develop-skill-with-evals/scripts/tests -v

    python3 .system/skill-creator/scripts/quick_validate.py \
      ./develop-skill-with-evals

    python3 -m json.tool \
      develop-skill-with-evals/references/eval-plan.schema.json >/dev/null

    python3 -m json.tool \
      develop-skill-with-evals/references/eval-result.schema.json >/dev/null

    for file in develop-skill-with-evals/evals/cases/*/case.json; do
      python3 -m json.tool "$file" >/dev/null
    done

    git diff --check
    git status --short

## Rollout and Recovery

Antes da promoção, recuperar significa abandonar a candidata isolada. Depois da promoção, recuperar significa aplicar o inverso do patch revisado somente nos arquivos em escopo.

Não usar `git reset --hard`, não copiar a árvore candidata inteira e não tocar nas mudanças preexistentes do usuário.

## Lessons Learned

Registrar durante a execução:

* Sessões e tokens economizados pela nova ordem.
* Casos em que oráculos eliminaram juízes sem perda observável.
* Cobertura real dos eventos JSONL de uso.
* Diferença entre plano diagnostic e consumo real.
* Qualquer contrato que ainda dependa de interpretação semântica.
* Custo externo do fresh agent, que não aparece no ledger interno.

Constatações iniciais:

* O commit solicitado é o próprio `HEAD`, então o isolamento pode usar diretamente o objeto Git e excluir com segurança arquivos ignorados e caches.
* `.codex/rules/python.rules` não aparece em `git status --short` porque está ignorado; verificar apenas o status normal seria insuficiente para preservar essa mudança explicitamente.
* O milestone 1 terminou com os 36 testes esperados em 3,966 segundos. A validação estrutural passou e o hash da regra ignorada permaneceu `131bace21ec670a146faba07e7b6ff8cb428087770cb47a847159545d2717554`.
* O RED novo foi específico: seis contratos falharam por interfaces ausentes, fingerprints e ordem falharam por comportamento antigo, e nenhum modelo real foi invocado.
* Um teste legado tratava o crash acidental de um fake executor como RED. A classificação nova revelou que isso era infraestrutura; o fake foi corrigido para gerar uma falha contratual observável.
* O gate determinístico integrado confirmou baseline `FAIL`, três candidatas `PASS`, assinatura estável e zero sessões de modelo.
* A premissa de disponibilidade dos diretórios históricos não se confirmou. `/tmp/skill-eval-artifacts` está vazio e `/tmp/auditable-skill-eval-runtime.d1bn53` não existe mais; somente o dossiê autossuficiente permanece.
* Os cinco casos migrados continuam usando executor real, mas agora usam oráculos fora da fixture e nenhum juiz. Os três casos semanticamente menos estruturados continuam com juiz.
* O plano cross cutting autoritativo caiu de 22 para 14 sessões de promoção. O diagnóstico custa no máximo 12.
* A revisão pós GREEN encontrou uma reserva de campanha que poderia sobreviver a uma falha anterior à criação do workspace. A inicialização foi movida para o bloco protegido e um teste confirma que a reserva é liberada.
* O workflow principal completo, `plan diagnostic`, `probe-change`, `plan promotion` e `validate-change`, passou com executor fake e zero sessões internas.
* O plano diagnostic final seleciona `explicit-runtime-promotion-workflow` como caso afetado e dez regressões. O máximo é 12 sessões, com fingerprint de manifesto `6c04db3a830e1ed46b722111e3e088b53d7ee2a943be66b555da57eb527d5481` e evaluation fingerprint `93c91792aa1d2d3ee6ddc46d038ae076cee73ab1be40b8abf0a8b06a32d8ea7b`.
* O diagnóstico aprovado executou exatamente 12 sessões, sendo nove de executor e três de juiz. O ledger passou de zero para 12 sessões consumidas.
* A telemetria ficou completa: 3.423.851 tokens de entrada, 2.952.192 tokens de entrada em cache, 40.312 tokens de saída e 3.464.163 tokens totais.
* Todas as dez regressões passaram. Os cinco casos migrados para oráculos mecânicos usaram executor real e zero sessões de juiz.
* O caso principal falhou somente porque o oráculo exigia que o log inteiro contivesse exatamente quatro invocações. O executor consultou quatro interfaces com `--help` antes de executar, uma única vez cada, `plan diagnostic`, `probe-change`, `plan promotion` e `validate-change`. As quatro operações produziram artefatos válidos; portanto, consultas somente leitura não podem invalidar o contrato.
* O progresso anunciou incorretamente `running judge` nos casos com juiz desativado, embora a contabilidade correta tenha permanecido em zero. Esse defeito de observabilidade será corrigido junto com a regressão do diagnóstico.
* O oráculo principal agora ignora consultas `--help` ao selecionar as quatro operações exigidas, mas continua proibindo `run`, `verify-change`, `stability` e `--all` em qualquer invocação. Ele passou diretamente contra o workspace preservado do diagnóstico.
* A emissão de progresso agora anuncia o juiz somente quando `judge.enabled` é verdadeiro. A suíte passou a 48 testes e o gate integrado repetiu baseline `FAIL` e três candidatas `PASS` com zero sessões.
* O plano promotion atual exige no máximo 14 sessões, sendo 11 de executor e três de juiz. Com as 12 já consumidas, a projeção cumulativa é exatamente 26. O novo evaluation fingerprint é `d1a76f01c4ecb2ba9963f2207c347256ef828cae5a323d371fdc54ee59bdcd55`.
* `plan` preserva o limite seguro padrão de oito sessões para sinalizar `approval_required` e o bloqueio de orçamento. Depois da aprovação explícita, `validate-change` deve receber `--approved-model-sessions 14`; a consulta de plano em si não aceita essa opção.
* A primeira tentativa de promoção produziu o RED esperado, GREEN 1 e seis regressões válidas antes de encontrar a fixture determinística desatualizada de `runner-progress-output`. Ela consumiu 11 sessões, sendo oito de executor e três de juiz, e parou antes de GREEN 2 e 3.
* O uso da tentativa ficou completo: 4.130.979 tokens de entrada, 3.686.656 tokens de entrada em cache, 39.891 tokens de saída e 4.170.870 tokens totais. O ledger cumulativo passou de 12 para 23 sessões.
* A nova ordem economizou duas sessões de executor nessa falha, pois as repetições candidatas 2 e 3 não ocorreram antes da regressão problemática.
* A falha determinística era uma expectativa obsoleta da própria avaliação: `runner-progress-output` ainda exigia `running judge` para um juiz desativado. A fixture foi alinhada, um teste passou a executar esse caso integrado com zero sessões e a suíte chegou a 49 testes verdes.
* Após a correção, validação estrutural, schemas, `git diff --check` e o hash da regra ignorada passaram novamente. O novo plano preserva 14 sessões máximas, projeta 37 cumulativas a partir das 23 já consumidas e tem evaluation fingerprint `218719f3bdd654adfc5a01b9d1467bba46ac6a2c88b896946d859d9d13b0d7ac`.
* A segunda tentativa consumiu as 14 sessões previstas, sendo 11 de executor e três de juiz. O uso ficou completo: 5.361.314 tokens de entrada, 4.832.512 tokens de entrada em cache, 50.919 tokens de saída e 5.412.233 tokens totais. O ledger chegou a 37.
* O gate produziu baseline RED, dez regressões PASS e três candidatas funcionalmente PASS. A promoção ficou `UNSTABLE` porque somente GREEN 2 atualizou a descrição opcional em `sample-skill/SKILL.md`; GREEN 1 e 3 limitaram a mudança ao renderer e aos artefatos exigidos.
* A assinatura de estabilidade considera corretamente arquivos de resultado. A avaliação, porém, não declarava que `sample-skill/SKILL.md` estava fora do escopo, permitindo duas soluções aceitáveis com assinaturas diferentes.
* O caso principal agora instrui alteração exclusiva de `sample-skill/scripts/render.py` e proíbe mecanicamente `sample-skill/SKILL.md`. A suíte chegou a 50 testes verdes e as validações estruturais passaram.
* O terceiro plano continua exigindo no máximo 14 sessões, mas a campanha já consumiu 37 e chegaria a no máximo 51. Seu evaluation fingerprint é `20f409aee802399f256d4e88c72a5a2fc4c8b01a63faca48272251ae8c82f52b`.
* A campanha já excedeu o objetivo inicial de 26 sessões devido a dois defeitos descobertos nos gates reais. Como reutilização é explicitamente excluída, obter um gate final PASS exige nova execução completa ou uma mudança de decisão do usuário.
* A terceira execução confirmou baseline RED, GREEN 1 e três regressões PASS antes de o juiz de `non-behavioral-no-artificial-red` encerrar com código 1. O runner classificou corretamente `failure_category: infrastructure` e interrompeu a campanha imediatamente.
* O executor e os checkers do caso problemático passaram. O processo do juiz não produziu resposta, stderr útil nem evento de uso completo; por isso os tokens agregados da operação permanecem `null` e `usage.complete: false`, nunca zero.
* A terceira execução consumiu oito sessões, sendo seis de executor e duas de juiz. O ledger passou de 37 para 45 e ficou sem reservas ativas. A candidata não foi promovida e a fonte canônica permanece intocada.
* O plano gerado após nova autorização mantém exatamente os mesmos fingerprints da candidata e avaliação: `20f409aee802399f256d4e88c72a5a2fc4c8b01a63faca48272251ae8c82f52b`. Ele exige no máximo 14 sessões adicionais e projeta 59 cumulativas.
* A quarta tentativa confirmou baseline RED e GREEN 1, mas `load-skill-creator-first` falhou porque o executor criou `weather-brief/creation-evidence.json`; o required path e o oráculo exigem `creation-evidence.json` na raiz do workspace. O prompt dizia apenas “em creation-evidence.json” e permitia ambas as interpretações.
* A tentativa consumiu três sessões de executor, com uso completo de 1.255.381 tokens de entrada, 1.108.480 tokens de entrada em cache, 15.985 tokens de saída e 1.271.366 tokens totais. O ledger passou de 45 para 48.
* A ordem otimizada evitou as 11 sessões restantes ao parar na primeira regressão problemática.
* O prompt agora exige `./creation-evidence.json` na raiz do workspace, fora de `./weather-brief`, e o manifesto proíbe explicitamente `weather-brief/creation-evidence.json`. Os outros oráculos migrados já tinham localizações explícitas.
* A suíte chegou a 51 testes verdes; validação estrutural, schemas, `git diff --check` e o hash da regra ignorada passaram.
* O novo plano exige no máximo 14 sessões, parte de 48 consumidas e projeta 62 cumulativas. Seu evaluation fingerprint é `6d9239838e0900183b68d4b5a0c65815b5efe6307fe21842bf44fe26b548611c`.
* O gate final passou com 14 sessões, sendo 11 de executor e três de juiz. Produziu baseline RED, GREEN 1, dez regressões PASS, GREEN 2 e GREEN 3 com assinaturas estáveis; `promotion_eligible` ficou verdadeiro.
* O uso final ficou completo: 5.357.883 tokens de entrada, 4.866.816 tokens de entrada em cache, 53.241 tokens de saída e 5.411.124 tokens totais. O ledger chegou exatamente a 62 sessões, sem reservas ativas.
* O custo real da campanha superou o objetivo inicial de 26 porque os gates encontraram três ambiguidades contratuais e uma falha externa. A nova ordem evitou duas sessões na primeira falha de regressão, 11 sessões na segunda e 11 sessões na quarta tentativa.
* O fresh agent recebeu apenas a candidata e a tarefa em `/tmp/cost-efficient-skill-evals-fresh.MbjF45`. Ele preservou uma baseline, criou uma candidata isolada, demonstrou baseline RED e três GREENs estáveis, promoveu uma árvore byte a byte equivalente e consumiu zero sessões aninhadas.
* Na tarefa realista, o fresh agent adicionou uma avaliação determinística para normalização de identificadores, passou schemas e `quick_validate.py`, e confirmou diretamente que `"  Demo-ID  "` produz `demo-id`.
* A fonte canônica permaneceu igual à baseline até a promoção. Foram promovidos 30 arquivos individualmente e removidos três checkers públicos, recuperáveis pelo Git, que agora existem somente sob `oracle/`.
* A promoção ficou byte a byte equivalente à candidata, excluindo caches Python preexistentes. A validação canônica final passou com 51 testes, `quick_validate.py`, todos os schemas e manifests JSON e `git diff --check`.
* O ledger final registrou 62 sessões. Isso ficou 36 acima da meta ideal de 26, mas 30 abaixo das 92 sessões do processo anterior. O gate final limpo consumiu 14 contra 21 no gate final anterior.
* A nova ordem evitou 24 sessões adicionais nas tentativas que pararam cedo: duas antes da regressão determinística desatualizada e 11 em cada uma de duas regressões subsequentes.
* Os cinco oráculos removeram oito sessões de juiz do plano cross cutting sem perder os defeitos observados. Os três casos menos estruturados mantiveram juiz e passaram no gate final.
* A soma dos relatórios com telemetria completa é pelo menos 19.729.756 tokens totais. Uma operação encerrada por falha de infraestrutura manteve uso agregado desconhecido, então não existe total exato da campanha e nenhum valor ausente foi convertido em zero.
* O fresh agent custou uma sessão externa ao ledger interno e nenhuma sessão aninhada.

## Assumptions

* O commit `3a358b16` permanece como baseline canônica.
* Os artefatos do dossiê continuam disponíveis para replay somente leitura.
* Sol e Terra permanecem iguais nesta entrega; nenhuma economia será atribuída a preço sem evidência.
* Aprovações de modelo serão solicitadas somente quando os planos otimizados estiverem disponíveis.
