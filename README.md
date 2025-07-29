# Detecção de Anomalias em Transações de Cartão de Crédito com Autoencoder Variacional (VAE)

##  Visão Geral do Projeto

Este projeto demonstra a aplicação de um **Autoencoder Variacional (VAE)** para a detecção de fraudes em transações de cartão de crédito. O problema é abordado como uma tarefa de **detecção de anomalias não supervisionada**, uma abordagem ideal para datasets com forte desbalanceamento de classes, onde as fraudes representam uma porcentagem mínima do total de transações.

O VAE é treinado exclusivamente com transações legítimas para aprender os padrões de normalidade. Posteriormente, ao avaliar novas transações, aquelas que apresentarem um alto **erro de reconstrução** são classificadas como anômalas, ou seja, potenciais fraudes.

## Dataset

O conjunto de dados utilizado é o [Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud), disponível no Kaggle.

- **Total de Transações:** 284.807
- **Transações Fraudulentas:** 492 (aproximadamente 0,17% do total)
- **Características:** As features `V1` a `V28` são componentes principais (PCA) para anonimização dos dados. As features `Time` e `Amount` são as únicas não anonimizadas. O dataset não contém valores nulos.

## Metodologia

1.  **Pré-processamento:**
    - As colunas `Time` e `Amount` são normalizadas utilizando `StandardScaler` para que tenham média 0 e desvio padrão 1, evitando que suas escalas distintas influenciem o modelo.
    - O dataset é dividido em transações normais e fraudulentas. O modelo VAE é treinado apenas com os dados normais.

2.  **Modelo (Autoencoder Variacional - VAE):**
    - **Encoder:** Uma rede neural que comprime os dados de entrada em um espaço latente de menor dimensão (16 dimensões neste projeto).
    - **Camada de Amostragem (Sampling):** Gera um vetor latente a partir da média (`z_mean`) e do log da variância (`z_log_var`) aprendidos pelo encoder, adicionando um componente probabilístico.
    - **Decoder:** Uma rede neural que tenta reconstruir os dados originais a partir do vetor latente.
    - O modelo foi implementado em Keras e adaptado do [exemplo oficial da documentação do Keras](https://keras.io/examples/generative/vae/).

3.  **Treinamento:**
    - O VAE é treinado para minimizar uma função de perda composta pelo **erro de reconstrução** (Mean Squared Error) e pela **divergência KL (Kullback-Leibler)**, que regulariza o espaço latente.
    - Uma técnica de **KL Annealing** é utilizada: o peso da divergência KL na função de perda aumenta gradualmente ao longo das épocas iniciais. Isso permite que o modelo foque primeiro em aprender a reconstruir os dados antes de impor a estrutura probabilística ao espaço latente.

4.  **Detecção de Anomalias:**
    - Após o treinamento, o modelo calcula o erro de reconstrução para todas as transações (normais e fraudulentas).
    - Um **limiar (threshold)** é definido para classificar as anomalias. Transações com erro de reconstrução acima desse limiar são consideradas fraudes.
    - O limiar foi otimizado manualmente para melhorar o equilíbrio entre precisão e recall, resultando em um F1-Score final de 87% (macro avg).

## Resultados

O modelo demonstrou ser eficaz na distinção entre transações normais e fraudulentas.

-   **Métricas de Avaliação:** A performance foi avaliada com o `classification_report` e a `confusion_matrix` do Scikit-learn.
-   **Curva Precision-Recall:** O gráfico da curva Precision-Recall vs. Limiar foi utilizado para visualizar o trade-off e ajudar na escolha de um limiar otimizado.
-   **Visualização com PCA:** Uma Análise de Componentes Principais (PCA) foi aplicada ao espaço latente do VAE para visualização em 2D. O gráfico resultante mostra uma separação clara entre os clusters de transações normais e fraudulentas, confirmando que o modelo aprendeu uma representação interna útil para a discriminação das classes.

## Como Executar o Projeto

1.  **Clone o repositório:**
    ```bash
    git clone https://github.com/R1chardJr/VAE-Anomaly-detection-credit-card.git
    cd VAE-Anomaly-detection-credit-card
    ```
    
2.  **Execute o Notebook:**
    Abra e execute o arquivo `VAE_anomaly_detection.ipynb` em um ambiente Jupyter.

