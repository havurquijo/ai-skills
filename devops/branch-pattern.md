# SKILL — Padronização Geral de Branches para DevOps

## Objetivo

Este arquivo define o contexto mínimo e obrigatório que uma IA deve usar ao criar, sugerir, revisar ou automatizar fluxos de branches, merges, releases, hotfixes e promoção entre ambientes.

A IA deve seguir este padrão por default, salvo instrução explícita em contrário.

---

## Princípios gerais

1. O fluxo de branches deve ser simples, previsível e auditável.
2. Cada branch deve ter um propósito único.
3. Toda branch deve nascer da branch correta.
4. Não deve haver uso aleatório de nomes de branch.
5. Mudanças promovidas entre ambientes devem respeitar a ordem definida.
6. A IA deve preferir padronização operacional a criatividade de nomenclatura.
7. A IA deve gerar nomes curtos, legíveis e consistentes.
8. A IA deve evitar fluxos que causem drift entre ambientes.

---

## Branches base do projeto

Estas branches são consideradas padrão:

* `main`: produção
* `develop`: integração contínua de desenvolvimento
* `staging`: homologação, quando existir

### Regra de ambientes

Se o projeto usar três ambientes, a ordem padrão é:

`develop` → `staging` → `main`

Se o projeto usar apenas dois ambientes, a ordem padrão é:

`develop` → `main`

A IA deve assumir essa ordem, salvo contexto explícito diferente.

---

## Tipos permitidos de branches temporárias

A IA deve usar apenas estes prefixos, salvo instrução contrária:

* `feature/`
* `bugfix/`
* `hotfix/`
* `release/`
* `chore/`
* `refactor/`
* `docs/`
* `test/`
* `ci/`
* `build/`

---

## Convenção de nomenclatura

Formato padrão:

`<tipo>/YYYYMM/<escopo-ou-ticket>-<descricao-curta>`

A data deve seguir o formato:

* YYYY = ano
* MM = mês (2 dígitos)

Exemplo para Março de 2026:

`202603`

---

### Exemplos válidos:

* `feature/202603/auth-login`
* `feature/202603/pet-profile-form`
* `bugfix/202603/token-refresh`
* `hotfix/202603/payment-webhook`
* `chore/202603/update-dependencies`
* `ci/202603/deploy-hml`
* `build/202603/docker-api`
* `docs/202603/setup-railway`

### Com ticket:

* `feature/202603/abc-123-auth-login`
* `bugfix/202603/crm-88-import-timeout`

---

### Regras de nomenclatura

1. Usar letras minúsculas.
2. Usar hífen (`-`) para separar palavras.
3. Não usar espaços.
4. Não usar acentos.
5. Não usar nomes genéricos como `feature/teste`, `bugfix/correcao`, `ajustes-final`.
6. A descrição deve refletir a intenção principal da branch.
7. A IA deve evitar nomes longos demais.
8. Sempre incluir o bloco de data `YYYYMM` após o tipo.
9. Quando houver ticket, pode usar:

`<tipo>/YYYYMM/<ticket>-<descricao-curta>`

---

## Origem correta de cada tipo de branch

A IA deve respeitar estas regras:

### Feature

* Origem: `develop`
* Destino: `develop`

### Bugfix comum

* Origem: `develop`
* Destino: `develop`

### Hotfix de produção

* Origem: `main`
* Destino primário: `main`
* Depois deve ser replicado para `develop`
* Se existir `staging`, também deve ser reconciliado com `staging` quando necessário

### Release

* Origem: `develop`
* Destino: `staging` ou `main`, conforme fluxo do projeto

### Chore / Refactor / Docs / Test / CI / Build

* Origem: preferencialmente `develop`
* Destino: `develop`

---

## Regras de merge e promoção

### Regra geral

A IA deve evitar pular ambientes sem necessidade.

### Fluxo padrão com staging

1. Desenvolvimento entra em `develop`
2. Promoção controlada para `staging`
3. Validação
4. Promoção para `main`

### Fluxo padrão sem staging

1. Desenvolvimento entra em `develop`
2. Validação
3. Promoção para `main`

### Hotfix

1. Criar `hotfix/...` a partir de `main`
2. Corrigir e validar
3. Merge em `main`
4. Replicar a correção para `develop`
5. Se existir `staging`, reconciliar também

---

## Estratégia de atualização de branches

A IA deve recomendar esta política geral:

### 1. Branch temporária sempre atualizada da branch de origem

Antes de abrir ou atualizar um PR, a branch de trabalho deve ser sincronizada com sua base.

Exemplo:

* `feature/*` deve ser atualizada com `develop`
* `hotfix/*` deve ser atualizada com `main`
* `release/*` deve ser atualizada com `develop` ou com a branch-base definida

