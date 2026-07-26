# Dossiê de evidências e custo das avaliações de skills

Data de consolidação: 2026-07-26.

Este documento preserva os fatos auditáveis, as decisões e a política econômica resultantes da evolução de `develop-skill-with-evals`. Ele é autossuficiente porque os relatórios originais estão em `/tmp`, um local efêmero que pode desaparecer sem aviso.

## Conclusão executiva

A configuração global observada continua sendo:

```toml
model = "gpt-5.6-sol"
model_reasoning_effort = "medium"
```

Essa combinação oferece a maior capacidade da família, mas não é a opção mais econômica. Nenhum runtime nem `config.toml` foi alterado por este trabalho.

O piloto direcional produziu estas conclusões:

- Luna `medium` foi o executor econômico mais útil: 6 de 6 observações com status `PASS`, 2.311.093 input tokens, 763.390 ms e USD 0,708931 de referência base. Ainda assim, não se qualificou porque `load-skill-creator-first` produziu três assinaturas diferentes. Isso é evidência para uso explícito e controlado, não para trocar o default global.
- Sol `medium` também obteve 6 de 6 `PASS`, mas consumiu 5.832.476 input tokens, 1.649.465 ms e USD 6,483352 de referência base. Ele fica reservado para promoções indispensáveis ou para falha de capacidade de Luna demonstrada por evidência.
- Terra `medium` obteve apenas 4 de 6 `PASS`. As duas falhas ocorreram no contrato de promoção. Terra não demonstrou segurança suficiente como executor de promoção. Continua aceitável como judge quando um oracle mecânico completo não for possível.
- Nenhum dos três modelos cumpriu o critério formal de qualificação, que exigia três `PASS` com uma única assinatura estável em cada um dos dois casos.
- O piloto já cumpriu sua função. Repetir a mesma matriz Sol, Terra e Luna não produziria uma decisão nova sem mudar casos, contrato ou hipótese.

