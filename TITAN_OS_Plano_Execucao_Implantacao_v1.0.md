# TITAN OS --- Plano de Execução e Implantação

## Versão 1.0 \| 9 de outubro de 2026

**Tipo:** plano técnico-operacional de referência\
**Status:** planejamento e especificação; não comprova implementação\
**Idioma:** português brasileiro

------------------------------------------------------------------------

## 0. Como interpretar o plano

Este documento transforma o escopo recuperado do TITAN OS, a arquitetura
técnica detalhada e a especificação do TITAN AI em uma sequência de
execução, validação e implantação.

O histórico recuperado não é uma exportação integral de todas as
conversas antigas. Também não foi fornecido um repositório para auditar
código, infraestrutura, migrations, testes ou ambientes. Portanto, este
plano não declara funcionalidades como implementadas.

### Classificação obrigatória

-   **\[HISTÓRICO\]** --- direção ou requisito presente no material
    recuperado.
-   **\[RECOMENDAÇÃO NOVA\]** --- abordagem proposta neste plano;
    precisa ser confirmada conforme o repositório e as necessidades
    reais.
-   **\[PENDENTE\]** --- decisão técnica, de produto, infraestrutura ou
    negócio ainda não confirmada.
-   **\[NÃO IMPLEMENTADO/VERIFICADO\]** --- não há evidência disponível
    de implementação funcional.
-   **\[GATE\]** --- condição que precisa ser satisfeita antes de
    avançar.
-   **\[EXEMPLO\]** --- configuração ou código ilustrativo, não
    executado.

### Estado operacional inicial

  Item                                         Estado verificável com o material disponível
  -------------------------------------------- ----------------------------------------------
  Master Blueprint                             Especificado em documento anterior
  Arquitetura técnica geral                    Especificada em documento anterior
  Especificação do TITAN AI                    Especificada em documento anterior
  Escopo de módulos                            Definido conceitualmente
  Stack efetivamente usada no repositório      \[PENDENTE\]
  Código atual, branches e migrations          \[NÃO IMPLEMENTADO/VERIFICADO\]
  Pipeline CI/CD ativo                         \[NÃO IMPLEMENTADO/VERIFICADO\]
  Ambientes dev/staging/production             \[NÃO IMPLEMENTADO/VERIFICADO\]
  Domínio, DNS, certificados, infraestrutura   \[PENDENTE\]
  Backups e restauração testada                \[NÃO IMPLEMENTADO/VERIFICADO\]
  Testes de isolamento multi-tenant            \[NÃO IMPLEMENTADO/VERIFICADO\]
  Lançamento e usuários-piloto                 \[PENDENTE\]

**Regra de verdade operacional:** classificar cada entrega como
`planejada`, `especificada`, `em desenvolvimento`, `implementada`,
`testada` e `validada em ambiente-alvo`. Avançar o estado somente com
evidências registradas.

## 1. Objetivo de execução

Entregar o TITAN OS em incrementos pequenos, com uma primeira
experiência utilizável por um segmento de clientes e uma capacidade de
operação sustentável. O projeto não deve tentar implementar todos os
módulos, agentes e integrações em paralelo.

### Resultados desejados

1.  Repositório auditado e documentação técnica versionada.
2.  Fundação TITAN CORE com identidade, organizações, autorização,
    auditoria e isolamento multi-tenant.
3.  Primeiro fluxo vertical de negócio --- recomendação inicial: TITAN
    CRM MVP.
4.  TITAN AI integrado por gateway, com execução controlada, limites,
    auditoria e avaliações.
5.  Pipeline de testes e deploy reproduzível.
6.  Ambientes separados, gestão de segredos, monitoramento, backups e
    restauração ensaiada.
7.  Piloto controlado, métricas de uso e processo de resposta a
    incidentes.
8.  Decisões de arquitetura registradas e evolução guiada por
    evidências.

## 2. Escopo de produto e prioridades

### 2.1 Escopo conceitual recuperado

O TITAN OS foi descrito como SaaS empresarial modular,
multiempresa/multiusuário, com IA, automações e visão operacional
integrada. Os módulos planejados são:

-   **TITAN CORE:** identidade, organizações, usuários, autorização,
    planos, auditoria e configurações.
-   **TITAN AI:** modelos, agentes, orquestração, RAG, ferramentas,
    memória e avaliações.
-   **TITAN CRM:** leads, contatos, empresas, oportunidades e
    atividades.
-   **TITAN FINANCE:** contas a pagar/receber, fluxo de caixa e
    relatórios.
-   **TITAN PROJECTS:** projetos, tarefas, responsáveis e cronogramas.
-   **TITAN DOCS:** documentos, OCR, pesquisa, versionamento e
    permissões.
-   **TITAN ANALYTICS:** indicadores, dashboards e análises.
-   **TITAN AUTOMATION:** workflows, eventos, integrações e filas.
-   **TITAN AGRO:** propriedades, safras, produção, estoque e custos
    rurais.
-   **TITAN COMMAND CENTER:** visão executiva e indicadores
    consolidados.

A lista representa escopo planejado, não dez módulos comprovadamente
implementados.

### 2.2 Priorização recomendada

