### Preparando os Dados

```python
#Verificando se há valores ausentes
missing_values = df.isnull().sum()

#Convertendo as colunas que são de data para a data no formato correto
df['Order Date'] = pd.to_datetime(df['Order Date'], format="%m/%d/%Y")
df['Ship Date'] = pd.to_datetime(df['Ship Date'], format="%m/%d/%Y")

#Criando as colunas derivadas de data em inglês (estuda inglês mano!)
df['Year'] = df['Order Date'].dt.year
df['Month'] = df['Order Date'].dt.month
df['Month Name'] = df['Order Date'].dt.strftime('%B')
df['Weekday'] = df['Order Date'].dt.day_name()
df['Profit per Sale'] = df['Profit'] / df['Quantity']
df['Sales per Unit'] = df['Sales'] / df['Quantity']

#Resumo final dos dados
prepared_info = {
    "Dimensão do dataset": df.shape,
    "Valores ausentes por coluna": missing_values[missing_values > 0],
    "Tipos de dados (amostragem)": df.dtypes.head(10)
}

prepared_info
```

![image](https://github.com/user-attachments/assets/18ceaa44-4197-4de3-a7a4-2f2ab8e3b9d2)

### Visão Geraç

```python
#Estilo do gráfico
sns.set(style="whitegrid")
plt.rcParams["figure.figsize"] = (12, 6)

#Criando a Receita e lucro por ano
year_summary = df.groupby("Year")[["Sales", "Profit"]].sum().reset_index()

#Receita e Lucro ao longo dos anos
plt.plot(year_summary["Year"], year_summary["Sales"], marker='o', label='Receita (Sales)')
plt.plot(year_summary["Year"], year_summary["Profit"], marker='s', label='Lucro (Profit)')
plt.title("Receita e Lucro ao Longo dos Anos")
plt.xlabel("Ano")
plt.ylabel("Valor em USD")
plt.legend()
plt.grid(True)
plt.show()
```

![image](https://github.com/user-attachments/assets/b3a2eadb-28fd-4583-90bd-64e9cae98dee)

### Categorias, Regiões e Clientes

```python
#Criando o Top 10 produtos mais vendidos
top_products = df.groupby("Product Name")["Quantity"].sum().sort_values(ascending=False).head(10)

#Top produtos
top_products.plot(kind="barh", color="skyblue")
plt.title("Top 10 Produtos Mais Vendidos")
plt.xlabel("Quantidade Vendida")
plt.gca().invert_yaxis()
plt.show()

#Criando a Receita por região
region_sales = df.groupby("Region")["Sales"].sum().sort_values(ascending=False)

#Receita por Região
region_sales.plot(kind="bar", color="orange")
plt.title("Receita por Região")
plt.ylabel("Receita em USD")
plt.xticks(rotation=45)
plt.show()
```

![image](https://github.com/user-attachments/assets/2bceb688-1d71-4fad-a905-d028ad02c8bf)

![image](https://github.com/user-attachments/assets/b8680684-0d2d-48e3-972d-953fa69f963c)

### Sazonalidade

```python
#Criando a Receita por mês agregado
monthly_sales = df.groupby("Month Name")["Sales"].sum()
month_order = ['January', 'February', 'March', 'April', 'May', 'June',
               'July', 'August', 'September', 'October', 'November', 'December']
monthly_sales = monthly_sales.loc[month_order]

#Receita por mês
monthly_sales.plot(kind="bar", color="green")
plt.title("Receita Total por Mês (Agregado)")
plt.ylabel("Receita em USD")
plt.xticks(rotation=45)
plt.show()
```

![image](https://github.com/user-attachments/assets/ee92b684-a675-4132-8ad7-456cf2b4d1ff)

### Mapa de Calor por Dia da Semana

```python
#Fazendo o agrupamento por dia da semana e categoria
heatmap_data = df.groupby(["Weekday", "Category"])["Sales"].sum().unstack().fillna(0)

#Reordenar os dias da semana em inglês
dias_semana = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]
heatmap_data = heatmap_data.reindex(dias_semana)

#Mapa de calor
sns.heatmap(heatmap_data, annot=True, fmt=".0f", cmap="YlGnBu")
plt.title("Mapa de Calor: Vendas por Dia da Semana e Categoria")
plt.ylabel("Dia da Semana")
plt.xlabel("Categoria")
plt.show()
```

![image](https://github.com/user-attachments/assets/2a25dce5-e1ba-4155-90c2-22d91072b860)

### RFM

#### Segmentação RFM

```python
import datetime as dt

#Usando a última data do dataset como minha referência
ref_date = df['Order Date'].max() + pd.Timedelta(days=1)

#Agrupando por id dos clientes
rfm = df.groupby("Customer ID").agg({
    "Order Date": lambda x: (ref_date - x.max()).days,  # Recência
    "Order ID": "nunique",                              # Frequência
    "Sales": "sum"                                      # Valor Monetário
}).reset_index()

#Renomeando as colunas
rfm.columns = ["Customer ID", "Recency", "Frequency", "Monetary"]

#Adicionando o score (1 a 4) por quartil
rfm['R_Score'] = pd.qcut(rfm['Recency'], 4, labels=[4, 3, 2, 1])
rfm['F_Score'] = pd.qcut(rfm['Frequency'].rank(method='first'), 4, labels=[1, 2, 3, 4])
rfm['M_Score'] = pd.qcut(rfm['Monetary'], 4, labels=[1, 2, 3, 4])

#Combinando os scores em um único segmento
rfm['RFM_Segment'] = rfm['R_Score'].astype(str) + rfm['F_Score'].astype(str) + rfm['M_Score'].astype(str)
rfm['RFM_Score'] = rfm[['R_Score','F_Score','M_Score']].astype(int).sum(axis=1)


def segmentar_cliente(score):
    if score >= 9:
        return 'VIP'
    elif score >= 6:
        return 'Potencial'
    else:
        return 'Inativo'

rfm['Segmento'] = rfm['RFM_Score'].apply(segmentar_cliente)

rfm.head()
```

![image](https://github.com/user-attachments/assets/0fbb555d-9d61-4276-9587-c181ea812723)


####  Distribuição de scores R F M

```python
fig, axs = plt.subplots(1, 3, figsize=(18, 5))
sns.histplot(rfm['Recency'], bins=20, kde=True, ax=axs[0])
axs[0].set_title('Distribuição da Recência')

sns.histplot(rfm['Frequency'], bins=20, kde=True, ax=axs[1])
axs[1].set_title('Distribuição da Frequência')

sns.histplot(rfm['Monetary'], bins=20, kde=True, ax=axs[2])
axs[2].set_title('Distribuição do Valor Monetário')

plt.tight_layout()
plt.show()
```

![image](https://github.com/user-attachments/assets/981bed1b-bf7e-4774-b7fa-e6db86c7aa64)

#### Tabela com os principais clientes (Top 10)

```python
top_clientes = rfm.sort_values(by="Monetary", ascending=False).head(10)
print(top_clientes[['Customer ID', 'Recency', 'Frequency', 'Monetary', 'Segmento']])
```

![image](https://github.com/user-attachments/assets/bf6fe984-0b49-4667-943e-2fa5e40f02f9)

### Sazonalidade e Séries Temporais

#### Preparando os Dados

```python
#Agrupando pela data (vendas diárias)
sales_daily = df.groupby("Order Date")["Sales"].sum().asfreq('D').fillna(0)

#Série temporal
sales_daily.plot(figsize=(15, 5), title="Vendas Diárias")
plt.xlabel("Data")
plt.ylabel("Receita (USD)")
plt.show()
```

![image](https://github.com/user-attachments/assets/027420be-fdb5-4ae6-a72f-4a77b2b1ea86)

#### Decomposição

```python
from statsmodels.tsa.seasonal import STL

#Aplicando o STL (Seasonal-Trend-Loess)
stl = STL(sales_daily, seasonal=13)
result = stl.fit()

#Plotando os componentes
result.plot()
plt.suptitle("Decomposição STL da Série de Vendas Diárias", fontsize=14)
plt.show()
```

![image](https://github.com/user-attachments/assets/ff4a21ea-9808-4af7-b461-a9a7cf21ebcf)

### Autocorrelação

```python
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf

#Criando a autocorrelação
plot_acf(sales_daily, lags=30)
plt.title("Autocorrelação (ACF)")
plt.show()

#Criando a autocorrelação parcial
plot_pacf(sales_daily, lags=30)
plt.title("Autocorrelação Parcial (PACF)")
plt.show()
```

![image](https://github.com/user-attachments/assets/0a2fed7e-b80a-4d11-9ee2-811a52bde689)

![image](https://github.com/user-attachments/assets/1b931892-db49-44dc-aadb-734022d17796)

### Previsão de Vendas Futuras

#### Preparando os Dados para receber o modelo SARIMA

```python
from statsmodels.tsa.statespace.sarimax import SARIMAX
from sklearn.metrics import mean_squared_error
import numpy as np

#Reutilizando a série de vendas diárias
sales_daily = df.groupby("Order Date")["Sales"].sum().asfreq("D").fillna(0)

#Dividindo em treino/teste (últimos 90 dias como teste)
train = sales_daily[:-90]
test = sales_daily[-90:]
```

#### Treinando o Modelo

```python
#Treinando modelo SARIMA 
model = SARIMAX(train, order=(1, 1, 1), seasonal_order=(1, 1, 1, 7))
model_fit = model.fit(disp=False)

#Prevendo no período de teste
pred = model_fit.predict(start=test.index[0], end=test.index[-1], dynamic=False)

#Avaliando os erros (RMSE)
rmse = np.sqrt(mean_squared_error(test, pred))
print(f"RMSE no período de teste: {rmse:.2f}")
```

#### Visualizando a previsão

```python
plt.figure(figsize=(15, 5))
plt.plot(train.index, train, label="Treino")
plt.plot(test.index, test, label="Real (Teste)", color="black")
plt.plot(pred.index, pred, label="Previsto (SARIMA)", color="red")
plt.title("Previsão SARIMA vs. Dados Reais")
plt.legend()
plt.show()
```

![image](https://github.com/user-attachments/assets/e08960e2-b970-4e6a-b6ec-ceb51a643105)

#### Previsão com base nos dias escolhidos nas varáveis

```python
#Número de dias para prever (modifique se quiser meu caro amigo(a))
dias_3m = 90
dias_6m = 180
dias_12m = 365

#Previsão para os próximos 365 dias (com intervalo)
future_forecast = model_fit.get_forecast(steps=dias_12m)
forecast_mean = future_forecast.predicted_mean
conf_int = future_forecast.conf_int()


plt.figure(figsize=(15, 5))
plt.plot(sales_daily, label="Histórico")
plt.plot(forecast_mean, label="Previsão SARIMA", color="green")
plt.fill_between(conf_int.index, conf_int.iloc[:, 0], conf_int.iloc[:, 1], color='green', alpha=0.2)
plt.title("Previsão para os Próximos 12 Meses")
plt.xlabel("Data")
plt.ylabel("Vendas Previstas")
plt.legend()
plt.show()
```

![image](https://github.com/user-attachments/assets/86ef1b54-b92a-4a51-831e-4e780fbcc91a)


### Análise de Desempenho dos Vendedores

#### Métricas por Vendedor

```python
vendas_segmento = df.groupby("Segment").agg({
    "Sales": "sum",
    "Profit": "sum",
    "Order ID": "nunique"
}).reset_index()

vendas_segmento["Ticket Médio"] = vendas_segmento["Sales"] / vendas_segmento["Order ID"]
vendas_segmento
```

![image](https://github.com/user-attachments/assets/5cb7c928-665c-4759-a025-2a2cbc974343)


#### Performance de Vendedores

```python
#Receita por Segmento
sns.barplot(data=vendas_segmento, x="Segment", y="Sales", palette="Blues_d")
plt.title("Total de Vendas por Segmento")
plt.ylabel("Receita (USD)")
plt.show()

#Lucro por Segmento
sns.barplot(data=vendas_segmento, x="Segment", y="Profit", palette="Greens_d")
plt.title("Lucro por Segmento")
plt.ylabel("Lucro (USD)")
plt.show()

#Ticket médio
sns.barplot(data=vendas_segmento, x="Segment", y="Ticket Médio", palette="Oranges_d")
plt.title("Ticket Médio por Segmento")
plt.ylabel("USD por Pedido")
plt.show()
```

![image](https://github.com/user-attachments/assets/6b81f41c-9484-4721-84e4-62611c671dac)

![image](https://github.com/user-attachments/assets/1a1f3125-a85c-4472-80ac-96b2e9ffc85f)

![image](https://github.com/user-attachments/assets/d838fbb2-692a-4ccf-ae4a-5d780b84306d)

### Análise por Região e Categoria

```python
#Ranking de receita por região
region_sales = df.groupby("Region")["Sales"].sum().sort_values(ascending=False)


region_sales.plot(kind='barh', color='steelblue')
plt.title("Receita por Região")
plt.xlabel("Vendas (USD)")
plt.gca().invert_yaxis()
plt.show()
```

![image](https://github.com/user-attachments/assets/94f5e291-9ef3-479a-86cb-3907750e10ca)



