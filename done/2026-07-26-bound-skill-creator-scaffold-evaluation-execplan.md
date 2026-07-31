# Limitar a avaliação do scaffold criado com skill-creator

## Purpose / Big Picture

O caso atual `load-skill-creator-first` deveria verificar uma obrigação estreita: antes de criar arquivos de uma nova skill, o agente carrega o `SKILL.md` oficial de `skill-creator` e usa o `init_skill.py` oficial para gerar o scaffold.

Hoje o prompt também pede uma skill funcional chamada `weather-brief`. Ao combinar isso com as instruções gerais de `develop-skill-with-evals`, o executor interpreta a tarefa como desenvolvimento completo: cria baseline, avaliações, oracles, executa RED e GREEN e pode tentar validação com fresh agent. Esse trabalho não é necessário para provar a obrigação que dá nome ao caso. Em execuções arquivadas com Sol `medium`, o caso consumiu de 287 a 405 segundos, com mediana de 344 segundos. O judge estava desativado e o oracle consumiu apenas milissegundos, portanto quase todo o tempo foi gasto pelo executor ampliando o escopo.

Depois desta mudança, a avaliação terminará assim que o scaffold oficial e a evidência externa forem criados. Uma execução bem sucedida deverá deixar somente os arquivos produzidos pelo inicializador oficial e `creation-evidence.json` na raiz do workspace. Ela não deverá personalizar a skill, criar avaliações, preparar baseline, executar outro Codex ou iniciar fresh agents.

O nome da skill `develop-skill-with-evals` será preservado. Essa skill realmente cobre um fluxo amplo e seu nome descreve esse propósito. O caso será renomeado para `load-skill-creator-before-scaffold`, pois seu contrato mudará de uma criação completa ambígua para uma verificação de ordenação e scaffold. O novo ID também impede que relatórios antigos e novos sejam comparados como observações equivalentes.

## Scope

### Incluído

- Renomear o diretório e o ID do caso de `load-skill-creator-first` para `load-skill-creator-before-scaffold`.
- Reescrever o prompt para pedir apenas o scaffold oficial e a evidência externa.
- Tornar explícito que o executor deve parar depois do scaffold e não deve criar avaliações, baseline, candidate, relatórios, sessões Codex aninhadas ou fresh agents.
- Fortalecer o contrato mecânico para aceitar somente os arquivos esperados do scaffold.
- Substituir falhas genéricas por mensagens específicas no oracle.
- Criar testes determinísticos para o prompt, o manifesto e cada classe relevante de falha do oracle.
- Executar uma única observação com Sol `medium`, sem judge, após aprovação explícita do usuário.
- Comparar duração e tokens dessa observação com os relatórios arquivados, sem transformar tempo de modelo em gate determinístico.

### Excluído

- Renomear `develop-skill-with-evals`.
- Alterar o fluxo geral que `develop-skill-with-evals` prescreve para tarefas reais de criação de skills.
- Criar um novo teste completo de ponta a ponta para criação de skill.
- Alterar outros casos que já verificam RED e GREEN, isolamento de baseline, promoção, regressões ou fresh agent.
- Adicionar timeout geral ao runner. Isso é uma mudança separada de política e pode interromper tarefas legítimas.
- Alterar modelos ou esforços padrão do runner.
- Executar judge.
- Migrar ou reescrever relatórios históricos.
- Fazer `git add`, commit, push ou publicar qualquer arquivo.

## Definitions

**Scaffold oficial** é o conjunto inicial de arquivos produzido por `.system/skill-creator/scripts/init_skill.py` sem personalização posterior. Para este caso, são esperados `weather-brief/SKILL.md` e `weather-brief/agents/openai.yaml`.

**Evidência externa** é `creation-evidence.json`, mantido na raiz do workspace e fora de `weather-brief`. Ele registra o caminho do `SKILL.md` de `skill-creator` carregado e o argv usado para invocar o inicializador.

**Oracle oculto** é o script determinístico em `oracle/` que inspeciona o workspace depois da sessão. Seus critérios não são copiados integralmente para o prompt, mas o comportamento solicitado ao executor deve ser claro.

**Observação com modelo** é uma execução exploratória de uma sessão Codex usada para verificar se o prompt produz o comportamento desejado. Ela não é uma promoção completa nem substitui os gates determinísticos.