**\[RECOMENDAÇÃO NOVA\]** Lançar um fluxo principal, não uma plataforma
inteira de uma vez.

**MVP sugerido:** TITAN CORE + TITAN CRM essencial + um caso de uso de
IA com risco baixo e benefício mensurável.

O CRM é uma hipótese inicial coerente com o histórico, mas a escolha
final deve depender de entrevistas com usuários, capacidade técnica
existente e disposição a pagar. Se o repositório já tiver outro módulo
parcialmente funcional, reavaliar antes de recomeçar.

Incluído no MVP: - criação de organização e associação de usuários; -
login e recuperação de acesso com provedor seguro; - papéis/permissões e
isolamento entre organizações; - cadastro e gestão básica de
leads/contatos/oportunidades; - trilha de auditoria das ações
relevantes; - AI Gateway com um modelo aprovado e um agente de
leitura/assistência; - logs, métricas, quotas e controle de custos; -
implantação em staging e piloto.

Fora do MVP, salvo evidência que mude a prioridade: - ERP completo e
contabilidade fiscal; - pagamentos ou transferências iniciados
autonomamente por IA; - todos os módulos de negócio simultaneamente; -
marketplace de agentes; - microsserviços separados sem necessidade
medida; - automações externas irreversíveis sem aprovação; -
customização profunda de infraestrutura antes de validar o produto.

## 3. Decisões arquiteturais: preservar e reconciliar

O material recuperado contém duas direções que precisam ser tratadas com
clareza:

-   **\[HISTÓRICO\]** Foram discutidos arquitetura modular, API-first,
    cloud-native, eventos e microserviços como visão de escalabilidade.
-   **\[HISTÓRICO\]** O plano técnico mais recente recomenda começar com
    monólito modular e extrair serviços somente quando houver
    necessidade comprovada.

**\[RECOMENDAÇÃO NOVA\]** Adotar monólito modular para o primeiro MVP,
mantendo contratos de módulo e eventos internos bem definidos. Isso não
elimina a visão event-driven: eventos de domínio, outbox e workers
assíncronos podem existir dentro da primeira implantação. A separação
física em microsserviços fica para uma decisão futura baseada em carga,
disponibilidade, autonomia de equipes ou necessidade de escala
independente.

### 3.1 Stack: candidatas, não decisões confirmadas

  -----------------------------------------------------------------------
  Área              Candidata         Uso previsto      Estado
                    discutida                           
  ----------------- ----------------- ----------------- -----------------
  Banco             PostgreSQL        dados centrais,   \[PENDENTE\]
  transacional                        transações,       confirmar stack
                                      isolamento        real
                                      multi-tenant      

  Cache/locks       Redis             cache, rate       \[PENDENTE\]
                                      limit,            
                                      coordenação       
                                      conforme          
                                      necessidade       

  Jobs/eventos      RabbitMQ ou fila  tarefas           \[PENDENTE\]
                    gerenciada        assíncronas,      
                                      retries e DLQ     

  Arquivos          S3/MinIO          documentos e      \[PENDENTE\]
                                      artefatos         
                                      privados          

  Busca             PostgreSQL        pesquisa          \[PENDENTE\]
                    full-text,        textual/híbrida   
                    Elasticsearch ou                    
                    equivalente                         

  Métricas          Prometheus        métricas          \[PENDENTE\]
                                      operacionais      

  Dashboards        Grafana           dashboards e      \[PENDENTE\]
                                      alertas           

  Telemetria        OpenTelemetry     traces, métricas  \[RECOMENDAÇÃO
                                      e logs            NOVA\]
                                      instrumentados    

  CI/CD             GitHub Actions ou testes e deploy   \[PENDENTE\]
                    ferramenta        controlado        
                    equivalente                         

  Containers        Docker; Compose   builds            \[PENDENTE\]
                    em ambientes      reprodutíveis     
                    adequados                           
  -----------------------------------------------------------------------

Não instalar toda a lista por padrão. Escolher a menor combinação que
satisfaça requisitos medidos e que a equipe consiga operar.

### 3.2 ADRs obrigatórias

Criar `/docs/adr/` e registrar cada decisão relevante com contexto,
opções, escolha, consequências, status e data.

ADRs iniciais: - ADR-001: monólito modular e fronteiras dos módulos. -
ADR-002: framework e runtime confirmados após auditoria. - ADR-003:
identidade e gestão de sessão. - ADR-004: estratégia de multi-tenancy e
RLS. - ADR-005: migrations e compatibilidade de schema. - ADR-006:
filas, outbox, idempotência e retries. - ADR-007: storage de documentos
e política de retenção. - ADR-008: AI Gateway, provedor inicial e
classificação de dados. - ADR-009: CI/CD e estratégia de deploy. -
ADR-010: backups, RPO/RTO e recuperação. - ADR-011: billing/quotas e
medição de uso. - ADR-012: requisitos de LGPD, retenção e auditoria.

Nenhuma ADR deve registrar uma recomendação como "decisão histórica" sem
confirmação.

## 4. Fase 0 --- Descoberta, auditoria e planejamento

**Objetivo:** descobrir o estado real antes de alterar o código.

### Tarefas

1.  Obter acesso ao repositório, instruções de execução e ambientes
    existentes.
