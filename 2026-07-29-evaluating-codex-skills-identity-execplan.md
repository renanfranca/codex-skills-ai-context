# Evoluir a identidade do site e criar a marca de evidências

Este ExecPlan é um documento vivo. Manter `Progresso`, `Decisões`, `Riscos` e `Lições aprendidas` atualizados durante a execução.

## Propósito / visão geral

Registrar com honestidade a mudança já realizada de “Codex Skills” para “Evaluating Codex Skills” e completar essa identidade com uma logo relacionada às avaliações. O visitante deverá reconhecer um “E” formado por registros de evidência, legível como favicon e na navegação, nos temas claro e escuro.

## Escopo

Inclui criar este ExecPlan, registrar como concluídas as mudanças de título, subtítulo, metadados, responsividade e testes, substituir as duas variantes SVG da logo, adicionar uma regressão visual pelo caminho público com TDD comportamental silencioso e validar desktop, Pixel 7, tema claro, tema escuro e favicon.

Não inclui renomear o repositório, pacote, URL ou caminho `/codex-skills/`; alterar o título do `README.md` principal; reescrever conteúdo das skills ou avaliações; publicar ou criar commit; usar geração raster; ou modificar `.gitattributes`.

## Definições

- **Marca de evidências:** um “E” cuja haste representa o arquivo e cujos três traços representam execuções registradas.
- **Regressão visual:** comparação da logo realmente renderizada pelo navegador com imagens de referência aprovadas.
- **Caminho público:** o site servido em `/codex-skills/`, observado como um visitante o utiliza.
- **TDD comportamental silencioso:** um comportamento por ciclo, teste falhando antes da implementação, suíte relevante completa e comunicação apenas para decisões ou falhas importantes.

## Contexto existente

A identidade textual já foi implementada em configuração, gerador de conteúdo, tema e testes. O título é `Evaluating Codex Skills`, o subtítulo é `Evidence of how effectively skills guide Codex behavior.`, o hero é responsivo em duas linhas e os metadados e rótulos acessíveis foram atualizados. Sete testes de conteúdo, dez testes de navegador, build, Prettier e `git diff --check` foram aprovados antes deste ExecPlan.

Essa implementação ocorreu antes da solicitação de usar TDD e não será retroativamente descrita como test first.

A logo atual é um “E” geométrico genérico em `website/public/mark-light.svg` e `website/public/mark-dark.svg`. Os caminhos públicos, as dimensões `32 × 32`, o fundo arredondado e a paleta já funcionam em ambos os temas.

Diretórios `_temporary/` e `__pycache__/` não rastreados já existiam e permanecerão fora do trabalho.

## Estado desejado

A identidade textual permanece inalterada. A logo representa um registro de avaliações sem sugerir certificação universal ou apenas resultados positivos. O símbolo continua legível em 16, 24 e 32 px. Tema claro, tema escuro e favicon usam a mesma geometria. A alteração é protegida por teste visual no navegador, sem testar elementos internos como comandos `<path>`.

As interfaces públicas permanecem `/codex-skills/`, `/mark-light.svg`, `/mark-dark.svg`, `viewBox="0 0 32 32"` e o nome acessível `Evaluating Codex Skills`. Muda apenas a aparência pública dos dois SVGs.

## Marcos

### Marco 1: persistir e reconciliar o ExecPlan

Criar este arquivo na raiz, registrar as alterações textuais existentes como concluídas e conferir o diff atual para não atribuir ao trabalho arquivos não relacionados.

Arquivos: `2026-07-29-evaluating-codex-skills-identity-execplan.md`.

Validação: `git status --short` e `git diff --check`.

Aceitação: o documento distingue claramente trabalho concluído de trabalho futuro e preserva arquivos não relacionados.

### Marco 2: definir o comportamento visual e obter RED

Preparar em `/tmp` o desenho alvo e suas referências visuais antes de alterar os SVGs de produção. O símbolo terá fundo `32 × 32` com `rx="3"`; haste em `(7,6)` com `3 × 20`; traços em `(10,6)` com `10 × 3`, `(10,14)` com `7 × 3` e `(10,23)` com `10 × 3`; e marcadores `3 × 3` em `(22,6)`, `(19,14)` e `(22,23)`. O espaço entre cada traço e marcador representa o resultado registrado separadamente da execução.

No tema claro, o fundo será `#171713` e o símbolo `#c8ea72`. No tema escuro, o fundo será `#f3f0e7` e o símbolo `#c14b2f`.

Arquivos: `website/e2e/site.spec.mjs`, referências em `website/e2e/site.spec.mjs-snapshots/` e protótipo descartável em `/tmp`.

Validação: `npm run test:e2e`, executado em `website/`.

Aceitação: apenas a nova regressão visual falha porque a logo atual não corresponde às referências; as jornadas anteriores continuam aprovadas.

### Marco 3: implementar a nova logo e obter GREEN

Substituir somente a geometria de `website/public/mark-light.svg` e `website/public/mark-dark.svg`, preservando caminhos, dimensões, papel de imagem e nome acessível.

Validação: `npm run test:e2e`, executado em `website/`.

Aceitação: a regressão visual e todas as jornadas existentes passam em desktop e Pixel 7. Esse resultado também é o checkpoint pelo caminho público exigido pelo TDD.

### Marco 4: validar e finalizar o documento vivo

Executar em `website/`, nesta ordem: `npm test`, `npm run prettier:check`, `npm run build` e `npm run test:e2e`. Depois executar `git diff --check` na raiz. Servir o build com `npm run preview -- --host 127.0.0.1` e inspecionar `http://127.0.0.1:4173/codex-skills/` em tema claro e escuro, confirmando legibilidade em desktop, celular e favicon.

