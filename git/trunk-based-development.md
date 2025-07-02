# Guia Prático de Desenvolvimento Baseado em Tronco (Trunk-Based Development)

Este documento serve como um guia completo para a estratégia de desenvolvimento baseado em tronco (Trunk-Based Development). O objetivo é maximizar a velocidade de desenvolvimento, reduzir conflitos de merge e facilitar a integração contínua e deploy contínuo.

## O que é Desenvolvimento Baseado em Tronco?

Desenvolvimento Baseado em Tronco (TBD - Trunk-Based Development) é uma estratégia de controle de versão onde todos os desenvolvedores trabalham em uma única branch principal (trunk/main/master) ou criam branches de vida muito curta que são integradas frequentemente ao tronco principal.

É o oposto do Git Flow: ao invés de múltiplas branches de longa duração, o TBD promove integração frequente e branches efêmeras.

## Filosofia Central

**"Integre cedo, integre frequentemente"**

O TBD baseia-se na premissa de que quanto mais tempo o código fica isolado em branches separadas, maior o risco de conflitos complexos e maior a dificuldade de integração. Por isso, promove:

- **Commits frequentes** na branch principal
- **Branches de vida curta** (máximo 1-2 dias)
- **Integração contínua** como práctica obrigatória
- **Feature flags** para controlar funcionalidades
- **Testes automatizados** robustos

## Modelos de Trunk-Based Development

### 1. TBD Puro (Committing Straight to Trunk)

**Quando usar**: Equipes pequenas (2-5 desenvolvedores) com alta confiança e disciplina.

```
main/trunk    ----*----*----*----*----*---->
                   |    |    |    |    |
                   A    B    C    D    E
```

**Características**:
- Commits diretos na branch principal
- Sem branches de feature
- Requer disciplina extrema
- Testes automatizados obrigatórios
- Code review antes do commit (pair programming ou pre-commit hooks)

### 2. TBD com Short-Lived Branches

**Quando usar**: Equipes médias a grandes (5+ desenvolvedores), ambientes corporativos.

```
main/trunk    ----*--------*--------*---->
                   |        |        |
feature/a     -----+*--*----+        |
                              |        |
feature/b                    +*--*----+
```

**Características**:
- Branches de feature de vida curta (max 1-2 dias)
- Pull requests obrigatórios
- Code review estruturado
- Integração frequente

## Estrutura de Branches

### Branch Principal (Trunk)

#### main/master/trunk

- **Propósito**: Branch única que representa o estado atual do desenvolvimento
- **Características**:
  - Sempre deployável (ou próximo disso)
  - Recebe commits diretos ou merges frequentes
  - Protegida por testes automatizados
  - Base para todos os deploys
- **Regras**:
  - Deve sempre compilar
  - Todos os testes devem passar
  - Código deve ser revisado (quando usando branches)

### Branches de Suporte (Quando Necessárias)

#### feature/* (vida ultra-curta)

- **Duração máxima**: 1-2 dias de trabalho
- **Tamanho**: Poucas horas de desenvolvimento
- **Propósito**: Isolamento mínimo para code review
- **Regra de ouro**: Se não foi integrada em 2 dias, algo está errado

#### release/* (opcional)

- **Quando usar**: Apenas se deploy não for automatizado
- **Duração**: Algumas horas para preparação de release
- **Propósito**: Estabilização final antes do deploy

#### hotfix/* (emergencial)

- **Quando usar**: Correções críticas que não podem esperar
- **Duração**: Máximo algumas horas
- **Propósito**: Correção rápida com processo simplificado

## Fluxos de Trabalho

### 1. Fluxo TBD Puro (Commit Direto)

```bash
# 1. Sempre comece com o trunk atualizado
git checkout main
git pull origin main

# 2. Faça mudanças pequenas e frequentes
# ... trabalho de desenvolvimento ...

# 3. Execute testes localmente
npm test  # ou seu comando de teste

# 4. Commit e push direto para main
git add .
git commit -m "feat: adiciona validação de email"
git push origin main

# 5. Pipeline CI/CD roda automaticamente
```

### 2. Fluxo TBD com Short-Lived Branch

