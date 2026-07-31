# Tornar a documentação de avaliações didática e auditável

Este ExecPlan é um documento vivo. Manter `Progress`, `Decisions`, `Risks and Mitigations` e `Lessons Learned` atualizados durante toda a execução.

## Purpose / Big Picture

Reorganizar a documentação pública de avaliações para que uma pessoa que já conhece Codex skills, mas ainda não conhece este sistema de evals, consiga entender como uma mudança é provada e como supervisionar a evidência produzida por `develop-skill-with-evals`.

O resultado será observável ao ler `README.md` e `EVALUATIONS.md` em sequência: o primeiro introduzirá o que é uma Codex skill, e o segundo apresentará uma avaliação real antes de detalhes de contrato, definirá cada conceito antes do primeiro uso e distinguirá claramente automação, evidência determinística, julgamento semântico e decisões humanas.

Safety boundary: This task is limited to authorized, defensive maintenance of this repository. Do not provide offensive guidance or policy bypassing instructions.

## Scope

Incluído:

* adicionar uma introdução curta sobre Codex skills em `README.md`;
* reestruturar `EVALUATIONS.md` como guia didático em camadas;
* explicar o que `develop-skill-with-evals` automatiza e o que a pessoa ainda precisa supervisionar;
* usar o caso real `impact-gate-selection` como exemplo inicial de ponta a ponta;
* definir prompt, fixture, verificações mecânicas, oracle, judge, baseline, candidate, RED, GREEN, gate, impacto, promoção, diagnóstico, fingerprint e campaign antes que esses termos sejam exigidos do leitor;
* corrigir a distinção entre casos semânticos e determinísticos;
* separar o papel conceitual de `EVALUATIONS.md` das receitas de `CODEX_CLI.md` e do contrato normativo em `develop-skill-with-evals/references/eval-contract.md`;
* preservar headings úteis como anchors quando seu conteúdo continuar válido e atualizar links locais afetados;
* manter detalhes suficientes para compreender e supervisionar o sistema, sem impor uma meta artificial de palavras.

Excluído:

* alterar `CODEX_CLI.md`, o runner, schemas, manifests, casos de avaliação, skills ou metadados;
* mudar comportamento, política econômica, limites de sessão, formatos JSON ou contratos normativos;
* criar um tutorial completo de autoria manual de casos;
* traduzir a documentação para português;
* executar avaliações model backed, RED, GREEN ou regressões;
* modificar, remover ou incorporar relatórios e caches já presentes no worktree;
* stage, commit, push ou publicação.

## Definitions

`Codex skill` é um workflow reutilizável composto por um diretório com `SKILL.md` e recursos opcionais, como referências e scripts. O Codex conhece inicialmente o nome e a descrição e carrega as instruções completas quando seleciona a skill.

`Eval` ou avaliação é uma execução controlada que coleta evidência sobre o comportamento de uma skill. Ela complementa a validação estrutural, que prova somente que a skill está bem formada.

`Executor` é uma sessão fresca e efêmera do Codex que recebe uma tarefa realista e, em casos semânticos, realiza o trabalho dentro de um workspace isolado.

`Prompt` é a solicitação pública apresentada ao executor. Ele não pode conter resposta esperada, diagnóstico, critérios do judge ou conteúdo do oracle.

`Fixture` é o estado inicial público e mínimo copiado para o workspace isolado. Pode conter código, testes, configurações e dados genéricos necessários para reproduzir o cenário.

`Verificações mecânicas` são observações genéricas declaradas no manifest do caso, como exit code, presença ou ausência de caminhos, arquivos alterados e comandos de validação.

`Oracle` é um verificador determinístico específico do caso, armazenado sob `oracle/` e executado pelo runner fora do workspace visível ao executor. Ele existe para verificar o contrato observável sem revelar a resposta esperada, com resultado repetível e sem sessão adicional de judge.

`Judge` é uma sessão separada do Codex que interpreta critérios semânticos ocultos quando código não consegue decidir o contrato completo. Ele não é requisito de todo caso semântico.

`Caso semântico` precisa de executor para realizar uma tarefa aberta. Seus resultados podem ser decididos por um oracle completo com judge desabilitado ou, quando interpretação continua necessária, por judge.