2.  Identificar linguagens, frameworks, gerenciador de pacotes, banco,
    autenticação e bibliotecas críticas.
3.  Inspecionar `README`, `package.json`/equivalentes, Dockerfiles,
    workflows, migrations, testes, arquivos de configuração e histórico
    de commits.
4.  Executar a aplicação localmente sem expor credenciais reais.
5.  Executar testes, lint, typecheck e build existentes; registrar
    resultados sem presumir sucesso.
6.  Identificar rotas e tabelas reais versus documentação.
7.  Verificar dependências vulneráveis, segredos acidentalmente
    versionados e permissões de CI.
8.  Fazer inventário de módulos por estado: ausente, esqueleto, parcial,
    implementado, testado, validado.
9.  Identificar dívida técnica, migrações perigosas, acoplamento e
    riscos de dados.
10. Entrevistar usuários potenciais e selecionar um problema
    prioritário.
11. Criar backlog com tarefas pequenas, responsáveis, dependências e
    critérios de aceite.

### Entregáveis

-   relatório de auditoria técnica;
-   matriz `existente × especificado × faltante`;
-   mapa de dependências;
-   riscos P0/P1/P2;
-   ADRs iniciais;
-   backlog priorizado;
-   baseline de build/teste;
-   decisão de MVP baseada em evidências.

### \[GATE 0\]

Não iniciar grandes refatorações antes de: - conseguir reproduzir o
ambiente; - saber qual código é fonte de verdade; - conhecer o mecanismo
atual de autenticação e persistência; - identificar riscos de dados e
segredos; - aprovar o MVP e os critérios de aceite.

## 5. Fase 1 --- Fundação do TITAN CORE

**Objetivo:** garantir que todos os módulos posteriores herdem
identidade, organização, autorização, auditoria e configuração
consistentes.

### 5.1 Identidade e organizações

Implementar ou validar: - `users` ou integração com provedor de
identidade; - `organizations`; - `memberships`; - `roles` e
`permissions` (ou modelo equivalente); - convites e expiração; - sessão,
logout, recuperação de conta e MFA conforme risco; - associação
explícita entre usuário e organização ativa; - trilha de auditoria; -
quotas e configuração da organização.

Não criar autenticação própria baseada em senhas se o projeto já usa um
provedor adequado sem primeiro avaliar riscos e custos.

### 5.2 Isolamento multi-tenant

Requisitos: - resolver a organização no servidor a partir de
sessão/membership autorizada; - validar tenant em cada operação de
leitura e escrita; - incluir organização nas entidades que pertencem ao
cliente; - aplicar políticas RLS se adotadas e testá-las com a role real
da aplicação; - não usar permissões administrativas do banco em rotas
normais; - evitar cache global sem chave de tenant; - isolar jobs,
arquivos, logs, buscas e índices vetoriais; - testar usuário de
organização A tentando ler, alterar, enumerar ou excluir dados de B.

### 5.3 Migrações

-   migrations versionadas no repositório;
-   alterações de schema revisadas em PR;
-   estratégia expand/contract para mudanças incompatíveis;
-   backup validado antes de alterações destrutivas;
-   migrações de dados idempotentes quando possível;
-   teste de migração em banco limpo e em cópia representativa;
-   plano de recuperação caso a migration falhe;
-   não depender de alteração manual não documentada no banco.

### 5.4 Auditoria

Registrar ações relevantes com ator, organização, ação, recurso,
resultado, timestamp e correlation/trace ID. Não registrar senhas,
tokens, chaves, prompts completos ou dados pessoais indiscriminadamente.
A retenção precisa ser definida por política.

### \[GATE 1\]

-   login e autorização verificados;
-   isolamento negativo entre tenants aprovado;
-   migrações reproduzíveis;
-   testes de acesso e auditoria aprovados;
-   gestão de segredos verificada;
-   documentação operacional do CORE atualizada.

## 6. Fase 2 --- Primeiro módulo funcional: TITAN CRM MVP

**Objetivo:** validar um fluxo empresarial ponta a ponta antes de
expandir o escopo.

### 6.1 Domínio mínimo

-   empresas/contas;
-   contatos;
-   leads;
-   oportunidades;
-   atividades e próximos passos;
-   origem e status;
-   responsáveis e permissões;
-   pesquisa, filtros e paginação;
-   histórico de alterações.

O modelo exato deve ser ajustado às entidades já existentes e ao público
inicial.

### 6.2 API e interface

-   contratos API versionados;
-   validação de entrada e resposta;
-   autorização no servidor por operação e recurso;
-   paginação consistente e limites de consulta;
-   mensagens de erro estáveis sem detalhes internos;
-   estados de carregamento, vazio, erro e sucesso;
-   ações destrutivas com confirmação;
-   telemetria e auditoria nas mutações;
-   acessibilidade básica e experiência responsiva.

### 6.3 Testes de aceitação

-   usuário autorizado cria, consulta, atualiza e arquiva registros;
-   usuário sem permissão não executa mutações;
-   organização A não consegue enumerar IDs nem acessar registros de B;
-   filtros e paginação não ignoram tenant;
-   repetição de uma requisição sensível não duplica efeito;
-   erros de validação são previsíveis;
-   auditoria corresponde à operação;
-   interface trata indisponibilidade e sessões expiradas.