**Contrato antigo** é o caso `load-skill-creator-first`, cujo prompt solicita uma skill funcional e pode provocar o fluxo completo de desenvolvimento.

**Contrato novo** é o caso `load-skill-creator-before-scaffold`, que solicita somente o carregamento de `skill-creator`, o scaffold oficial e a evidência externa.

## Existing Context

O caso atual está nestes arquivos:

- `develop-skill-with-evals/evals/cases/load-skill-creator-first/case.json`
- `develop-skill-with-evals/evals/cases/load-skill-creator-first/prompt.md`
- `develop-skill-with-evals/evals/cases/load-skill-creator-first/oracle/check_creation_evidence.py`

O prompt atual começa com “Create a small skill”. Isso ativa corretamente o fluxo amplo documentado em `develop-skill-with-evals/SKILL.md`: inicialização oficial, baseline congelada, casos focados, RED, três GREEN, regressões e fresh agent. A demora não decorre de um judge nem do oracle.

O oracle atual valida o arquivo de evidência, o caminho de `skill-creator`, o argv do inicializador e a existência de `weather-brief/SKILL.md`. Ele usa `assert`, então falhas podem aparecer apenas como `AssertionError`, sem dizer qual obrigação foi violada. O arquivo de evidência é uma declaração do executor. Ele melhora a auditabilidade, mas não prova que o processo realmente leu o arquivo ou executou exatamente aquele argv.

Casos existentes já separam as outras responsabilidades:

- `eval-before-behavior` verifica RED antes da implementação e GREEN posterior.
- `self-evolution-candidate` verifica isolamento entre baseline e candidate.
- `impact-gate-selection` verifica planejamento de gates.
- `explicit-runtime-promotion-workflow` verifica diagnóstico e promoção com runtime explícito.
- `full-regression-gate` verifica bloqueio por regressão.

O histórico mostra que o caso começou como uma verificação de carregamento e scaffold. A evidência externa e o oracle oculto foram acrescentados depois para reduzir custo e aumentar auditabilidade. Não há indicação de que a expansão para um fluxo completo tenha sido uma decisão deliberada.

O runner usa `subprocess.run` sem timeout ao invocar `codex exec`. Esse fato explica por que uma interpretação ampla pode continuar por vários minutos, mas não é a causa primária. A causa primária é o contrato ambíguo do caso.

## Desired End State

O diretório do caso será:

```text
develop-skill-with-evals/evals/cases/load-skill-creator-before-scaffold/
├── case.json
├── prompt.md
└── oracle/
    └── check_creation_evidence.py
```

O `case.json` terá `id` igual a `load-skill-creator-before-scaffold`.

Os caminhos obrigatórios serão:

- `creation-evidence.json`
- `weather-brief/SKILL.md`
- `weather-brief/agents/openai.yaml`

Os caminhos proibidos incluirão:

- `.agents/skills/**`
- `weather-brief/creation-evidence.json`
- `weather-brief/evals/**`
- `weather-brief/scripts/**`
- `weather-brief/references/**`
- `weather-brief/assets/**`
- `baseline/**`
- `candidate/**`
- `eval-reports/**`
- `evaluation-reports/**`

O prompt deverá declarar, em linguagem direta:

1. Carregue o `SKILL.md` oficial de `skill-creator` antes de criar ou editar arquivos de `weather-brief`.
2. Execute o `init_skill.py` oficial para gerar `./weather-brief`.
3. Grave o caminho carregado e o argv exato em `./creation-evidence.json`.
4. Não personalize o scaffold gerado.
5. Não crie avaliações, baseline, candidate, relatórios ou recursos adicionais.
6. Não execute Codex aninhado, subagents ou fresh agents.
7. Pare assim que o scaffold e a evidência existirem.
8. Não faça commit.

O oracle deverá encerrar com uma mensagem específica para cada falha. Exemplos:

```text
creation-evidence.json is missing from the workspace root
creation-evidence.json must contain a JSON object
skill_creator_path must point to .system/skill-creator/SKILL.md
scaffold_argv must be a non-empty list of strings
scaffold_argv must invoke the official init_skill.py
scaffold_argv must include weather-brief
scaffold_argv must include --path followed by a destination
weather-brief/SKILL.md was not generated
weather-brief/agents/openai.yaml was not generated
weather-brief/SKILL.md no longer looks like the untouched official scaffold
```