`Caso determinístico` não precisa de executor nem judge, pois código consegue executar e observar o contrato completo diretamente.

`Baseline` é a skill preservada antes da mudança. `Candidate` é a versão proposta. Uma avaliação comportamental confiável demonstra RED, ou falha esperada, na baseline e GREEN, ou sucesso esperado, na candidate.

`Gate` é um checkpoint obrigatório de evidência antes de uma candidate poder ser promovida. `Promoção` é o workflow que exige RED válido, três GREEN estáveis e regressão proporcional ao impacto. `Diagnóstico` é uma execução única para entender problemas e nunca torna uma candidate elegível para promoção.

`Fingerprint` é um hash que vincula manifests, casos, fontes, runtime e seleção ao plano aprovado. Ele detecta mudanças entre planejamento e execução.

`Campaign` é o conjunto auditável de operações relacionadas, como diagnóstico e promoção, que podem compartilhar um ledger e um limite cumulativo de sessões.

`Supervisão` significa revisar se a avaliação observa o comportamento pretendido, se o oracle não testa detalhes de implementação nem vaza a resposta, se o impacto está correto, se o custo foi autorizado e se a evidência permite promoção.

## Existing Context

A fonte está em `/home/renanfranca/.codex/skills`, no commit `8da0b50e2475a99296eb10a7ec7d32525eb14d8e`.

Os documentos públicos relevantes são:

* `README.md`, com catálogo, instalação e links para os demais guias;
* `EVALUATIONS.md`, com 825 linhas e aproximadamente 6.094 palavras;
* `CODEX_CLI.md`, cookbook de comandos e fluxos operacionais;
* `develop-skill-with-evals/references/eval-contract.md`, contrato normativo do runner;
* `develop-skill-with-evals/references/eval-plan.schema.json` e `eval-result.schema.json`, formatos normativos de plano e resultado.

O `README.md` afirma que o repositório contém skills e ensina instalação e invocação, mas não oferece uma introdução suficiente ao conceito de skill. A documentação oficial define skill como um workflow reutilizável com instruções, recursos e scripts opcionais, carregado por divulgação progressiva. A referência oficial atual usada por este plano é `https://learn.chatgpt.com/docs/build-skills`.

`EVALUATIONS.md` contém informação técnica útil, porém mistura três finalidades no mesmo fluxo: aprendizagem do sistema, referência do runner e cookbook de comandos. O primeiro exemplo concreto aparece somente depois de conceitos, estrutura, planejamento, execução, persistência e referência de comandos.

Conceitos essenciais aparecem antes de uma explicação operacional:

* `fixture` aparece no modelo mental e só recebe sua própria seção mais tarde;
* `gate` aparece na abertura e não é definido em linguagem comum;
* `promotion`, `campaign` e `fingerprint` surgem dentro do fluxo antes de sua motivação;
* o diagrama usa `oracle` antes de explicar o que ele observa, por que é oculto e como difere de verificações mecânicas e judge;
* “mechanical checker” e “oracle” parecem nomes concorrentes para o mesmo papel;
* “Adding evals to another skill”, a seção mais próxima de uma jornada prática, aparece perto do final.

Há ainda uma imprecisão factual. O texto afirma que um caso semântico precisa de julgamento de agente porque código não consegue observar o resultado completo. O caso real `develop-skill-with-evals/evals/cases/impact-gate-selection` contradiz essa afirmação: ele usa executor, oracle determinístico completo e judge desabilitado. A distinção correta é que um caso semântico precisa de executor para realizar a tarefa; o veredito pode ser inteiramente determinístico.

O caso `impact-gate-selection` é o melhor exemplo inicial disponível porque contém:

* um prompt real que pede planejamento sem alteração;
* uma fixture com `target-skill` e `target-baseline`;
* verificações mecânicas para arquivos obrigatórios e fontes protegidas;
* um oracle curto que lê `evaluation-plan.json` e verifica impacto, seleção, sessões e blockers;
* judge explicitamente desabilitado.

O worktree já contém mudanças e arquivos não rastreados alheios a esta revisão, incluindo relatórios sob `evaluation-reports/`, caches Python e o diretório `_temporary/`. A execução preservou esse estado e editou somente `README.md`, `EVALUATIONS.md` e este ExecPlan. O usuário autorizou a implementação em uma solicitação posterior à criação do plano.

