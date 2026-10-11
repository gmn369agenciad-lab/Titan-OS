# TITAN OS --- Arquitetura Técnica Detalhada

**Versão:** 1.0\
**Data de referência:** 9 de outubro de 2026\
**Status:** Proposta técnica para validação; não representa
implementação já verificada.

## 1. Objetivo e regras de leitura

Este documento transforma o escopo recuperado do TITAN OS em uma
arquitetura de referência implementável. Ele diferencia:

-   **\[HISTÓRICO\]**: assuntos explicitamente presentes no histórico
    disponível.
-   **\[RECOMENDAÇÃO\]**: decisão técnica proposta neste documento.
-   **\[PENDENTE\]**: decisão que depende do repositório, da equipe, do
    orçamento ou da validação comercial.
-   **\[NÃO IMPLEMENTADO/VERIFICADO\]**: não há evidência disponível de
    que a funcionalidade esteja codificada, implantada ou testada.

O histórico disponível menciona um SaaS modular, IA multimodal, agentes,
RAG, memória persistente, multi-LLM, CRM, Finance, Projects, Docs,
Analytics, Automation, Agro, Command Center, `.env.production`,
PostgreSQL, Redis, RabbitMQ, MinIO/S3, Elasticsearch, Grafana e
Prometheus. Isso confirma que esses tópicos foram discutidos, mas não
comprova que exista código executável para eles.

## 2. Resumo executivo da arquitetura

**\[RECOMENDAÇÃO\]** Começar com um **monólito modular**, API-first, com
processamento assíncrono para tarefas demoradas. Separar serviços apenas
quando escala, isolamento, implantação independente ou requisitos de
disponibilidade justificarem a complexidade.

### Diagrama lógico

``` mermaid
flowchart TB
    U[Web app / Mobile futuro] --> EDGE[HTTPS ingress / reverse proxy]
    EDGE --> API[API application]
    API --> AUTH[Identity + Authorization]
    API --> CORE[TITAN CORE]
    API --> CRM[TITAN CRM]
    API --> DOCS[TITAN DOCS]
    API --> FIN[TITAN FINANCE]
    API --> PROJ[TITAN PROJECTS]
    API --> ANA[TITAN ANALYTICS]
    API --> AUTO[TITAN AUTOMATION]
    API --> AGRO[TITAN AGRO]
    API --> AI[TITAN AI gateway / orchestrator]
    CORE --> PG[(PostgreSQL)]
    CRM --> PG
    DOCS --> PG
    FIN --> PG
    PROJ --> PG
    ANA --> PG
    AUTO --> PG
    AGRO --> PG
    AI --> PG
    API --> REDIS[(Redis, opcional)]
    API --> OBJ[(S3 / MinIO)]
    API --> MQ[(RabbitMQ ou fila gerenciada)]
    MQ --> WORKERS[Background workers]
    WORKERS --> AI
    WORKERS --> OBJ
    API --> OBS[OpenTelemetry]
    WORKERS --> OBS
    OBS --> MON[Metrics / logs / traces]
```

### Princípios

1.  O banco transacional é a fonte autoritativa para dados de negócio.
2.  Cada módulo é dono de suas tabelas e regras.
3.  Módulos não alteram diretamente tabelas de outros módulos; usam
    interfaces de aplicação ou eventos.
4.  Toda operação é autenticada e autorizada no servidor.
5.  Toda requisição de negócio carrega um contexto organizacional
    validado.
6.  A IA não recebe acesso irrestrito ao banco nem às ferramentas.
7.  Operações assíncronas precisam de idempotência, retentativas
    limitadas e registro de estado.
8.  Segredos e dados pessoais não devem aparecer em logs
    desnecessariamente.
9.  APIs, eventos e prompts são versionados.
10. As decisões de arquitetura são registradas como ADRs.

