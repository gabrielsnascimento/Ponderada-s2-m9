# Diagrama de Contexto — C4 Model (Nível 1)
## Sistema Integrado de Monitoramento e Gestão de Configuração para Máquinas Agrícolas (SIMA)


### 1. Identificação dos Atores e Sistemas Externos

#### 1.1 Pessoas (Atores Humanos)

| Ator | Papel | Necessidades principais |
|---|---|---|
| Operador Agrícola | Profissional que opera as máquinas no campo | Dashboards em tempo real, alertas críticos, interface mobile |
| Administrador de Configuração | Técnico responsável pelos perfis operacionais | Editor de configurações, versionamento, rollout e rollback de firmware |
| Gestor da Fazenda | Tomador de decisão estratégico | Relatórios gerenciais, KPIs de produtividade e custos operacionais |
| Equipe de Manutenção | Responsável pelas intervenções técnicas | Recebimento de alertas e abertura automática de ordens de serviço |

#### 1.2 Sistemas Externos

| Sistema | Função | Protocolo |
|---|---|---|
| Frota de Máquinas Agrícolas (IoT) | Origem da telemetria e destino dos comandos | MQTT-SN / 4G / LoRaWAN |
| Plataforma de Integração / Middleware IoT | Broker que abstrai a comunicação bidirecional com os dispositivos | MQTT / AMQP |
| Serviço de Dados Meteorológicos | API externa de previsão do tempo e alertas climáticos por região | HTTPS / REST |
| Banco de Dados Central | Repositório de telemetria, configurações, usuários e logs de auditoria | JDBC / SQL / TLS |
| Sistema ERP da Fazenda | Gestão financeira, insumos, estoque e custos operacionais | REST / Webhook |
| Provedor de Identidade (IdP) | Autenticação SSO corporativa (Keycloak, Azure AD, Auth0) | OAuth2 / OIDC / SAML |


### 2. Definição das Fronteiras do Sistema

#### 2.1 Dentro do escopo do SIMA

- Ingestão e processamento de telemetria das máquinas em tempo real
- Dashboards web e mobile para operadores e gestores
- Motor de regras para disparo de alertas por thresholds (temperatura, pressão, consumo)
- Gestão do ciclo de vida das configurações: criação, versionamento, rollout, rollback e auditoria
- Orquestração de comandos para a frota, incluindo atualização de firmware OTA
- Correlação de dados climáticos com a performance operacional das máquinas
- Geração de relatórios gerenciais e analytics
- Autenticação, autorização e auditoria interna (RBAC, logs de ação)

#### 2.2 Fora do escopo do SIMA (delegado a sistemas externos)

- **Coleta bruta dos sinais dos sensores** — responsabilidade do firmware embarcado nas máquinas
- **Roteamento e protocolos de baixo nível** — responsabilidade da Plataforma de Integração (Middleware)
- **Modelagem meteorológica** — o SIMA consome a API de clima, mas não calcula previsões
- **Persistência física, backup e replicação** — gerenciados pela equipe do Banco de Dados Central
- **Gestão financeira e contábil** — delegada ao ERP da fazenda
- **Gestão de identidade dos usuários** (cadastro, MFA, recuperação de senha) — delegada ao Provedor de Identidade


### 3. Diagrama de Contexto (como código)

O diagrama foi desenvolvido seguindo a notação oficial do C4 Model proposta por Simon Brown e documentado em duas versões equivalentes de "diagrama como código".

- **PlantUML** com a biblioteca C4-PlantUML — versão recomendada por aderir à notação canônica do C4
- **Mermaid** — versão alternativa com renderização nativa em GitHub, GitLab e Notion

Os arquivos estão disponíveis em `/diagrams/c4.puml` e `/diagrams/c4.mmd`. Instruções de visualização no `README.md`.

### 4. Descrição dos Relacionamentos

#### 4.1 Atores → Sistema

| Origem | Destino | Descrição | Protocolo |
|---|---|---|---|
| Operador Agrícola | SIMA | Monitora máquinas e visualiza alertas em tempo real | HTTPS / WebSocket |
| Administrador de Configuração | SIMA | Define e publica configurações da frota | HTTPS |
| Gestor da Fazenda | SIMA | Consulta dashboards e relatórios gerenciais | HTTPS |
| SIMA | Equipe de Manutenção | Notifica alertas e abre ordens de serviço | Email / Push / SMS |

#### 4.2 Sistema → Sistemas Externos

| Origem | Destino | Descrição | Protocolo |
|---|---|---|---|
| SIMA | Middleware IoT | Publica comandos; assina tópicos de telemetria | MQTT / AMQP |
| Middleware IoT | Frota de Máquinas | Encaminha comandos e coleta telemetria | MQTT-SN / 4G / LoRaWAN |
| Frota de Máquinas | Middleware IoT | Envia telemetria: RPM, temperatura, GPS, consumo | MQTT-SN / 4G |
| SIMA | Serviço Meteorológico | Consulta previsão e alertas climáticos por região | HTTPS / REST |
| SIMA | Banco de Dados Central | Persiste e consulta dados operacionais e configurações | JDBC / SQL / TLS |
| SIMA | ERP da Fazenda | Sincroniza custos, insumos e ordens de serviço | REST / Webhook |
| SIMA | Provedor de Identidade | Autentica e autoriza usuários | OAuth2 / OIDC / SAML |


### 5. Referências

- Brown, Simon. *The C4 Model for Visualising Software Architecture*. Disponível em: https://c4model.com
- Biblioteca C4-PlantUML. Disponível em: https://github.com/plantuml-stdlib/C4-PlantUML
- Documentação oficial do Mermaid. Disponível em: https://mermaid.js.org