```bash
# 1. Comece sempre com main atualizado
git checkout main
git pull origin main

# 2. Crie branch de vida curta
git checkout -b feature/validacao-email

# 3. Trabalhe rapidamente (max 1-2 dias)
# ... desenvolvimento focado ...
git add .
git commit -m "feat: adiciona validação de email"

# 4. Mantenha branch atualizada com main
git checkout main
git pull origin main
git checkout feature/validacao-email
git rebase main  # ou merge, conforme preferência da equipe

# 5. Push e crie PR/MR imediatamente
git push origin feature/validacao-email
# Criar Pull Request via interface web

# 6. Após aprovação e merge, delete a branch
git checkout main
git pull origin main
git branch -d feature/validacao-email
git push origin --delete feature/validacao-email
```

### 3. Fluxo de Hotfix Rápido

```bash
# 1. Parta do main atual
git checkout main
git pull origin main

# 2. Crie hotfix focado
git checkout -b hotfix/correcao-urgente

# 3. Faça correção mínima
# ... correção específica ...
git add .
git commit -m "fix: corrige bug crítico no login"

# 4. Fast-track para produção
git push origin hotfix/correcao-urgente
# PR com aprovação express
# Deploy imediato após merge
```

## Feature Flags (Essencial para TBD)

### O que são Feature Flags?

Feature flags (ou feature toggles) são condicionais no código que permitem ativar/desativar funcionalidades sem alterar o código.

```javascript
// Exemplo em JavaScript
function renderUserProfile(user) {
    if (featureFlags.isEnabled('NEW_PROFILE_DESIGN')) {
        return renderNewProfile(user);
    }
    return renderOldProfile(user);
}
```

### Tipos de Feature Flags

#### 1. Release Flags
```javascript
// Controla se uma funcionalidade está pronta para produção
if (flags.enabled('USER_NOTIFICATIONS')) {
    showNotifications();
}
```

#### 2. Experiment Flags
```javascript
// Para A/B testing
const buttonStyle = flags.variant('CHECKOUT_BUTTON') === 'green' 
    ? 'btn-success' 
    : 'btn-primary';
```

#### 3. Ops Flags
```javascript
// Para controle operacional
if (flags.enabled('DATABASE_MIGRATION_MODE')) {
    useNewDatabaseSchema();
} else {
    useOldDatabaseSchema();
}
```

#### 4. Permission Flags
```javascript
// Controle de acesso
if (flags.enabledForUser('ADMIN_PANEL', user.id)) {
    renderAdminPanel();
}
```

### Implementação de Feature Flags

```bash
# Instalar biblioteca de feature flags
npm install @unleash/nextjs  # exemplo com Unleash

# Ou implementação simples
npm install simple-feature-flags
```

```javascript
// Configuração básica
const flags = {
    NEW_CHECKOUT: process.env.ENABLE_NEW_CHECKOUT === 'true',
    BETA_FEATURES: process.env.NODE_ENV === 'development',
    USER_ANALYTICS: true
};

// Uso no código
function processPayment(paymentData) {
    if (flags.NEW_CHECKOUT) {
        return newPaymentProcessor.process(paymentData);
    }
    return legacyPaymentProcessor.process(paymentData);
}
```

## Estratégias de Release

### 1. Continuous Deployment
```yaml
# .github/workflows/cd.yml
name: Continuous Deployment
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Tests
        run: npm test
      - name: Deploy to Production
        if: success()
        run: ./deploy.sh
```

### 2. Scheduled Releases
```bash
# Deploy diário automático às 10h
# crontab -e
0 10 * * * /path/to/deploy-script.sh
```

### 3. Feature Flag Releases
```javascript
// Release gradual controlada por flags
const ROLLOUT_PERCENTAGE = {
    'NEW_FEATURE': 10,  // 10% dos usuários
    'BETA_UI': 50,      // 50% dos usuários
};

function shouldShowFeature(featureName, userId) {
    const percentage = ROLLOUT_PERCENTAGE[featureName] || 0;
    const hash = hashUserId(userId);
    return (hash % 100) < percentage;
}
```

## Configuração de CI/CD para TBD

### Pipeline Básico