## 3. Registro de estado: histórico, recomendação e pendências

  --------------------------------------------------------------------------------------
  Área                 Estado identificado         Recomendação      Pendente
  -------------------- --------------------------- ----------------- -------------------
  SaaS modular         \[HISTÓRICO\]               Monólito modular  Validar repositório
                                                   inicialmente      e equipe

  Multi-tenant         \[HISTÓRICO\]               `tenant_id` +     Modelo final de
                       multiempresa/multiusuário   autorização       isolamento
                                                   central + RLS     
                                                   onde adequada     

  IA multimodal        \[HISTÓRICO\]               Gateway de        Provedores, custos
                                                   modelos e tarefas e capacidades
                                                   assíncronas       

  Agentes              \[HISTÓRICO\]               Registro          Casos de uso
                                                   versionado de     prioritários
                                                   agentes e         
                                                   ferramentas       
                                                   allowlisted       

  RAG/memória          \[HISTÓRICO\]               Recuperação       Estratégia de
                                                   filtrada por      embeddings/índice
                                                   tenant e          
                                                   permissões        

  PostgreSQL           \[HISTÓRICO\]               Fonte principal   Versão, hosting e
                                                   transacional      HA

  Redis                \[HISTÓRICO\]               Cache/rate        Necessidade real e
                                                   limiting quando   política de cache
                                                   necessário        

  RabbitMQ             \[HISTÓRICO\]               Broker assíncrono Escolha vs serviço
                                                   se houver workers gerenciado

  MinIO/S3             \[HISTÓRICO\]               Armazenamento de  Provedor, retenção
                                                   objetos privado   e custo

  Elasticsearch        \[HISTÓRICO\]               Não adotar até    Busca exata,
                                                   haver necessidade full-text ou
                                                   clara             vetorial

  Prometheus/Grafana   \[HISTÓRICO\]               Observabilidade   Operação e alertas
                                                   com métricas,     
                                                   logs e traces     

  `.env.production`    \[HISTÓRICO\]               Secret manager +  Ambiente de
                                                   variáveis não     implantação
                                                   secretas          

  Marketplace/API      \[HISTÓRICO\] futuro        Não bloquear MVP; Prioridade e
  pública/mobile                                   desenhar          orçamento
                                                   contratos         
                                                   evolutivos        
  --------------------------------------------------------------------------------------

## 4. Arquitetura de repositório

**\[RECOMENDAÇÃO\]** Estrutura monorepo para facilitar mudanças
coordenadas no início. A linguagem/framework deve ser escolhida após
inspeção do código existente.

``` text
titan-os/
├── apps/
│   ├── web/                     # interface web
│   ├── api/                     # API HTTP
│   └── worker/                  # jobs assíncronos
├── packages/
│   ├── contracts/               # schemas, DTOs, eventos
│   ├── config/                  # configuração validada
│   ├── observability/            # logging, tracing, metrics
│   ├── auth-client/              # integrações internas de identidade
│   └── test-kit/                 # factories e helpers de teste
├── modules/
│   ├── core/
│   ├── ai/
│   ├── crm/
│   ├── finance/
│   ├── projects/
│   ├── docs/
│   ├── analytics/
│   ├── automation/
│   ├── agro/
│   └── command-center/
├── infrastructure/
│   ├── containers/
│   ├── deployment/
│   ├── observability/
│   └── scripts/
├── migrations/
├── tests/
│   ├── integration/
│   ├── e2e/
│   ├── security/
│   └── tenancy/
├── docs/
│   ├── adr/
│   ├── api/
│   ├── threat-model/
│   └── runbooks/
├── .env.example
├── .gitignore
├── README.md
└── SECURITY.md
```

### Regra de propriedade dos módulos

Cada módulo deve conter, no mínimo:

``` text
module-name/
├── domain/          # entidades e regras de negócio
├── application/     # casos de uso
├── infrastructure/  # persistência, provedores e adapters
├── http/            # controllers/routes/schemas
├── events/          # eventos publicados/consumidos
└── tests/
```

Não é obrigatório adotar literalmente essa estrutura se a stack
escolhida tiver convenções diferentes. A intenção é separar regras de
negócio de infraestrutura.

## 5. Fronteiras dos módulos

### TITAN CORE

Dono de organizações, membros, papéis, permissões, planos, quotas,
preferências, configurações e auditoria da plataforma.

### TITAN AI

Dono de provedores de modelo, catálogo de modelos, versões de prompts,
definições de agentes, execuções, chamadas de ferramentas, avaliação e
consumo de IA.

### TITAN CRM

Dono de leads, contatos, empresas, oportunidades, etapas de pipeline e
atividades comerciais.

