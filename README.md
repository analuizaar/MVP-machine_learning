# MVP: Machine Learning & Analytics
**Autora:** Ana Luiza Abrao Roriz S. de Carvalho  
**Data:** junho/2026  
**Curso:** Pós-graduação em Data Science & Analytics — PUC-Rio

---

## Objetivo

Segmentar debêntures brasileiras em grupos de risco utilizando algoritmos de clusterização não supervisionada, com o objetivo de apoiar a recomendação de produtos de renda fixa de acordo com o perfil de suitability dos clientes (conservador, moderado e arrojado).

## Problema

Aprendizado não supervisionado — clusterização de dados tabulares. Não há rótulos; os grupos são descobertos a partir das características de risco dos títulos.

## Dados

- **Fonte:** Anbima Data (taxas e características das debêntures) + Bloomberg (rating, setor, garantias e séries históricas de YTM)
- **Data de referência:** 24 de abril de 2026
- **Universo:** 1.184 debêntures após limpeza
- **Disponibilidade:** dataset carregado em repositório público no GitHub

## Features do modelo

| Feature | Descrição |
|---|---|
| `duration_anos` | Prazo médio ponderado dos fluxos de caixa — medida de risco de mercado |
| `z_spread` | Spread sobre a curva soberana equivalente — medida de risco de crédito |
| `vol_ytm` | Desvio padrão dos retornos diários do YTM em janela de 252 dias úteis — medida de volatilidade |

## Metodologia

1. Carga e limpeza dos dados (Anbima + Bloomberg)
2. Cálculo da volatilidade via série histórica de YTM
3. EDA com análise de distribuições, outliers e correlações entre as features
4. Divisão treino/teste (80/20)
5. Pipeline de pré-processamento: `SimpleImputer` + `RobustScaler`
6. Seleção do número de clusters via Elbow Method e Silhouette Score → k=3
7. Comparação de algoritmos: K-Means (baseline), Hierarchical e GMM
8. Interpretação dos clusters com variáveis categóricas (rating, setor, garantia, indexador)
9. Visualização via PCA 2D
10. Análise de fronteira com Silhouette individual

## Resultados

| Algoritmo | Silhouette (treino) | Silhouette (teste) |
|---|---|---|
| K-Means | 0.665 | 0.517 |
| Hierarchical | 0.629 | 0.507 |
| GMM | 0.414 | 0.481 |

**K-Means** foi selecionado como modelo final. Os três clusters identificados:

- **Cluster 0 — Perfil Padrão** (864 títulos): debêntures AAA/AA+ do setor de Utilities, spread baixo, volatilidade controlada
- **Cluster 2 — Alto Spread** (71 títulos): emissores com ratings mais baixos (BBB-, BB+), maior presença de garantias reais, alto risco de crédito
- **Cluster 1 — Alta Volatilidade** (12 títulos): títulos com comportamento atípico, duration curto e volatilidade extrema

## Ambiente

- Python 3 — Google Colab
- Bibliotecas: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`
- Seed: 42

## Limitações

- Base cobre um único dia de referência — não avalia estabilidade temporal dos clusters
- Features não capturam liquidez, convexidade ou estrutura de pagamentos
- Silhouette mede coesão geométrica, não relevância econômica
- Mais de 90% dos títulos concentrados em um único cluster, sugerindo base homogênea