### \[GATE 2\]

Um usuário-piloto consegue concluir o fluxo principal sem intervenção
manual da equipe. As métricas de sucesso são definidas antes do piloto:
ativação, tempo até primeiro valor, frequência de uso, conclusão da
tarefa e feedback.

## 7. Fase 3 --- TITAN AI controlado

**Objetivo:** incorporar inteligência artificial sem permitir que o
modelo contorne autorização ou execute ações sem controle.

### 7.1 Escopo inicial

-   AI Gateway para um provedor/modelo aprovado;
-   Model Registry mínimo;
-   Prompt Registry versionado;
-   um agente de leitura/assistência;
-   registro de execuções e uso;
-   limites de tempo, passos, tokens e custo;
-   saída validada;
-   logs redigidos e métricas;
-   avaliação de qualidade e segurança.

### 7.2 Ordem de construção

1.  Implementar um adaptador de provedor e testes de contrato.
2.  Criar endpoint de execução e máquina de estados.
3.  Persistir run, versão do agente/prompt, status, uso e erros.
4.  Adicionar quotas, timeout, cancelamento e idempotência.
5.  Adicionar ferramenta de leitura com autorização no servidor.
6.  Adicionar aprovação humana antes de qualquer ação sensível.
7.  Integrar RAG quando houver uma fonte documental e caso de uso
    definidos.
8.  Criar golden set e testes adversariais antes de publicar novas
    versões.

### 7.3 Critérios de aceite de IA

-   modelo nunca determina identidade, organização ou permissões;
-   tool calls são validadas por allowlist e schema;
-   saída do modelo não é executada como SQL, shell ou código;
-   nenhuma ferramenta de escrita está liberada sem política explícita;
-   limites de execução são aplicados pelo servidor;
-   falhas de provedor não geram resultados inventados;
-   custo e uso são atribuídos à organização correta;
-   conteúdo sensível é redigido dos logs;
-   prompts e agentes têm versão e rollback;
-   testes de prompt injection e isolamento passam;
-   existe mecanismo para desligar agente/provedor.

### \[GATE 3\]

O agente opera em staging com dados sintéticos ou minimizados,
avaliações aprovadas e capacidade de desligamento. Só depois é
habilitado para um grupo restrito de usuários.

## 8. Fase 4 --- Hardening e preparação para produção

**Objetivo:** preparar a plataforma para falhas, ataques, recuperação e
operação cotidiana.

### 8.1 Segurança de aplicação e infraestrutura

-   autenticação forte e gestão segura de sessão;
-   autorização por recurso e organização;
-   rate limiting por usuário, organização e endpoint;
-   validação de entradas, limites de payload e proteção contra abuso;
-   política de CORS estrita;
-   HTTPS/TLS e cookies seguros quando aplicável;
-   proteção contra CSRF conforme modelo de sessão;
-   headers de segurança;
-   secrets manager ou mecanismo equivalente;
-   imagens de container mínimas, versões fixadas e varredura;
-   dependências e SBOM conforme maturidade;
-   usuários de processo e banco com privilégio mínimo;
-   redes privadas para banco, fila e storage;
-   acesso administrativo restrito e auditado;
-   plano de rotação de credenciais e resposta a incidentes;
-   testes de segurança baseados no OWASP ASVS e nos riscos específicos
    de IA.

### 8.2 Privacidade e LGPD

Antes do piloto com dados reais: - mapear dados pessoais, finalidade e
base legal aplicável; - definir controlador/operadores e suboperadores
conforme o modelo; - avaliar provedor de IA, região, retenção e uso de
dados para treinamento; - estabelecer retenção e exclusão para
registros, documentos, embeddings e backups; - definir atendimento a
solicitações dos titulares; - rever transferências internacionais,
contratos e transparência; - manter registro de incidentes e processo de
resposta; - buscar análise jurídica/privacidade adequada ao contexto.

### 8.3 Observabilidade

Instrumentar logs estruturados, métricas e traces correlacionados.
OpenTelemetry é uma opção vendor-neutral para instrumentação e
exportação de telemetria. Referência: https://opentelemetry.io/docs/

Métricas mínimas: - disponibilidade e latência das rotas principais; -
taxa de erro por endpoint; - saturação de CPU, memória, conexões e
fila; - erros de banco e pool de conexões; - jobs pendentes, retries e
dead-letter queue; - sucesso/falha de login e sinais de abuso; -
execução, custo, latência e erro de IA; - uso por organização; - backups
realizados e idade do último backup; - resultados de restauração
ensaiada.

Definir alertas com ação operacional clara. Evitar alertas que não
tenham responsável ou runbook.

### 8.4 Backups e recuperação

-   backups automáticos do banco e storage;
-   criptografia em trânsito e em repouso quando suportada;
-   cópias fora do domínio de falha principal conforme criticidade;
-   controle de acesso e retenção;
-   monitoramento do sucesso dos backups;
-   ensaios periódicos de restauração em ambiente isolado;
-   verificar integridade dos dados e funcionamento da aplicação após
    restore;
-   documentar RPO e RTO com base nas necessidades de negócio;
-   incluir arquivos, configurações e dados necessários para recuperar o
    serviço;