### TITAN FINANCE

Dono de contas, lançamentos, categorias, centros de custo, contas a
pagar/receber, conciliações e relatórios financeiros. Integrações
bancárias exigem validação específica.

### TITAN PROJECTS

Dono de projetos, tarefas, dependências, marcos, comentários e
cronogramas.

### TITAN DOCS

Dono dos metadados de arquivos, versões, classificação, processamento
OCR, extração e estado de indexação. O conteúdo binário reside em object
storage.

### TITAN ANALYTICS

Dono das definições de KPI, agregações, snapshots e dashboards. Não deve
copiar indiscriminadamente dados sensíveis.

### TITAN AUTOMATION

Dono das definições de workflow, versões, gatilhos, execuções,
tentativas, filas e estados de aprovação.

### TITAN AGRO

Dono das entidades rurais, propriedades, safras, culturas, operações,
insumos, estoque e custos agro.

### TITAN COMMAND CENTER

Agrega métricas técnicas e empresariais autorizadas. Não deve tornar-se
o dono dos dados transacionais dos demais módulos.

## 6. Modelo de identidade e autorização

### Entidades essenciais

-   `users`: identidade da pessoa.
-   `organizations`: cliente/tenant.
-   `memberships`: associação de usuário a organização.
-   `roles`: papéis nomeados.
-   `permissions`: ações permitidas.
-   `role_permissions`: relação entre papéis e permissões.
-   `membership_roles`: papéis associados à participação.
-   `sessions` ou configuração equivalente do provedor de identidade.
-   `audit_events`: eventos de auditoria.
-   `subscriptions`, `plans`, `usage_records`: plano e consumo.

### Regras

1.  A autenticação prova a identidade; não prova autorização para um
    recurso.
2.  Toda requisição organizacional resolve um `organization_id`
    permitido para o usuário.
3.  O servidor não confia em `tenant_id` enviado pelo cliente sem
    validar membership.
4.  Verificar autorização em cada operação de leitura, alteração,
    exportação e exclusão.
5.  Papéis são atalhos de permissão, não substituem verificações por
    objeto.
6.  Usuários administradores da plataforma devem ter acesso privilegiado
    explicitamente separado.
7.  Jobs e webhooks precisam carregar contexto organizacional validado e
    auditável.

### Padrão de autorização

Uma verificação deve responder pelo menos:

-   Quem está agindo?
-   Em qual organização?
-   Sobre qual recurso?
-   Qual ação deseja executar?
-   Qual regra concede a permissão?
-   A ação exige aprovação adicional?
-   Como a decisão será auditada?

### PostgreSQL Row-Level Security

**\[RECOMENDAÇÃO\]** Avaliar Row-Level Security (RLS) como defesa em
profundidade para tabelas tenant-scoped. RLS não substitui autorização
na aplicação. O papel de conexão, políticas, bypass de RLS, jobs e
migrações precisam ser revisados. Testes devem comprovar que o contexto
de tenant não vaza entre conexões reutilizadas no pool.

Não usar `tenant_id` como única proteção sem testes e sem controle no
servidor.

## 7. Modelo de dados inicial

Exemplo conceitual, não migration pronta:

``` sql
CREATE TABLE organizations (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  status TEXT NOT NULL DEFAULT 'active',
  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE memberships (
  id UUID PRIMARY KEY,
  organization_id UUID NOT NULL REFERENCES organizations(id),
  user_id UUID NOT NULL,
  status TEXT NOT NULL DEFAULT 'active',
  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  UNIQUE (organization_id, user_id)
);

CREATE INDEX memberships_user_org_idx
  ON memberships(user_id, organization_id);
```

O exemplo não inclui a implementação completa de identidade, chaves
externas, políticas RLS ou migrações. Isso precisa ser adaptado ao
provedor de identidade e ao padrão de IDs escolhido.

### Convenções de dados

-   Usar UUID ou outro identificador não enumerável como identificador
    público.
-   Usar `TIMESTAMPTZ` para instantes.
-   Armazenar valores monetários em `NUMERIC` ou representação decimal
    adequada, nunca `FLOAT` para cálculos contábeis.
