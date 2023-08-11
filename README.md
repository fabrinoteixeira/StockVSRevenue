# código completo do projeto StockVSRevenue

import yfinance as yf
import requests
from bs4 import BeautifulSoup
import pandas as pd
import matplotlib.pyplot as plt

# Função para criar o gráfico de ações
def make_stock_graph(data, title):
    plt.figure(figsize=(10, 6))
    plt.plot(data.index, data["Close"], color='blue', label='Stock Price')
    plt.title(title)
    plt.xlabel("Date")
    plt.ylabel("Stock Price (USD)")
    plt.grid()
    plt.legend()
    plt.show()

# Função para criar o gráfico de receitas
def make_revenue_graph(data, title):
    plt.figure(figsize=(10, 6))
    plt.bar(data["Year"], data["Revenue"], color='green', label='Revenue')
    plt.title(title)
    plt.xlabel("Year")
    plt.ylabel("Revenue (USD)")
    plt.grid()
    plt.legend()
    plt.show()

# Símbolos das empresas
tesla_ticker = "TSLA"
gme_ticker = "GME"

# Obtendo os dados de preços das ações da Tesla
tesla = yf.Ticker(tesla_ticker)
tesla_data = tesla.history(period="1y")

# Criando o gráfico de ações da Tesla
make_stock_graph(tesla_data, "Tesla Stock Price")

# URL da página com os dados de receita da GameStop
gme_revenue_url = "https://pt.stock-analysis-on.net/NYSE/Empresa/GameStop-Corp/Demonstracao-Financeira/Demonstracao-do-Resultado-Abrangente"

# Enviando um pedido HTTP para a página
gme_revenue_response = requests.get(gme_revenue_url)

# Analisando o conteúdo HTML da página
gme_soup = BeautifulSoup(gme_revenue_response.content, 'html.parser')

# Encontrando a seção que contém os dados de receita
revenue_section = gme_soup.find('div', class_='csTable moduletable')

# Inicializando listas para armazenar anos e receitas
years = []
revenues = []

# Percorrendo as linhas da tabela e extraindo os dados
for row in revenue_section.find_all('tr')[1:]:
    cols = row.find_all('td')
    year = cols[0].get_text()
    revenue = cols[1].get_text().replace('.', '')  # Remover pontos
    years.append(year)
    revenues.append(int(revenue))

# Criando um DataFrame para os dados de receita da GameStop
gme_revenue_data = pd.DataFrame({
    "Year": years,
    "Revenue": revenues
})

# Exibindo as últimas cinco linhas do DataFrame de receitas da GameStop
print(gme_revenue_data.tail())

# Criando o gráfico de receitas da GameStop
make_revenue_graph(gme_revenue_data, "GameStop Revenue")

# Imprimindo descrição do projeto
project_description = """
O projeto StockVSRevenue é uma análise abrangente que combina a exploração de dados históricos de ações e receitas das empresas Tesla e GameStop. O projeto começa extraindo meticulosamente os dados históricos de preços das ações da Tesla usando a biblioteca yfinance e os dados de receita da GameStop através de web scraping.
Os gráficos de ações da Tesla e de receitas da GameStop são gerados usando funções de visualização personalizadas. Essas visualizações permitem uma análise comparativa entre os aspectos financeiros e o desempenho do mercado das duas empresas. O projeto visa fornecer insights sobre a interação entre o desempenho das ações e os indicadores financeiros, auxiliando investidores e analistas na tomada de decisões informadas e estratégicas.
"""

print(project_description)
