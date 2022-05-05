# Case previsão de série temporal de passageiros de voos entrantes na Austrália

# Introdução
Nesse case será analisado a série temporal de número de passageiros entrantes na Austrália, e para isso será utilizado uma base de dados de linhas aéreas de países do mundo todo. O objetivo final desse case será desenvolver um modelo de predição de 6 meses a frente para cada continente de origem.

Os dados estão disponíveis em
https://www.bitre.gov.au/publications/ongoing/international_airline_activity-time_series , no arquivo ‘Airline by
country of port data–passengers, freight and mail–2009 to current International Airline Activity Table1 2009 to
current ́ no formato .xlsx

Os dados estão disponíveis na aba `Data`.

|   Entrada   |   Descrição  |
|:---------------------------------------:|:------------:|
|   `Month`   |  Mês da observação  |
| `Scheduled Operator` |Linha aérea.   |
|`Country to/from`|País de Origem/Destino.|
|  `Passengers In`|Quantidade de passageiros entrantes na Australia.   |
| `Freight In` | Fretes entrantes na Australia, em toneladas. |
|   `Mail In`  | Correio entrante na Australia, em toneladas. |
|`Freight Out ` |Fretes saindo da Australia, em toneladas   |
|   `Mail Out`  |   Correio saindo da Australia, em toneladas  |

# Case
O case está disponível em: [Case série de passageiros entrantes](https://github.com/rodgdutra/case_passager_time_series/blob/main/case_time_series_pred.ipynb).

Caso não renderize, aqui está o link alternativo: [Case série de passageiros entrantes](https://nbviewer.org/github/rodgdutra/case_passager_time_series/blob/main/case_time_series_pred.ipynb).

# Objetivos
- A. Observar suas características relevantes, como comportamento sazonal, estacionariedade, presença de
mudanças de regime.
- B. Entender seu relacionamento com os outros valores disponíveis no dataset.
- C. Criar um modelo preditivo para o valor nos próximos 6 meses (i.e. usar os dados disponíveis até o tempo
t para prever t+1, t+2, ..., t+6).
- D. Transforme o país de origem em continente e faça a previsão por origem.

# Conclusões
## A. Observar suas características relevantes, como comportamento sazonal, estacionariedade, presença de mudanças de regime.

### `Estacionaridade`
Para essa análise foi realizado o teste de estacionaridade de sentido amplo analisando-se a série de passageiros entrantes vindo do continente asiático. Para isso foi analisado assim a média, segundo momento e variancia da série temporal em relação ao tempo. Além desse teste foi realizado também o teste de raíz unitária de dickey fuller.
O resultado dos testes concluiu que a série temporal de passageiros entrantes é não estacionária.

### `Sasonalidade e tendência de subida ou descida`
Ao se analisar o comportamento da série temporal e sua autocorrelação foi detectado que a série temporal tem tendencia de subida e sasonalidade anual e trimestral.

### `Mudança de regime`
Analizando-se a média do número de passageiros vindos de todos os continentes em relação aos anos foi detectado que a partir do ano de 2020 o número de passageiros entrantes na Austrália caiu de forma expressiva, o que era esperado visto o impacto da pandemia do `covid-19` no mundo todo. Dessa forma, o modelo de previsão para prever corretamente deve ser robusto a essa mudança de regime.

## B. Entender seu relacionamento com os outros valores disponíveis no dataset.
Ao analisar a correlação entre as outras features presentes no dataset foi possível notar uma correlação alta entre a série temporal que queremos prever (`Passengers In`) e as colunas de `Mail In` e `Passengers Out`. Essa análise foi feita não considerando nenhum atraso entre a série temporal que queremos prever e as outras colunas e também considerando um atraso de 6 meses entre as séries. Ou seja a série de  quando a série `Passengers In` está em $ t[6]$ as séries temporais das outras colunas estariam em $ t[1]$. A análise com os as outras séries temporais atrasadas em relação a série principal para que podermos avaliar se há alguma relação que o nosso modelo de previsão poderá utilizar, visto que o modelo preverá 6 meses a frente de uma vez só, não podendo assim levar em conta a relação da série principal e das covariantes em tempo real, somente da série temporal principal e das outras colunas atrasadas de 6 meses.


## C. & D. Criar um modelo preditivo para o valor nos próximos 6 meses (i.e. usar os dados disponíveis até o tempo). Transforme o país de origem em continente e faça a previsão por origem.

Para criar um modelo de previsão de multiplos passos foi utilizado os métodos de previsão `ARIMA`, `Light GBM`, `facebook Prophet` e `Regressão linear`.

- `ARIMA`: Modelo amplamente utilizado na literatura para vários casos, altamente performático e bastante utilizado no mercado.
- `Light GBM`: Modelo baseado em árvores de decisão e gradient boosting, é dito estar no estado da arte na modelagem de dados tabulares para vários tipos de tarefas e altamente performático.

- `Prophet`: Modelo de previsão de séries temporais desenvolvido pelo Facebook, amplamente utilizado e performático.
- `Regressão linear`: Modelo baseado em regressão linear de covariantes em relação a série temporal desejada.


### Melhor modelo
Após os testes em todos os continentes foi possível ver que os melhores modelos foram o `ARIMA` e o `LGBM regressor`. Sendo que, o modelo ARIMA performou melhor na Asia e na América do norte, nos resto dos continentes (Europa, América do sul, Oceania e Africa) o modelo `LGBM` performou melhor.

### Uso de covariantes
Quanto ao uso ou não de entradas exógenas, podemos afirmar que para o modelo `ARIMA` o uso de entradas exógenas não foi benéfico para o modelo, porém para o modelo `LGBM` na maioria das vezes o uso de entradas exógenas foi benéfico para o modelo.

### Melhor modelo para cada continente
Os melhores modelos para cada continente foram salvos na pasta `best_models` nesse repositório.