## O QUE HOUVE?

Dia 05/05 -> consultas da CINFO derrubam o FHEPROD

GTIC e Phillips nos acionam



## QUAL É A SITUAÇÃO?

* A CINFO atua em um ambiente hospitalar crítico, com sistemas ERP em produção que não podem sofrer impacto de performance (Tasy).
* Crescimento do uso de BI para apoio à decisão assistencial, gerencial e estratégica. Quatro painéis principais: BE, Leitos, FF e Descentralização.
* Necessidade de agilidade na atualização, confiabilidade dos dados e escalabilidade.



*Problema central:*

*Como evoluir o BI sem travar o Tasy (e demais sistemas) e sem tornar a manutenção insustentável?*



*Em termos técnicos:*

Não temos staging: camadas intermediárias entre os sistemas ERP e o BI

ERP (Tasy - FHEPROD) → Power BI ❌

ERP (FHEPROD)

&#x20;      ↓

STAGING

&#x20;      ↓

Power BI ✅



## COMO TRABALHAMOS HOJE? DE FORMA INEFICIÊNTE

#### ARQUITETURA DE DADOS - PHILLIPS

* 3 bancos de dados OLTP:

  * FHEPROD (tempo real)
  * FHEHOM (último dump em março)
  * FHETRN (último dump em março)



* Ausência de ODS (Operational Data Store): não temos camada analítica intermediária dedicada (staging)

  * “Tabelas derivadas e estruturadas para análise, abastecidas a partir do ERP”



**CONSEQUÊNCIAS:**

* Usar o banco de produção (OLTP: Online Transaction Processing) como se fosse um banco analítico (OLAP: Online Analytical Processing).

  * "Hoje usamos o banco que foi feito para operar o hospital como se fosse um banco para análise. Isso naturalmente gera sobrecarga."
  * "O problema não é a existência dos BIs — é que eles estão ligados no banco errado."



O Tasy (e qualquer ERP hospitalar) é otimizado para:

* Inserir registros
* Atualizar rapidamente
* Buscar poucos registros por vez (transacional)



NÃO foi feito para:

* COUNT DISTINCT
* varrer milhões de linhas
* cruzar várias tabelas grandes
* fazer agregações mensais históricas



Quando o BI roda:

* disputa CPU com o sistema assistencial
* disputa disco (I/O)
* pode bloquear tabelas (dependendo do nível de isolamento)
* aumenta tempo de resposta do Tasy
* Resultado: lentidão ou até indisponibilidade do sistema clínico

#### 

#### CINFO (não aproveitamos todos as inovações disponíveis do Microsoft Fabric Pro)

* Consultas diretas e pesadas no banco de produção (OLTP)
* Queries complexas sem documentação
* Regras de negócio espalhadas em várias queries, transformações, colunas personalizadas e medidas DAX
* Retrabalho frequente para ajustes metodológicos
* Uso do .pbix e publicações via servidor 10.49.10.95



**CONSEQUÊNCIAS:**

* Sobrecarga do ERP assistencial e risco direto à operação hospitalar
* Opacidade dos cálculos e agregações, dificultando a auditoria e correção dos resultados em tela
* Baixa reutilização dos dados importados e alto custo de manutenção (refreshs constantes e lentos)
* Dificuldade no versionamento dos PBIX com risco de sobreposição de trabalho
* Impossibilidade de desenvolvimento colaborativo simultâneo: cada hora um trabalha





## COMO DEVERÍAMOS TRABALHAR? PROPOSTAS DE SOLUÇÃO

#### ARQUITETURA DE DADOS - PHILLIPS (e GTIC?)

##### 

##### *CURTO-MÉDIO PRAZO: Staging / ODS (Operational Data Store)*

Criar uma camada de staging com tabelas físicas derivadas do FHEPROD, voltadas exclusivamente ao consumo analítico.

* Sub-banco intermediário no FRPROD (metatabelas: censo, produção profissional, contas)
* Dados extraídos das tabelas fato do FHEPROD com atualização períodica e incremental em metatabelas (extração controlada de madrugada via ETL: procedure, job, event, etc.)
* Pode ter transformações leves (JOIN, CASE, FLAGS)
* Melhora significativa na performance
* Conversa com Linconl hoje e reunião com Phillips