O oracle verificará que `weather-brief/SKILL.md` ainda contém marcadores `[TODO:` do scaffold oficial. Essa verificação demonstra que o executor parou antes da personalização. Ela não deverá depender do texto completo do template, para não quebrar com mudanças pequenas no gerador.

Os relatórios históricos conservarão `load-skill-creator-first`. Novas execuções usarão `load-skill-creator-before-scaffold`. Comparadores não deverão unir os dois IDs.

## Milestones

### Milestone 1: Fixar o contrato determinístico antes da implementação

#### Changes

Criar `develop-skill-with-evals/scripts/tests/test_load_skill_creator_contract.py`.

O teste deverá localizar o caso pelo novo ID esperado e inicialmente falhar contra a baseline imutável. Ele verificará:

- ID e diretório novos;
- termos de parada e exclusões explícitas no prompt;
- caminhos obrigatórios e proibidos no `case.json`;
- mensagens específicas do oracle;
- sucesso do oracle com um scaffold mínimo válido;
- falha individual para evidência ausente, JSON inválido, caminho incorreto, argv inválido, inicializador incorreto, ausência de `--path`, scaffold ausente e scaffold já personalizado.

O teste usará `tempfile.TemporaryDirectory`, criará apenas arquivos mínimos e invocará o oracle por subprocesso. Ele não chamará Codex.

Criar uma cópia imutável da baseline em um diretório temporário antes de editar o caso. Não usar a árvore de trabalho como baseline.

#### Validation

Executar o teste novo contra a baseline. O resultado esperado é RED porque o novo diretório, as restrições do prompt e as mensagens específicas ainda não existem.

Registrar no próprio ExecPlan o comando, o código de saída e a causa observada do RED.

#### Acceptance

O RED será válido somente se a falha corresponder ao contrato novo ausente. Erro de import, caminho temporário incorreto ou fixture quebrada não contará como RED comportamental.

### Milestone 2: Limitar e renomear o caso

#### Changes

Mover:

```text
develop-skill-with-evals/evals/cases/load-skill-creator-first/
```

para:

```text
develop-skill-with-evals/evals/cases/load-skill-creator-before-scaffold/
```

Atualizar o `id` em `case.json`, os caminhos mecânicos, o prompt e o oracle conforme o estado desejado.

Substituir `assert` por uma função explícita, por exemplo:

```python
def fail(message):
  raise SystemExit(message)
```

Capturar separadamente arquivo ausente, erro de decodificação JSON e tipo JSON inválido. Não imprimir stack trace para falhas contratuais esperadas.

Atualizar referências determinísticas ao ID antigo em `develop-skill-with-evals/scripts/tests/`. Não alterar relatórios arquivados.

#### Validation

Executar o teste focado três vezes em processos separados:

```bash
PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover \
  -s develop-skill-with-evals/scripts/tests \
  -p 'test_load_skill_creator_contract.py' -v
```

As três execuções devem ficar GREEN e produzir resultados equivalentes.

Em seguida, executar a suíte determinística completa:

```bash
PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover \
  -s develop-skill-with-evals/scripts/tests -v
```

Validar a estrutura da skill:

```bash
PYTHONDONTWRITEBYTECODE=1 python3 \
  .system/skill-creator/scripts/quick_validate.py \
  ./develop-skill-with-evals
```

Validar whitespace:

```bash
git diff --check
```

#### Acceptance

O teste focado passa três vezes. A suíte completa passa. `quick_validate.py` passa. `git diff --check` não encontra erros. Uma busca pelo ID antigo fora de relatórios históricos não encontra referências executáveis restantes.

### Milestone 3: Revisar o comportamento sem gastar sessões

#### Changes

Não há mudança de código nesta etapa. Revisar manualmente o diff e simular o oracle apenas com fixtures determinísticas.

Confirmar que o contrato não exige uma skill funcional. Confirmar que os arquivos proibidos representam trabalho fora do escopo e não arquivos normais do inicializador oficial.

#### Validation

Inspecionar:

```bash
git diff -- \
  develop-skill-with-evals/evals/cases \
  develop-skill-with-evals/scripts/tests
```

Buscar termos que reintroduzam desenvolvimento completo:

```bash
rg -n \
  'baseline|candidate|fresh agent|subagent|evals|evaluation-reports|eval-reports' \
  develop-skill-with-evals/evals/cases/load-skill-creator-before-scaffold
```

A ocorrência desses termos no prompt deverá ser proibitiva, não instrutiva.

