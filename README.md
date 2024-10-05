# Previsão de cliques em anúncios online
<img src="./img/adddd.jpg" alt="Texto Alternativo" width="300"/>

## 1. Problema de Negócio
### 1.1 Contexto
O cenário da publicidade digital é muito competitivo, e empresas gastam milhões para atrair a atenção dos usuários. No entanto, apenas uma pequena parte dessas visualizações, resulta em cliques o que torna a otimização das campanhas crucial.
Portanto o problema que buscamos resolver é: **Como prever se um usuário vai clicar em um anúncio online?**

### 1.2 Objetivo
O objetivo é construir um modelo preditivo que ajude anunciantes a direcionarem seus anúncios de forma eficaz, aumentando a taxa de conversão (número de cliques).
O modelo será capaz de prever a possibilidade de cliques com base em informações demográficas do usuário, comportamento de navegação e contexto do anúncio.

## 2. Sobre os dados
Os dados foram coletados através da plataforma Kaggle.

**Dicionário dos dados**
|nome | Descrição
|--- | ---
|id | Identificador único do usuário
|full_name| Nome do usuário formatado como "UserX" para manter anonimato
|age | Idade do usuário (entre 18 a 64 anos)
|gender | O gênero do usuário (categorizado como Male, Female, ou Non-Binary)
|device_type | O tipo de dispositivo usado pelo usuário para acessar o ad (Mobile, Desktop, Tablet).
|ad_position | A posição do ad na pagina web (Top, Side, Bottom).
|browsing_history | A atividade de navegação do usuário antes de ver o ad (Shopping, News, Entertainment, Education, Social Media).
|time_of_day | O momento do dia em que o usuário viu o ad (Morning, Afternoon, Evening, Night).
|click | O rotulo que indica que se o usuário clicou no anúncio (1 para click, 0 para não click).

## 3. Análise exploratória

### 3.1 Feature engineering
Como a variável "Age" tem uma ampla faixa etária, criei outra variável chamada “Faixa etária” para ter uma visualização melhor dos dados e da relação entre idade e cliques.
Também excluí do dataset a coluna 'id' que se trata apenas de um identificador único.

### 3.2 Análise univariada
Os dados apresentam uma quantidade significativa de dados faltantes, com taxas entre 20% e 47%, e uma média de 33%. Isso pode indicar uma maior preocupação dos usuários com sua privacidade.

### 3.3 Análise bivariada
Observamos que adultos (entre 30 e 60 anos) têm mais probabilidade de clicar no anúncio. Entretanto, isso pode ser resultado da maior presença dessa classe no dataset. Proporcionalmente, jovens (menores de 30 anos) têm uma probabilidade de clique de 66%, enquanto adultos têm 64%.

## 4. Modelagem
Como dito anteriormente, temos muitos dados faltantes em nosso dataset. Fazer algum tipo de imputação seja da media, mediana, moda ou até mesmo algo mais avançado, poderia enviesar nossos dados ou até mesmo tirar uma possível característica do comportamento dos usuários, que é manter sua privacidade.
Portanto, precisamos de um algoritmo robusto que consiga lidar bem com dados faltantes. Por isso escolhi testar o XGboost e o LightGBM, já que ambos tem como característica lidar bem com dados faltantes.

## 5. Avaliação dos modelos
### 5.1 Desempenho dos modelos

## Classification Reports

### XGBoost

|              | precision | recall | f1-score | support |
|:-------------|:---------:|:------:|:--------:|--------:|
| 0            |    0.75   |  0.45  |   0.56   |     707 |
| 1            |    0.75   |  **0.92**  |   0.83   |    1293 |
| **accuracy** |           |        |          |    0.75 |
| **macro avg**|    0.75   |  0.68  |   0.69   |    2000 |
| **weighted avg** | 0.75   |  0.75  |   0.73   |    2000 |


<img src="./img/xgboost.png" alt="Texto Alternativo" width="450"/>


### LightGBM

|              | precision | recall | f1-score | support |
|:-------------|:---------:|:------:|:--------:|--------:|
| 0            |    0.73   |  0.49  |   0.59   |     707 |
| 1            |    0.76   |  **0.90** |   0.83   |    1293 |
| **accuracy** |           |        |          |    0.76 |
| **macro avg**|    0.75   |  0.70  |   0.71   |    2000 |
| **weighted avg** | 0.75   |  0.76  |   0.74   |    2000 |

<img src="./img/lightgbm.png" alt="Texto Alternativo" width="450"/>


### 5.2 Escolha do modelo
Olhando a acurácia dos dois modelos, conseguimos ver que o LightGBM teve a maior acurácia(76%) comparado ao XGboost(75%).

Mas o objetivo do nosso modelo é prever se o usuário clicará no anúncio, ou seja, é "garantir" que não se perca cliques de usuários interessados.
Portanto, ter a menor quantidade de falsos negativos é crucial, o que se traduz em maximizar o recall.

*Sendo assim, para o nosso objetivo, o XGboost performou um pouco melhor que o LightGBM, conseguindo acertar **92%** dos usuários que clicaram no anúncio.*


### 5.3 Impacto no Negócio (Assumptions e Métricas)

**Assumptions (Suposições):**

- **Base de Usuários:** 10.000 usuários visualizam o anúncio.
- **CTR Sem o Modelo:** Taxa de cliques de 3% sem o modelo, resultando em **300 cliques**.
- **Conversão Sem o Modelo:** 10% de conversão para os cliques, resultando em **30 conversões**.
- **Receita por Conversão:** R$ 50,00 por conversão, gerando uma receita total de **R$ 1.500,00.**
- **Custo por Clique (CPC):** R$ 2,00 por clique, totalizando **R$ 600,00** em custos de campanha.
- **CTR Com o Modelo:** Aumenta para 4,5%, resultando em **450 cliques.**
- **Conversão Com o Modelo:** Mantida em 10%, resultando em **45 conversões**.
- **Custo Com o Modelo:** R$ 900,00 (450 cliques * R$ 2,00).

**Comparativo de Resultados:**

| Métrica       | Sem o Modelo  | Com o Modelo | Diferença         |
|---------------|---------------|--------------|-------------------|
| Cliques       | 300           | 450          | +150 (50% de aumento) |
| Conversões    | 30            | 45           | +15 (50% de aumento)  |
| Receita Total | R$ 1.500,00   | R$ 2.250,00  | +R$ 750,00 (50% de aumento) |
| Custo Total   | R$ 600,00     | R$ 900,00    | +R$ 300,00         |
| **ROI**       | **150%**      | **150%**     | **-**              |

Com base nessas métricas fictícias, podemos concluir que o modelo aumenta significativamente a quantidade de cliques e conversões, resultando em uma receita 50% maior com um aumento moderado de custos, mantendo o **ROI** consistente. Isso demonstra a eficiência do modelo em melhorar a performance de campanhas de anúncios.

## 6. Conclusão

Conseguimos implementar nosso modelo e alcançar o objetivo de melhorar a performance dos anúncios, prevendo corretamente 92% dos cliques. O impacto do modelo no negócio é claro, aumentando o número de conversões e otimizando o direcionamento de anúncios para usuários mais propensos a interagir, o que gera mais receita para a empresa.

Futuramente, podemos explorar estratégias para lidar com os dados faltantes, como o uso de privacidade como uma característica para ajustar campanhas de marketing.