-   Definir moeda explicitamente em dados financeiros.
-   Usar constraints e chaves estrangeiras.
-   Indexar com base em consultas reais.
-   Criar migrações versionadas e reversíveis quando possível.
-   Não misturar dados de diferentes tenants em caches ou índices.
-   Estabelecer políticas de exclusão, retenção e anonimização.

## 8. API e contratos

**\[RECOMENDAÇÃO\]** REST/JSON como ponto de partida, com OpenAPI para
documentação. GraphQL ou gRPC só se requisitos concretos justificarem.

### Convenções

-   Prefixo versionado, por exemplo `/api/v1`.
-   Paginação consistente.
-   Filtros permitidos e validados.
-   Erros com códigos estáveis e mensagens sem dados sensíveis.
-   Correlation ID em cada requisição.
-   Idempotency key para operações de criação/execução sujeitas a retry.
-   Limites de requisição e quotas.
-   Validação de entrada em todos os limites de confiança.
-   Autorização por recurso.
-   Não devolver campos internos por padrão.

### Exemplos de rotas conceituais

``` text
GET    /api/v1/me
GET    /api/v1/organizations
POST   /api/v1/organizations
GET    /api/v1/organizations/{orgId}/members
POST   /api/v1/organizations/{orgId}/invites

GET    /api/v1/crm/leads
POST   /api/v1/crm/leads
GET    /api/v1/crm/opportunities
POST   /api/v1/crm/opportunities

POST   /api/v1/ai/runs
GET    /api/v1/ai/runs/{runId}
POST   /api/v1/ai/runs/{runId}/approve

POST   /api/v1/docs
GET    /api/v1/docs/{documentId}
POST   /api/v1/workflows/{workflowId}/runs
GET    /api/v1/workflows/runs/{runId}
```

Essas rotas são exemplos de contrato, não APIs existentes. Definir
autorização, schemas, códigos de erro, paginação e versionamento antes
de implementação.

## 9. Eventos e processamento assíncrono

Tarefas demoradas ou suscetíveis a retry devem ser executadas em
workers.

### Exemplos de eventos

-   `crm.lead.created`
-   `crm.opportunity.stage_changed`
-   `docs.uploaded`
-   `docs.ocr.completed`
-   `ai.run.requested`
-   `ai.run.completed`
-   `workflow.run.failed`
-   `finance.transaction.imported`

Eventos devem incluir `event_id`, `event_type`, `occurred_at`,
`organization_id`, `schema_version` e dados mínimos necessários. Evitar
colocar documentos ou dados pessoais completos no payload quando basta
uma referência.

### Garantias

-   Consumidores idempotentes.
-   Tentativas limitadas com backoff.
-   Dead-letter queue ou estratégia equivalente.
-   Observabilidade por `correlation_id`.
-   Tratamento de mensagens duplicadas.
-   Ordem de processamento somente quando necessária.
-   Política para mensagens inválidas.
-   Outbox transacional quando for necessário garantir publicação
    coerente com a transação do banco.

RabbitMQ foi mencionado no histórico, mas **\[PENDENTE\]** a escolha
entre RabbitMQ, serviço gerenciado ou outra fila. Não introduzir um
broker apenas por antecipação se uma fila mais simples atender ao MVP.

## 10. TITAN AI: arquitetura implementável

### Componentes

1.  `ModelGateway`: interface uniforme para provedores.
2.  `ModelRegistry`: capacidades, limites, versões e disponibilidade.
3.  `PromptRegistry`: prompts versionados e revisados.
4.  `AgentRegistry`: configuração e versão de cada agente.
5.  `RunOrchestrator`: ciclo de execução, limites e estados.
6.  `ToolRegistry`: ferramentas explicitamente permitidas.
7.  `KnowledgeService`: ingestão e recuperação RAG.
8.  `PolicyEngine`: permissões, risco e aprovações.
9.  `EvaluationService`: datasets e avaliações.
10. `UsageMeter`: tokens, duração, custo e quotas.
11. `AI Audit`: registro de execuções e ações.

### Estados de execução

``` text
queued
  -> running
  -> waiting_for_approval
  -> completed

running -> failed
running -> cancelled
waiting_for_approval -> running
waiting_for_approval -> cancelled
```