#### Acceptance

Uma pessoa que leia apenas o prompt entende quando deve parar. Uma falha do oracle identifica diretamente a obrigação violada. Nenhuma sessão de modelo foi consumida até este ponto.

### Milestone 4: Fazer uma única observação com Sol medium

#### Changes

Esta etapa só será executada após aprovação explícita do usuário para consumir uma sessão real. Não haverá judge.

Primeiro executar o planejamento sem sessões:

```bash
PYTHONDONTWRITEBYTECODE=1 python3 \
  develop-skill-with-evals/scripts/run_skill_evals.py plan \
  --skill ./develop-skill-with-evals \
  --baseline /tmp/narrow-load-skill-creator-baseline/develop-skill-with-evals \
  --impact scoped \
  --case load-skill-creator-before-scaffold \
  --workflow diagnostic \
  --model gpt-5.6-sol \
  --reasoning-effort medium
```

O plano diagnóstico poderá projetar mais sessões do que a observação autorizada. Ele será usado apenas para verificar fingerprint, runtime e custo máximo. A execução autorizada continuará limitada a um único `run`.

Depois da aprovação, executar:

```bash
PYTHONDONTWRITEBYTECODE=1 python3 \
  develop-skill-with-evals/scripts/run_skill_evals.py run \
  --skill ./develop-skill-with-evals \
  --case load-skill-creator-before-scaffold \
  --source working-tree \
  --model gpt-5.6-sol \
  --reasoning-effort medium \
  --artifacts-dir /tmp/narrow-load-skill-creator-forward-artifacts \
  --no-report \
  --progress
```

Essa execução consome uma sessão de executor, zero sessões de judge e não dispara sessões adicionais por meio do runner.

#### Validation

Confirmar no resultado:

- estado `pass`;
- judge desativado;
- uma sessão consumida;
- oracle aprovado;
- somente `creation-evidence.json`, `weather-brief/SKILL.md` e `weather-brief/agents/openai.yaml` como artefatos relevantes;
- ausência de evals, baseline, candidate, relatórios e chamadas Codex aninhadas observáveis;
- telemetria de tokens e duração registrada nos artefatos temporários.

Comparar a duração com a mediana histórica de 344 segundos e os tokens com as observações arquivadas. Essa comparação é direcional. Uma execução isolada não prova estabilidade de latência.

#### Acceptance

O contrato funcional passa em uma sessão. Se o agente ainda executar o fluxo completo, a mudança será considerada insuficiente mesmo que o oracle passe. Se a duração não melhorar, não declarar ganho de desempenho sem investigar a transcrição estruturada permitida e os artefatos sanitizados.

### Milestone 5: Concluir a revisão

#### Changes

Atualizar as seções Progress, Decisions, Risks and Mitigations e Lessons Learned deste ExecPlan com os resultados reais.

Revisar todo conteúdo novo para credenciais, dados pessoais, respostas completas de modelo e caminhos temporários acidentais. Não persistir JSONL bruto ou transcrições completas.

#### Validation

Executar novamente:

```bash
PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover \
  -s develop-skill-with-evals/scripts/tests -v
PYTHONDONTWRITEBYTECODE=1 python3 \
  .system/skill-creator/scripts/quick_validate.py \
  ./develop-skill-with-evals
git diff --check
git status --short
```

#### Acceptance

Todos os gates determinísticos passam, a única observação autorizada está reconciliada, o diff contém apenas mudanças previstas e nenhum arquivo foi staged, commitado ou enviado.

## Progress

