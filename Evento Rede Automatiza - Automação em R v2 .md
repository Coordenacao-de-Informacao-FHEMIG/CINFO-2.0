# Evento Rede Automatiza MG – Automação em R

#### 1\. Quem sou eu e o que eu faço



Ana Clara Mendes Rezende – Coord. de Informação (Fhemig) + Mestranda em Filosofia da Física pela UFMG

Gestão de dados, BI e apoio à decisão

Integração de sistemas: Tasy | SIGH | DataSUS | DRG

Stack: R + SQL + Power BI + Microsoft Fabric

CINFO 2.0



Arquitetura moderna (Fabric / Lakehouse)

Dataflows corporativos + camada semântica

Integração SIGH → Tasy (continuidade histórica)

Automação em R (ETL) + incremental refresh





#### 2\. Estrutura do setor (CINFO)

A Coordenação de Informação (CINFO) está vinculada à Administração Central da Fhemig e é responsável por transformar dados operacionais em informação qualificada para gestão.

Principais atribuições:



Integração de dados provenientes de múltiplos sistemas (assistenciais e administrativos);

Estruturação de bases analíticas e arquitetura de dados;

Desenvolvimento de indicadores institucionais e dashboards de BI;

Suporte informacional às unidades hospitalares;

Promoção de transparência ativa (ex: Portal de Dados Abertos);

Implantação de práticas de governança e padronização de indicadores.

A CINFO atua como elo entre tecnologia, gestão e assistência, garantindo que a informação seja confiável, acessível e útil para decisão.





#### 3\. Situação-problema que originou a iniciativa em R

Contexto: Monitoramento do Contrato do HRAD (https://www.fhemig.mg.gov.br/parcerias/organizacao-social/contratos-vigentes/contrato-de-gestao-hrad)

Caderno técnico com dezenas de indicadores contratuais (produção, qualidade, assistência e gestão), cada um acompanhado de ficha técnica completa (descrição, fórmula, fonte, periodicidade, meta, linha de base e metodologia detalhada de apuração).





**Complexidade extrema do modelo de medição:**

* Estrutura com múltiplas áreas temáticas e dezenas de indicadores (30+);
* Cada indicador possui: Fórmula própria (muitas com múltiplas etapas);
* Regras específicas de cálculo e exceções;
* Fontes distintas (DataSUS, DRG, sistemas internos, CNES);
* Dependência de conceitos técnicos avançados (DRG, AIH, glosas, case mix, protocolos assistenciais);





Processo manual descrito no caderno, para um único indicador envolve:

* Instalar e utilizar o TABWIN (ferramenta legada);
* Baixar os arquivos .dbc no site do DATASUS;
* Navegar por múltiplas telas e menus técnicos;
* Selecionar manualmente: Linhas, colunas, filtros e parâmetros; CNES da unidade;
* Executar tabulações;
* Exportar resultados para Excel;
* Calcular desempenho;
* Compilar série histórica;
* E isso precisa ser repetido para cada indicador e para cada período.





**Fragmentação:**

* DRG com dependência de extração em data específica (ex.: 3º dia útil);
* Sistemas internos isolados;
* Planilhas paralelas e dados declaratórios;
* Resultado: impossibilidade de visão única e tempestiva dos indicadores.





**Escala operacional insustentável:**

* Processo executado mensalmente por equipe dedicada;
* Cada indicador exige um roteiro próprio, longo e técnico;
* Alto volume de tarefas repetitivas;
* Forte dependência de conhecimento individual (não institucionalizado);





**Risco crítico para a gestão:**

* Elevado risco de erro humano (cálculos, filtros, exclusões);
* Baixa rastreabilidade e dificuldade de auditoria;
* Possibilidade de inconsistências entre indicadores;
* Impacto direto na avaliação contratual e financeira;





***Síntese:***

*Um modelo de indicadores tecnicamente robusto — porém operacionalizado por um processo manual, fragmentado e artesanal — resultando em alto custo operacional, baixa confiabilidade e risco institucional elevado.*

*Cenário incompatível com volume, complexidade e criticidade dos dados.*





#### 4\. O que é o R e por que utilizamos

O R é uma linguagem de programação voltada para análise de dados e estatística, amplamente utilizada para automação, integração de fontes e construção de pipelines de dados.



**Principais vantagens no contexto do projeto:**

1. Automação de processos repetitivos;
2. Integração com APIs e servidores (FTP, bancos de dados);
3. Manipulação eficiente de grandes volumes de dados;
4. Reprodutibilidade e rastreabilidade dos processos;
5. Redução significativa de erros manuais.





##### Como foi o processo de automação?

###### 4.1. Extração automatizada de dados por meio do R

**DataSUS:**

* Uso da biblioteca microdatasus para download direto da FTP do Ministério da Saúde, processamento e exportação para banco MySQL local.
* Principais bibliotecas:

  * microdatasus: acesso e padronização dos dados;
  * dplyr: transformação e organização;
  * lubridate: tratamento de datas;
  * DBI / RMySQL: conexão e exportação para MySQL.



**DRG Brasil:**

Integração via API, conversão de JSON em DF e exportação para MySQL local.

Principais bibliotecas:

httr: requisições HTTP;

jsonlite: transformação de dados;

purrr: automação de chamadas;





###### 4.2 Integração com outras fontes

* SIGH: importação via ODBC (MySQL 5) → Fabric;
* Pesquisa de satisfação: Forms → Fabric;
* Dados declaratórios: SharePoint → Fabric;





###### 4.3 Camada analítica - Microsoft Fabric

1. DataFlows por fonte/periodicidade de atualização
2. Datasets (silver): modelagem de dados, flags, merges e joins
3. Datasets (gold): KPIs do contrato de gestão
4. Dashboard interativo: https://app.powerbi.com/view?r=eyJrIjoiNDI4ZWFiNWYtZjZmMC00ZjI5LWFhM2ItMzQzOGY3NDRmZGFhIiwidCI6IjJhN2M1ZWQ1LWE5MGEtNGEyMy1hNTA4LTFmNjFkMzRlZmZhOCJ9







#### 5\. Resultados – antes x depois



**Aspecto			Antes		Depois**

Tempo de processamento	Alto		Automatizado

Esforço operacional	Manual		Reduzido

Confiabilidade		Baixa		Alta

Integração		Fragmentada	Centralizada

Escalabilidade		Limitada	Alta

Transparência		Restrita	Dashboard







#### 6\. Reflexões e aprendizados



* Automação melhora eficiência e qualidade;
* R viável para ETL no setor público;
* Pequenas equipes podem gerar alto impacto;
* R combinado ao BI: potencializa valor dos dados.





#### 7\. Dicas para quem quer começar



* Comece por processos repetitivos;
* Automatize aos poucos;
* Use bibliotecas prontas;
* Documente;
* Pense em arquitetura.