-   não considerar backup válido até demonstrar restauração.

A documentação do PostgreSQL explica `pg_dump`, `pg_restore` e os
formatos de backup:
https://www.postgresql.org/docs/current/backup-dump.htm. Escolher o
método conforme tamanho, versão, requisitos de recuperação e serviço
gerenciado.

### 8.5 Desempenho e capacidade

-   criar baseline de latência e throughput;
-   testar fluxos críticos com carga representativa;
-   testar concorrência por organização;
-   medir pool de conexões e consultas lentas;
-   definir índices com base em planos de consulta;
-   limitar exportações e jobs pesados;
-   verificar comportamento sob rate limit do provedor de IA;
-   testar backpressure e recuperação de filas;
-   definir SLOs depois de medir o comportamento real.

Não inventar metas de disponibilidade ou latência antes de definir o
serviço e testar a infraestrutura. Definir metas iniciais explicitamente
como objetivos, não como garantias.

### \[GATE 4\]

-   threat model e checklist de segurança revisados;
-   testes críticos de isolamento aprovados;
-   logs, alertas e runbooks disponíveis;
-   backup concluído e restauração comprovada;
-   rollback de aplicação ensaiado;
-   vulnerabilidades críticas bloqueantes resolvidas;
-   política de retenção/privacidade aprovada;
-   suporte e responsável operacional definidos.

## 9. Pipeline de desenvolvimento e CI/CD

### 9.1 Fluxo de branches recomendado

**\[RECOMENDAÇÃO NOVA\]** - branches curtas por tarefa; - pull request
para branch principal protegida; - revisão de código obrigatória; - CI
executado em pull request; - deploy para staging a partir de versão
aprovada; - deploy de produção com aprovação e rastreabilidade; -
tags/releases imutáveis e notas de versão.

O fluxo deve ser adaptado ao tamanho da equipe; evitar complexidade de
GitFlow sem necessidade.

### 9.2 Pipeline mínimo

1.  checkout de código;
2.  instalação determinística de dependências;
3.  lint e formatação;
4.  typecheck/compilação;
5.  testes unitários;
6.  testes de integração com serviços isolados;
7.  testes de contrato e migrations;
8.  verificação de dependências e segredos;
9.  build de artefato/container;
10. testes de smoke do artefato;
11. publicação com tag/digest;
12. deploy em staging;
13. smoke tests e verificação de readiness;
14. aprovação para produção;
15. deploy progressivo ou janela controlada;
16. smoke tests pós-deploy e observação;
17. rollback se os critérios de saúde falharem.

Não usar artefatos diferentes entre staging e produção; promover o mesmo
artefato imutável, mudando somente a configuração segura do ambiente.

### 9.3 Exemplo de workflow GitHub Actions

**\[EXEMPLO\]** É um esqueleto conceitual. Precisa ser ajustado ao
stack, comandos, testes e infraestrutura reais. Não deve ser copiado
para produção sem revisão.

``` yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # Substituir pelos comandos reais do repositório:
      # - instalar runtime e dependências com lockfile
      # - lint
      # - typecheck
      # - testes unitários
      # - testes de integração
      # - auditoria de dependências
      # - build
      - name: Placeholder
        run: echo "Substituir por validações reais antes de ativar"
```

Para produção, fixar ações de terceiros conforme política de segurança,
limitar permissões, proteger ambientes e exigir aprovação onde
necessário. Documentação:
https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments

### 9.4 Política de migrations no deploy

-   revisar migrations em PR;
-   fazer backup ou confirmar mecanismo de recuperação;
-   aplicar migrations compatíveis antes do novo código quando
    necessário;
-   evitar remover colunas/contratos no mesmo release que deixa de
    usá-los;
-   executar verificação de saúde após a migration;
-   interromper rollout se a migration falhar;
-   preferir migrations expansivas e remoção posterior;
-   rollback de código não implica rollback automático seguro do banco.

## 10. Ambientes e estratégia de implantação

### 10.1 Ambientes

  ------------------------------------------------------------------------------
  Ambiente          Objetivo          Dados                    Acesso
  ----------------- ----------------- ------------------------ -----------------
  Local             desenvolvimento   sintéticos ou dados      desenvolvedores
                                      anonimizados             

  CI/test           testes            fixtures e bancos        pipeline
                    automatizados     descartáveis             

  Staging           ensaio integrado  sintéticos/minimizados   equipe e pilotos
                                                               autorizados

  Production        serviço real      dados reais sob política acesso restrito
                                      aprovada                 
  ------------------------------------------------------------------------------

Separar credenciais, bancos, buckets, filas e domínios por ambiente.
Nunca apontar testes para dados de produção por conveniência.

### 10.2 Implantação inicial

**\[RECOMENDAÇÃO NOVA\]** Se a escala e a stack permitirem, iniciar com
aplicação containerizada e dependências gerenciadas, mantendo a
arquitetura simples. Docker Compose pode ser suficiente para alguns
ambientes iniciais de servidor único, mas não deve ser confundido com
alta disponibilidade por si só. Avaliar plataforma
gerenciada/orquestrador conforme requisitos reais de disponibilidade,
escala, equipe e orçamento.