- [x] Auditar o propósito, o prompt, o manifesto e o oracle atuais.
- [x] Identificar os casos que já cobrem RED, GREEN, isolamento, promoção e regressões.
- [x] Reconciliar a demora com relatórios arquivados e confirmar que o executor é o custo dominante.
- [x] Decidir preservar o nome `develop-skill-with-evals`.
- [x] Escrever este ExecPlan.
- [x] Criar baseline imutável temporária em `/tmp/narrow-load-skill-creator-baseline/develop-skill-with-evals` e remover permissões de escrita.
- [x] Escrever o teste determinístico novo antes da implementação.
- [x] Demonstrar baseline RED pela causa esperada. O comando focado com `SKILL_UNDER_TEST=/tmp/narrow-load-skill-creator-baseline/develop-skill-with-evals` encerrou com código `1`; a única causa foi a ausência esperada de `evals/cases/load-skill-creator-before-scaffold`.
- [x] Renomear e limitar o caso.
- [x] Implementar mensagens específicas no oracle.
- [x] Obter três GREEN determinísticos estáveis. As três execuções separadas passaram os mesmos 16 testes.
- [x] Executar a suíte completa e `quick_validate.py`. A suíte passou 82 testes; `quick_validate.py` informou `Skill is valid!`; `git diff --check` passou.
- [x] Revisar o diff e os padrões sensíveis. Todos os termos de fluxo amplo no caso novo são proibições; não restam referências executáveis ao ID antigo fora de relatórios históricos; nenhuma sessão foi consumida.
- [x] Pedir aprovação para uma sessão Sol `medium`. O usuário aprovou explicitamente em 2026-07-26. O plano sem sessões passou sem blockers, com runtime fingerprint `ac60f718c5b1f8ec9e68aab13ceb3015d1c31ee4ac743c6cbc52bdd91b88ce43`; a execução diagnóstica completa projetaria duas sessões, mas o comando autorizado continuará sendo um único `run`.
- [x] Executar uma única observação comportamental, sem judge. A primeira invocação contabilizada encerrou em `ERROR` de infraestrutura após 181 ms, antes do modelo. Depois de nova aprovação explícita, o mesmo comando fora do sandbox passou em 58.468 segundos com um executor, zero judges e oracle aprovado.
- [x] Reconciliar comportamento, duração e tokens. A observação aprovada produziu somente os três arquivos esperados, consumiu 175.055 tokens de entrada, 1.912 de saída e 176.967 no total. Contra seis PASS históricos Sol `medium` do contrato antigo, a duração caiu de uma mediana de aproximadamente 344.458 para 58.468 segundos e o total de tokens caiu de uma mediana de aproximadamente 957.438 para 176.967. Essa é uma comparação direcional de uma observação, não evidência de estabilidade.
- [x] Fazer a validação final e atualizar este documento. A suíte final passou 82 testes em 11.680 segundos; `quick_validate.py` passou; `git diff --check` passou; as buscas por ID antigo executável e padrões de credenciais não encontraram ocorrências.

## Decisions

### 2026-07-26: Preservar o nome da skill

`develop-skill-with-evals` descreve corretamente a capacidade ampla da skill. A confusão está no caso, não no produto principal. Renomear a skill seria uma migração grande e não reduziria o trabalho do executor neste prompt.

### 2026-07-26: Renomear o caso

O caso passará a se chamar `load-skill-creator-before-scaffold`. Além de deixar a ordem observada mais clara, um ID novo impede que relatórios do contrato antigo sejam agregados aos do contrato limitado. O histórico permanece disponível pelo ID antigo.

### 2026-07-26: Não criar outro caso completo agora

As partes do fluxo completo já têm casos focados. Um novo teste de ponta a ponta acrescentaria custo alto e sobreposição sem uma falha específica ainda não coberta. Ele só deve ser proposto futuramente se houver um incidente que atravesse componentes e não possa ser detectado pelos casos existentes.

### 2026-07-26: Tratar a implementação como manutenção determinística do contrato

As mudanças de arquivos, mensagens e exclusões podem ser verificadas sem modelo. O comportamento do prompt receberá uma observação única depois dos gates determinísticos. Não será alegada promoção comportamental completa com base nessa sessão.

### 2026-07-26: Não adicionar timeout ao runner

Um timeout esconderia a ambiguidade do caso e poderia interromper avaliações legítimas. Primeiro o contrato será limitado. Uma política de timeout exigiria requisitos próprios, telemetria histórica e recuperação de artefatos.

### 2026-07-26: Manter a evidência externa com uma ressalva explícita

`creation-evidence.json` melhora a inspeção e permite validação mecânica. Ele continua sendo evidência declarada pelo executor, não prova independente de leitura ou execução. O plano não apresentará essa evidência como garantia de processo.

### 2026-07-26: Usar no máximo uma sessão Sol medium

Os gates de desenvolvimento serão determinísticos. A única sessão servirá para observar se um agente real respeita o novo limite. Judge, repetição de estabilidade com modelo e fresh agent ficam fora do escopo.

### 2026-07-26: Permitir que o teste focado selecione a raiz da skill