## Desired End State

`README.md` contém, imediatamente antes do catálogo ou em posição igualmente introdutória, uma seção curta “What is a Codex skill?” que:

* define skill em linguagem comum;
* explica `SKILL.md`, recursos opcionais e divulgação progressiva;
* diferencia seleção explícita e implícita sem antecipar detalhes desnecessários;
* aponta para a documentação oficial;
* conduz quem quer entender avaliações até `EVALUATIONS.md`.

`EVALUATIONS.md` funciona em camadas:

1. explica o problema resolvido por evals e o pré requisito de conhecer skills;
2. separa o trabalho automatizado por `develop-skill-with-evals` das decisões humanas;
3. conduz o leitor pelo caso `impact-gate-selection` de ponta a ponta;
4. dá nome e função a prompt, fixture, verificações mecânicas, oracle e judge;
5. ensina a escolha entre caso semântico, caso determinístico, oracle e judge;
6. explica baseline, candidate, RED, GREEN, impacto e regressão proporcional;
7. mostra como supervisionar plano, custo, fingerprints, resultados e artefatos;
8. termina com referência curta e links para CLI, contrato e schemas.

Ao terminar o guia, o leitor consegue responder:

* o que a avaliação tenta provar;
* por que validação estrutural não basta;
* o que o executor pode e não pode ver;
* a diferença entre fixture, verificações mecânicas, oracle e judge;
* por que um caso semântico pode dispensar judge;
* o que RED e três GREEN demonstram;
* quais decisões `develop-skill-with-evals` toma e quais precisam de supervisão;
* quais campos e resultados devem bloquear promoção.

Os detalhes normativos permanecem canônicos no contrato e schemas. As receitas extensas permanecem em `CODEX_CLI.md`. `EVALUATIONS.md` preserva um caminho operacional mínimo e resumos suficientes para não se transformar em uma coleção de links.

## Milestones

### Milestone 1: Introduzir Codex skills no README

#### Goal

Dar ao leitor a base conceitual exigida por `EVALUATIONS.md` sem transformar o README em outro manual.

#### Changes

* Editar `/home/renanfranca/.codex/skills/README.md`.
* Inserir uma seção `## What is a Codex skill?` depois da abertura e antes de `## Skill catalog`.
* Explicar em dois a quatro parágrafos:
  * skill como workflow reutilizável;
  * diretório com `SKILL.md` e recursos opcionais;
  * carregamento progressivo a partir de nome e descrição;
  * invocação explícita e seleção implícita.
* Linkar `https://learn.chatgpt.com/docs/build-skills`.
* Ajustar a seção `Skill evaluations` para apresentar `EVALUATIONS.md` como guia de compreensão e supervisão, não como depósito de todas as interfaces do runner.
* Preservar o catálogo, instalação, comandos e convenções existentes.

#### Validation

* Command: `sed -n '1,110p' README.md`
* Expected result: a definição aparece antes do catálogo, usa linguagem introdutória e conduz naturalmente aos guias posteriores.
* Command: `rg -n 'What is a Codex skill|build-skills|EVALUATIONS\\.md' README.md`
* Expected result: a seção, a fonte oficial e o link para avaliações aparecem uma vez cada no contexto correto, sem links duplicados ou conflitantes.

#### Acceptance Criteria

* Uma pessoa que nunca leu a documentação oficial entende o papel de uma skill sem precisar aprender evals.
* A explicação não duplica instalação, catálogo ou cookbook.
* O README estabelece explicitamente o pré requisito usado por `EVALUATIONS.md`.

### Milestone 2: Ensinar o sistema por um caso real

#### Goal

Substituir a abertura abstrata de `EVALUATIONS.md` por uma progressão que parte do problema, mostra uma execução concreta e só então generaliza os conceitos.

#### Changes

* Editar `/home/renanfranca/.codex/skills/EVALUATIONS.md`.
* Manter o título `# Evaluating Codex Skills`.
* Reescrever a abertura para:
  * apontar leitores novos em skills para `README.md#what-is-a-codex-skill`;
  * distinguir validação estrutural de avaliação comportamental;
  * declarar que o objetivo do guia é compreender e supervisionar evidência.
* Adicionar uma seção inicial sobre responsabilidades:
  * `develop-skill-with-evals` cria ou altera casos, preserva fontes, planeja e executa gates;
  * a pessoa confirma intenção, observabilidade, impacto, custo e promoção.
* Apresentar `impact-gate-selection` antes da árvore genérica de suite e antes da referência de `case.json`.
* Narrar o caso em cinco passos:
  1. prompt recebido pelo executor;
  2. fixture copiada ao workspace;
  3. arquivos de evidência produzidos;
  4. verificações mecânicas e oracle executados;
  5. PASS sem judge porque código cobre o contrato completo.
* Mostrar apenas os fragmentos essenciais do prompt, manifest e oracle. Linkar os arquivos reais para a versão completa.
* Substituir o diagrama inicial denso por uma visualização menor ou movê lo para depois do exemplo. A visualização escolhida deve mostrar claramente visibilidade e ordem, não repetir a narrativa.

#### Validation

* Command: `sed -n '1,260p' EVALUATIONS.md`
* Expected result: um leitor encontra propósito, responsabilidades e exemplo completo antes de schemas, budgets ou referência de comandos.
* Command: `sed -n '1,180p' develop-skill-with-evals/evals/cases/impact-gate-selection/prompt.md && sed -n '1,220p' develop-skill-with-evals/evals/cases/impact-gate-selection/case.json && sed -n '1,220p' develop-skill-with-evals/evals/cases/impact-gate-selection/oracle/check_plan.py`
* Expected result: toda afirmação e todo fragmento do exemplo correspondem aos arquivos reais.

#### Acceptance Criteria

* Oracle é definido antes de aparecer em diagrama, tabela ou comando.
* O leitor entende por que o oracle é oculto, o que observa e por que elimina a necessidade de judge nesse caso.
* O exemplo não introduz resposta fictícia, comportamento inexistente ou detalhes desnecessários do runner.

### Milestone 3: Reorganizar conceitos e decisões de evidência

#### Goal

Dar ao leitor um modelo mental preciso para distinguir as peças do sistema e escolher evidência sem confundir execução semântica com julgamento semântico.

#### Changes

* Criar uma seção compacta de anatomia após o exemplo.
* Incluir uma tabela ou lista que compare:
  * prompt;
  * fixture;
  * verificações mecânicas;
  * oracle;
  * judge.
* Para cada peça, declarar função, visibilidade para o executor, tipo de evidência e custo de sessão quando aplicável.
* Corrigir a definição de caso semântico:
  * executor é obrigatório;
  * judge é opcional;
  * oracle completo pode decidir o resultado.
* Definir caso determinístico como execução e verificação integral por código, sem prompt, executor ou judge.
* Explicar que `behavioral`, `non_behavioral` e `trigger` são kinds semânticos aceitos pelo manifest, enquanto `deterministic` seleciona o caminho sem modelo.
* Apresentar uma sequência de decisão:
  1. a tarefa precisa de agente para ser realizada?
  2. código consegue observar o contrato completo?
  3. se não, qual interpretação exige judge?
* Mover `Suite structure`, `case.json`, `prompt.md` e `fixture/` para depois dessa explicação.
* Manter a tabela de campos mais comuns apenas se cada campo apoiar compreensão ou supervisão. Direcionar formatos completos ao contrato e schemas.
* Remover formulações que chamem oracle e verificações mecânicas pelo mesmo papel sem explicar a diferença.

#### Validation

* Command: `rg -n 'semantic case|deterministic case|oracle|mechanical|judge|fixture|prompt' EVALUATIONS.md`
* Expected result: a primeira ocorrência relevante de cada conceito contém ou segue imediatamente uma definição em linguagem comum.
* Command: `rg -n 'kind|oracle|judge|prompt_file|implicit_skill' develop-skill-with-evals/references/eval-contract.md develop-skill-with-evals/references/eval-plan.schema.json develop-skill-with-evals/references/eval-result.schema.json`
* Expected result: as distinções públicas não contradizem o contrato nem os schemas.

#### Acceptance Criteria

* O texto não afirma que todo caso semântico precisa de judge.
* Oracle é apresentado como verificador específico e oculto, não como IA, sinônimo de judge ou nome genérico para todo checker.
* A decisão entre caminhos pode ser aplicada ao caso real sem conhecimento do código interno do runner.

### Milestone 4: Organizar o ciclo de prova e a supervisão

#### Goal

Explicar como a evidência progride de uma mudança proposta até uma decisão de promoção, sem transformar o guia em cookbook.

#### Changes

* Reorganizar as seções existentes em torno desta sequência:
  1. preservar baseline e candidate;
  2. reduzir um caso real antes da implementação;
  3. classificar impacto;
  4. planejar gates e sessões sem efeitos colaterais;
  5. demonstrar RED;
  6. obter três GREEN estáveis;
  7. executar regressão proporcional;
  8. interpretar resultado e artefatos.
* Explicar `static`, `deterministic`, `scoped` e `cross-cutting` pela pergunta “qual evidência consegue observar esta mudança?”.
* Definir gate antes de usar o termo nas tabelas de impacto.
* Definir promoção e diagnóstico quando seus workflows forem introduzidos.
* Explicar fingerprint como proteção contra mudança entre plano e execução, não apenas listar os tipos existentes.
* Explicar campaign e ledger somente depois de introduzir planejamento de sessões e aprovação cumulativa.
* Manter um único caminho mínimo de comando para planejamento e validação. Linkar receitas, flags e modos compatíveis para as seções correspondentes de `CODEX_CLI.md`.
* Preservar a tabela de status e ações, mas aproximá la da explicação de resultados.
* Declarar claramente que todo status diferente de `PASS` bloqueia promoção.
* Reformular “Adding evals to another skill” como referência sobre o que a automação cria e o que um mantenedor revisa. Preservar o heading se isso evitar quebrar o anchor e não induzir uma finalidade incorreta.

#### Validation

* Command: `rg -n '^## |^### ' EVALUATIONS.md`
* Expected result: a ordem segue aprendizagem, exemplo, conceitos, ciclo de prova, supervisão e referência avançada.
* Command: `rg -n 'baseline|candidate|RED|GREEN|static|deterministic|scoped|cross-cutting|promotion|diagnostic|fingerprint|campaign' EVALUATIONS.md`
* Expected result: cada termo aparece primeiro com motivação ou definição, e só depois em tabelas e comandos.
* Command: `python3 develop-skill-with-evals/scripts/run_skill_evals.py plan --help`
* Expected result: nomes de comandos e opções preservados no guia continuam existindo.

#### Acceptance Criteria

* O leitor entende o motivo de cada gate antes de encontrar contagens de sessões.
* O guia permite supervisionar plano, impacto, custo, resultados e artefatos.
* Receitas extensas não são duplicadas.

### Milestone 5: Compactar a referência sem perder informação útil

#### Goal

Manter `EVALUATIONS.md` autossuficiente para compreensão e supervisão, enquanto detalhes normativos e operacionais permanecem em suas fontes próprias.

#### Changes

* Revisar `Durable evidence and pricing` para manter:
  * por que relatórios duráveis existem;
  * qual arquivo é canônico;
  * limitações essenciais de pricing;
  * links para cookbook e contrato.
* Revisar `Command reference` e `Optional runtime controls` para manter somente orientação de escolha e links para `CODEX_CLI.md`.
* Revisar `Economic runtime policy` para manter a política conceitual sem repetir receitas.
* Manter `Troubleshooting`, `Design principles` e `Structural validation` concisos e orientados a decisões.
* Reduzir ou reposicionar o exemplo de `refactor-design` como exemplo adicional, pois ele não ensina oracle. Não apresentá lo como exemplo principal.
* Atualizar o índice depois que a ordem final estiver estável.
* Preservar, quando ainda verdadeiros, os headings `Durable evidence and pricing` e `Economic runtime policy`, atualmente referenciados pelo `README.md`.
* Atualizar os links no `README.md` se qualquer anchor for renomeado ou se o destino conceitualmente correto passar a ser `CODEX_CLI.md` ou `eval-contract.md`.
* Não remover informação exclusiva de `EVALUATIONS.md` sem apontar para uma fonte que realmente a contenha. Se não houver destino adequado dentro do escopo, reter um resumo suficiente no próprio guia.

