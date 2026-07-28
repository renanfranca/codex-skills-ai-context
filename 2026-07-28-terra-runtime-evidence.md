# Evidência, decisões e desfecho sobre runtimes de avaliação

## Conclusão executiva

Terra medium foi uma escolha operacional ruim para a campanha de avaliação de `restructure-documentation`. Quatro operações foram bloqueadas por judges Terra que retornaram `INCONCLUSIVE` mesmo depois de verificações mecânicas e oracles ocultos terem passado. Esses bloqueios exigiram revisões sucessivas da superfície de evidência, novos fingerprints, novos planos e novas autorizações, contribuindo para uma campanha de 58 sessões.

Isso não prova que Sol medium seja causalmente superior. Não houve comparação A/B com entradas, contratos e artefatos idênticos. A promoção final de `restructure-documentation` passou com Terra depois de alterações nos contratos, e dois testes posteriores com Sol encontraram defeitos diferentes. A decisão final é uma preferência operacional fundamentada no comportamento observado, no custo acumulado e na confiança do usuário, não uma alegação científica sobre os modelos.

O desfecho no repositório foi:

- recomendar `gpt-5.6-sol` com esforço `medium` para executor e judge;
- usar zero sessões quando o contrato for totalmente determinístico;
- preservar escolhas explícitas do usuário;
- exigir runtime explícito em promoções com modelos;
- remover completamente `economic_runtime`, pois o campo apenas aconselhava e nunca controlava o modelo executado;
- manter `runtime`, blockers, sessões e fingerprints como as interfaces auditáveis;
- não repetir avaliações inalteradas depois de um bloqueio.

## Fontes e limites

Esta análise usa:

- o contexto preservado desta sessão;
- o ExecPlan de criação de `restructure-documentation`;
- os sete relatórios canônicos em `evaluation-reports/restructure-documentation/operations/`;
- os dois relatórios posteriores em `evaluation-reports/develop-skill-with-evals/operations/`;
- o ExecPlan `_temporary/codex-skills-ai-context/2026-07-28-sol-medium-runtime-policy-execplan.md`;
- o commit `4f1c208`.

Ela não reproduz a conversa integral, respostas geradas completas, critérios ocultos ou raciocínio privado. Partes compactadas da conversa que não aparecem em artefatos duráveis não são tratadas como citações verificáveis.

Valores monetários nos relatórios são estimativas de referência da API, não cobranças observadas do plano ChatGPT. Limitações de telemetria de contexto longo impedem tratar essas estimativas como custo real.

## Campanha Terra de `restructure-documentation`

### Resultado agregado

| Medida | Valor observado |
| --- | ---: |
| Operações de promoção | 7 |
| Sessões totais | 58 |
| Sessões de executor | 39 |
| Sessões de judge | 19 |
| Tokens de entrada | 6.417.440 |
| Tokens de entrada em cache | 5.415.680 |
| Tokens de saída | 183.263 |
| Operações `INCONCLUSIVE` | 4 |
| Operações `FAIL` | 1 |
| Operações `UNSTABLE` | 1 |
| Operações `PASS` | 1 |

### Linha de evidência

| Operação | Sessões | Resultado | Causa imediata |
| --- | ---: | --- | --- |
| `20260727T170833.654743Z-a06b21740974` | 5 | `INCONCLUSIVE` | Judge exigiu conteúdo ou diff adicional apesar de mecânica e oracle aprovados. |
| `20260727T171652.898155Z-23d46f21e916` | 9 | `INCONCLUSIVE` | Judge recusou o resultado sem alterações por ausência de headings e caminhos concretos na resposta estruturada. |
| `20260727T172917.092637Z-24a0aea91593` | 5 | `INCONCLUSIVE` | Judge tratou fatos confirmados pelo oracle como afirmações insuficientemente independentes. |
| `20260727T174503.293755Z-31ee59c08177` | 4 | `FAIL` | O oracle de primeira ocorrência falhou; o judge foi corretamente ignorado. |
| `20260727T181016.250690Z-0aafa27ac157` | 7 | `INCONCLUSIVE` | Judge exigiu justificativas que existiam no arquivo gerado, mas não em sua superfície de evidência. |
| `20260727T182350.025533Z-9643f8cb7f3b` | 21 | `UNSTABLE` | Gates individuais passaram, mas uma repetição criou um ExecPlan e duas não, alterando a assinatura de caminhos. |
| `20260727T233326.147750Z-48228593ef91` | 7 | `PASS` | O caso sistêmico revisado produziu RED válido e três GREEN estáveis. |

### Por que Terra foi inadequado nesse fluxo

Nos quatro bloqueios de judge, a mecânica e o oracle já tinham aprovado. Os rationales pediram diffs, trechos, headings ou justificativas que não estavam disponíveis na entrada do judge. Isso mostra incompatibilidade repetida entre o judge e o contrato de evidência usado naquela campanha.

