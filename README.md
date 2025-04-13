# Analise de anúncios de emprego em Glassdoor com PostgreSQL

## Visão Geral do Projeto

Este projeto foca na análise de um conjunto de dados de anúncios de emprego para posições em Ciência de Dados, extraídos do Glassdoor. O objetivo inicial era transformar dados brutos e inconsistentes em um conjunto estruturado e confiável. O escopo foi expandido para incluir uma análise exploratória detalhada (EDA), engenharia de features e a geração de insights relevantes.

Utilizando SQL (via PostgreSQL) e Python (Pandas, SQLAlchemy) em um ambiente Jupyter Notebook, o projeto busca responder a perguntas de negócio chave, como:

*   Quais são as habilidades mais requisitadas para diferentes cargos em Ciência de Dados?
*   Como os salários variam por cargo, região (estado) ou setor da indústria?
*   Quais empresas estão contratando mais profissionais da área?
*   Existe correlação entre habilidades específicas e faixas salariais?

O foco principal da análise e manipulação foi realizado via SQL, demonstrando a capacidade de preparar e analisar dados diretamente no banco de dados.

## Compreensão dos Dados

**Origem:** O projeto utiliza o dataset `Uncleaned_DS_jobs.csv`, disponível publicamente no Kaggle ([link](https://www.kaggle.com/datasets/rashikrahmanpritom/data-science-job-posting-on-glassdoor)), contendo informações de anúncios de emprego do Glassdoor.

**Estrutura Inicial:** O dataset bruto continha colunas como `Job Title`, `Salary Estimate`, `Job Description`, `Company Name`, `Location`, `Sector`, `Industry`, `Rating`, `Founded`, entre outras. Apresentava inconsistências como valores ausentes representados por '-1' ou 'Unknown', formatação irregular em nomes de empresas e a faixa salarial (`Salary Estimate`) como texto.

**Processo de Limpeza e Transformação:**
1.  **Padronização:** Colunas renomeadas para snake_case; textos em `company` normalizados (remoção de espaços extras, tabs).
2.  **Valores Ausentes:** Marcadores como '-1' e 'Unknown' foram convertidos para `NULL` em colunas relevantes.
3.  **Salário:** A coluna `Salary Estimate` foi processada para extrair valores numéricos mínimo (`salary_estimate_lower`) e máximo (`salary_estimate_higher`), removendo texto como '(Glassdoor est.)'.
4.  **Duplicatas:** Entradas duplicadas foram identificadas e removidas usando CTEs em SQL.
5.  **Outliers:** Identificados via IQR; um outlier em `founded` foi corrigido com base em pesquisa externa. Outliers salariais foram mantidos por representarem possíveis remunerações reais.

**Engenharia de Features:** Para facilitar a análise, novas colunas foram criadas:
*   **Flags de Habilidades:** Colunas binárias (`tem_python`, `tem_sql`, `tem_ml`, `tem_cloud`, `tem_tableau`, `tem_power_bi`, `tem_excel`) baseadas na presença de palavras-chave na `Job Description`.
*   **Salário Médio (`salary_estimate_avg`):** Média entre o salário mínimo e máximo estimados.
*   **Estado (`state`):** Sigla do estado extraída da coluna `Location`.
*   **Título Simplificado (`job_title_simplified`):** Agrupamento de títulos de cargos semelhantes (ex: "Data Scientist", "Data Analyst", "ML/AI Engineer/Scientist").

**Seleção de Features:** Colunas como `id`, `competitors` (muitos nulos), `job_description` (informação extraída para flags) e `salary_estimate` (substituída) foram removidas para focar nas variáveis mais relevantes para as perguntas de negócio.

**Limitações:** Os dados salariais são estimativas do Glassdoor. A identificação de habilidades é baseada em palavras-chave e pode não capturar todas as nuances. O desbalanceamento por cargo e localização foi apontado durante a interpretação dos resultados.

## Análise

A análise focou em responder às perguntas de negócio utilizando o dataset processado (`ds_jobs_fs`):

*   **Habilidades Mais Requisitadas:** Python, Machine Learning (ML) e SQL são as habilidades mais demandadas no geral. A importância relativa varia por cargo: SQL é crucial para Analistas de Dados, enquanto Cloud é mais relevante para Engenheiros de Dados e ML para Engenheiros de ML/IA.
*   **Habilidades vs. Salários:** A presença das habilidades técnicas mais comuns (Python, SQL, ML) não mostrou correlação direta com a *mediana* salarial, sugerindo que são requisitos básicos ("custo de entrada"). A presença de Power BI associou-se a uma mediana ligeiramente superior, e Excel a uma inferior.
*   **Variação Salarial:**
    *   **Por Cargo:** Data Scientists e ML/AI Engineers tendem a ter salários médios/medianos mais altos, mas com maior variabilidade. Engenheiros de Dados apresentam salários competitivos com menor dispersão.
    *   **Por Estado:** CA, VA, MA, NY concentram o maior número de vagas. Estados como NC, DC, NY, TX, WA mostraram médias salariais mais elevadas.
    *   **Por Setor:** TI, Serviços de Negócios e Biotech/Pharma lideram em volume de vagas. Setores como Mídia e Aeroespacial/Defesa podem oferecer salários médios altos, mas com menos vagas e maior variabilidade.
*   **Empresas Líderes:** Empresas como Maxar, Klaviyo e Novetta se destacaram em volume de contratação em hubs específicos (VA, MA). As práticas salariais variam consideravelmente mesmo entre as empresas que mais contratam.

## Conclusão

O projeto transformou com sucesso um dataset bruto de vagas de Ciência de Dados em uma base estruturada e rica para análise. A análise revelou que, embora habilidades técnicas como Python, SQL e Machine Learning sejam fundamentais, a remuneração é mais fortemente influenciada por fatores como o cargo específico, localização geográfica (estado), setor de atuação e a própria empresa contratante. Habilidades fundamentais parecem ser pré-requisitos, enquanto outros fatores determinam a diferenciação salarial. Embora o dataset seja de 2020, as técnicas de manipulação e análise demonstradas são atemporais e aplicáveis a qualquer conjunto de dados similar.