O teste novo usará `SKILL_UNDER_TEST` somente como entrada opcional de teste. Sem essa variável, ele verificará a árvore de trabalho. Com a variável apontando para a cópia imutável, o mesmo contrato demonstrará o RED sem editar ou contaminar a baseline.

### 2026-07-26: Não executar o comando diagnóstico projetado

O planejamento confirmou duas sessões para `probe-change`, uma na baseline e uma na candidate. Essa projeção é útil para auditar fingerprints e runtime, mas excede a observação única definida neste plano. A etapa com modelo usará somente `run --source working-tree`, que planeja e consome um executor e nenhum judge.

### 2026-07-26: Repetir somente após corrigir a infraestrutura e obter nova aprovação

A primeira invocação não alcançou o modelo porque o sandbox bloqueou a inicialização local do cliente. O comando não foi repetido no mesmo ambiente. Após uma segunda aprovação explícita, ele foi executado fora do sandbox sem mudar prompt, caso, modelo ou esforço. Essa segunda invocação produziu a única observação comportamental válida.

## Risks and Mitigations

### O prompt limitado pode conflitar com o fluxo geral da skill

As instruções de `develop-skill-with-evals` mandam aplicar o ciclo completo a uma criação real. A mitigação é declarar que esta tarefa é intencionalmente limitada a preparar e inspecionar o scaffold e que a personalização ocorrerá em uma tarefa futura. A instrução específica do usuário deve delimitar o trabalho.

### O mesmo caso poderia misturar resultados semanticamente diferentes

Manter `load-skill-creator-first` após reduzir seu contrato faria relatórios antigos e novos parecerem comparáveis. A mitigação é usar o ID novo `load-skill-creator-before-scaffold` e preservar os arquivos históricos sem reescrita.

### A verificação de `[TODO:` pode quebrar se o template oficial mudar

O scaffold atual inclui vários marcadores `[TODO:`. Se `skill-creator` mudar o template, o oracle poderá rejeitar uma inicialização válida. A mitigação é verificar apenas a presença genérica de pelo menos um marcador e manter um teste determinístico que revele imediatamente essa incompatibilidade.

### Caminhos proibidos podem ficar rígidos demais

Uma versão futura do inicializador pode criar recursos adicionais por padrão. A mitigação é proibir apenas categorias que representam trabalho além do scaffold solicitado e revisar o contrato quando o inicializador oficial mudar. Não se deve afrouxar o oracle silenciosamente.

### Uma sessão rápida não prova estabilidade

Latência de modelo varia. A mitigação é separar aceitação funcional de comparação de desempenho. O caso estará correto se respeitar o escopo. A alegação de redução de tempo será descrita como uma observação, não como garantia estatística.

### O executor pode declarar um argv que não executou

O arquivo de evidência é autorrelatado. A mitigação dentro deste escopo é combinar a declaração com os artefatos característicos do scaffold oficial e linguagem documental precisa. Prova de processo exigiria instrumentação do runner e é uma mudança futura, separada.

### O sandbox pode impedir a inicialização do executor antes do modelo

A primeira observação autorizada confirmou esse risco: `codex exec` encerrou em 181 ms com `Read-only file system` ao inicializar o cliente in-process. O runner contabilizou uma sessão, mas não recebeu resposta nem eventos de uso. Os artefatos foram preservados em `/tmp/narrow-load-skill-creator-forward-artifacts/run-8y7sl8qg`. A mitigação é alterar somente o ambiente de execução, repetindo o mesmo comando fora do sandbox após uma nova aprovação explícita; a falha de infraestrutura não justifica repetir silenciosamente.

### O diretório `_temporary` não é versionado

Este ExecPlan é um documento local de execução e não fará parte de uma contribuição normal. A mitigação é mantê-lo completo durante a implementação. Se o usuário quiser preservar o plano no Git, será necessária uma decisão explícita sobre um diretório versionável apropriado.

## Validation Strategy

A validação progride do mais barato e determinístico para o mais caro:

1. Novo teste focado em RED contra uma baseline imutável.
2. Três execuções GREEN do teste focado.
3. Suíte determinística completa.
4. Validação estrutural com `quick_validate.py`.
5. Revisão de diff, referências e padrões sensíveis.
6. Planejamento sem sessão para inspecionar fingerprint e custo.
7. Uma observação Sol `medium`, sem judge, após aprovação.
8. Reexecução dos gates determinísticos após a observação.

