# Tech Challenge - NPS preditivo em e-commerce

Projeto desenvolvido para a Fase 1 da Pós-Tech em Data Analytics da FIAP.

O trabalho analisa quais acontecimentos da jornada de compra estão mais associados à satisfação e avalia, como etapa opcional, se um modelo de classificação consegue priorizar clientes com maior risco de apresentar nota abaixo de 7.

## Pergunta de negócio

Quais fatores operacionais estão mais associados à satisfação e onde a empresa deveria concentrar seus esforços?

Na parte preditiva, a pergunta é complementar: usando as informações disponíveis antes da pesquisa de satisfação, é possível identificar clientes com maior risco de apresentar nota abaixo de 7?

## Entendimento do negócio

O problema é que a empresa conhece a nota de satisfação somente depois que a jornada termina. A análise busca reconhecer sinais de uma experiência ruim durante a operação, quando ainda pode existir tempo para priorizar o caso e corrigir parte do problema.

O NPS ajuda o e-commerce a acompanhar a percepção dos clientes e a localizar pontos da jornada que merecem atenção. Logística e atendimento podem usar os resultados para organizar ações preventivas. Produto, pricing e a área de experiência do cliente também podem acompanhar se mudanças na operação melhoram a satisfação.

Uma experiência ruim pode reduzir a intenção de recompra e aumentar relatos negativos para outras pessoas. Se o problema se repete, a empresa também pode perder preferência frente aos concorrentes. A base deste projeto não permite medir diretamente esses efeitos, portanto eles são tratados como hipóteses de negócio que precisam de acompanhamento.

Alguns indicadores podem complementar a análise: recompra e retenção, cancelamentos, devoluções, prazo de entrega, entregas no prazo, contatos repetidos, tempo de resolução, CSAT, reclamações, referências externas de NPS e participação de mercado.

## Base de dados

A base possui 2.500 registros e 19 variáveis relacionadas ao cliente, pedido, logística, atendimento e satisfação.

Principais verificações:

- não há valores ausentes;
- não há `customer_id` ou `order_id` duplicados;
- cada cliente aparece em um único pedido;
- existem 35 registros com desconto maior que o valor do pedido, tratados como possível inconsistência e mantidos na análise principal.

O campo `nps_score` representa uma nota individual de satisfação, de 0 a 10, coletada depois da experiência. Para a modelagem, foi adotada a seguinte regra operacional:

- nota abaixo de 7: detrator operacional;
- nota igual ou superior a 7: não detrator operacional.

Essa classificação foi criada para o projeto e não representa o cálculo do NPS agregado da empresa.

## Principais resultados da análise

Os sinais mais fortes de insatisfação aparecem durante a operação, principalmente em atraso, reclamações e atendimento.

| Indicador | Resultado |
|---|---:|
| Nota média de satisfação | 4,38 |
| Registros com nota abaixo de 7 | 84,36% |
| Correlação entre atraso e nota | -0,597 |
| Correlação entre reclamações e nota | -0,497 |
| Correlação entre contatos e nota | -0,351 |
| Correlação entre tempo de resolução e nota | -0,191 |

Dois pontos merecem atenção:

- sem atraso, a nota média é 6,86; com um dia de atraso, cai para 5,55;
- com uma reclamação, a nota média é 7,77; com duas, cai para 6,05.

Esses resultados mostram associação, não causa e efeito.

## Modelo preditivo

Foi utilizada uma Regressão Logística com divisão estratificada entre treino e teste. A validação cruzada foi realizada somente no conjunto de treinamento, mantendo o teste reservado para a avaliação final.

Variáveis conhecidas depois da experiência, como recompra em 30 dias e CSAT interno, não foram usadas como entrada do modelo.

| Etapa | ROC AUC no teste |
|---|---:|
| Cliente e pedido | 0,507 |
| Com logística | 0,798 |
| Com logística e atendimento | 0,885 |

Resultados do modelo completo:

| Métrica | Resultado |
|---|---:|
| ROC AUC | 0,885 |
| Acurácia balanceada | 0,786 |
| Acurácia | 0,806 |
| Recall dos detratores | 81,6% |
| Precisão dos detratores | 94,7% |

A Regressão Logística foi mantida como modelo principal porque apresentou melhor resultado médio na validação feita no treinamento e é mais simples de explicar. O Random Forest foi usado apenas como comparação.

Na validação cruzada do treinamento, a Regressão Logística obteve ROC AUC de 0,888 ± 0,013 e o Random Forest obteve 0,878 ± 0,015. O resultado de teste do Random Forest foi 0,894, mas não foi usado para escolher o modelo.

Os testes de sensibilidade mantiveram a conclusão geral: ROC AUC de 0,870 após retirar os 35 casos de possível inconsistência financeira e 0,873 com uma regra alternativa para a variável-alvo.

O modelo deve apoiar a ordenação e a priorização dos casos. A decisão final continua com a operação.

## Recomendações

- identificar e comunicar atrasos o quanto antes;
- criar uma regra de escalonamento na segunda reclamação;
- acompanhar contatos repetidos e tempo de resolução;
- testar as intervenções de forma controlada antes de afirmar que melhoram a satisfação.

## Limitações

- a base não informa datas e horários dos eventos;
- o período e a forma de seleção dos registros não foram informados;
- há somente 2.500 registros e um pedido por cliente;
- as relações encontradas não comprovam causalidade;
- o resultado foi avaliado de forma retrospectiva, sem validação em períodos futuros;
- a pontuação do modelo não representa uma probabilidade absoluta.

## Estrutura do repositório

```text
tech-challenge/
├── Banco_de_dados/
│   └── Base_NPS.csv
├── Modelo/
│   ├── Eda_negocio_FINAL.ipynb
│   └── Modelo_opcional_FINAL.ipynb
├── Reports/
│   └── Apresentacao_Tech_Challenge_FINAL.pptx
├── README.md
├── requirements.txt
└── .gitignore
```

## Como executar

1. Clone o repositório.
2. Crie e ative um ambiente virtual com Python 3.12.
3. Instale as dependências:

```bash
pip install -r requirements.txt
```

4. Execute primeiro `Modelo/Eda_negocio_FINAL.ipynb`.
5. Depois execute `Modelo/Modelo_opcional_FINAL.ipynb`.

Os notebooks, executados a partir da pasta `Modelo`, usam o caminho relativo `../Banco_de_dados/Base_NPS.csv`. Por isso, a estrutura de pastas deve ser mantida.

## Autor

Gabriel Acerbi de Alcântara - RM 378627