#### Validation

* Command: `rg -n 'EVALUATIONS\\.md#|CODEX_CLI\\.md#|eval-contract\\.md|eval-plan\\.schema\\.json|eval-result\\.schema\\.json' README.md EVALUATIONS.md CODEX_CLI.md`
* Expected result: todos os links locais apontam para arquivos e headings existentes e representam a responsabilidade correta.
* Command: `wc -l -w EVALUATIONS.md`
* Expected result: o número é registrado apenas como observação; não existe limite de aprovação baseado em tamanho.
* Command: `git diff --word-diff=plain -- README.md EVALUATIONS.md`
* Expected result: conceitos necessários continuam presentes, repetições são reduzidas e nenhuma mudança normativa é introduzida.

#### Acceptance Criteria

* Todo parágrafo ensina um conceito, orienta uma decisão ou apoia diagnóstico.
* O guia não vira uma página de links sem explicação suficiente.
* Cookbook, contrato e schemas são apresentados como fontes complementares com responsabilidades claras.

### Milestone 6: Validar consistência e entregar sem executar evals

#### Goal

Provar que a revisão documental é coerente, navegável e limitada aos arquivos autorizados.

#### Changes

* Atualizar este ExecPlan com progresso, decisões, riscos e lições observadas.
* Corrigir somente defeitos documentais encontrados pela validação.
* Não executar model backed evals porque a mudança é `static` e não altera seleção nem comportamento de skill.

#### Validation

* Command: `git diff --check -- README.md EVALUATIONS.md`
* Expected result: nenhuma falha de whitespace.
* Command: `rg -n '^#{1,4} ' README.md EVALUATIONS.md CODEX_CLI.md`
* Expected result: hierarquia de headings coerente e anchors referenciados presentes.
* Command: `rg -n 'oracle|judge|semantic case|deterministic case' EVALUATIONS.md develop-skill-with-evals/SKILL.md develop-skill-with-evals/references/eval-contract.md`
* Expected result: terminologia pública consistente com as fontes normativas.
* Command: `git diff -- README.md EVALUATIONS.md`
* Expected result: somente a introdução mínima no README e a reorganização didática em EVALUATIONS aparecem no patch rastreado deste trabalho.
* Command: `git status --short`
* Expected result: o status ainda pode listar alterações preexistentes em relatórios e caches; nenhuma delas foi criada, removida ou modificada por este plano.

#### Acceptance Criteria

* Todos os cenários de leitura do `Desired End State` são satisfeitos.
* Nenhum link local conhecido está quebrado.
* Nenhum contrato, schema, runner, caso, skill ou relatório foi alterado.
* Nenhuma avaliação model backed, commit, push ou publicação ocorreu.

## Progress

* [x] ExecPlan solicitado e escopo confirmado.
* [x] Estado atual de `README.md`, `EVALUATIONS.md`, `CODEX_CLI.md`, `develop-skill-with-evals/SKILL.md`, contrato e caso de exemplo inspecionado.
* [x] ExecPlan criado em `_temporary/codex-skills-ai-context`.
* [x] Execução explicitamente autorizada pelo usuário.
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
* [x] Milestone 6 iniciado.
* [x] Milestone 6 concluído.

## Decisions

* Decision: Usar `EVALUATIONS.md` como piloto editorial e alterar apenas a introdução mínima necessária em `README.md`.
  Rationale: Permite validar a progressão didática antes de harmonizar todos os guias, sem deixar um pré requisito conceitual ausente.
  Date/Author: 2026-07-27 / User and Codex

* Decision: Assumir que o leitor conhece Codex skills, mas não conhece evals.
  Rationale: `EVALUATIONS.md` pode explicar o sistema desde o início sem repetir catálogo, instalação ou criação básica de skills.
  Date/Author: 2026-07-27 / User

* Decision: Definir como resultado principal “entender e supervisionar”.
  Rationale: `develop-skill-with-evals` orienta o Codex a criar e alterar casos; a documentação deve permitir que uma pessoa revise intenção, evidência, impacto, custo e promoção.
  Date/Author: 2026-07-27 / User