Arquivos: este ExecPlan, atualizado com resultados, decisões finais, riscos e lições.

Aceitação: todas as validações passam, a inspeção pública confirma a marca e o documento registra qualquer surpresa encontrada.

## Progresso

- [x] Alterar o título para `Evaluating Codex Skills`.
- [x] Adicionar o novo subtítulo.
- [x] Ajustar o hero para desktop e celular.
- [x] Atualizar metadados, acessibilidade e testes existentes.
- [x] Validar conteúdo, formatação, build e jornadas antes deste ExecPlan.
- [x] Criar o arquivo persistente do ExecPlan.
- [x] Adicionar a regressão visual e confirmar RED.
- [x] Implementar a marca “E de evidências”.
- [x] Confirmar GREEN pelo caminho público.
- [x] Executar a validação completa.
- [x] Atualizar o ExecPlan com resultados e lições.

## Decisões

- Decisão: usar o conceito “E de evidências”.
  Razão: preserva continuidade com a marca atual e representa múltiplas execuções arquivadas.
  Data/autor: 2026-07-29 / usuário e Codex.

- Decisão: preservar paleta, dimensões e URLs dos SVGs.
  Razão: evita quebra de referências e mantém integração com o tema existente.
  Data/autor: 2026-07-29 / Codex.

- Decisão: usar regressão visual pelo navegador.
  Razão: a forma percebida é o contrato público; testar comandos SVG seria acoplamento à implementação.
  Data/autor: 2026-07-29 / Codex.

- Decisão: não usar geração de imagem.
  Razão: a logo é um ativo vetorial pequeno e nativo do sistema visual existente.
  Data/autor: 2026-07-29 / Codex.

- Decisão: manter referências separadas por projeto Playwright.
  Razão: desktop e Pixel 7 renderizam o mesmo SVG com características de dispositivo diferentes; referências próprias evitam mascarar uma regressão específica de viewport.
  Data/autor: 2026-07-29 / Codex.

## Riscos e mitigações

- Risco: detalhes desaparecerem como favicon.
  Mitigação: geometria inteira, poucos elementos e inspeção em 16, 24 e 32 px.

- Risco: snapshots variarem por ambiente.
  Mitigação: capturar apenas o SVG, usar Chromium fixado pelo lockfile e evitar texto ou efeitos suavizados.

- Risco: o símbolo parecer um relatório com resultados exclusivamente positivos.
  Mitigação: usar marcadores neutros, sem check, selo ou cor semântica de aprovação.

- Risco: cache manter a logo anterior após publicação.
  Mitigação: confirmar o artefato do build e documentar recarregamento forçado durante a verificação.

- Risco: arquivos não relacionados entrarem na mudança.
  Mitigação: revisar `git status --short` e limitar o diff ao site, aos snapshots e a este ExecPlan.

## Estratégia de validação

A prova principal é a jornada visual no navegador em ambos os temas e perfis de tela. Testes de conteúdo preservam título e subtítulo; o build confirma a integração do VitePress; a inspeção manual verifica o significado visual que a automação não consegue julgar.

O aviso já existente sobre bundle acima de 500 kB será registrado como não relacionado e não bloqueará esta mudança.

Validação final em 2026-07-29:

1. `npm test`: sete testes aprovados.
2. `npm run prettier:check`: todos os arquivos verificados seguem o formato.
3. `npm run build`: build concluído; permaneceu apenas o aviso já conhecido de chunk acima de 500 kB.
4. `npm run test:e2e`: doze jornadas aprovadas em desktop e Pixel 7.
5. `git diff --check`: nenhuma ocorrência.
6. Preview público: o artefato contém os mesmos SVGs de produção, o HTML aponta o favicon para `/codex-skills/mark-light.svg` e as variantes foram inspecionadas em 16, 24 e 32 px.

## Publicação e recuperação

Nenhuma publicação será feita durante a implementação. Depois de revisão e commit separados, o fluxo existente do GitHub Pages publicará o site quando as mudanças chegarem à branch principal.

Para recuperar, reverter os dois SVGs e suas referências visuais. Título, subtítulo, slug e relatórios não precisam ser revertidos para desfazer apenas a logo.

## Lições aprendidas

- O controle de aparência do VitePress expõe o papel acessível `switch`, não `button`. No Pixel 7 ele só fica disponível depois que o visitante abre o botão `mobile navigation`; a jornada visual passou a reproduzir esses dois caminhos públicos.
- As referências do símbolo alvo foram geradas a partir dos SVGs descartáveis em `/tmp` antes de qualquer alteração na geometria de produção.
- O RED esperado foi confirmado nos dois projetos: somente a nova jornada falhou, com 85 pixels diferentes na variante clara; as outras dez jornadas passaram.
- O GREEN pelo caminho público aprovou as doze jornadas em desktop e Pixel 7, incluindo as variantes clara e escura.
- A geometria inteira permaneceu legível em 16, 24 e 32 px. Em 16 px os marcadores ainda se distinguem dos traços, embora essa seja a menor escala útil; reduzir além disso exigiria uma variante óptica simplificada.
- A inspeção confirmou o mesmo “E de evidências” no favicon, na navegação clara e na navegação escura. O favicon existente usa deliberadamente `mark-light.svg`, enquanto a navegação alterna entre os dois ativos.
- As referências ficaram estáveis em execuções repetidas nos dois projetos. Elas permanecem específicas por projeto porque a densidade de pixels do Pixel 7 faz parte do ambiente observado.
