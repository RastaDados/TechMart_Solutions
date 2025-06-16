## Dashboard Utilizando o Dash

```python
import pandas as pd
import plotly.express as px
from dash import Dash, dcc, html, Input, Output
from statsmodels.tsa.holtwinters import ExponentialSmoothing

#Carregando os dados
file_path = 'dados/Sample - Superstore.csv'
df = pd.read_csv(file_path, encoding='latin1', parse_dates=['Order Date'])

#Ajustando o formato de data
df['Month'] = df['Order Date'].dt.to_period('M').dt.to_timestamp()
df['Year'] = df['Order Date'].dt.year

#Agregando os dados para usar na série temporal de mês (mensal)
ts = df.groupby('Month')['Sales'].sum().asfreq('MS')

#Criando o modelo de Previsão com Holt-Winters
model = ExponentialSmoothing(ts, seasonal='add', seasonal_periods=12).fit()
forecast = model.forecast(12)
forecast_df = forecast.reset_index()
forecast_df.columns = ['Month', 'Forecast']

#Iniciando o app
app = Dash(__name__)

#Layout do Dashboard
app.layout = html.Div([
    html.H1("Dashboard Comercial - Superstore", style={"textAlign": "center"}),

    html.Div([
        html.Div([
            html.Label("Selecionar Ano:"),
            dcc.Dropdown(
                options=[{"label": str(ano), "value": ano} for ano in sorted(df['Year'].unique())],
                value=df['Year'].max(),
                id="ano-dropdown"
            )
        ], style={"width": "20%", "display": "inline-block", "padding": "10px"}),

        html.Div([
            html.Label("Selecionar Região:"),
            dcc.Dropdown(
                options=[{"label": regiao, "value": regiao} for regiao in sorted(df['Region'].unique())],
                value=None,
                id="regiao-dropdown",
                multi=True,
                placeholder="Todas as regiões"
            )
        ], style={"width": "25%", "display": "inline-block", "padding": "10px"}),

        html.Div([
            html.Label("Selecionar Categoria:"),
            dcc.Dropdown(
                options=[{"label": cat, "value": cat} for cat in sorted(df['Category'].unique())],
                value=None,
                id="categoria-dropdown",
                multi=True,
                placeholder="Todas as categorias"
            )
        ], style={"width": "25%", "display": "inline-block", "padding": "10px"}),
    ]),

    dcc.Tabs([
        dcc.Tab(label='Visão Geral', children=[
            dcc.Graph(id="vendas-mensal"),
            dcc.Graph(id="top-categorias"),
            dcc.Graph(id="lucro-por-regiao"),
            dcc.Graph(id="mapa-categorias-regiao")
        ]),

        dcc.Tab(label='Previsão de Vendas', children=[
            dcc.Graph(id="previsao-vendas",
                      figure=px.line(
                          pd.concat([ts.reset_index().rename(columns={0: 'Sales'}), forecast_df], axis=0),
                          x='Month', y=['Sales', 'Forecast'],
                          title='Previsão de Vendas (Holt-Winters)',
                          labels={"value": "Vendas", "variable": "Tipo"}
                      ))
        ])
    ])
])

#Callbacks
@app.callback(
    [Output("vendas-mensal", "figure"),
     Output("top-categorias", "figure"),
     Output("lucro-por-regiao", "figure"),
     Output("mapa-categorias-regiao", "figure")],
    [Input("ano-dropdown", "value"),
     Input("regiao-dropdown", "value"),
     Input("categoria-dropdown", "value")]
)
def atualizar_graficos(ano, regioes, categorias):
    df_filtrado = df[df['Year'] == ano]

    if regioes:
        df_filtrado = df_filtrado[df_filtrado['Region'].isin(regioes)]

    if categorias:
        df_filtrado = df_filtrado[df_filtrado['Category'].isin(categorias)]

    #Receita mensal
    vendas_mes = df_filtrado.groupby('Month')['Sales'].sum().reset_index()
    fig1 = px.line(vendas_mes, x='Month', y='Sales', title=f"Receita Mensal - {ano}")

    #Top categorias
    top_categorias = df_filtrado.groupby('Category')['Sales'].sum().reset_index().sort_values('Sales', ascending=False)
    fig2 = px.bar(top_categorias, x='Category', y='Sales', title="Vendas por Categoria", color='Category')

    #Lucro por Região
    lucro_regiao = df_filtrado.groupby('Region')['Profit'].sum().reset_index()
    fig3 = px.bar(lucro_regiao, x='Region', y='Profit', title="Lucro por Região", color='Region')

    #Mapa de calor (Categoria x Região)
    mapa = df_filtrado.groupby(['Region', 'Category'])['Sales'].sum().reset_index()
    fig4 = px.density_heatmap(mapa, x='Region', y='Category', z='Sales', title="Mapa de Calor: Categoria vs Região")

    return fig1, fig2, fig3, fig4

#Iniciando o servidor localmente (geralmente fica na porta 8050)
if __name__ == '__main__':
     app.run_server(debug=True)
```

![01](https://github.com/user-attachments/assets/3a314ec2-87e1-4a57-a573-1cb1f689c486)

![02](https://github.com/user-attachments/assets/7e2c51db-23ef-4d7b-8249-2af35b75b658)

![03](https://github.com/user-attachments/assets/320124c0-4982-476e-a0e2-5675e1338f65)

![04](https://github.com/user-attachments/assets/7d55aa8c-7842-4515-885e-7f4bef6849ed)

![05](https://github.com/user-attachments/assets/ebd43fd9-0450-48df-a3f3-7c1cd67f9761)