A documentação do Docker descreve ajustes específicos para Compose em
produção: https://docs.docker.com/compose/how-tos/production/

### 10.3 Ordem de provisionamento

1.  contas de cloud/infra e acessos com menor privilégio;
2.  DNS e certificados TLS;
3.  secret manager e variáveis não secretas;
4.  banco e política de backup;
5.  storage privado;
6.  fila/cache apenas se necessários ao MVP;
7.  serviço da aplicação;
8.  migrations;
9.  health/readiness endpoints;
10. logs, métricas, traces e alertas;
11. deploy de staging;
12. smoke tests e restauração ensaiada;
13. aprovação e deploy de produção;
14. verificação pós-deploy e observação.

### 10.4 Readiness e liveness

-   **Liveness:** confirma que o processo está vivo e não travado; não
    deve falhar por uma dependência externa temporariamente
    indisponível.
-   **Readiness:** confirma se a instância pode receber tráfego,
    considerando dependências necessárias para operar.
-   **Startup check:** útil quando a inicialização é demorada.

Os endpoints devem evitar expor credenciais, detalhes internos ou dados
sensíveis. Definir timeout e política para cada dependência.

## 11. Runbooks operacionais

Todo runbook deve ter: sintoma, impacto, detecção, ações seguras,
validação, rollback, comunicação e responsável.

### 11.1 Deploy com falha

1.  pausar novos deploys;
2.  verificar saúde, logs e métricas;
3.  comparar release, configuração e migrations;
4.  se o código for causa provável, reverter para artefato anterior
    compatível;
5.  não reverter schema automaticamente sem plano;
6.  executar smoke tests;
7.  registrar incidente e ação preventiva.

### 11.2 Banco indisponível

1.  confirmar alcance e estado do serviço gerenciado;
2.  verificar pool, conexões, limites e métricas;
3.  evitar retries agressivos que agravem a falha;
4.  habilitar modo degradado se disponível;
5.  restaurar somente após avaliar integridade e perda de dados;
6.  documentar impacto e reconciliação.

### 11.3 Provedor de IA indisponível

1.  marcar provedor/modelo como degradado;
2.  desativar retries sem limite;
3.  respeitar quotas e circuit breaker;
4.  usar fallback apenas se aprovado para o tipo de dado e tarefa;
5.  preservar execução e informar falha sem fabricar resposta;
6.  reconciliar consumo e custo quando o provedor voltar.

### 11.4 Vazamento ou suspeita de cruzamento entre tenants

1.  tratar como incidente crítico;
2.  bloquear rota, consulta, ferramenta ou índice afetado;
3.  preservar logs de auditoria sem divulgar dados;
4.  identificar escopo, tenants e período;
5.  revogar credenciais/sessões quando indicado;
6.  avaliar obrigações de comunicação e resposta com responsáveis
    jurídicos/privacidade;
7.  corrigir, testar regressão e só reativar após aprovação.

### 11.5 Backup/restore

1.  selecionar ponto de recuperação aprovado;
2.  restaurar em ambiente isolado;
3.  validar integridade, contagem e relações críticas;
4.  validar acesso multi-tenant e funcionamento da aplicação;
5.  registrar RPO observado e tempo total de restauração;
6.  promover restauração somente após aprovação.

## 12. Rollout e rollback

### 12.1 Estratégia de rollout

-   desenvolvimento interno;
-   staging;
-   grupo piloto restrito;
-   liberação gradual por organização/feature flag;
-   monitoramento reforçado;
-   ampliação apenas quando métricas e feedback forem aceitáveis.

### 12.2 Critérios de rollback

Definir por release, mas considerar: - aumento anormal de erros; - falha
de autenticação/autorização; - regressão de isolamento; - migration
incompatível; - latência ou saturação acima do limite acordado; - falha
de cobrança ou duplicação de ações; - custo de IA acima do orçamento; -
comportamento inseguro do agente; - perda de dados ou integridade.

Um incidente de isolamento ou execução não autorizada de ação sensível
exige desativação imediata da funcionalidade afetada, mesmo que a
disponibilidade geral continue normal.

### 12.3 Feature flags de emergência

Preparar flags para: - desligar todas as chamadas de IA; - desativar um
provedor ou modelo; - desativar agente específico; - desativar
ferramenta de escrita; - suspender ingestão RAG; - desligar integração
externa; - restringir novos cadastros ou operações custosas.

A flag precisa ser aplicada no servidor e testada; esconder um botão no
frontend não é controle de segurança.

## 13. Cronograma de referência --- 12 semanas

Este é um cenário de planejamento, não uma promessa. A duração real
depende do estado do repositório, da equipe, do escopo e das
integrações.

  ------------------------------------------------------------------------
  Período           Foco              Entregável         Gate
                                      verificável        
  ----------------- ----------------- ------------------ -----------------
  Semanas 1--2      Auditoria e       inventário, ADRs,  G0
                    decisões          MVP, backlog e     
                                      baseline           

  Semanas 3--4      CORE              identidade,        G1
                                      organizações,      
                                      permissões,        
                                      auditoria, testes  
                                      de tenant          

  Semanas 5--7      CRM MVP           fluxo principal    G2
                                      funcional, APIs,   
                                      interface, testes  
                                      E2E                

  Semanas 8--9      TITAN AI          gateway, agente    G3
                                      controlado,        
                                      limites,           
                                      auditoria, evals   
                                      iniciais           

  Semanas 10--11    Hardening         CI/CD,             G4
                                      observabilidade,   
                                      backup/restore,    
                                      segurança,         
                                      runbooks           

  Semana 12         Piloto            rollout restrito,  G5
                                      feedback,          
                                      métricas, decisão  
                                      de ampliar         
  ------------------------------------------------------------------------