```yaml
# .github/workflows/trunk-ci.yml
name: Trunk-Based CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Run tests
        run: npm test -- --coverage
      
      - name: Run e2e tests
        run: npm run test:e2e
      
      - name: Build application
        run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to staging
        run: ./deploy-staging.sh
      
      - name: Run smoke tests
        run: ./smoke-tests.sh
      
      - name: Deploy to production
        if: success()
        run: ./deploy-production.sh
```

### Pipeline Avançado com Feature Flags

```yaml
# Pipeline que integra com sistema de feature flags
deploy:
  steps:
    - name: Deploy with flags disabled
      run: |
        export FEATURE_FLAGS_DEFAULT=false
        ./deploy.sh
    
    - name: Run deployment tests
      run: ./test-deployment.sh
    
    - name: Gradually enable features
      run: |
        ./enable-feature.sh "NEW_FEATURE" 5   # 5% rollout
        sleep 300  # Wait 5 minutes
        ./check-metrics.sh
        ./enable-feature.sh "NEW_FEATURE" 25  # 25% rollout
```

## Técnicas de Desenvolvimento

### 1. Branch by Abstraction

```javascript
// Exemplo: Migração de API antiga para nova
class UserService {
    constructor() {
        this.useNewApi = featureFlags.enabled('NEW_USER_API');
    }
    
    async getUser(id) {
        if (this.useNewApi) {
            return await this.newApiClient.fetchUser(id);
        }
        return await this.legacyApiClient.getUser(id);
    }
}
```

### 2. Parallel Change (Expand and Contract)

```javascript
// Fase 1: Expand - Adicionar nova funcionalidade
function processOrder(orderData) {
    // Código antigo
    const oldResult = legacyProcessor.process(orderData);
    
    // Código novo (apenas logging por enquanto)
    if (featureFlags.enabled('NEW_ORDER_PROCESSOR_SHADOW')) {
        try {
            const newResult = newProcessor.process(orderData);
            logger.info('Shadow processing result', { oldResult, newResult });
        } catch (error) {
            logger.error('Shadow processing failed', error);
        }
    }
    
    return oldResult;
}

// Fase 2: Contract - Remover código antigo (depois de validação)
function processOrder(orderData) {
    return newProcessor.process(orderData);
}
```

### 3. Dark Launching

```javascript
// Executar novo código em produção sem afetar usuários
async function handleUserRegistration(userData) {
    // Fluxo principal
    const result = await legacyRegistration.process(userData);
    
    // Dark launch do novo sistema
    if (featureFlags.enabled('NEW_REGISTRATION_DARK_LAUNCH')) {
        // Executa em paralelo mas não usa o resultado
        newRegistration.process(userData)
            .then(darkResult => {
                metrics.record('dark_launch.new_registration.success');
                logger.info('Dark launch successful', { darkResult });
            })
            .catch(error => {
                metrics.record('dark_launch.new_registration.error');
                logger.error('Dark launch failed', error);
            });
    }
    
    return result;
}
```

## Monitoramento e Métricas

### 1. Métricas de Desenvolvimento

```javascript
// Tracking de deployment frequency
const deploymentMetrics = {
    deploymentsPerDay: 12,
    leadTime: '2 hours',        // Tempo de commit até produção
    mttr: '15 minutes',         // Mean Time To Recovery
    changeFailureRate: '5%'     // Taxa de falha de mudanças
};

// Métricas de branch
const branchMetrics = {
    averageBranchLifetime: '18 hours',
    branchesOpenCurrently: 3,
    averageCommitsPerBranch: 2.5
};
```

### 2. Alertas Automatizados

```javascript
// Sistema de alertas para TBD
const alerts = {
    branchTooOld: {
        condition: 'branch_age > 2_days',
        action: 'notify_developer_and_lead'
    },
    buildFailure: {
        condition: 'main_build_failed',
        action: 'block_all_merges_and_alert_team'
    },
    testCoverageDown: {
        condition: 'coverage < 80%',
        action: 'require_additional_tests'
    }
};
```

## Comparação: TBD vs Git Flow

| Aspecto | Trunk-Based Development | Git Flow |
|---------|------------------------|----------|
| **Branches principais** | 1 (main/trunk) | 2 (main + develop) |
| **Duração de branches** | Horas a 2 dias | Dias a semanas |
| **Frequência de integração** | Múltiplas vezes por dia | Semanal/Sprint |
| **Complexidade** | Baixa | Alta |
| **Conflitos de merge** | Raros e simples | Frequentes e complexos |
| **Velocidade de desenvolvimento** | Alta | Média |
| **Adequado para** | CI/CD, deploys frequentes | Releases programados |
| **Tamanho da equipe** | Qualquer (com disciplina) | Médias a grandes |
| **Curva de aprendizado** | Baixa | Alta |

