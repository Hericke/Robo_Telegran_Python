# Robo_Telegran_Python

Dashboard com as seguintes funcoes:
Pagina para agendamentos de servicos inserindo no banco os agendamentos e me enviando um push por whatsapp e email informando os dados de agendamento.
duas paginas estaticas para eu inserir conteudos.
uma pagina utilitarios exibindo os softwares que eu fiz upload pelo painel adm para download.

Painel Admin:

pagina para adicionar,editar e excluir clientes
pagina para o upload dos arquivos da pagina utilitarios
pagina para inserir o conteudo das paginas estaticas
pagina de vizualização e edição dos agendamentos. 


codigo:

import telegram
from selenium import webdriver
from bs4 import BeautifulSoup
import tkinter as tk
import sqlite3

# Biblioteca para interagir com a API do Telegram
bot = telegram.Bot(token='seu_token_do_Telegram')

# Configuração do Selenium para acessar a página da roleta
options = webdriver.ChromeOptions()
options.add_argument('headless') # Executar o Chrome em segundo plano
driver = webdriver.Chrome(options=options)
driver.get('url_da_pagina_da_roleta')

# Utilização do BeautifulSoup para extrair informações da página da roleta
soup = BeautifulSoup(driver.page_source, 'html.parser')
numeros_sorteados = soup.find_all('div', class_='numero')

# Definição das estratégias pré-definidas e comparação com os números sorteados
estrategias = {'estrategia_1': [1, 2, 3, 4, 5], 'estrategia_2': [6, 7, 8, 9, 10]}
for estrategia, numeros in estrategias.items():
    if set(numeros) != set([int(num.text) for num in numeros_sorteados]):
        bot.send_message(chat_id='seu_chat_id_do_Telegram', text=f'Estratégia {estrategia} sendo validada.')

# Criação da interface gráfica para inserir as regras das estratégias
janela = tk.Tk()
janela.title('Minhas Estratégias')
# Adicione aqui os componentes da interface gráfica, como labels, entrys, buttons, etc.
janela.mainloop()

# Leitura do arquivo com as regras das estratégias
with open('arquivo_de_estrategias.txt', 'r') as f:
    estrategias_personalizadas = eval(f.read())

# Comparação das estratégias personalizadas com os números sorteados
for estrategia, numeros in estrategias_personalizadas.items():
    if set(numeros) != set([int(num.text) for num in numeros_sorteados]):
        bot.send_message(chat_id='seu_chat_id_do_Telegram', text=f'Estratégia {estrategia} sendo validada.')

# Armazenamento dos dados de acerto diário por estratégia em um banco de dados SQLite
conn = sqlite3.connect('dados_de_acerto.db')
cursor = conn.cursor()

# Criação da tabela para armazenar os dados
cursor.execute('''CREATE TABLE IF NOT EXISTS acerto_diario
                  (estrategia text, data text, acertos integer)''')

# Adição dos dados na tabela
data_atual = '2023-04-18'
acertos_estrategia_1 = 5
acertos_estrategia_2 = 7
cursor.execute("INSERT INTO acerto_diario VALUES ('estrategia_1', ?, ?)", (data_atual, acertos_estrategia_1))
cursor.execute("INSERT INTO acerto_diario VALUES ('estrategia_2', ?, ?)", (data_atual, acertos_estrategia_2))

# Salvar as alterações e fechar a conexão com o banco de dados
conn.commit()
conn.close()