Os estados reais devem ser modelados por transições válidas e
concorrência controlada. Uma execução cancelada não pode continuar a
disparar ferramentas sem verificar o estado.

### Contrato conceitual de uma execução

``` json
{
  "run_id": "UUID",
  "organization_id": "UUID",
  "agent_id": "sales_assistant",
  "agent_version": "1",
  "prompt_version": "1",
  "requested_by": "USER_ID",
  "task": "Summarize an opportunity",
  "status": "queued",
  "approval_policy": "risk_based"
}
```

O JSON é ilustrativo. IDs e versões reais são atribuídos pelo sistema.
Não aceitar do cliente uma versão de agente como forma de elevar
permissões.

### Tool calling

Toda ferramenta deve ter: - Nome e versão. - Schema de entrada. -
Validação de argumentos. - Permissões exigidas. - Timeouts. - Limite de
execução. - Política de aprovação. - Resultado estruturado. - Auditoria.

Ferramentas devem executar casos de uso específicos, não SQL arbitrário
fornecido pelo modelo.

### Aprovação humana

Exigir aprovação para ações de alto impacto, como: - Enviar comunicações
externas em nome da empresa, quando a política assim definir. - Efetuar
pagamentos ou transferências. - Excluir dados de forma irreversível. -
Alterar permissões. - Assinar ou formalizar contratos. - Executar ações
em massa.

A aprovação precisa ser vinculada à ação e aos parâmetros exatos
aprovados; mudanças posteriores invalidam a aprovação.

## 11. RAG e documentos

### Pipeline proposto

1.  Receber upload autenticado.
2.  Validar tamanho, tipo e conteúdo.
3.  Armazenar o objeto em bucket privado.
4.  Registrar metadados e permissões.
5.  Enfileirar OCR/extração.
6.  Segmentar texto e registrar páginas/locais.
7.  Criar embeddings se necessários.
8.  Indexar com metadados tenant-scoped.
9.  Recuperar apenas documentos autorizados.
10. Responder com referências às fontes.
11. Registrar a versão da fonte e da pipeline.

### Segurança

-   Tratar documentos como conteúdo não confiável.
-   Não executar instruções contidas nos documentos.
-   Filtrar autorização antes de fornecer conteúdo ao modelo.
-   Não expor URLs permanentes de arquivos privados.
-   Usar URLs assinadas com validade curta quando adequadas.
-   Definir retenção e exclusão também para índices e embeddings.
-   Registrar a origem das respostas.
-   Avaliar prompt injection e extração de dados.

**\[PENDENTE\]** Escolher mecanismo de busca e embeddings após medir
qualidade, latência, custos e requisitos de escala. PostgreSQL com
extensão vetorial pode ser suficiente no início; um motor dedicado só
deve ser adicionado com justificativa.

## 12. Segurança e configuração de produção

### Configuração

Separar: - Configuração não secreta. - Segredos. -
Certificados/chaves. - Configuração por ambiente. - Configuração por
organização.

Variáveis previamente discutidas incluem: - `ENCRYPTION_MASTER_KEY` -
`ENCRYPTION_SALT` - `SESSION_SECRET` - `ALLOWED_ORIGINS`

Nomes finais dependem da stack. O secret manager deve ser preferido para
produção. `.env.example` contém apenas nomes e placeholders seguros.
Nunca incluir chaves reais no Git.

### Validação de configuração

No startup: - Verificar variáveis obrigatórias. - Validar formato e
tamanho. - Rejeitar origens CORS amplas não autorizadas em produção. -
Falhar de forma segura quando segredos obrigatórios faltarem. - Não
imprimir valores secretos nos logs.

### Controles mínimos

-   TLS.
-   MFA para administradores.
-   Hash seguro de senhas se geridas localmente.
-   Gestão de sessão segura.
-   Rate limiting.
-   Proteção CSRF conforme o mecanismo de autenticação.
-   Proteção contra SSRF e upload malicioso.
-   Verificação de dependências.
-   Backups criptografados.
-   Rotação de segredos.
-   Auditoria de privilégios.
-   Threat modeling.
-   Testes de isolamento tenant.
-   Processo de resposta a incidentes.
-   Política de retenção e descarte.

## 13. Arquivos e `.env.production`