## Pré-requisitos para TBD

### 1. Técnicos
- **Testes automatizados robustos** (unitários, integração, e2e)
- **Pipeline CI/CD confiável** (build, test, deploy automatizados)
- **Feature flags** (sistema de toggles)
- **Monitoramento em produção** (logs, métricas, alertas)
- **Deploy automatizado** (rollback rápido)

### 2. Culturais
- **Disciplina da equipe** (commits pequenos, testes sempre)
- **Code review eficiente** (rápido mas rigoroso)
- **Comunicação forte** (integração frequente requer coordenação)
- **Mentalidade de qualidade** (responsabilidade coletiva pelo trunk)

### 3. Organizacionais
- **Apoio da liderança** (investimento em tooling e processo)
- **Tolerância a falhas** (erros podem chegar à produção)
- **Foco em métricas** (DORA metrics, lead time, etc.)

## Comandos Git Úteis para TBD

### 1. Mantendo Sincronização

```bash
# Sync frequente com main
git checkout main
git pull origin main

# Rebase branch de feature
git checkout feature/minha-feature
git rebase main

# Ou merge se preferir
git merge main
```

### 2. Commits Atômicos

```bash
# Stage mudanças específicas
git add -p  # Interactive staging

# Commit focado
git commit -m "feat: adiciona validação de CPF no formulário"

# Amend se necessário (apenas se não foi pushed)
git add .
git commit --amend --no-edit
```

### 3. Limpeza de Branches

```bash
# Script para limpeza automática
#!/bin/bash
# cleanup-branches.sh

echo "Limpando branches mergeadas..."

# Delete branches locais já mergeadas
git branch --merged main | grep -v main | xargs -n 1 git branch -d

# Delete referências remotas órfãs
git remote prune origin

# Lista branches remotas já mergeadas
git branch -r --merged main | grep -v main | sed 's/origin\///'

echo "Limpeza concluída!"
```

## Migração do Git Flow para TBD

### Fase 1: Preparação (1-2 sprints)
```bash
# 1. Configurar CI/CD robusto
# 2. Implementar feature flags
# 3. Melhorar cobertura de testes
# 4. Treinar equipe
```

### Fase 2: Transição Gradual (2-4 sprints)
```bash
# 1. Reduzir duração de branches feature
git checkout -b feature/quick-fix develop  # ainda do develop
# ... trabalho rápido (max 1 dia) ...
git checkout develop
git merge --no-ff feature/quick-fix

# 2. Aumentar frequência de merges para develop
# 3. Simplificar processo de release
```

### Fase 3: TBD Completo (após 4 sprints)
```bash
# 1. Eliminar branch develop
git branch -d develop
git push origin --delete develop

# 2. Trabalhar direto com main
git checkout main
git pull origin main
git checkout -b feature/nova-funcionalidade
# ... trabalho rápido ...
# PR para main
```

## Boas Práticas para TBD

### 1. Commits
- **Pequenos e frequentes**: cada commit deve ser uma unidade lógica completa
- **Mensagens claras**: use conventional commits (feat:, fix:, docs:, etc.)
- **Sempre testados**: nunca commite código que quebra testes
- **Buildável**: cada commit deve compilar com sucesso

### 2. Branches
- **Vida curta**: máximo 1-2 dias de trabalho
- **Focadas**: uma branch = uma funcionalidade/correção
- **Atualizadas**: sempre rebase/merge com main antes de PR
- **Descritivas**: nomes claros (feature/login-oauth, fix/memory-leak)

### 3. Code Review
- **Rápido**: review em até 4 horas
- **Construtivo**: foque na qualidade, não em preferências pessoais
- **Automático**: use ferramentas (linting, testes) para checks básicos
- **Padrão**: estabeleça checklist de review

### 4. Testing
- **Test-first**: TDD ou pelo menos testes simultâneos
- **Cobertura alta**: mínimo 80% de cobertura
- **Testes rápidos**: suite completa em menos de 10 minutos
- **Múltiplas camadas**: unitário, integração, e2e

### 5. Deployment
- **Automatizado**: zero intervenção manual
- **Rollback rápido**: capacidade de voltar em minutos
- **Monitorado**: alertas automáticos para problemas
- **Gradual**: use feature flags para rollout controlado

## Ferramentas Recomendadas

### Feature Flags
- **Unleash**: Open source, robusto
- **LaunchDarkly**: Enterprise, muitas funcionalidades
- **Split**: Focado em experimentação
- **ConfigCat**: Simples e acessível

### CI/CD
- **GitHub Actions**: Integrado ao GitHub
- **GitLab CI**: Integrado ao GitLab
- **Jenkins**: Flexível e customizável
- **CircleCI**: Cloud-native

### Monitoramento
- **Datadog**: APM completo
- **New Relic**: Performance monitoring
- **Sentry**: Error tracking
- **Prometheus + Grafana**: Open source

### Testing
- **Jest**: JavaScript testing
- **Cypress**: E2E testing
- **JUnit**: Java testing
- **pytest**: Python testing

## Métricas de Sucesso

### DORA Metrics (DevOps Research and Assessment)

1. **Deployment Frequency**: Quantas vezes por dia/semana você deploya
   - **Elite**: Múltiplas vezes por dia
   - **High**: Entre uma vez por dia e uma vez por semana

2. **Lead Time for Changes**: Tempo do commit até produção
   - **Elite**: Menos de 1 hora
   - **High**: Entre 1 dia e 1 semana

3. **Change Failure Rate**: Porcentagem de deploys que causam problemas
   - **Elite**: 0-15%
   - **High**: 16-30%

4. **Time to Restore Service**: Tempo para recuperar de falhas
   - **Elite**: Menos de 1 hora
   - **High**: Menos de 1 dia

### Métricas Específicas de TBD

```javascript
// Exemplo de tracking
const tbdMetrics = {
    averageBranchAge: '14 hours',
    branchesPerDeveloper: 1.2,
    commitsToMainPerDay: 45,
    timeFromPRToMerge: '2.5 hours',
    buildsPerDay: 67,
    testExecutionTime: '8 minutes'
};
```

## Problemas Comuns e Soluções

### 1. "Meu código não está pronto para produção"
**Solução**: Use feature flags
```javascript
if (featureFlags.enabled('NEW_FEATURE') && user.isBeta) {
    return newFeature();
}
return oldFeature();
```

### 2. "Testes são muito lentos"
**Solução**: Paralelização e otimização
```bash
# Testes paralelos
npm test -- --parallel

# Cache de dependências
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

### 3. "Muitos conflitos de merge"
**Solução**: Integração mais frequente
```bash
# Integre pelo menos 2x por dia
git checkout main && git pull
git checkout feature/minha-branch
git rebase main
```

### 4. "Pipeline CI/CD muito complexo"
**Solução**: Simplifique e automatize
```yaml
# Pipeline simples mas efetivo
steps:
  - test
  - build
  - deploy-staging
  - smoke-test
  - deploy-production
```

## Considerações Finais

O Desenvolvimento Baseado em Tronco é uma prática poderosa que pode acelerar significativamente o desenvolvimento de software, mas requer disciplina, tooling adequado e mudança cultural. Não é apenas uma mudança técnica, mas uma transformação na forma como a equipe trabalha.

### Quando usar TBD:
- Equipes que fazem deploy frequente
- Organizações com cultura de qualidade forte
- Projetos que se beneficiam de feedback rápido
- Ambientes com testes automatizados robustos

### Quando considerar alternativas:
- Equipes muito grandes (100+ desenvolvedores) sem estrutura
- Projetos com releases muito espaçados
- Ambientes sem tolerância a riscos
- Organizações sem investimento em tooling

### Próximos passos:
1. Avalie a maturidade da sua equipe e tooling
2. Comece com branches de vida mais curta
3. Invista em testes automatizados e CI/CD
4. Implemente feature flags
5. Monitore métricas e ajuste o processo

O TBD não é apenas sobre Git - é sobre entregar valor mais rapidamente e com mais qualidade. É uma jornada que vale a pena para equipes comprometidas com excelência técnica.
