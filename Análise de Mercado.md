## Análise de Mercado - ComMerce 360

<hr>

### Objetivos da Análise

#### Problema de Negócio

A empresa buscava entender melhor sua performance comercial para:

- Identificar as principais fontes e o que influenciava na receita e lucro
- Avaliar o desempenho por região, produto e vendedor
- Antecipar tendências sazonais e prever futuras vendas
- Descobrir oportunidades e riscos operacionais

#### Perguntas-Chave

- Quais são os produtos, categorias e regiões que mais contribuem para o faturamento?
- Existem sazonalidades ou tendências claras ao longo do tempo?
- Quem são os melhores clientes e vendedores?
- Como prever receitas futuras com maior assertividade?
- Quais segmentos precisam de atenção ou ajustes estratégicos?

<hr>

### Principais Indicadores (KPIs)

| Indicador              | Descrição                                           | Relevância                                              |
| ---------------------- | --------------------------------------------------- | ------------------------------------------------------- |
| **Receita Total**      | Soma total das vendas                               | Indica o volume de faturamento da empresa               |
| **Lucro Total**        | Soma dos lucros                                     | Mede a rentabilidade dos produtos                       |
| **Ticket Médio**       | Receita / Número de pedidos                         | Avalia valor médio gasto por pedido                     |
| **Vendas Mensais**     | Receita por mês                                     | Permite identificar tendências e sazonalidades          |
| **Top Categorias**     | Ranking de produtos por volume de venda             | Direciona ações de estoque, marketing e mix de produtos |
| **Lucro por Região**   | Comparação entre regiões em termos de lucratividade | Suporta decisões geográficas e operacionais             |
| **Previsão de Vendas** | Estimativa de vendas futuras com Holt-Winters       | Ajuda no planejamento e metas estratégicas              |

<hr>

### Interpretação das Visualizações

### Receita Mensal por Ano

- Gráfico de linha
- Variáveis: Month, Sales

<b>Insights:</b>

- Tendência de crescimento ao longo dos anos
- Picos sazonais recorrentes em novembro e dezembro
- Quedas em meses como janeiro e julho, indicando sazonalidade

### Vendas por Categoria

- Gráfico de barras
- Variáveis: Category, Sales

<b>Insights:</b>

- A categoria “Technology” lidera as vendas com folga
- “Office Supplies” tem volume alto mas menor lucratividade
- “Furniture” tem vendas moderadas com lucro relativamente baixo

#### Lucro por Região

- Gráfico de barras
- Variáveis: Region, Profit

<b>Insights:</c>

- Região Oeste é a mais lucrativa
- Região Sul apresenta lucro baixo ou até perdas
- Nordeste tem desempenho médio com potencial de crescimento

#### Mapa de Calor (Categoria x Região)

- Heatmap
- Variáveis: Region, Category, Sales

<b>Insights:</b>

- Alta concentração de vendas de “Technology” no Oeste e Centro-Oeste
- Baixo volume de vendas de “Furniture” no Sul

#### Previsão de Vendas (Holt-Winters)

- Linha com previsão
- Variáveis: Month, Sales, Forecast

<b>Insights:</b>

- Previsão para os próximos 12 meses segue tendência de crescimento suave
- Expectativa de picos sazonais mantidos para o fim do ano

<hr>

### Insights Estratégicos e Operacionais

#### Desempenho Atual

- Receita e lucro cresceram de forma consistente entre 2015 e 2018
- Tecnologia impulsiona as vendas com boa margem
- Algumas regiões (como o Sul) requerem atenção devido à baixa rentabilidade

#### Oportunidades

- Expandir produtos tecnológicos nas regiões com baixa penetração
- Reavaliar política de descontos em Furniture, onde o lucro é crítico
- Alinhar metas regionais com o desempenho histórico

#### Riscos

- Possível excesso de dependência da categoria “Technology”
- Regiões com prejuízo podem indicar falhas logísticas ou promocionais

<hr>

### Análise Segmentada

#### Por Região

- Oeste: maior receita e lucro
- Sul: performance fraca, possível gargalo
- Nordeste: médio desempenho, pode crescer com investimento

#### Por Categoria

- Tecnologia domina em vendas e lucro
- Móveis precisam de reestruturação (baixo retorno)
- Por Cliente (próximas etapas com RFM)
- Potencial para identificar clientes VIP e inativos
- RFM pode direcionar campanhas personalizadas

### Algumas Recomendações que EU Sugiro

#### Estratégicas

- Expandir estoque e marketing da linha tecnológica nas regiões Sul e Nordeste
- Redesenhar políticas de preços e promoções em Furniture
- Priorizar regiões e categorias com maior retorno histórico

#### Operacionais

- Monitorar KPIs regionais mensalmente
- Criar metas específicas para vendedores por categoria/região
- Automatizar alertas para desvios sazonais ou de lucro

<hr>


### Resumo Executivo

- A análise revelou crescimento sustentável, com forte contribuição da categoria Technology e da região Oeste.
- A previsão de vendas confirma a manutenção dessa tendência, com sazonalidade clara.
- Há oportunidades relevantes de crescimento ao focar em regiões com performance média ou baixa, e reavaliar estratégias para categorias menos lucrativas.
- O dashboard criado com Dash permite exploração dinâmica e facilita a tomada de decisão baseada em dados.
- O dashboard no Pwer BI permite uma granularidade maior na análise e axuilia na tomada de decisão da equipe.