* Decision: Separar responsabilidades documentais.
  Rationale: `EVALUATIONS.md` ensina conceitos e supervisão, `CODEX_CLI.md` concentra receitas e `eval-contract.md` com schemas permanece normativo.
  Date/Author: 2026-07-27 / User

* Decision: Usar `impact-gate-selection` como exemplo mínimo real.
  Rationale: É o menor caso existente que demonstra prompt, fixture, verificações mecânicas, oracle completo e judge desabilitado em um único fluxo.
  Date/Author: 2026-07-27 / User and Codex

* Decision: Não impor meta de tamanho.
  Rationale: Conexão lógica e utilidade determinam concisão; contagem de palavras não deve forçar lacunas nem uma navegação fragmentada.
  Date/Author: 2026-07-27 / User

* Decision: Manter a documentação em inglês.
  Rationale: Preserva o idioma atual do repositório e evita ampliar esta revisão para tradução e terminologia bilíngue.
  Date/Author: 2026-07-27 / Codex, assumido a partir do estado atual

* Decision: Classificar a futura alteração como `static`.
  Rationale: Somente documentação pública será reorganizada; runner, skill, metadata, seleção e comportamento permanecem inalterados.
  Date/Author: 2026-07-27 / Codex

* Decision: Não iniciar a execução após criar este arquivo.
  Rationale: O usuário autorizou apenas persistir o plano e pediu explicitamente que ele ainda não fosse executado.
  Date/Author: 2026-07-27 / User

* Decision: Iniciar a execução documental sem avaliações model backed.
  Rationale: O usuário autorizou explicitamente a implementação em 2026-07-27, e o escopo continua limitado a documentação estática sem mudança de comportamento.
  Date/Author: 2026-07-27 / User and Codex

* Decision: Preservar os headings `Requirements` e `Example: evaluating refactor-design` em forma compacta.
  Rationale: Ambos mantêm informação útil e anchors públicos sem devolver ao guia o volume de cookbook anterior.
  Date/Author: 2026-07-27 / Codex

## Risks and Mitigations

* Risk: Simplificar até transformar `EVALUATIONS.md` em uma coleção de links.
  Mitigation: Manter no guia definições, exemplo completo, modelo de decisão, ciclo de prova e interpretação de resultados; mover somente detalhes normativos e receitas extensas.

* Risk: Reordenar conteúdo sem corrigir conceitos usados prematuramente.
  Mitigation: Auditar primeiras ocorrências e exigir definição antes de diagramas, tabelas e comandos.

* Risk: Continuar confundindo caso semântico com judge obrigatório.
  Mitigation: Usar `impact-gate-selection` como contraexemplo verificável e alinhar toda redação ao contrato real.

* Risk: Descrever verificações mecânicas e oracle como o mesmo mecanismo.
  Mitigation: Comparar explicitamente função, especificidade, localização, visibilidade e papel no veredito.

* Risk: Ensinar detalhes internos que uma pessoa não precisa para supervisionar.
  Mitigation: Julgar cada parágrafo pelo critério de ensinar, orientar decisão ou apoiar diagnóstico.

* Risk: Remover detalhes exclusivos sob a suposição incorreta de que estão em outro guia.
  Mitigation: Verificar o destino antes de remover; reter resumo quando `CODEX_CLI.md` ou contrato não cobrir a informação.

* Risk: Quebrar links externos por renomear headings.
  Mitigation: Preservar headings ainda válidos, atualizar todos os links locais encontrados e evitar renomeações sem ganho didático concreto.

* Risk: Alterar política ou contrato por meio de paráfrase imprecisa.
  Mitigation: Comparar afirmações sobre kinds, gates, sessões, statuses, oracle e judge com `SKILL.md`, contrato, schemas e caso real.

* Risk: Misturar mudanças preexistentes do worktree com esta revisão.
  Mitigation: Editar apenas `README.md`, `EVALUATIONS.md` e este ExecPlan; usar diffs com paths explícitos e não limpar relatórios, caches ou `_temporary/`.

* Risk: Executar evals desnecessárias para uma mudança documental estática.
  Mitigation: Limitar validação a consistência, links, headings, comandos preservados e `git diff --check`.

