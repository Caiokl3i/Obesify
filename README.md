# Obesify — Classificação do Estado Nutricional

**Integrantes:** Ana Clara Silvestre, Caio Victor Santos e Matheus Camarotto.

## Problema
Estimar a classificação do estado nutricional, identificar fatores do perfil que merecem atenção e apresentar orientações educativas adequadas às respostas.

## Dataset
[Estimation of Obesity Levels Based on Eating Habits and Physical Condition — UCI](https://archive.ics.uci.edu/dataset/544/estimation+of+obesity+levels+based+on+eating+habits+and+physical+condition)

## Métricas
- F1-score
- Precisão (Precision)
- Recall
- Matriz de confusão

## Baseline
`DummyClassifier` (Scikit-learn).

## Como executar
1. Crie um ambiente virtual.
2. Instale as dependências: `pip install -r requirements.txt`.
3. Abra `notebooks/01_eda.ipynb` no Jupyter ou Google Colab.
4. Execute todas as células, de cima para baixo.

O notebook obtém o dataset diretamente da UCI com `ucimlrepo` (ID 544), portanto não é necessário manter uma cópia local do CSV.

## Decisões de pré-processamento
| Coluna(s) | Tipo | Decisão | Onde |
|---|---|---|---|
| Age | Numérica | Valores <= 0 ou > 120 viram ausentes; imputação pela mediana e escala | `limpar(df)` + Pipeline |
| Height, Weight | Numéricas | Valores <= 0 viram ausentes; imputação pela mediana e escala; manter valores plausíveis | `limpar(df)` + Pipeline |
| FCVC, NCP, CH2O, FAF, TUE | Numéricas/ordinais | Manter como numéricas nesta versão; mediana e escala | Pipeline |
| Gender, family_history_with_overweight, FAVC, SMOKE, SCC | Categóricas/binárias | Moda + One-Hot Encoding | Pipeline |
| CAEC, CALC, MTRANS | Categóricas nominais | Moda + One-Hot Encoding | Pipeline |
| NObeyesdad | Alvo | Separar como `y`; nunca usar como entrada | Fora do pré-processamento |

Duplicatas exatas são contabilizadas e removidas antes da divisão. A função `limpar(df)` faz somente operações linha a linha. Imputação, escala e codificação são aprendidas dentro do Pipeline, usando somente dados de treino. `OneHotEncoder(handle_unknown='ignore')` permite categorias não vistas durante o treino.

## EDA
O notebook responde às cinco perguntas exigidas:
1. Distribuição do alvo.
2. Dados ausentes.
3. Distribuição das variáveis.
4. Relação das variáveis com o alvo.
5. Redundância entre variáveis numéricas.

Cada análise inclui observação e decisão impressas ou documentadas no notebook. A tabela de frequências, estatísticas e correlações é gerada durante a execução; os valores efetivos devem ser registrados após executar o notebook.

## Validação
O notebook:
- separa treino/teste com `test_size=0.20`, `stratify=y` e `random_state=42`;
- mantém o teste reservado para a Entrega 3;
- verifica que o pré-processamento preserva o número de linhas e não produz NaN;
- compara `DummyClassifier(strategy='most_frequent')` e Regressão Logística por validação cruzada de 5 dobras, usando F1-macro.