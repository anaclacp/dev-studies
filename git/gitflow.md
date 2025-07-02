# Guia Prático do Git Flow

Este documento serve como um guia para a estratégia de gerenciamento de branches (ramificações) utilizando o Git Flow. O objetivo é padronizar o fluxo de trabalho, facilitar a colaboração em equipe e garantir a estabilidade do código em produção.

## O que é Git Flow?

Git Flow é um modelo de ramificação para o Git. Ele não é uma ferramenta nova, mas sim uma estratégia sobre como e quando criar, mesclar (fazer merge) e deletar branches. Ele foi projetado para organizar o desenvolvimento contínuo e o ciclo de lançamento de versões de um software.

A principal vantagem é manter a branch de produção (master/main) sempre limpa e estável, enquanto o desenvolvimento de novas funcionalidades e correções acontece em branches separadas e organizadas.

## O Modelo de Ramificações

O Git Flow utiliza duas branches principais que vivem para sempre e três tipos de branches de suporte, que são temporárias.

### Branches Principais (Eternas)

#### master (ou main)

- **Propósito**: Representa o código que está em produção. Tudo que está aqui deve ser estável, testado e funcional.
- **Como funciona**: A master só recebe código de branches de release ou hotfix. Cada novo commit na master é uma nova versão e deve ser identificado com uma tag (ex: v1.0.1, v2.0.0).
- **⚠️ NÃO se deve fazer commit diretamente aqui.**

#### develop

- **Propósito**: É a branch de desenvolvimento ativo. Ela concentra todas as novas funcionalidades que estão sendo preparadas para o próximo lançamento. Pode ser considerada a "versão do futuro".
- **Como funciona**: Ela serve como base para a criação de todas as branches de feature. Quando uma feature é finalizada, ela é mesclada de volta na develop.
- **É normal que esta branch esteja instável às vezes, pois é onde o trabalho "vivo" acontece.**

### Branches de Suporte (Temporárias)

#### feature/* (ex: feature/login-com-google)

- **Origem**: Criada a partir da develop.
- **Propósito**: Desenvolver uma nova funcionalidade específica. O trabalho é feito de forma isolada para não interferir com outras partes do desenvolvimento.
- **Ciclo de vida**:
  1. Cria-se a partir da develop
  2. Trabalha-se nela
  3. Ao finalizar, é mesclada de volta na develop
  4. Pode ser deletada após o merge

#### release/* (ex: release/v1.1.0)

- **Origem**: Criada a partir da develop.
- **Propósito**: Preparar uma nova versão para o lançamento em produção. É nesta branch que acontecem os últimos testes, correções de bugs menores (last-minute fixes) e a atualização da documentação e número de versão.
- **Como funciona**: Nenhuma nova feature pode entrar numa branch de release. Ela é uma "sala de preparação".
- **Ciclo de vida**:
  1. Cria-se a partir da develop quando se decide que a próxima versão está pronta
  2. Testes e pequenas correções são feitas aqui
  3. Ao finalizar, ela é mesclada em DOIS LUGARES:
     - Na master (para oficializar a nova versão de produção) e tagueada
     - De volta na develop (para garantir que as correções de última hora não se percam)
  4. Pode ser deletada após o merge duplo

#### hotfix/* (ex: hotfix/correcao-bug-pagamento)

- **Origem**: Criada a partir da master.
- **Propósito**: Corrigir um bug crítico e urgente que foi encontrado em produção e não pode esperar pelo próximo ciclo de release.
- **Como funciona**: Permite criar uma correção rápida sem interferir com o trabalho que está acontecendo na develop.
- **Ciclo de vida**:
  1. Cria-se a partir da master (pois o bug está na versão de produção)
  2. A correção é feita e testada
  3. Ao finalizar, ela é mesclada em DOIS LUGARES:
     - Na master (para corrigir o bug em produção imediatamente) e tagueada (ex: v1.0.2)
     - De volta na develop (para garantir que a correção também esteja presente no próximo lançamento)
  4. Pode ser deletada após o merge duplo

## Fluxos de Trabalho Comuns

### 1. Desenvolvendo uma Nova Funcionalidade (feature)

```bash
# 1. Vá para a develop e garanta que ela está atualizada
git checkout develop
git pull origin develop

# 2. Crie sua nova branch de feature
git checkout -b feature/nome-da-sua-feature

# 3. Trabalhe na sua feature... (faça commits)
git add .
git commit -m "feat: implementa funcionalidade X"

# 4. Ao terminar, volte para a develop e junte seu trabalho
git checkout develop
git merge --no-ff feature/nome-da-sua-feature

# 5. Envie as atualizações para o repositório remoto
git push origin develop

# 6. (Opcional) Apague a branch da feature
git branch -d feature/nome-da-sua-feature
```

### 2. Preparando uma Nova Versão (release)