* Risk: Atribuir ao fixture arquivos disponibilizados pelo harness.
  Mitigation: Conferir a árvore real do caso e descrever separadamente fixture pública e skill instalada pelo runner.

## Validation Strategy

Validar do comportamento de leitura para a mecânica documental:

1. Ler `README.md` e o primeiro terço de `EVALUATIONS.md` como uma pessoa que conhece skills, mas não evals.
2. Confirmar que o caso `impact-gate-selection` explica o fluxo antes da generalização.
3. Auditar primeiras ocorrências de todos os termos definidos neste plano.
4. Comparar afirmações com `develop-skill-with-evals/SKILL.md`, `eval-contract.md`, schemas e arquivos reais do caso.
5. Verificar headings, anchors, caminhos, links e comandos preservados.
6. Inspecionar o diff limitado a `README.md` e `EVALUATIONS.md`.
7. Executar `git diff --check -- README.md EVALUATIONS.md`.
8. Confirmar pelo status que nenhum arquivo fora do escopo foi alterado por esta execução.

Os cenários de aceitação manuais são:

* um leitor explica por que uma skill bem formada ainda precisa de eval;
* um leitor descreve o que `develop-skill-with-evals` automatiza e o que continua sendo decisão humana;
* um leitor diferencia prompt, fixture, verificações mecânicas, oracle e judge;
* um leitor reconhece que `impact-gate-selection` é semântico mesmo sem judge;
* um leitor decide quando código pode cobrir o contrato e quando interpretação semântica permanece;
* um leitor interpreta baseline RED, candidate GREEN, estabilidade e regressão proporcional;
* um leitor encontra onde consultar comandos completos, contrato e schemas;
* um leitor identifica statuses que bloqueiam promoção e onde procurar artefatos diagnósticos.

## Rollout and Recovery

Não existe deployment separado. A revisão foi aplicada diretamente aos dois arquivos documentais do repositório, validada e entregue sem stage ou commit.

Antes de editar, registrar o diff e status dos paths em escopo. Como o worktree já está sujo, não usar `git reset`, `git checkout`, limpeza recursiva ou qualquer comando destrutivo.

Se a revisão precisar ser revertida, recuperar apenas os trechos modificados em `README.md` e `EVALUATIONS.md` por meio de um patch inverso revisado. Não tocar nas alterações preexistentes sob `evaluation-reports/`, nos caches Python ou em outros arquivos de `_temporary/`.

Este ExecPlan permanece em `_temporary/codex-skills-ai-context` como contexto local e não deve ser commitado sem autorização explícita.

## Lessons Learned

* Extensão não é o principal problema atual. A ordem mistura aprendizagem, referência normativa e cookbook antes de apresentar uma avaliação concreta.
* `impact-gate-selection` demonstra que “semântico” descreve a necessidade de executor, não a obrigatoriedade de judge.
* Um oracle neste runner é código determinístico oculto, não uma sessão de IA nem um sinônimo de toda verificação mecânica.
* A pessoa normalmente não precisa escrever evals manualmente quando usa `develop-skill-with-evals`, mas precisa compreender a prova para revisar intenção, observabilidade, custo e promoção.
* A introdução atual do README permite instalar e invocar skills, mas não estabelece uma base didática suficiente sobre o conceito.
* Headings Markdown são parte da interface documental porque outros arquivos podem depender de seus anchors.
* O worktree já contém alterações de relatórios e caches não relacionadas; qualquer execução futura precisa preservar esse estado.
* A primeira reescrita atribuiu incorretamente o runner à fixture de `impact-gate-selection`; a inspeção da árvore mostrou que a fixture contém somente candidate e baseline, enquanto o harness instala a skill avaliada separadamente.
* Links conceituais precisam ser validados contra headings reais; nomes plausíveis de seções do cookbook não são evidência de que o anchor existe.
* A versão final passou por validação local de todos os links Markdown entre arquivos e anchors, além de `git diff --check`; o guia ficou com cerca de 4.200 palavras sem usar tamanho como gate.
* A ajuda real de `run_skill_evals.py plan` confirmou os nomes de impacto, workflow, runtime e campaign mantidos no guia.