Não haverá comparação baseada em judge. O oracle é inteiramente mecânico. A aceitação não dependerá de texto persuasivo do executor.

Os principais critérios observáveis são:

- o agente para após o scaffold;
- nenhuma avaliação ou baseline é criada;
- nenhum Codex aninhado ou fresh agent é iniciado;
- o scaffold permanece não personalizado;
- erros identificam a obrigação violada;
- o novo ID separa relatórios futuros do contrato antigo.

## Rollout and Recovery

A mudança afeta somente o diretório do caso e testes determinísticos. Não há migração de dados em runtime.

O rollout local está concluído na árvore de trabalho, sem staging, commit, push ou publicação. A observação bem sucedida usou `--no-report`, portanto não criou relatório versionável nem alterou o arquivo histórico. Os artefatos da tentativa de infraestrutura que falhou permanecem recuperáveis em `/tmp/narrow-load-skill-creator-forward-artifacts/run-8y7sl8qg`; a workspace da observação bem sucedida foi removida automaticamente pelo runner após o PASS.

Antes de editar, copiar a versão atual de `develop-skill-with-evals` para um diretório temporário imutável. Essa cópia será usada somente para o RED e para consulta.

Se a mudança quebrar a suíte:

1. Identificar se a falha decorre do novo ID, do contrato de caminhos ou do oracle.
2. Corrigir apenas os arquivos do caso e os testes relacionados.
3. Não modificar outros casos para acomodar uma falha específica deste contrato.

Se a observação com modelo ainda executar o fluxo completo:

1. Preservar os artefatos sanitizados em `/tmp`.
2. Não repetir a sessão automaticamente.
3. Revisar o prompt e a precedência de instruções.
4. Pedir nova autorização antes de qualquer outra sessão.

Nenhum comando destrutivo será necessário. Nenhum relatório histórico será apagado ou alterado. Nenhum commit ou push faz parte do rollout.

## Lessons Learned

### 2026-07-26: O contrato estreito reduziu o trabalho observado sem depender de judge

Na observação válida, o executor parou após `creation-evidence.json`, `weather-brief/SKILL.md` e `weather-brief/agents/openai.yaml`. O oracle passou, o judge permaneceu desativado e nenhum artefato de desenvolvimento completo apareceu. A duração de 58.468 segundos e 176.967 tokens totais ficaram muito abaixo das medianas históricas do contrato antigo, mas uma amostra não demonstra estabilidade futura.

### 2026-07-26: Uma invocação contabilizada pode falhar antes de alcançar o modelo

O runner registrou um executor e zero judge, mas a duração de 181 ms, a ausência de resposta e a telemetria vazia mostram que a falha ocorreu na inicialização local do cliente. A contagem conservadora de sessões continua correta para orçamento, enquanto a evidência não pode ser tratada como observação comportamental.

### 2026-07-26: Mensagens contratuais devem ter uma representação única no oracle

A primeira execução candidata encontrou uma fragilidade no teste de presença das mensagens: uma mensagem correta em runtime estava dividida em literais adjacentes no código fonte. Centralizar a mensagem longa em uma constante preserva o texto exato tanto para quem executa o oracle quanto para o teste determinístico.

### 2026-07-26: O mesmo teste pode demonstrar o RED sem duplicar fixtures

Selecionar a raiz da skill por uma variável exclusiva de teste permitiu executar exatamente o contrato novo contra a baseline imutável. O RED foi direto: o diretório renomeado não existia. Nenhum erro incidental de import, caminho ou preparação mascarou a ausência do comportamento.

### 2026-07-26

Um nome estreito não limita sozinho uma avaliação. O prompt atual solicita um resultado amplo, e o agente segue corretamente o fluxo amplo da skill. O contrato observável precisa dizer tanto o que produzir quanto quando parar.

### 2026-07-26

Tempo alto de executor não deve ser tratado primeiro como problema de timeout. Quando o agente realiza trabalho coerente, mas fora do propósito do caso, o primeiro conserto é reduzir a ambiguidade do caso.

### 2026-07-26

Mensagens de oracle fazem parte da usabilidade da suíte. `AssertionError` informa que algo falhou, mas não orienta quem mantém o caso. Falhas determinísticas devem nomear a obrigação quebrada.

### 2026-07-26

IDs de casos também são identidades históricas. Se o significado muda de forma relevante, preservar o mesmo ID pode contaminar comparações mesmo que o novo nome pareça opcional.