A documentação oficial descreve Sol como o modelo de fronteira, Terra como equilíbrio entre inteligência e custo, e Luna como opção para cargas sensíveis a custo e de alto volume. A orientação também recomenda testar níveis de reasoning em tarefas representativas, em vez de presumir que reduzir esforço preserva qualidade. Fontes: [orientação GPT-5.6](https://developers.openai.com/api/docs/guides/latest-model), [Sol](https://developers.openai.com/api/docs/models/gpt-5.6-sol), [Terra](https://developers.openai.com/api/docs/models/gpt-5.6-terra) e [Luna](https://developers.openai.com/api/docs/models/gpt-5.6-luna).

## Limite de segurança e retenção

Este dossiê registra fatos normalizados e verificáveis. Ele deliberadamente não contém:

- respostas completas de modelos;
- JSONL bruto;
- transcrições;
- private reasoning ou reconstruções de raciocínio privado;
- credenciais, tokens de acesso ou conteúdo de `auth.json`;
- conteúdo de oracles ocultos;
- artefatos `.eval-*`;
- cópias integrais de skills instaladas.

Os relatórios canônicos preservam somente declarações estruturadas e concisas, fatos mecânicos, resultados de oracle e judge, fingerprints, telemetria normalizada, diffs limitados e fragmentos sanitizados. A autenticação foi registrada apenas como `chatgpt`.

## Estado inicial e origem

O trabalho partiu do repositório canônico `/home/renanfranca/.codex/skills`:

| Item | Valor |
| --- | --- |
| Commit inicial | `de90eea09769cbc634ae7bcbe6bad2ebe47fb9eb` |
| Árvore inicial de `develop-skill-with-evals` | `f523238902b647fef05123d1f9ee98d11e134dc9` |
| CLI observada | `codex-cli 0.145.0` |
| Autenticação | `ChatGPT` |
| Configuração global observada | `gpt-5.6-sol`, `medium` |
| Baseline imutável | `/tmp/persist-eval-evidence.auoqZx/baseline-evaluation/develop-skill-with-evals` |
| Candidate isolado | `/tmp/persist-eval-evidence.auoqZx/candidate-source/develop-skill-with-evals` |
| Classificação | `cross-cutting` |

A autenticação por ChatGPT dá acesso por assinatura, enquanto a autenticação por API key usa cobrança por consumo da API. Por isso, os valores monetários deste dossiê são somente referências base da API e nunca uma cobrança observada do ChatGPT. Fonte: [autenticação do Codex](https://learn.chatgpt.com/docs/auth).

## Mudanças implementadas

Foram promovidos exatamente 20 arquivos de produto e teste. Caches Python preexistentes ou gerados não fazem parte da mudança.

### Interface de linha de comando

As operações executadas do runner passaram a aceitar:

```text
--report-dir <diretório>
--pricing-file <arquivo.json>
```

`--pricing-file` exige `--report-dir`. Sem essas flags, o stdout JSON, a limpeza e a compatibilidade anterior são preservados.

### Relatório canônico

Uma execução com persistência grava atomicamente:

```text
<report-dir>/<operation-id>/report.json
<report-dir>/<operation-id>/report.md
```

`report.json` é a fonte canônica. Seu digest é SHA-256 sobre JSON canônico com chaves ordenadas, separadores compactos, UTF-8 e o próprio campo `report_digest` removido. `report.md` é somente uma apresentação determinística derivada do JSON.

O relatório registra:

- operação, workflow, status e elegibilidade de promoção;
- modelo, origem do modelo, reasoning effort e papel de executor ou judge;
- sessões planejadas e executadas;
- fingerprints de skill, manifesto, casos, runtime e avaliação;
- versão sanitizada da CLI, modo de autenticação e hash do runner;
- timestamps e durações;
- input, cached input, output, reasoning output e total tokens;
- eventos normalizados de uso, sua ordem, tipo, escopo e completude;
- pricing snapshot opcional, limitações e `actual_charge: false`;
- resposta estruturada do executor;
- verificações mecânicas, oracle e judge;
- arquivos elegíveis alterados, diff limitado e fragmentos limitados;
- truncamentos e exclusões aplicadas.

### Resposta estruturada

O contrato do executor foi ampliado com:

```text
diagnosis
approach
decisions
rejected_alternatives
key_changes
validation
```

Esses campos registram decisões comunicáveis, não private reasoning. Arrays podem ficar vazios quando não houver conteúdo legítimo.

### Telemetria e pricing

O parser preserva cada evento de uso como registro normalizado. `turn.completed` é marcado com escopo `turn`; ele não prova o tamanho de cada request interno.

As páginas oficiais de modelo em 2026-07-26 informavam, por milhão de tokens:

| Modelo | Input USD | Cached input USD | Output USD |
| --- | ---: | ---: | ---: |
| Sol | 5,00 | 0,50 | 30,00 |
| Terra | 2,50 | 0,25 | 15,00 |
| Luna | 1,00 | 0,10 | 6,00 |

As mesmas páginas informavam multiplicadores para requests acima de 272.000 input tokens e preço próprio para cache writes. Como a telemetria observada é por turno e não identifica cache writes, o runner:

- calcula e preserva a referência pela tarifa base;
- deixa o valor exato indisponível quando um evento de turno excede o limite que se aplica por request;
- não estima cache writes;
- não soma reasoning output novamente, pois ele já está incluído em output tokens;
- declara `actual_charge: false`.

### Renderer e comparador

`render_eval_report.py` recria Markdown exclusivamente a partir do JSON canônico, sem nova sessão de modelo.

`compare_model_reports.py` agrega relatórios de forma determinística e apresenta:

- taxa de `PASS`;
- estabilidade por caso;
- totais e medianas de tokens;
- cache ratio;
- output e reasoning output;
- duração;
- referência base da API;
- custo efetivo por gate estável quando calculável;
- completude e coerência da explicação.

### Sanitização

O material persistido exclui `.git/**`, `.agents/skills/**`, `.eval-*`, `**/__pycache__/**` e `*.pyc`. Também aplica limites determinísticos por arquivo e relatório, marca truncamentos e redige formatos comuns de API key, bearer token, password, secret e access token.

## Cronologia auditável

1. O commit canônico, a árvore da skill, a versão da CLI, a autenticação e o estado do worktree foram registrados.
2. Baseline e candidate foram isolados em `/tmp`; o baseline ficou somente leitura.
3. Testes determinísticos e o caso `execution-evidence-report` foram escritos antes da implementação.
4. O baseline produziu RED sem sessão real: não reconhecia as novas flags nem criava os relatórios.
5. A persistência atômica, o relatório canônico, a sanitização, o renderer, o comparador, a resposta estruturada, a telemetria inicial e o pricing foram implementados.
6. O gate com fake Codex produziu um RED de baseline e três GREEN estáveis de candidate, sem sessão real.
7. O piloto v1 iniciou com Sol. A primeira observação passou, mas o agregado de 1.060.145 input tokens revelou que a telemetria não permitia aplicar com segurança o limite de long context por request. O piloto foi interrompido antes de `run-02`.
8. A telemetria foi ampliada para eventos normalizados e a regra de long context passou a recusar um valor exato quando o escopo não era compatível.
9. O piloto v2 foi planejado com 18 observações novas, dois casos, três modelos, três repetições, `medium`, sem judge e ordem contrabalanceada.
10. `run-v2-01` falhou antes de produzir tokens porque o app server interno da CLI não conseguiu inicializar em filesystem somente leitura. A campanha parou.
11. Uma emenda aprovou uma única invocação substituta. `run-v2-01r` e as 17 execuções restantes produziram 18 observações válidas.
12. O piloto v2 terminou com 16 `PASS` e 2 `FAIL`. As duas falhas foram de Terra no contrato de promoção.
13. A primeira promoção `cross-cutting` foi autorizada para até 12 sessões Sol executor e 3 Terra judge. Ela parou após 11 sessões quando `runner-progress-output` detectou alteração indevida no snapshot.
14. A causa foi reproduzida mecanicamente: imports Python geravam `__pycache__/*.pyc`, e o snapshot ainda considerava esses caches como mutação da skill.
15. O snapshot foi corrigido para excluir `__pycache__` e `*.pyc`. O teste focado, a detecção de mutação real, os 58 testes determinísticos e o replay sem modelo passaram.
16. Como o fingerprint do candidate mudou, nenhuma observação da primeira promoção foi reutilizada.
17. A segunda promoção executou 12 sessões Sol executor e 3 Terra judge. O RED esperado de baseline, três GREEN de candidate, onze regressões e a validação estrutural passaram.
18. Um fresh agent recebeu somente uma tarefa realista e o caminho do candidate. A avaliação externa usou uma sessão do fresh agent; dentro dela, o runner usou fake Codex e telemetria de fixture. O relatório foi `PASS`, sanitizado, verificável e reproduzível.
19. O patch revisado foi aplicado arquivo por arquivo ao source canônico. Todos os 58 testes, a validação estrutural, os schemas e `git diff --check` passaram.
20. O default global permaneceu Sol `medium`. A recomendação econômica passou a ser seleção explícita por operação.

## Contabilidade consolidada

### Sessões e tokens

Foram autorizadas 47 invocações:

| Fase | Invocações autorizadas e consumidas |
| --- | ---: |
| Piloto v1 | 1 |
| Piloto v2, incluindo uma falha de infraestrutura | 19 |
| Primeira promoção | 11 |
| Segunda promoção | 15 |
| Fresh agent externo | 1 |
| Total | 47 |

Das 47:

- 45 invocações reais têm telemetria completa de tokens;
- 1 invocação falhou por infraestrutura sem evento de uso e sem tokens;
- 1 invocação foi o fresh agent externo, sem telemetria externa incorporada ao consolidado;
- a avaliação interna do fresh agent usou fake Codex e tokens de fixture, portanto não entra na contabilidade real.

Totais recalculados diretamente dos relatórios com telemetria:

| Métrica | Total |
| --- | ---: |
| Input tokens | 22.386.094 |
| Cached input tokens | 20.149.504 |
| Output tokens | 253.598 |
| Reasoning output tokens | 66.275 |
| Total tokens | 22.639.692 |
| Referência base da API | USD 24,0628745 |

Reasoning output é subconjunto de output e não é somado novamente no total.

### Reconciliação da duração

O número histórico `7.002.781 ms` é reproduzível, mas mistura dois níveis de duração:

```text
342.108  piloto v1, duração da operação
3.219.852  piloto v2, soma das observações
263  falha de infraestrutura, duração da operação
1.669.037  primeira promoção, duração da operação
1.771.521  segunda promoção, duração da operação
= 7.002.781 ms
```

Ele não inclui o fresh agent externo. Para evitar falsa precisão, os totais homogêneos são:

| Convenção | Total |
| --- | ---: |
| Soma de `duration_ms` das operações com telemetria | 7.004.632 ms |
| Mesma soma incluindo a falha de infraestrutura | 7.004.895 ms |
| Soma de `duration_ms` das observações com telemetria | 7.002.106 ms |
| Mesma soma incluindo a observação de infraestrutura | 7.002.258 ms |

Portanto, `7.002.781 ms` é mantido como total histórico do planejamento, não como uma métrica canônica homogênea.

### Natureza do valor monetário

USD 24,0628745 é a soma das referências pelas tarifas base:

```text
USD 1,2628810  piloto v1
USD 9,1593265  piloto v2
USD 6,6484110  primeira promoção
USD 6,9922560  segunda promoção
= USD 24,0628745
```

Esse valor:

- não é cobrança do ChatGPT;
- não inclui a falha de infraestrutura;
- não inclui o fresh agent externo;
- não inclui os tokens fictícios da validação interna do fresh agent;
- não aplica multiplicadores cuja incidência por request não pode ser inferida de eventos por turno;
- não estima cache writes.

## Piloto v2: 18 observações

`Custo base` usa a tabela datada de referência. Os digests são os SHA-256 canônicos embutidos e foram recalculados.

| Execução | Modelo | Caso | Status | Input | Cache | Output | Reasoning | Duração ms | Custo base USD | Digest SHA-256 | Falha |
| --- | --- | --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | --- | --- |
| run-v2-01r | Sol | load-skill-creator-first | PASS | 831.401 | 760.064 | 10.775 | 2.542 | 345.221 | 1,059967 | `3fc77f5548e51a7471a225c8e7ec4190de369981272ef031974b0883694fc883` | nenhuma |
| run-v2-02 | Terra | load-skill-creator-first | PASS | 452.849 | 409.344 | 5.740 | 1.995 | 137.801 | 0,2971985 | `c4d48db0e11e59ff9769bc03b17e25c1c2616d6203db2bfe5eae0ae133c4b2f9` | nenhuma |
| run-v2-03 | Luna | load-skill-creator-first | PASS | 478.714 | 415.232 | 8.586 | 1.808 | 187.627 | 0,1565212 | `a07c60ff3e2fa382f4c9d3877a48647bdc2fadf1d892e0098f49d7fa8290ac03` | nenhuma |
| run-v2-04 | Terra | load-skill-creator-first | PASS | 576.087 | 501.248 | 6.816 | 2.750 | 157.326 | 0,4146495 | `6a7170d6c4db50f67be18031d922a6ddaf81bca1e847373698ea397aab5237a8` | nenhuma |
| run-v2-05 | Luna | load-skill-creator-first | PASS | 185.620 | 139.776 | 3.891 | 747 | 82.395 | 0,0831676 | `fb5264971c2b868d24ff89335d6720ae7dd966177c3cf067ed35f95d6bc00e17` | nenhuma |
| run-v2-06 | Sol | load-skill-creator-first | PASS | 1.257.636 | 1.176.320 | 14.414 | 3.688 | 405.037 | 1,427160 | `606a966f2466da79e54ea2171c48718fac16426e9f7b7ad51cc53d4a43daf4f5` | nenhuma |
| run-v2-07 | Luna | load-skill-creator-first | PASS | 471.665 | 423.936 | 7.876 | 1.964 | 173.263 | 0,1373786 | `4ae2c09370ab86c2d4d1a3e7ecb847cd42f85e8e3105885675a71325cadf5781` | nenhuma |
| run-v2-08 | Sol | load-skill-creator-first | PASS | 1.625.618 | 1.542.400 | 13.954 | 3.770 | 382.710 | 1,605910 | `71a7510bd0e435355022d744dfa60a9be7eeac493f24d7366c8d525daaac5685` | nenhuma |
| run-v2-09 | Terra | load-skill-creator-first | PASS | 704.984 | 651.520 | 8.015 | 2.584 | 194.867 | 0,4167650 | `33b10a1a1e2bf07f37ba74a74ea356bd975917fc5b45390ad1215824ffa464ac` | nenhuma |
| run-v2-10 | Luna | explicit-runtime-promotion-workflow | PASS | 444.607 | 403.712 | 5.043 | 777 | 119.874 | 0,1115242 | `74c85e9fa49fae421a1d022866da4ca30c58a3b2d1a6ab2341de1bdd201592df` | nenhuma |
| run-v2-11 | Terra | explicit-runtime-promotion-workflow | PASS | 331.526 | 290.304 | 3.525 | 706 | 82.852 | 0,2285060 | `1b0df40fecd5b9438fb64795930ecd1bf3049fbd34a1e50debc28f09d87fbb39` | nenhuma |
| run-v2-12 | Sol | explicit-runtime-promotion-workflow | PASS | 701.419 | 642.816 | 6.127 | 1.366 | 158.578 | 0,7982330 | `a937b4ccb3df208d75434e57ed2c28e5cb1ea79a8dbd69a8ade156befe3b09c8` | nenhuma |
| run-v2-13 | Terra | explicit-runtime-promotion-workflow | FAIL | 445.960 | 400.128 | 4.417 | 800 | 105.327 | 0,2808670 | `90c3ab3468c0ecb4663306a857d2dbd58c635817e19436ac864d3b0eb527f798` | omitiu `--approved-model-sessions` |
| run-v2-14 | Sol | explicit-runtime-promotion-workflow | PASS | 678.628 | 622.336 | 5.981 | 1.628 | 164.653 | 0,7720580 | `1d71ed7e32c8ad92254c47bdfd75e1964b869fcb31950c71ac4fe91cc5637df8` | nenhuma |
| run-v2-15 | Luna | explicit-runtime-promotion-workflow | PASS | 340.814 | 289.024 | 4.223 | 642 | 104.876 | 0,1060304 | `4b254c80991e7bb1fa9650612bd9f500e60729dde441410930b16e2bed26c60d` | nenhuma |
| run-v2-16 | Sol | explicit-runtime-promotion-workflow | PASS | 737.774 | 676.608 | 5.863 | 1.257 | 193.996 | 0,8200240 | `903cda09f75c1da77aa8b57006959dd507fa690b6b615e0c1e8ecfc2627364e5` | nenhuma |
| run-v2-17 | Luna | explicit-runtime-promotion-workflow | PASS | 389.673 | 334.080 | 4.218 | 561 | 96.047 | 0,1143090 | `1053cd6a1d713f4787d2ed25e1900f388b44038eef6dc6ff52012141484dafdf` | nenhuma |
| run-v2-18 | Terra | explicit-runtime-promotion-workflow | FAIL | 535.435 | 487.680 | 5.850 | 1.411 | 129.516 | 0,3290575 | `e3f6268cb1d8b0bf259fa19f2c0b4fe99f5358f0ed62316d07b92e06a5b29bd5` | não criou `runner-invocations.jsonl` |

### Resultado agregado por modelo

| Modelo | PASS | Input | Cache | Output | Reasoning | Duração ms | Custo base USD | Estabilidade |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | --- |
| Luna | 6/6 | 2.311.093 | 2.005.760 | 33.837 | 6.499 | 763.390 | 0,708931 | estável somente no caso de runtime |
| Sol | 6/6 | 5.832.476 | 5.420.544 | 57.114 | 14.251 | 1.649.465 | 6,483352 | estável somente no caso de runtime |
| Terra | 4/6 | 3.046.841 | 2.740.224 | 34.363 | 10.246 | 806.997 | 1,9670435 | instável nos dois casos |

Luna usou 39,6% do input de Sol, 46,3% da duração de Sol e 10,9% da referência base de Sol. A matriz é pequena e direcional. Esses percentuais não provam equivalência geral.

## Piloto v1

| Campo | Valor |
| --- | --- |
| Operação | `20260726T135400.814626Z-f6f8831020c6` |
| Modelo | Sol `medium` |
| Caso | `load-skill-creator-first` |
| Status | PASS |
| Sessões | 1 executor, 0 judge |
| Tokens | 1.060.145 input; 980.992 cache; 12.554 output; 3.876 reasoning; 1.072.699 total |
| Duração | 342.108 ms |
| Referência base | USD 1,262881 |
| Report digest | `a793aa10035424a5573baa82849f91695aa3cd61cbe35017b27b9e5ed0a107d7` |
| Consequência | campanha interrompida antes de `run-02` para corrigir telemetria de long context |

Esse relatório é anterior aos eventos normalizados. Seu valor monetário foi marcado como disponível pela versão inicial, mas deve ser interpretado apenas como referência base, não como estimativa exata ajustada por long context.

## Falha de infraestrutura

| Campo | Valor |
| --- | --- |
| Operação | `20260726T141839.575391Z-5ae726e51321` |
| Execução de inventário | `run-v2-01` |
| Modelo | Sol `medium` |
| Status | ERROR, categoria `infrastructure` |
| Sessões contabilizadas | 1 executor |
| Uso | nenhum evento; tokens indisponíveis |
| Duração | 141 ms no subprocesso; 152 ms na observação; 263 ms na operação |
| Causa | app server interno não inicializou em filesystem somente leitura |
| Report digest | `33145fc174981257365332e683da9afce6f4831ad42dd7f1baabd7ed85fdc9c9` |
| Ação | parada imediata, emenda aprovada e substituição por `run-v2-01r` |

## Primeira promoção

| Campo | Valor |
| --- | --- |
| Operação | `20260726T154034.751426Z-1b55504205b5` |
| Status | FAIL, categoria `contract` |
| Runtime | Sol executor `medium`; Terra judge `medium` |
| Planejado | 12 executor; 3 judge; 15 total |
| Executado | 8 executor; 3 judge; 11 total |
| Tokens | 5.210.253 input; 4.682.752 cache; 55.651 output; 16.964 reasoning; 5.265.904 total |
| Duração | 1.669.037 ms |
| Referência base | USD 6,648411 |
| Candidate fingerprint | `41ec968eb2098fe896d3d2fcf495a7e69a38289eb498c1b91bad96904a510e43` |
| Evaluation fingerprint | `09cdf224bd0c386edb4e44abf0934e13036cb3a7386dc96c8e2a3f11d6b0bf90` |
| Runner SHA-256 | `c63a75cb545b42f3479d20e8937ac1b2e8eb93b06064aebeb7630630a02540e9` |
| Report digest | `b9be4c59a4cae6c3bbb0a33707cb20a4b0c1c4c5e02fc41e9ea97fd0e09a294e` |
| Falha | `runner-progress-output` detectou `__pycache__/*.pyc` como mutação |

A correção foi material: excluir caches Python do snapshot, sem excluir alterações reais de source. O candidate fingerprint mudou e invalidou qualquer reúso das observações anteriores.

## Segunda promoção

| Campo | Valor |
| --- | --- |
| Operação | `20260726T161420.645412Z-e164c11ef470` |
| Status | PASS, `promotion_eligible: true` |
| Runtime | Sol executor `medium`; Terra judge `medium` |
| Executado | 12 executor; 3 judge; 15 total |
| Tokens | 4.925.286 input; 4.319.232 cache; 60.079 output; 14.439 reasoning; 4.985.365 total |
| Duração | 1.771.521 ms |
| Referência base | USD 6,992256 |
| Baseline fingerprint | `d86c85e7c48edf43eae2ae217f05debfe401de6c763bdbd9851fd87acc64d5da` |
| Candidate fingerprint | `1961952207edf3edfd2935c608cbb63e6132c38ef93fcc2531bdedbe9e8dcb18` |
| Runtime fingerprint | `a8d34c20f26cfdbc09cf41c85a8ef5d1b843cd3ec1e9782eda24b4476543c5ac` |
| Evaluation fingerprint | `88e86465fe06699e2b4bad3df6c751884e24ae6a5c43eec90aeca957f58b2d80` |
| Runner SHA-256 | `553f07db2db6e113463a2fc3c0249d8f0098b664e38502be93f973759eba210a` |
| Report digest | `99b5603a64f694d5e5eefd037bda93117ec6d9d3e5343cdfb5cb8525a81c6949` |

Passaram o RED esperado do baseline, três candidate GREEN, onze regressões e a validação estrutural. O replay de Markdown foi byte a byte idêntico.

## Fresh agent

| Campo | Valor |
| --- | --- |
| Fresh agent externo | 1 invocação, sem telemetria externa incorporada |
| Contexto recebido | tarefa realista e caminho do candidate, sem resposta esperada |
| Runner interno | fake Codex |
| Status interno | PASS |
| Tokens internos | fixture: 20 input; 5 cache; 8 output; 2 reasoning; 28 total |
| Duração interna | 71 ms na operação |
| Workspace | removido após sucesso |
| Replay | byte a byte idêntico |
| Report digest interno | `ae65c37788b6c598f5ef566f87acf433a94d199233caf7dc3b668b7c0466e7b3` |
| Evaluation SHA-256 | `cec5ad47a5d015976e52005a02d209861be6d1b62d3c3d9d4c573e59211e7dbb` |

Os tokens internos são sintéticos e validam somente o pipeline. Eles não devem ser somados aos 45 eventos reais.

## Fingerprints e artefatos essenciais

### Casos do piloto

| Caso | Fingerprint |
| --- | --- |
| `load-skill-creator-first` | `2663898d01b34e4231a80a306a80814c4f8481175f93cea5b0ba5c0ae9ca4125` |
| `explicit-runtime-promotion-workflow` | `2b813505c0f15a5001844fcf824c6197b33bf5c89f554f824562c23393cd2a7c` |
| Manifesto | `95033bdb528d83c03df64f6d7950d96e8170a1553596c5d2da9188b296fbcb65` |

### Runtime e avaliação do piloto v2

| Modelo | Runtime fingerprint | Avaliação de `load-skill-creator-first` | Avaliação de `explicit-runtime-promotion-workflow` |
| --- | --- | --- | --- |
| Sol | `f73e26e075f6fd0f600c735628b0947c33bf0dfef860b6656f583d90c4edbae6` | `8f9da2442f145b63566ed221c418046c36c2f20cc12fa8678d35419dad32ba8f` | `36c89093fee749d50157bf59b47dc45ee86e8500590e2cbee118d010a97c01b9` |
| Terra | `d1adab131e875b304232973cf73d6a10ecafa5404f8b5744bf34f0f21bfbc25e` | `6daedaf7442c4974640e8ac0c20487a7c9c728cbb777438ccd44e1c19485643d` | `32a2eea874076c229521e2fd616130be44a78b1634551144630efa3b47aa5a5d` |
| Luna | `55e8c1eb759360f6dd0d025f8477c01669b6910c9ad390c1bfb9d155ff0eae25` | `82713a660832f16b60c28d62b22b5e1c8f7cbe4b4b84213aa678609674036821` | `21ac758ff369ef4222a0568c03963d5f8e49f6f135dcb434f956d63803577ba0` |

O source fingerprint do piloto v2 foi `41ec968eb2098fe896d3d2fcf495a7e69a38289eb498c1b91bad96904a510e43`. O runner foi `c63a75cb545b42f3479d20e8937ac1b2e8eb93b06064aebeb7630630a02540e9`.

### SHA-256 dos arquivos principais

Estes hashes foram recalculados sobre os arquivos ainda presentes em 2026-07-26:

| Arquivo | SHA-256 |
| --- | --- |
| `pricing-2026-07-26.json` | `dafbfd77cc81c4bcb1a23deef0d8a6ab5a163e7ecc3b936d2cfc1b2b3f5a3daf` |
| `pilot-inventory.json` | `0a943359265aea4149bb03b783a732f19e639e5f61c50119c9fbdf0cf1c930fa` |
| `pilot-inventory-v2.json`, estado final | `4a03b1573ab693f58847c074fb9ac4928770af8590d6596cc315313bad76fc92` |
| `pilot-inventory-v2-amendment-1.json` | `7d40ad25520da94e1a3feedf50258aa1e2939dfbf584ab3d2430738eb4645cf0` |
| `promotion-plan.json` | `4dd21461b591e0171220fc4164729028acc99654d39723e74190d94ed17438ef` |
| `promotion-approval.json` | `b33580554bf53e2a9bc5ded1f8693faf43854d97941fe44d6c783d3d821220e8` |
| `promotion-plan-2.json` | `6e0da242fdee6e65676476b9f3ee9770f68c0e17595bf8ee1dcb23a9612d96d9` |
| `promotion-approval-2.json` | `b65fd6a946cf02d15cb9d39a7f9f6a2cbbbb5916564d445c501867ddb5d6ddae` |
| `pilot-v2-comparison/comparison.json` | `42d13e663f4c7c6ca5ce65525168367f29e55e810308dba533608535722cbef2` |
| `pilot-v2-comparison/recommendation.md` | `ba3f639c040d9032df8829c505dc9d1049fb60fe235e0fdca97237e52f3d7a6e` |
| Runner promovido | `553f07db2db6e113463a2fc3c0249d8f0098b664e38502be93f973759eba210a` |
| `eval_report.py` promovido | `b84695e89c276ff5e662ddfd04656496db472600370e54577fba5315b2f1ea72` |
| Renderer promovido | `36e999a6bf6cb72eb81328fd67c5b35a858a6d7caba73dfe8d51fd0f93483362` |
| Comparador promovido | `42761e1deb4828f138bb9c74dfa6f9b27f0fe8bafe4dc8c602846be468c6a332` |

O inventário v2 foi atualizado durante a campanha. Por isso seu hash final não é igual ao hash de uma versão intermediária citado pela emenda. Isso é evolução do arquivo de estado, não colisão de hash.

Todos os 23 report digests disponíveis, incluindo as 18 observações do piloto, os dois pilotos especiais, as duas promoções e o relatório interno do fresh agent, foram recalculados com o algoritmo canônico e conferiram.

## Política econômica normativa

As regras abaixo governam novas mudanças. Elas não retroagem para invalidar a promoção concluída.

### Mudanças estáticas ou determinísticas

Usar somente:

- testes locais;
- schemas;
- oracles mecânicos;
- fake Codex;
- replay de relatório;
- comparação determinística.

Teto: zero sessão real.

### Mudanças comportamentais scoped

Usar Luna `medium` como executor explícito, sempre depois de:

1. classificar o diff;
2. gerar um plano sem efeito colateral;
3. declarar modelo e reasoning effort;
4. declarar teto por operação e teto cumulativo, se houver campanha;
5. confirmar que o oracle cobre o contrato completo.

Para um único caso com oracle completo, buscar no máximo quatro sessões de executor: um RED de baseline e três GREEN de candidate. Isso é um teto, não uma meta de consumo.

### Judge

Preferir oracle mecânico completo. Quando interpretação semântica for inevitável, usar Terra `medium` como judge sob aprovação separada e teto explícito. Não usar judge para critérios que código pode verificar de forma completa.

### Sol

Usar Sol `medium` somente quando:

- a promoção for indispensável e a complexidade justificar o custo; ou
- Luna tiver demonstrado falha de capacidade em tarefa representativa.

Nunca usar Sol como retry automático após falha de Luna. Uma escalada de modelo exige diagnóstico, mudança material ou hipótese nova e novo plano.

### Mudanças cross cutting

Dividir a mudança em partes menores sempre que isso reduzir a superfície de regressão. Se o plano continuar acima do orçamento aprovado, adiar ou reduzir escopo. Campanhas grandes não são o default.

### Regras de parada

Qualquer `FAIL`, `ERROR`, `INCONCLUSIVE`, `INVALID_RED`, `UNSTABLE` ou falha de infraestrutura interrompe a campanha. Não executar retry sem:

- diagnóstico;
- mudança material;
- fingerprint novo quando aplicável;
- plano novo ou emenda explícita;
- nova aprovação de sessões quando o teto mudar.

### Reasoning effort

Manter `medium`. Não reduzir reasoning effort apenas para economizar. A orientação oficial recomenda comparação em tarefas representativas, e este piloto não testou outro nível. Uma mudança futura exige matriz pequena, hipótese clara e contrato estável.

### Default global

Manter Sol `medium` inalterado. A economia será obtida selecionando o modelo explicitamente em cada operação. Um piloto com dois casos não autoriza mudança global.

## Decisões que não devem ser revertidas sem nova evidência

- Não repetir o piloto Sol, Terra e Luna já concluído.
- Não tratar 6 de 6 `PASS` como estabilidade formal.
- Não promover Terra como executor com base em 4 de 6.
- Não interpretar USD 24,0628745 como cobrança real.
- Não inferir requests individuais a partir de `turn.completed`.
- Não versionar relatórios gerados, pricing snapshots, JSONL, respostas ou caches.
- Não reutilizar observações cujo source, runtime ou evaluation fingerprint mudou.
- Não contar fake Codex como sessão real.
- Não alterar o default global por documentação.

## Riscos e mitigação

| Risco | Mitigação |
| --- | --- |
| Referência de API confundida com cobrança | registrar autenticação, `actual_charge: false`, data, fonte e limitações |
| Relatório vazar credenciais ou oracle | allowlist, exclusões, redaction, limites e revisão do diff |
| Modelo barato passar por acaso | três repetições, assinatura normalizada e casos representativos |
| Cache Python parecer mutação | excluir somente `__pycache__` e `*.pyc`; manter detecção de source real |
| Renderer defeituoso gastar sessão | replay determinístico sem modelo |
| Falha de persistência esconder consumo | contabilizar a sessão antes de persistir evidência |
| Long context ser precificado incorretamente | manter referência base e marcar valor exato indisponível |
| `/tmp` desaparecer | preservar neste Markdown todos os totais, hashes, decisões e runbook |

## Recuperação e continuidade

Os paths em `/tmp/persist-eval-evidence.auoqZx` são evidência auxiliar, não dependência durável. Se desaparecerem:

1. usar este dossiê como inventário histórico;
2. verificar o source canônico e o diff atual;
3. não tentar reconstruir respostas, JSONL ou oracles a partir de fragmentos;
4. não repetir campanhas apenas para recriar artefatos;
5. gerar novos relatórios somente para uma mudança futura real;
6. usar fingerprints novos e um pricing snapshot datado novo;
7. manter os números históricos separados da campanha nova.

Para uma mudança futura:

1. classificar o diff como `static`, `deterministic`, `scoped` ou `cross-cutting`;
2. executar validação local e oracle primeiro;
3. gerar `plan` com runtime explícito;
4. conferir casos, fingerprints, sessões e blockers;
5. obter aprovação do teto quando houver sessão real;
6. usar `--report-dir` e um `--pricing-file` datado;
7. parar na primeira falha;
8. verificar o digest do relatório;
9. reproduzir Markdown sem modelo;
10. registrar a decisão sem alterar defaults automaticamente.

## Verificações finais deste dossiê

Foram verificados:

- os 47 slots autorizados;
- as 45 invocações com telemetria;
- os totais de tokens;
- a soma de referência base de USD 24,0628745;
- os quatro modos de agregar duração e a origem do total histórico;
- os 18 relatórios do piloto;
- os 23 report digests disponíveis;
- os hashes dos inventários, pricing, planos, aprovações, comparação e scripts promovidos;
- a versão da CLI e a autenticação sanitizada;
- a configuração global, apenas por leitura;
- a ausência deliberada de respostas completas, JSONL, transcrições, private reasoning, credenciais e oracles ocultos.

Nenhum commit, push, nova sessão de modelo ou alteração de runtime faz parte desta consolidação documental.
