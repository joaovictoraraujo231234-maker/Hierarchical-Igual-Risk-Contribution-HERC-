# Hierarchical Equal Risk Contribution (HERC) com Foco em Risco de Cauda

Este repositório documenta o desenvolvimento de uma infraestrutura quantitativa em Python para otimização e alocação de portfólios. O projeto utiliza técnicas de *Machine Learning* não-supervisionado (Clustering) e métricas de risco de cauda (CDaR e CVaR) para construir carteiras resilientes, focadas na sobrevivência do capital em cenários de estresse sistêmico e choques macroeconômicos.

## 1. O Problema da Otimização Tradicional
Otimizadores clássicos baseados em Média-Variância (Markowitz) frequentemente assumem distribuições normais de retornos e dependem da inversão da matriz de covariância. No mundo real, essa premissa resulta em grave instabilidade matemática (*overfitting*) e alocações hiper-concentradas que colapsam diante de "caudas gordas" (eventos extremos) e crises de liquidez. Este projeto rejeita abordagens superficiais de alocação, exigindo um processo robusto de modelagem *step-by-step* adequado aos padrões institucionais de *Equity Research*.

## 2. A Arquitetura HERC
Para mitigar a fragilidade topológica do mercado, este projeto implementa o algoritmo **Hierarchical Equal Risk Contribution (HERC)**, proposto originalmente por Thomas Raffinot. O modelo opera em duas frentes:

* **Topologia de Mercado (Machine Learning):** Aplicação do Método de Ligação de Ward (*Ward Linkage*) para agrupar hierarquicamente os ativos em um Dendrograma. O algoritmo identifica correlações implícitas e separa "clusters" econômicos naturais (ex: Renda Fixa atrelada à inflação, Utilities, Câmbio e Criptoativos).
* **Motor Híbrido de Risco de Cauda:** A alocação de capital ocorre de forma Top-Down (bisseção recursiva). Em vez da volatilidade tradicional, a função objetivo distribui o capital inversamente ao risco extremo, balanceado em **70% CDaR** (*Conditional Drawdown at Risk*) e **30% CVaR** (*Conditional Value at Risk*).

<img width="1384" height="684" alt="dendograma" src="https://github.com/user-attachments/assets/acb55fb8-7945-4ec1-a3c4-50a3411cbefd" />

## 3. Engenharia de Dados e Fricção de Mercado
Modelos teóricos colapsam na prática devido à má qualidade dos dados brutos (*Data Leakage*). Este código foi desenhado com salvaguardas quantitativas para lidar com anomalias de provedores gratuitos (como o Yahoo Finance) e a fricção do mundo real:

* **Filtro de Calendário e Ruído Quantitativo:** Tratamento da assimetria de pregões entre mercados globais (24/7) e locais (B3). Injeção de Epsilon ($10^{-8}$) para impedir a singularidade da matriz em dias de variação nula.
* **Winsorization e Curadoria de Qualidade:** Mecanismos de expurgo de *outliers* para neutralizar agrupamentos/desdobramentos não ajustados e *spikes* irreais nos dados de provedores, mantendo a integridade da variância.
* **Walk-Forward Analysis (Janelas Rolantes):** Backtest estruturado através de simulação progressiva (Jan/2020 a Ago/2025). O rebalanceamento trimestral ocorre estritamente com informações passadas, sem olhar para o futuro.
* **Custos de Transação (Turnover):** Desconto operacional de 0.20% a cada giro de portfólio, garantindo viabilidade executável.

<img width="1023" height="500" alt="newplot (2)" src="https://github.com/user-attachments/assets/bee92cdb-6192-4643-83cb-52b759b5d00d" />

## 4. Validação Institucional (Holdout Set)
Para validar cientificamente a tese de alocação, o último ano da série temporal (Set/2025 a Set/2026) foi blindado em um *Holdout Set* (dados completamente cegos para o algoritmo). 

A aplicação dos pesos definitivos gerados no período de treinamento sobre este ano cego resultou em uma proteção de capital excepcional, atestada pelas métricas de Thomas Raffinot:

* **Max Drawdown (MDD):** 1.85% (Controle quase absoluto contra perdas permanentes).
* **Volatilidade Anual:** 4.15%.
* **Concentração (SSPW):** Níveis sustentáveis que provam a harmonia topológica entre classes de ativos de diferentes betas.

<img width="241" height="122" alt="WhatsApp Image 2026-09-08 at 12 08 50" src="https://github.com/user-attachments/assets/a87cb613-6ce0-4d2c-95b1-ed8bac0e5676" />

<img width="1023" height="500" alt="newplot" src="https://github.com/user-attachments/assets/c7b0b5b5-0756-43b1-88c1-f47038061734" />

## 5. Stack Tecnológico
Infraestrutura desenvolvida inteiramente em Python:
* `pandas` e `numpy`: Limpeza de dados, vetorização e álgebra linear.
* `scipy.cluster.hierarchy`: Modelagem hierárquica e cálculo da matriz de ligação de Ward.
* `yfinance`: Integração via API para extração do histórico de cotações.
* `plotly` e `seaborn`: Visualização espacial, mapas de calor interativos e gráficos de rosca dinâmicos.

## 6. Como Executar o Modelo
1. Clone este repositório: `git clone https://github.com/joaovictoraraujo231234-maker/Hierarchical-Igual-Risk-Contribution-HERC-`
2. Instale as dependências: `pip install -r requirements.txt`
3. Abra o arquivo `.ipynb` no Jupyter Notebook ou Google Colab.
4. O Bloco 1 permite a customização completa do universo de ativos e o reajuste dos pesos das funções de risco de cauda (CDaR/CVaR). Execute os blocos sequencialmente.

## 7. Referências Bibliográficas e Framework Teórico
O modelo integra fundamentos de macroeconomia e *valuation* quantitativo baseados nas seguintes obras:
* LÓPEZ DE PRADO, M. *Advances in Financial Machine Learning*. John Wiley & Sons, 2018.
* RAFFINOT, T. *Hierarchical Clustering-Based Asset Allocation*. The Journal of Portfolio Management, 2017.
* ASSAF NETO, A. *Mercado Financeiro*. Atlas.