```bash
# 1. Crie a branch de release a partir da develop
git checkout -b release/v1.1.0 develop

# 2. Faça os últimos ajustes (ex: bump de versão, correções) e commite
# ...trabalho de preparação...
git commit -am "chore: prepara versão 1.1.0"

# 3. (IMPORTANTE) Finalize a release fazendo merge na master e develop
# Primeiro, merge na master
git checkout master
git merge --no-ff release/v1.1.0
git tag -a v1.1.0 -m "Lançamento da versão 1.1.0"

# Depois, merge de volta na develop
git checkout develop
git merge --no-ff release/v1.1.0

# 4. Envie as atualizações e a nova tag para o remoto
git push origin master
git push origin develop
git push origin --tags

# 5. (Opcional) Apague a branch de release
git branch -d release/v1.1.0
```

### 3. Corrigindo um Bug Urgente em Produção (hotfix)

```bash
# 1. Crie a branch de hotfix a partir da master
git checkout -b hotfix/bug-critico master

# 2. Faça a correção necessária e commite
# ...código da correção...
git commit -am "fix: corrige bug crítico no sistema de pagamento"

# 3. (IMPORTANTE) Finalize o hotfix fazendo merge na master e develop
# Primeiro, merge na master e crie a tag
git checkout master
git merge --no-ff hotfix/bug-critico
git tag -a v1.0.2 -m "Corrige bug crítico no pagamento"

# Depois, merge de volta na develop
git checkout develop
git merge --no-ff hotfix/bug-critico

# 4. Envie as atualizações e a nova tag para o remoto
git push origin master
git push origin develop
git push origin --tags

# 5. (Opcional) Apague a branch de hotfix
git branch -d hotfix/bug-critico
```

## Resumo Visual do Fluxo

```
master    ----*----*----*----*-----> (tags: v1.0.0, v1.1.0, v1.0.1)
               |    |    |    |
               |    |    |    +-- hotfix merge
               |    |    +-- release merge
               |    +-- release merge
               +-- initial release

develop   ----*----*----*----*----*-----> 
               |    |    |    |    |
               |    |    |    |    +-- hotfix merge back
               |    |    |    +-- release merge back
               |    |    +-- feature merge
               |    +-- feature merge
               +-- feature merge

feature        +----*----+
                    |
                    +-- work done

release             +----*----+
                         |
                         +-- final prep

hotfix                        +----*----+
                                   |
                                   +-- bug fix
```

## Comandos Úteis Adicionais

### Verificar o status das branches
```bash
# Ver todas as branches (locais e remotas)
git branch -a

# Ver apenas branches remotas
git branch -r

# Ver branches com último commit
git branch -v
```

### Trabalhando com repositórios remotos
```bash
# Sincronizar com o repositório remoto
git fetch origin

# Atualizar branch atual com mudanças do remoto
git pull origin nome-da-branch

# Enviar branch local para o remoto
git push origin nome-da-branch

# Deletar branch remota
git push origin --delete nome-da-branch
```

### Gestão de tags
```bash
# Listar todas as tags
git tag

# Criar tag anotada
git tag -a v1.0.0 -m "Versão 1.0.0"

# Enviar tags para o remoto
git push origin --tags

# Deletar tag local
git tag -d v1.0.0

# Deletar tag remota
git push origin --delete v1.0.0
```

## Boas Práticas

1. **Sempre use `--no-ff`** ao fazer merge de features, releases e hotfixes para manter o histórico claro
2. **Nunca trabalhe diretamente na master/main** - sempre crie branches específicas
3. **Mantenha commits pequenos e focados** - cada commit deve representar uma mudança lógica
4. **Use mensagens de commit descritivas** - siga convenções como Conventional Commits
5. **Teste antes de fazer merge** - especialmente para releases e hotfixes
6. **Delete branches após o merge** - mantenha o repositório limpo
7. **Use tags para marcar versões** - facilita a identificação de releases
8. **Mantenha a develop atualizada** - faça pull regularmente antes de criar novas features

## Vantagens do Git Flow

- **Organização clara**: Cada tipo de trabalho tem sua branch específica
- **Estabilidade da produção**: A master sempre representa código estável
- **Desenvolvimento paralelo**: Múltiplas features podem ser desenvolvidas simultaneamente
- **Releases controladas**: Processo estruturado para preparar e lançar versões
- **Correções de emergência**: Hotfixes permitem correções rápidas sem interferir no desenvolvimento
- **Histórico limpo**: O uso de `--no-ff` mantém a história das branches visível

## Considerações Finais

O Git Flow é uma metodologia robusta e bem estabelecida, especialmente adequada para projetos com releases programados e equipes maiores. Embora possa parecer complexo inicialmente, sua estrutura organizada facilita muito a colaboração e a manutenção do código a longo prazo.

Para projetos menores ou com deploy contínuo, considere também outras estratégias como GitHub Flow ou GitLab Flow, que são mais simples mas podem ser mais adequadas dependendo do contexto do seu projeto.