O judge também repetiu verificações já delimitadas mecanicamente ou exigiu formulações literais adicionais. Seu valor incremental ficou abaixo do custo das sessões e das revisões necessárias para adaptar o contrato.

Cada bloqueio interrompeu corretamente a promoção, mas exigiu correção material, novo fingerprint, novo plano e nova autorização. O fluxo de segurança funcionou; a escolha de runtime e a superfície de evidência não funcionaram economicamente para o usuário.

### O que Terra não causou

O `FAIL` veio do oracle de primeira ocorrência. O `UNSTABLE` veio da divergência de caminhos alterados. A promoção final passou com Terra. Portanto, não é correto atribuir as 58 sessões ou todos os defeitos ao modelo.

## Testes posteriores com Sol medium

Depois da campanha Terra, duas promoções de `develop-skill-with-evals` usaram Sol medium para executor e judge.

| Operação | Executor | Judge | Sessões | Resultado | Causa imediata |
| --- | ---: | ---: | ---: | --- | --- |
| `20260728T104228.600043Z-ac887a5415d6` | 2 | 0 | 2 | `FAIL` | O prompt deixou ambíguo onde salvar os planos; os executores alteraram a árvore protegida e não produziram os caminhos esperados na raiz. |
| `20260728T113254.537174Z-d01790d9d1d6` | 10 | 3 | 13 | `FAIL` | Todos os três judges Sol executados passaram, mas a regressão determinística `cost-efficient-runtime-contract` ainda exigia `economic_runtime.policy_version == 1`. |

Essas operações consumiram 15 sessões adicionais:

- 12 sessões de executor;
- 3 sessões de judge;
- 4.387.652 tokens de entrada;
- 3.728.128 tokens de entrada em cache;
- 56.641 tokens de saída.

O primeiro bloqueio foi um defeito do prompt, não uma falha de capacidade do modelo. O segundo foi um erro determinístico de classificação de impacto e contrato obsoleto. Os três judges Sol passaram, o que é evidência positiva dessa execução, mas não constitui comparação equivalente com Terra.

Somando as campanhas descritas neste documento, foram observadas 73 sessões. Essa soma registra consumo, não custo monetário real.

## Por que `economic_runtime` foi removido

`economic_runtime` era um objeto consultivo emitido por `plan`. Ele podia recomendar um modelo e produzir warnings de divergência, mas:

- não preenchia `--model` nem `--reasoning-effort`;
- não alterava comandos planejados;
- não controlava o subprocesso `codex exec`;
- não substituía autorização de custo;
- duplicava uma preferência que deveria estar nas instruções do repositório;
- criava a impressão de que influenciava a execução quando não influenciava.

A proposta intermediária de criar `economic_runtime.policy_version: 2` e recomendar Sol para todos os papéis foi abandonada. Manter o campo com outra política preservaria a mesma ambiguidade. A decisão final foi removê-lo sem campo substituto e sem adicionar uma versão artificial ao plano.

Essa é uma alteração incompatível da interface pública de planos. Consumidores não devem mais esperar `economic_runtime`. Devem inspecionar:

- `runtime`;
- `runtime_fingerprint`;
- `sessions`;
- `execution_blockers`;
- `evaluation_fingerprint`;
- valores explícitos dos comandos planejados.

## Como o runtime é escolhido agora

### Promoção com modelos

`develop-skill-with-evals plan` e `validate-change` não leem `config.toml` para qualificar uma promoção. Quando o plano contém sessões de modelo:

- executor exige `--model` e `--reasoning-effort` explícitos;
- judge pode receber `--judge-model` e `--judge-reasoning-effort`;
- se os parâmetros do judge forem omitidos, ele herda o runtime completo do executor;
- ausência de runtime explícito gera blocker antes de qualquer sessão.

O repositório recomenda Sol medium para executor e judge, mas o runner não aplica essa recomendação automaticamente.

### Sessões normais e comandos exploratórios

Uma sessão Codex normal, ou um `codex exec` sem flags, segue a precedência de configuração do Codex. Neste ambiente, sem override do projeto, `~/.codex/config.toml` define `gpt-5.6-sol` com esforço `medium`.

Comandos exploratórios compatíveis do runner que não recebem modelo repassam a escolha ao `codex exec`. O subprocesso usa a configuração efetiva, mas o runner registra apenas `configured-default`, pois não lê `config.toml`.

Uma configuração de projeto ou flags de CLI com maior precedência podem mudar esse resultado. A recomendação em `AGENTS.md` também só governa agentes que carregam essas instruções; não é configuração executável.

### Uso a partir de outro repositório

Ao criar ou atualizar uma skill a partir de outro repositório:

- o modelo da sessão principal segue a configuração efetiva daquele ambiente;
- o `AGENTS.md` deste repositório não acompanha automaticamente outro projeto;
- a skill instalada contém sua própria orientação para recomendar Sol medium;
- uma promoção com `develop-skill-with-evals` continua exigindo runtime explícito, independentemente do repositório de origem;
- escolhas explícitas do usuário prevalecem.

Portanto, a recomendação documental aumenta consistência, mas não garante sozinha qual modelo será executado.

## Política operacional final

Para trabalhos futuros neste repositório:

- usar zero sessões para mudanças estáticas ou determinísticas quando testes, schemas, oracles, fakes ou comparação direta cobrirem integralmente o contrato;
- recomendar `gpt-5.6-sol` com esforço `medium` para executor e judge em avaliações com modelos e validações com agente fresco;
- declarar runtime explicitamente em promoções;
- obter autorização de custo separada de aprovação de shell ou sandbox;
- preservar qualquer runtime escolhido explicitamente pelo usuário;
- não repetir avaliação inalterada depois de `FAIL`, `ERROR`, `INCONCLUSIVE`, `INVALID_RED` ou `UNSTABLE`;
- não afirmar que Sol garante aprovação ou que Terra causou todos os gastos.

## Implementação final

O commit `4f1c208` (`feat(develop-skill-with-evals)!: remove economic runtime guidance`) implementou a decisão.

Foram alterados:

- `AGENTS.md`, com a recomendação do repositório;
- `develop-skill-with-evals/SKILL.md`, com orientação Sol medium e preservação de escolha explícita;
- `develop-skill-with-evals/references/eval-contract.md`;
- `EVALUATIONS.md` e `CODEX_CLI.md`;
- `run_skill_evals.py`, removendo geração, warnings, validação, estabilidade e fingerprint de `economic_runtime`;
- `eval-plan.schema.json`, removendo o campo e suas definições;
- o contrato determinístico `cost-efficient-runtime-contract`;
- a interface `agents/openai.yaml`.

Também foram removidos:

- o caso comportamental `economic-runtime-guidance`;
- suas fixtures e oracle;
- os oito testes unitários dedicados à política consultiva.

Os dois relatórios Sol bloqueados foram preservados no mesmo commit como evidência histórica. Eles registram tentativas anteriores à remoção e ainda contêm o contrato antigo; não representam o estado atual do runner.

## Evidência de validação da remoção

A remoção foi desenvolvida em baseline e candidate isolados sob `/tmp/remove-economic-runtime.n7Eyj7`.

Resultado determinístico:

- baseline RED porque ainda emitia `economic_runtime`;
- candidate GREEN em três repetições estáveis;
- status final `PASS`;
- `model_sessions.executor: 0`;
- `model_sessions.judge: 0`;
- `model_sessions.total: 0`;
- evaluation fingerprint `2661ade4ed3d399facb31368f3e1336217561df40242a46c2dc7a0a78c2ba286`.

Validação canônica:

- 82 testes unitários passaram;
- `quick_validate.py` passou;
- JSON Schema passou;
- links e anchors Markdown passaram;
- candidate e skill canônica eram idênticos;
- `git diff --check` passou.

O total caiu de 90 para 82 testes porque os oito testes exclusivos de `economic_runtime` foram removidos. Isso não representa perda acidental de cobertura: a ausência do campo passou a ser coberta pelo teste de schema e pelo contrato determinístico.

Não houve diagnóstico, forward test ou sessão de modelo para validar a remoção. Por isso, o resultado é promoção determinística do contrato de código, não promoção semântica da capacidade de agentes seguirem a recomendação.

## Ocorrências históricas preservadas

Continuam válidas:

- relatórios históricos que registram runtimes realmente executados;
- comparações datadas entre modelos;
- referências factuais à tabela de preços;
- testes e fixtures que usam modelos diferentes para validar parsing, herança ou propagação;
- prompts históricos que registram uma escolha explícita.

Reescrever essas ocorrências apagaria evidência ou reduziria cobertura. Elas não devem ser interpretadas como recomendação normativa atual.

## Estado final e ressalvas

- A recomendação atual do projeto é Sol medium para executor e judge.
- Essa recomendação está documentada, não codificada como default do runner.
- `economic_runtime` não existe mais no contrato atual.
- A remoção é um breaking change para consumidores do plano JSON.
- A validação determinística consumiu zero sessões.
- O commit local é `4f1c208`.
- No momento do commit, `main` ficou um commit à frente de `origin/main`; nenhum push foi realizado.
- `_temporary/` e caches continuaram fora do commit.

Ainda não existe evidência causal suficiente para afirmar que Sol sempre terá melhor julgamento que Terra. O que existe é evidência suficiente para dizer que Terra foi inadequado naquela campanha, que o campo consultivo não controlava execução e que a política operacional escolhida pelo usuário é Sol medium.
