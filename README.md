<h1 align="center">Projeto de Web Scrapping</h1>

<p align="center">
 <a href="#resumo-do-projeto">Resumo do projeto</a> •
  <a href="#arquivos">Arquivos</a> •
 <a href="#tecnologias-utilizadas">Tecnologias utilizadas</a> •
 <a href="#obtenção-dos-dados">Obtenção dos dados</a> • 
 <a href="#tratamento-dos-dados">Tratamento dos dados</a> • 
 <a href="#análise-dos-dados">Análise dos dados</a
</p>

## Resumo do projeto
<p align="justify">
Este é um projeto de cunho pessoal com o objetivo de explorar técnicas de webscrapping utilizando as bibliotecas BeautifulSoup e requests do Python. Foi feita uma raspagem de alguns dados da wiki da <a href="https://en.wikipedia.org/wiki/Copa_Am%C3%A9rica">Copa América</a>, um torneio de futebol disputado entre seleções sul-americanas (e eventualmente outras convidadas) desde 1916. Os dados extraídos inicialmente foram: edição, país-sede, quantidade de cidades e estádios que receberam partidas, período de realização do torneio, quantidade de seleções participantes, nome dos 4 primeiros colocados, gols marcados e público. Foram então aplicadas diversas técnicas para extrair novas variáveis e fazer a limpeza destes dados e, por fim, foi feita uma análise exploratória dos dados tratados para buscar por padrões e tendências, assim como relações entre as variáveis.

## Arquivos
<p align="justify">
Os arquivos disponibilizados neste repositório são:
  
- dados_copa_raw: arquivo .pkl contendo os dados brutos raspados
- dados_tratados: arquivo .pkl contendo os dados limpos e tratados
- web_scrapping: arquivo .ipynb contendo o código e os gráficos gerados, assim como a análise dos dados

## Tecnologias utilizadas
- Python
- BeautifulSoup
- Requests
- Pandas
- Numpy
- Regex
- Matplotlib
- Seaborn

## Obtenção dos dados
<p align="justify">
Foram utilizadas as bibliotecas requests e BeautifulSoup para obter uma lista com os links que deveriam ser acessados para fazer a raspagem dos dados. A partir dela, foi criado um loop para acessar individualmente cada um dos 48 links (correspondentes à cada uma das 48 edições do torneio) e salvar os dados de interesse em um dataframe. Um trecho do código utilizado nesta etapa foi o seguinte:
  
```
data = []
for url in links:
    resp = requests.get(url)
    soup = BeautifulSoup(resp.text)
    paragrafo = soup.find("table", class_='infobox vcalendar').find("tbody").find_all("tr")
    mydict = {}
    rows = ['Host country', 'Dates', 'Teams', 'Venue(s)', 'Champions', 'Runners-up', 'Third place', 'Fourth place', 'Matches played', 'Goals scored', "Attendance"]
    for i in range(len(paragrafo)):
        mydict['Edition'] = (soup.find(class_="infobox-title summary").text)
        if paragrafo[i].find(string=rows):
            mydict[paragrafo[i].th.text] = paragrafo[i].td.get_text(strip=True)
    mydict['Link'] = url
    data.append(mydict.copy()) 
```
  
E um exemplo de uma linha do dataframe criado:

| Edition | Host country | Dates | Teams | Venue(s) | Champions | Runners-up | Third place | Fourth place | Goals scored | Link | Attendance | 
| ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |  ------ | ------ | ------ | ------ |
| 2015 Copa América | Chile | 11 June – 4 July | 12 (from 2 confederations) | 9 (in 8 host cities) | Chile(1st title)	| Argentina | Peru | Paraguay | 59 (2.27 per match) | https://en.wikipedia.org/wiki/2015_Copa_Am%C3%...	 | 655,902 (25,227 per match)

## Tratamento dos dados
<p align="justify">
O primeiro passo foi fazer um pré-processamento destes dados. Nesta etapa, foi utilizado principalmente regex para corrigir os nomes da colunas e extrair ou separar algumas das informações. Por exemplo, a variável goals_scored foi dividida em duas novas colunas, uma contendo o total de gols marcados e outra contendo a média de gols por partida.

<div align='center'>
  
| Goals scored | --> | Total goals | Average goals |
| ------ | ------ | ------ | ------ |
| 59 (2.27 per match) | --> | 59 | 2.27 |

</div>

<p align="justify">
O próximo passo foi fazer o tratamento dos dados. Nesta etapa, foram feitas a correção dos tipos de variáveis numéricas, a verificação de valores duplicados e de valores faltantes. Para alguns valores faltantes, devido à ausência da informação resumida, foi necessário fazer uma abordagem um pouco mais específica. Por exemplo, alguns links não continham a informação total do público no mesmo formato dos demais, mas tinham o público de cada uma das partidas. Assim, foi feita uma nova raspagem para obter estes dados, consolidá-los em um novo dataframe e substituir pelos dados faltantes no dataframe principal. Os outros dados faltantes foram justificados no documento principal, seja por motivos históricos (como a falta de disputa para definição do 3º e 4º lugar em algumas edições) ou técnicos (como a falta de registros do público nas primeiras edições).


## Análise dos dados
<p align="justify">
Utilizando os dados tratados, foi feita uma visualização geral das variáveis numéricas usando a função describe, de onde observarmos que as mudanças no regulamento ao longo da competição como no número de seleções participantes, quantidade de cidades e estádios que receberam jogos, que refletiram diretamente em outros aspectos como quatidade de partidas e gols. Foi feita em seguida uma análise univariada, gerando gráficos para resumir cada variável e buscar por padrões e tendências. Pegando como exemplo a variável gols:

<p align="center">
  <img src="https://github.com/viniciusendo/projeto_web_scrapping/assets/134152277/400ce899-2fb4-4607-8ab2-18d44896d6ce" width="600" height="340">
</p>

<p align="justify">
Observamos uma grande variação no total de gols marcados em virtude da quantidade de partidas não ser constante ao longo das edições do torneio. Em relação à media de gols por partida, observamos uma tendência de queda, possivelmente em virtude das mudanças ocorridas na dinâmica e nas táticas do jogo (como maior ênfase em formações defensivas) ou ainda pelo fato do número de partidas ter aumentado, diminuindo a influência de outliers (placares mais elásticos). 

<p align="justify">
Por fim, foram feitas algumas análises multivariadas para tentar entender a relação entre as variáveis. Para isso, foram usados métodos gráficos e de correlação. Por exemplo, foi investigada a hipótese de uma seleção performar melhor quando ela é o país-sede. Utilizando uma tabela consolidada com o número de vezes que o país foi sede e suas aparições no Top 4 e fazendo uma matriz de correlação entre as variáveis, temos:

<div align='center'>

|  | First place | Second place | Third place | Fourth place | Top 4 |
| ------ | ------ | ------ | ------ | ------ | ------ |
| Host_n | 0.77 | 0.68 | 0.56 | 0.36 | 0.80 |
</div>

<p align="justify">
De onde observamos que existe de fato uma correlação positiva entre sediar a Copa América e a colocação, mas esta pode ser devida à diferença de qualidade entre os times, visto que as seleções mais tradicionais também sediaram mais vezes o torneio.

<p align="justify">
Todo o processo de obtenção, limpeza (e sua justificativa) e análise dos dados está documentada no notebook do repositório, onde também é possível ver os gráficos e tabelas gerados e outras análises. 