### Fases paralelas permitidas

-   pesquisa com usuários pode acontecer durante a auditoria;
-   documentação e CI podem ser preparados antes do MVP;
-   observabilidade deve ser introduzida desde a fundação, não deixada
    para o fim;
-   segurança, privacidade e testes de tenant atravessam todas as fases.

### GATE 5 --- piloto aprovado

-   usuário-alvo conclui tarefa prioritária;
-   não há vulnerabilidades críticas abertas;
-   isolamento multi-tenant passou;
-   métricas e alertas têm responsável;
-   backup/restore foi ensaiado;
-   rollback está documentado;
-   custos de infraestrutura e IA são observáveis;
-   suporte, privacidade e incidentes têm responsáveis;
-   limitações conhecidas são comunicadas.

## 14. Gestão de trabalho e documentação

### 14.1 Modelo de tarefa

Cada tarefa deve conter: - objetivo e problema; - escopo
incluído/excluído; - dependências; - módulos/arquivos esperados; -
critérios de aceite verificáveis; - testes obrigatórios; - riscos e
plano de rollback; - responsável e prioridade; - evidência final (PR,
build, teste, captura/log sem dados sensíveis).

### 14.2 Definition of Ready

Uma tarefa está pronta quando: - requisito é compreensível; - critério
de aceite é verificável; - dependências estão identificadas; - impacto
de dados/segurança foi avaliado; - design/contrato está definido quando
necessário.

### 14.3 Definition of Done

Uma tarefa só é concluída quando: - código revisado; - testes relevantes
executados; - lint/typecheck/build passam; - controles de segurança
verificados; - documentação atualizada; - telemetria e tratamento de
erros incluídos quando necessários; - migration e compatibilidade
revisadas; - evidências anexadas; - deploy validado no ambiente exigido
para o estado declarado.

## 15. Gestão de riscos

  ------------------------------------------------------------------------
  Risco             Probabilidade     Impacto           Mitigação
                    inicial                             
  ----------------- ----------------- ----------------- ------------------
  Escopo grande     Alta              Alto              MVP estreito e
  demais                                                gates

  Stack real        Alta              Alto              auditoria antes de
  desconhecida                                          codificar

  Vazamento entre   Média             Crítico           RLS/ACL, testes
  tenants                                               negativos, revisão
                                                        de acesso

  Dependência       Média             Alto              gateway abstrato e
  excessiva de                                          política de
  provedor de IA                                        fallback

  Custo de IA       Média             Alto              quotas, budget por
  imprevisível                                          run e ledger de
                                                        uso

  Falhas em         Média             Alto              expand/contract,
  migrations                                            backup e ensaio

  Backups nunca     Média             Crítico           restore drills com
  restaurados                                           evidência

  Logs contendo     Média             Alto              redaction e
  dados sensíveis                                       retenção mínima

  Integrações       Alta              Médio/alto        timeouts, retries
  externas                                              controlados,
  instáveis                                             idempotência

  Lançamento sem    Média             Alto              entrevistas,
  validação de                                          piloto e métricas
  mercado                                               de retenção

  Microsserviços    Média             Médio/alto        monólito modular
  prematuros                                            até evidência de
                                                        escala

  Falta de          Média             Alto              owner, alertas e
  responsável                                           runbooks
  operacional                                           
  ------------------------------------------------------------------------

As probabilidades são uma avaliação inicial qualitativa, não uma análise
quantitativa. Revisar após auditoria.

## 16. Métricas de execução, produto e operação

### Engenharia

-   lead time de alteração;
-   frequência de deploy;
-   taxa de falha de mudança;
-   tempo de recuperação;
-   percentual de builds/testes aprovados;
-   defeitos escapados para staging/produção.

### Produto

-   tempo até primeiro valor;
-   ativação de organizações;
-   usuários ativos por organização;
-   conclusão do fluxo principal;
-   retenção;
-   conversão de piloto em cliente;
-   feedback e motivos de abandono.

### Operação

-   disponibilidade e latência por fluxo;
-   erros por versão;
-   idade do último backup;
-   sucesso de restauração;
-   filas pendentes e retries;
-   incidentes e tempo de resolução.

### TITAN AI

-   execuções por agente/organização;
-   sucesso, timeout, recusa e cancelamento;
-   custo por tarefa concluída;
-   tokens e latência;
-   chamadas de ferramentas e taxa de aprovação;
-   qualidade em golden set;
-   citações corretas e relevância do RAG;
-   violações de política ou falhas de isolamento.

Não usar métricas de vaidade como substituto de usuários ativos,
retenção ou conclusão real de tarefas.

## 17. Orçamento e custos