O pedido histórico mencionou um `.env.production` com mais de 50
variáveis. **\[RECOMENDAÇÃO\]** Não criar dezenas de variáveis sem
consumidor real. Criar um inventário por módulo com proprietário,
finalidade, obrigatoriedade, ambiente e política de segredo.

Categorias esperadas, se os serviços correspondentes forem usados: - App
e URL pública. - Banco de dados. - Cache. - Broker/fila. - Storage. -
Identidade e sessão. - Criptografia. - E-mail. - Provedores de IA. -
Busca/embeddings. - Observabilidade. - Rate limiting. - Backups. -
Integrações. - Billing. - Feature flags.

Não registrar valores reais neste documento. Gerar segredos com
ferramentas apropriadas no ambiente-alvo. O valor de `ENCRYPTION_SALT`
deve ser definido segundo o uso criptográfico real, e não tratado
automaticamente como uma chave mestra.

## 14. Observabilidade

Usar logs estruturados, métricas e traces correlacionados.

Campos úteis: - `timestamp` - `severity` - `service` - `environment` -
`request_id` - `correlation_id` - `organization_id` quando permitido e
necessário - `user_id` pseudonimizado quando necessário - `operation` -
`duration_ms` - `status` - `error_code`

Não registrar: - Senhas. - Tokens. - Chaves de API. - Conteúdo integral
de documentos sensíveis. - Dados financeiros completos sem
necessidade. - Prompt ou resposta integral por padrão sem política de
retenção e proteção.

Métricas: - Taxa de erro. - Latência p50/p95/p99. - Disponibilidade. -
Pool de conexões. - Jobs pendentes. - Retentativas e dead letters. -
Custo e latência por modelo. - Taxa de aprovação/rejeição de agentes. -
Uso por tenant. - Falhas de autorização.

## 15. Estratégia de testes

### Unitários

Regras de negócio, cálculos financeiros, validações e transições de
estado.

### Integração

Persistência, filas, armazenamento e adapters externos.

### Multi-tenant

Tentar ler, alterar, exportar e apagar recursos de outro tenant. Testar
APIs, jobs, cache, RAG e downloads.

### Segurança

Autorização por objeto, abuso de sessão, upload, SSRF, rate limiting,
prompt injection e uso indevido de ferramentas.

### IA

Avaliar groundedness, precisão, fontes, qualidade, custo, latência,
recusas apropriadas e segurança de execução.

### End-to-end

Fluxos completos: onboarding, criar lead, executar agente, aprovar ação
e consultar auditoria.

### Operação

Restauração de backup, falha de provedor, indisponibilidade de fila,
migrações e rollback.

Nenhum teste deve ser declarado aprovado sem execução e resultado
registrados.

## 16. Infraestrutura e implantação

### MVP

Uma aplicação web, API, worker e PostgreSQL gerenciado podem ser
suficientes. Redis, broker separado e mecanismo de busca devem ser
adicionados quando requisitos demonstrarem necessidade.

### Crescimento

Separar workers por tipo de tarefa, aplicar limites por tenant, otimizar
consultas e monitorar custos.

### Escala avançada

Avaliar serviços independentes para IA, documentos ou workflows quando
houver carga, isolamento de falhas ou requisitos de implantação
independentes.

### CI/CD

1.  Lint e formatação.
2.  Análise estática.
3.  Testes unitários.
4.  Testes de integração.
5.  Testes tenant/security.
6.  Verificação de dependências e segredos.
7.  Build imutável.
8.  Deploy em homologação.
9.  Testes de aceitação.
10. Aprovação e deploy de produção.
11. Smoke tests.
12. Rollback quando necessário.

## 17. Modelo de negócio e billing técnico

Entidades candidatas: - `plans` - `subscriptions` -
`subscription_items` - `usage_records` - `invoices` ou referências ao
sistema financeiro externo - `entitlements` - `quota_policies`

O billing deve distinguir: - Plano contratado. - Recursos habilitados. -
Quotas. - Consumo medido. - Uso cobrável. - Cortes e limites. - Período
de cobrança. - Créditos e ajustes.

Não confiar em contadores do frontend. Consumo cobrável deve ser
registrado no backend, com idempotência e reconciliação.

## 18. Roadmap técnico