##### *MÉDIO-LONGO PRAZO: Data Warehouse*

* Modelado para análise
* Histórico estruturado
* Fatos e dimensões
* Melhor cenário para BI estratégico
* Projeto do Márcio (DBA)





#### 

#### COORDENAÇÃO DE INFORMAÇÃO

A revolução da CINFO se baseia em 5 princípios estruturantes:

* Proteção absoluta do banco de produção
* Separação clara de responsabilidades (dados x métricas)
* Arquitetura em camadas
* Reutilização e padronização corporativa
* Escalabilidade com atualização incremental



Esses princípios são traduzíveis na adoção de novas ferramentas:

1. Staging 1: ODS metatabelas importantes
2. Staging 2: PBI -> Power BI DataFlows + Query Folding + Flags --> de 15 minutos para poucos segundos
3. PBI -> Atualização incremental (histórico fechado preservado + reprocessamento dos últimos 3 meses) --> de poucos segundos para poucos milésimos de segundo
4. KPI (Key Performance Indicator) calculados apenas em DAX
5. Projetos no GitHub: BI as Code

   1. Utilização do formato .pbir para os projetos Power BI.
   2. Separação clara entre modelo, medidas, consultas e metadados.
   3. Projetos armazenados em repositórios GitHub institucionais.
   4. Resultado: transparência total sobre o que mudou, quando mudou e por quem.
6. Github Branches:

   1. main: Versão oficial e produtiva (protegida contra alterações diretas)
   2. develop: Integração contínua de melhorias (base para validação funcional)
   3. feature\_X: Desenvolvimento de novos indicadores, ajustes metodológicos ou melhorias específicas
   4. Esse modelo permite trabalho simultâneo, sem conflitos e sem risco para o ambiente produtivo.
7. Github Actions:

   1. Pipeline automatizado de publicação do Power BI.
   2. A publicação ocorre somente após aprovação e merge na branch main.
   3. Eliminação de processos manuais e dependência de ações locais





Projeto CINFO 2.0: Para implementar essas inovações precisaremos de **refazer toda a arquitetura de dados por trás dos painéis existentes**:

1. Boletim Estratégico: prioridade!
2. Painel de leitos: caso especial -> retrato atual
3. Painel FF: aproveitamento de fluxos de dados
4. Painel dos Contratos Descentralizados: aproveitamento do fluxo de dados
5. Painel de OS: ideal para começar!



## Quais serão os ganhos estratégicos?

✅ Segurança operacional

✅ Confiabilidade dos indicadores

✅ Agilidade para mudanças metodológicas

✅ Reaproveitamento corporativo

✅ BI sustentável no longo prazo





#### *"Não são apenas novos BIs. É uma nova forma de trabalhar com informação."*

#### *Posso contar com o patrocínio dos meus superiores e o engajamento dos meus técnicos?*









# \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_











## Por onde começar?

Assistir vídeos da playlist: https://youtube.com/playlist?list=PLL02hqcLCThA2e0KLn9PGfY2\_jeECJ2TF\&si=eZWcf2jtyXCS-xQq





Cronograma do projeto:

1. 19-22/05: Varredura de todas as fontes de dados utilizadas atualmente (excel)

   1. Banco de dados
   2. Tabela (nome e tipo f/d)
   3. Colunas
   4. Gera flag?
2. 25-29/05: Importação dos dados via DataFlow:

   1. DataFlow Tasy Hist
   2. DataFlow Tasy Snapshot
   3. DataFlow SIGH Hist
   4. DataFlow SIGH Snapshot
   5. DataFlow DRG Hist
   6. DataFlow DATASUS Hist
   7. DataFlow SAPT Hist
3. 01-03/06: Validação e testes
4. 08-19/06: Reconstrução do BE (equipe dividida por abas do BE)

   1. Obter dados >> Fluxo de dados (de uma vez)
   2. Criar relacionamentos (de uma vez)
   3. Construção de medidas DAX (documentar cada commit no github)
   4. Encaixe das medidas nos visuais
5. 22/06-03/07: Reconstrução dos demais painéis (equipe dividida por painéis)

   1. FF (Maristela)
   2. Leitos (Eduardo)
   3. Descentralização (Ana e Eder)