Valores de infraestrutura não podem ser estimados com precisão sem
stack, provedor, região, carga, retenção, volume documental e tráfego.
Não fixar uma cifra como se fosse orçamento aprovado.

Construir um modelo de custo com: - compute e tráfego; - banco e
armazenamento; - backups e retenção; - cache/fila/busca; -
observabilidade e logs; - provedores de IA (tokens, embeddings,
reranking, áudio/visão); - e-mail, SMS ou integrações externas; -
domínio, certificados e ferramentas de engenharia; - suporte, segurança
e incidentes.

Acompanhar custo por organização, tarefa e resultado útil. Definir
limites de gastos, alertas e ações automáticas de contenção antes de
abrir o piloto.

## 18. Referências técnicas consultadas

As referências abaixo fundamentam partes do plano. Elas não provam que
qualquer tecnologia já foi adotada no TITAN OS.

1.  **Docker --- Use Compose in production:** práticas para configuração
    de produção e atualização de serviços.\
    https://docs.docker.com/compose/how-tos/production/
2.  **GitHub Actions --- Deployment environments:** ambientes,
    proteções, aprovação e segredos por ambiente.\
    https://docs.github.com/en/actions/concepts/workflows-and-actions/deployment-environments
3.  **PostgreSQL --- SQL Dump and Restore:** `pg_dump`, `pg_restore` e
    estratégias de backup.\
    https://www.postgresql.org/docs/current/backup-dump.htm
4.  **OpenTelemetry --- Documentation:** instrumentação e exportação de
    traces, métricas e logs.\
    https://opentelemetry.io/docs/
5.  **OWASP --- Application Security Verification Standard (ASVS):**
    requisitos verificáveis para segurança de aplicações.\
    https://owasp.org/www-project-application-security-verification-standard/
6.  **Microsoft --- SaaS multitenant solution architecture:** decisões
    de isolamento e arquitetura SaaS.\
    https://learn.microsoft.com/en-us/azure/architecture/guide/saas-multitenant-solution-architecture/
7.  **OWASP GenAI Security Project --- Top 10:** riscos de aplicações de
    IA generativa.\
    https://genai.owasp.org/llm-top-10/
8.  **NIST --- AI Risk Management Framework:** gestão de riscos de IA.\
    https://www.nist.gov/itl/ai-risk-management-framework
9.  **ANPD --- materiais educativos e publicações:** orientação
    institucional para proteção de dados pessoais no Brasil.\
    https://www.gov.br/anpd/pt-br/centrais-de-conteudo/materiais-educativos-e-publicacoes

## 19. Checklist de lançamento

### Produto

-   [ ] Público inicial e problema prioritário definidos.
-   [ ] Fluxo principal demonstrável.
-   [ ] Critérios de sucesso e feedback preparados.
-   [ ] Limitações conhecidas comunicadas.

### Código e qualidade

-   [ ] Repositório auditado.
-   [ ] Build reproduzível.
-   [ ] Testes unitários, integração e E2E relevantes passam.
-   [ ] Migrations revisadas e testadas.
-   [ ] Dependências e segredos verificados.

### Segurança e privacidade

-   [ ] Autenticação e autorização verificadas.
-   [ ] Isolamento multi-tenant testado negativamente.
-   [ ] Segredos fora do código.
-   [ ] Logs com redação e retenção definidas.
-   [ ] Política de privacidade/LGPD revisada.
-   [ ] Riscos de IA avaliados e ferramentas limitadas.

### Implantação e operação

-   [ ] Staging funcional.
-   [ ] CI/CD com proteção de produção.
-   [ ] Artefato imutável promovido entre ambientes.
-   [ ] Health/readiness endpoints verificados.
-   [ ] Monitoramento e alertas com responsável.
-   [ ] Backup e restauração ensaiados.
-   [ ] Rollback testado.
-   [ ] Runbooks e responsáveis publicados.
-   [ ] Custos e quotas monitorados.

### TITAN AI

-   [ ] Agente e prompt versionados.
-   [ ] Limites de execução aplicados.
-   [ ] Tool allowlist e autorização server-side.
-   [ ] Aprovação para ações sensíveis.
-   [ ] Evals e testes adversariais aprovados.
-   [ ] Procedimento de desligamento testado.

O checklist deve conter links para as evidências. Marcar uma caixa sem
evidência não equivale a aprovação.

## 20. Próximas ações imediatas

1.  Disponibilizar o repositório ou um arquivo compactado com o código
    atual.
2.  Executar a auditoria e preencher a matriz
    `existente / parcial / ausente / testado`.
3.  Confirmar o público inicial e o primeiro fluxo de valor.
4.  Aprovar as ADRs de stack, identidade, multi-tenancy, deploy e IA.
5.  Converter as fases 0--2 em issues pequenas com critérios de aceite.
6.  Fazer o primeiro deploy em staging somente após testes e
    configurações mínimas.
7.  Integrar o TITAN AI após a fundação de identidade, autorização e
    auditoria estar validada.

**Conclusão:** este plano é uma especificação de execução e implantação.
Não há evidência disponível de que o código, os ambientes, o CI/CD, os
backups ou os testes de produção já existam. O próximo passo técnico
correto é auditar o repositório e transformar este plano em tarefas
compatíveis com o estado real do projeto.