### Fase 0 --- Descoberta

-   Inspecionar repositório.
-   Confirmar stack.
-   Validar público inicial.
-   Definir PRD e critérios de aceite.
-   Elaborar threat model.
-   Registrar ADRs iniciais.

### Fase 1 --- Fundação

-   Estruturar repositório.
-   Configuração tipada.
-   Identidade.
-   Organizations e memberships.
-   Autorização.
-   Isolamento tenant.
-   Auditoria.
-   Migrações.
-   CI/CD e observabilidade.

### Fase 2 --- CRM MVP

-   Leads e contatos.
-   Empresas e oportunidades.
-   Pipeline.
-   Atividades.
-   Pesquisa e filtros.
-   Relatórios básicos.
-   Testes E2E e tenant isolation.

### Fase 3 --- TITAN AI inicial

-   Gateway de modelo.
-   Prompt registry.
-   Agent registry.
-   Execuções assíncronas.
-   Tool registry.
-   Aprovação.
-   Usage metering.
-   Dataset de avaliação.

### Fase 4 --- Documentos e automações

-   Upload privado.
-   OCR/extração.
-   RAG com controle de acesso.
-   Workflows e eventos.
-   Retry, idempotência e auditoria.

### Fase 5 --- Expansão

-   Projects.
-   Finance.
-   Analytics avançado.
-   Agro.
-   Command Center.
-   Integrações adicionais.

### Fase 6 --- Plataforma

-   API pública.
-   SDK.
-   Marketplace.
-   Mobile.
-   Isolamento dedicado e recursos enterprise.

As fases não possuem datas de conclusão aprovadas. Estimativas devem ser
feitas após inspeção do código, tamanho da equipe e definição do MVP.

## 19. Critérios de pronto para produção

Uma funcionalidade só é considerada pronta quando: - O requisito está
documentado. - O comportamento esperado tem critérios de aceite. - As
permissões foram testadas. - O isolamento entre tenants foi testado. -
Há testes automatizados relevantes. - Logs e métricas são adequados. -
Falhas e retentativas são tratadas. - Dados sensíveis estão
protegidos. - A documentação foi atualizada. - O deploy e rollback são
conhecidos. - O estado é confirmado por evidências.

Estados oficiais: `planned` → `specified` → `in_development` →
`implemented` → `tested` → `validated_in_target_environment`.

## 20. Referências técnicas

Usar as fontes oficiais para decisões e revisões: - Microsoft Azure
Architecture Center --- SaaS e multitenancy:
https://learn.microsoft.com/en-us/azure/architecture/guide/saas-multitenant-solution-architecture/ -
PostgreSQL --- Row Security Policies:
https://www.postgresql.org/docs/current/ddl-rowsecurity.html - OpenAI
--- Prompt engineering:
https://developers.openai.com/api/docs/guides/prompt-engineering - OWASP
GenAI Security Project: https://genai.owasp.org/llm-top-10/ - NIST AI
Risk Management Framework:
https://www.nist.gov/itl/ai-risk-management-framework - OpenTelemetry:
https://opentelemetry.io/docs/ - ANPD --- materiais educativos e
publicações:
https://www.gov.br/anpd/pt-br/centrais-de-conteudo/materiais-educativos-e-publicacoes

Verificar a versão e a data da documentação antes de fixar escolhas
dependentes de comportamento específico de produto.

## 21. Próximas ações executáveis

1.  Obter e inspecionar o repositório atual.
2.  Identificar linguagem, framework, estrutura e estado dos módulos.
3.  Confirmar público e escopo do MVP.
4.  Selecionar o provedor de identidade.
5.  Aprovar o modelo multi-tenant.
6.  Especificar migrations de CORE.
7.  Definir contratos da API.
8.  Implementar testes de isolamento.
9.  Entregar o primeiro fluxo completo de CRM.
10. Adicionar IA somente após estabelecer autorização, auditoria e
    avaliação.

**Conclusão:** esta arquitetura é uma proposta técnica detalhada baseada
no escopo recuperado. Não declara que o repositório, as APIs, os módulos
ou a infraestrutura já existam. O próximo passo de engenharia é comparar
este desenho com o código real e registrar as diferenças como plano de
implementação.