### 2. Preferência por histórico limpo

A IA deve preferir:

* `rebase` para atualizar branch de trabalho local
* `merge` controlado para promoção entre branches de ambiente

### 3. Política padrão

* Branch de trabalho: preferir `rebase` ao sincronizar com a base
* Branches de ambiente: preferir `merge` ou PR auditável

### 4. Nunca sugerir force push sem necessidade clara

A IA só deve sugerir `push --force-with-lease` quando:

* houver rebase local já realizado
* a branch for de trabalho controlado
* não houver risco de sobrescrever trabalho de terceiros

---

## Política de pull request

A IA deve assumir que toda mudança relevante passa por PR.

### Requisitos mínimos de PR

* título claro
* descrição curta do objetivo
* branch de origem correta
* branch de destino correta
* pipeline obrigatória passando
* revisão quando aplicável

### Título recomendado de PR

Formato preferido:

`[<tipo>] <descrição curta>`

Exemplos:

* `[feature] add auth login flow`
* `[bugfix] fix token refresh handling`
* `[hotfix] fix payment webhook timeout`

---

## Política de proteção de branches

A IA deve recomendar proteção pelo menos para:

* `main`
* `develop`
* `staging` quando existir

### Regras sugeridas

* bloquear push direto
* exigir pull request
* exigir pipeline passando
* exigir branch atualizada antes do merge quando possível
* exigir pelo menos um review em produção, se o time comportar isso

---

## Política de versionamento e release

Quando houver versionamento semântico, a IA deve assumir:

* `release/x.y.z`
* exemplo: `release/1.4.0`

### Uso esperado

* preparar validação final
* pequenos ajustes de release
* gerar tag após aprovação

### Após release

A IA deve recomendar:

* merge final em `main`
* tag de versão
* reconciliação da branch com `develop` se necessário

---

## Política de hotfix

Hotfix é exceção operacional e deve ser tratado com prioridade.

### Regras

* usar apenas para incidente real em produção
* origem em `main`
* nome com prefixo `hotfix/`
* merge em `main`
* replicação imediata para `develop`

Exemplo:

* `hotfix/login-timeout`
* `hotfix/webhook-signature`

---

## Política para CI/CD

A IA deve considerar que pipelines podem ser disparadas por:

* push em branches específicas
* pull request
* promoção entre ambientes
* tags de release

### Convenção recomendada

* `develop`: deploy em ambiente dev/hml de desenvolvimento
* `staging`: deploy em homologação
* `main`: deploy em produção

Se o projeto usar naming por ambiente, a IA deve respeitar o mapeamento definido no repositório.

---

## O que a IA deve evitar

A IA não deve:

* criar branch diretamente de outra feature, salvo instrução explícita
* mandar hotfix nascer de `develop`
* sugerir merge direto em `main` sem controle
* sugerir nomes vagos de branch
* ignorar reconciliação após hotfix
* propor múltiplos padrões de nome ao mesmo tempo
* misturar estratégia de trunk-based com gitflow sem aviso claro

---

## Padrão default que a IA deve assumir

Se o usuário não definir outra regra, usar este padrão:

* branch principal: `main`
* branch de integração: `develop`
* branch de homologação: `staging` se existir
* features saem de `develop`
* bugfixes comuns saem de `develop`
* hotfixes saem de `main`
* promoção entre ambientes por PR
* branch temporária usa nome curto, minúsculo e com hífen
* atualização de branch de trabalho via rebase
* promoção entre ambientes via merge auditável

---

## Instrução operacional para IAs

Ao gerar qualquer material de DevOps relacionado a Git e branches, a IA deve:

1. identificar o tipo de mudança
2. escolher a branch de origem correta
3. gerar o nome da branch no padrão definido
4. indicar a branch de destino correta
5. respeitar a política de promoção entre ambientes
6. considerar proteção de branch e PR como padrão
7. tratar hotfix como fluxo separado
8. não assumir push direto em produção

---

## Template rápido para uso da IA

Use este resumo como contexto curto:

```text
Padrão de branches do projeto:
- main = produção
- develop = integração
- staging = homologação, quando existir
- feature/* e bugfix/* nascem de develop e voltam para develop
- hotfix/* nasce de main, volta para main e depois deve ser replicada para develop
- release/* nasce de develop
- nomes em minúsculo, com hífen, claros e curtos
- branch de trabalho atualiza com rebase
- promoção entre ambientes acontece por PR/merge auditável
- evitar push direto em branches protegidas
```

---

## Finalidade deste arquivo

Este arquivo deve ser usado como skill base para IAs que precisem:

* gerar nomes de branch
* propor fluxos de Git
* criar pipelines CI/CD baseadas em branches
* revisar PRs e políticas de promoção
* documentar convenções de engenharia
* padronizar automações DevOps
