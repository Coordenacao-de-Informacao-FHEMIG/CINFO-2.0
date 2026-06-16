# Evento Rede Automatiza - Automação em R

1. #### Quem sou eu e o que eu faço:



Coordenadora de Informação da Rede Fhemig (a maior rede hospitalar 100% SUS do Brasil)

\[Deixar mais dinâmico e compreensível]

Sou bacharel em Administração Pública, com atuação voltada à gestão da informação, governança de dados e inovação em processos no setor público. Atualmente, coordeno a área de Informação da Fundação Hospitalar do Estado de Minas Gerais (Fhemig), sendo responsável pela estruturação e gestão de bases de dados assistenciais e administrativas, bem como pelo desenvolvimento de soluções analíticas — relatórios gerenciais, indicadores estratégicos e painéis de Business Intelligence — que subsidiam a tomada de decisão e fortalecem a transparência institucional.



Lidero iniciativas de modernização da arquitetura de dados, automação de processos de monitoramento assistencial e consolidação de práticas de governança de dados, incluindo a coordenação do Conselho de Governança de Dados da Fhemig. Minha atuação envolve também a integração de sistemas, padronização de indicadores e adoção de boas práticas em engenharia de dados e analytics, com foco em eficiência, qualidade da informação e suporte à gestão hospitalar em larga escala.



Possuo experiência prévia em gestão da informação aplicada a diferentes políticas públicas, com destaque para atuação na Secretaria de Estado de Saúde de Minas Gerais (SES/MG), na gestão e acompanhamento de repasses financeiros (2021–2025), na Secretaria de Planejamento e Gestão (Seplag/MG), na área de gestão patrimonial (2021), na Secretaria de Estado de Desenvolvimento Econômico (Sede/MG), em projetos e políticas públicas (2020), e no Instituto de Desenvolvimento do Norte e Nordeste de Minas Gerais (Idene/MG), na gestão de estoques (2019).



Minha trajetória é orientada pela utilização estratégica de dados como instrumento de transformação da gestão pública, promovendo maior eficiência, transparência e qualidade na prestação de serviços à sociedade.



1. Estrutura do setor (em que órgão fica, o que fazem): \[explicar CINFO]

   ---
2. #### Situação problema ou ideia que originou a iniciativa em R:

SITUAÇÃO PROBLEMA: Monitoramento do contrato do HRAD, conforme caderno de indicadores (anexo).

DESAFIOS: Fontes diversas, processo manual de extração, metodologias complexas com chance de erro, cálculos em várias planilhas Excel espalhadas. Processo realizado mensalmente por uma equipe de 4 pessoas dedicada exclusivamente ao monitoramento do contrato.

CENÁRIO: consulta web do DRG Brasil (difícil usabilidade e restrições de acesso) + consulta web do site do DataSUS (erros e lentidão) + uso do Tabwin (difícil, limitado) + uso do Pentaho para extração dos dados SIGH (lentidão e restrições de acesso) + recebimento dos dados declaratórios via email (consolidados manualmente numa planilha excel) + cálculos manuais feitos mensalmente no Excel



#### 3\. Como foi o processo: quais ferramentas foram utilizadas (explicar brevemente antes de tudo o que é o R e quais as potencialidades dele para quem não conhece tanto), necessidade de treinamentos, receptividade pela equipe;

Automatização de todo processo de extração de dados (R + SQL) e cálculo dos indicadores (Power BI):

1. Datasus: extração via biblioteca microdatasus no R, que busca dados da FTP do MS mensalmente e abastece um banco local MySQL (biblioteca RMySQL), que abastece um dataflow no Fabric

   1. library(microdatasus)
   2. library(RMySQL)
   3. library(dplyr)
   4. library(lubridate)
2. DRG: extração via API da IAG DRG Brasil e abastece banco local MySQL

   1. library(jsonlite)
   2. library(purrr)
   3. library(DBI)
   4. library(RMySQL)
   5. library(httr)
   6. library(lubridate)
3. SIGH: importação dos dados MySQL num dataflow no Fabric (apenas citar, foco é automação em R)
4. Pesquisa de satisfação: feita no Microsoft Forms, excel com resultados importados para o Fabric (apenas citar, foco é automação em R)
5. Dados declaratórios: feita em planilhas excel no SharePoint, importadas para o Fabric (apenas citar, foco é automação em R)
6. Todos os dataflows são importados num dataset no Power BI e os KPIs (do caderno) são calculados lá. Esse dataset abastece um report (https://app.powerbi.com/view?r=eyJrIjoiNDI4ZWFiNWYtZjZmMC00ZjI5LWFhM2ItMzQzOGY3NDRmZGFhIiwidCI6IjJhN2M1ZWQ1LWE5MGEtNGEyMy1hNTA4LTFmNjFkMzRlZmZhOCJ9) publicado no site oficial da Fhemig (**)**







#### 4\. Resultados antes x depois:



