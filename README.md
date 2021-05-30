> # Avaliação dos Serviços de Streaming Netflix, Disney+ e Prime Video.

## Contexto

É fácil observar que a pandemia alterou e muito nossas práticas cotidianas. O uso de máscaras e o cuidado com a etiqueta respiratória são exemplos claros e facilmente observáveis, uma vez que se relacionam diretamente a medidas sanitárias que visam o combate à pandemia. No entanto, algumas mudanças secundárias nos nossos hábitos podem ser notadas. Um exemplo disso é o consumo de entretenimento via streaming. 

Seja no segmento fonográfico ou audiovisual, o mercado teve de se ajustar ao novo comportamento de consumo do público. Gigantes da indústria, como Disney e Amazon, lançaram seus próprios serviços para competir pelo mercado. E esse mercado entrou em franca expansão com a pandemia da COVID-19.

Conforme medidas restritivas eram tomadas por todo o mundo, o público viu no consumo de streaming uma das poucas alternativas ainda viáveis para o lazer. Conforme [estudo da Ofcom noticiado pela BBC](https://www.bbc.com/news/entertainment-arts-53637305), o Reino Unido registrou um aumento de 12 milhões de usuários em serviços de streaming, além de um aumento no tempo que esses consumidores passavam assistindo conteúdos nesses serviços. 

Já segundo a [Motion Picture Association](https://www.marketwatch.com/story/global-streaming-subscriptions-top-1b-during-covid-2021-03-18), em 2020 o número total de subscrições a serviços de streaming ao redor do mundo chegou a 1.1 bilhões de utilizadores, enquanto receitas de bilheteria nos cinemas seguiram em derrocada devido às medidas de isolamento.

Por conta do aumento da demanda por conteúdos via streaming, é natural que a oferta acabe se diversificando em meio à disputa por market shares. Dessa forma, cada serviço de streaming oferece diferentes conteúdos e disputam entre si para disponibilizar aqueles que melhor atenderão seu público. Do ponto de vista do consumidor, é interessante portanto saber qual serviço possui os conteúdos que melhor se adequem às suas preferências.

Pensando nisso, concebemos este presente projeto como uma forma mostrar os dados obtidos de maneira a ajudar o público a escolher melhor quais serviços assinar.

## Os dados utilizados

Ao buscarmos dados para realizar o embasamento em nossa análise sobre os serviços de streaming encontramos diversos trabalhos semelhantes, mas cuja proposta não se destinava tanto ao consumidor. A maioria dos trabalhos com dados de streaming não instrumentalizam esses dados para o consumidor.

Um dataset que encontramos que melhor se adequou a nossa proposta foi o [dataset](https://github.com/ruch798/Movies-and-TV-shows) organizado por [Ruchi Bhatia](https://www.linkedin.com/in/ruchi798/) .

Bhatia é uma executiva associada em Colgate-Palmolive e embaixadora global em data science para Nvidia e HP. Analisamos como ela realizou o data scrapping:

### Data Scrapping

 No repositório disponibilizado por Bhatia podemos visualizar o código do programa utilizado para obter os dados dos serviços de streaming. Não foi uma tarefa tão fácil realizar essa análise, tendo sido necessário pesquisar um pouco sobre linguagem HTML. Isso porque o approach escolhido por Bhatia foi utilizar as informações fornecidas na página web do serviço [Reelgood](https://reelgood.com/about), um hub que reune o catálogo de serviços de streaming variados. Vejamos o código utilizado por Bhatia:

"print(\"Netflix TV shows\")\n",
        "records = []\n",
        "\n",
        "for i in range(0,501):\n",
        "  URL = \"https://reelgood.com/tv/source/netflix?offset=\" + str(i*50)\n",
        "  r = requests.get(URL, timeout=5)\n",
        "  soup = BeautifulSoup(r.text,'html.parser')\n",
        "  row = soup.find_all('tr',attrs={'class':'css-gfsdx9'})\n",
        "\n",
        "  for t in row:\n",
        "        title = t.find(attrs={'class':'css-1u7zfla e126mwsw1'}).text\n",
        "        year = t.find(attrs={'class':'css-1u11l3y'}).text\n",
        "        age = t.find(attrs={'class':'css-1u11l3y'}).findNext('td').text\n",
        "        IMDb = t.find(attrs={'class':'css-1u11l3y'}).findNext('td').findNext('td').text\n",
        "        rottenTomatoes = t.find(attrs={'class':'css-1u11l3y'}).findNext('td').findNext('td').findNext('td').text\n",
        "        records.append([title, year, age, IMDb, rottenTomatoes])\n",
        "  i =  i + 1\n",
        "\n",
        "df1 = pd.DataFrame(records) \n",
        "df1.rename(columns = {0:'Title', 1:'Year', \n",
        "                              2:'Age', 3:'IMDb', 4:'Rotten Tomatoes'}, inplace = True) \n",
        "df1['Netflix'] = 1\n",
        "df1['Hulu'] = 0\n",
        "df1['Prime Video'] = 0\n",
        "df1['Disney+'] = 0\n",
        "df1"

Basicamente, este programa analisa cada parte da página do catálogo Netflix presente no site Reelgood, na URL https://reelgood.com/tv/source/netflix?offset= . Em detalhes, analisemos as linhas do código:

"print(\"Netflix TV shows\")\n",
        "records = []\n",
        "\n",
        "for i in range(0,501):\n",
        "  URL = \"https://reelgood.com/tv/source/netflix?offset=\" + str(i*50)\n",
        "  r = requests.get(URL, timeout=5)\n",
        "  soup = BeautifulSoup(r.text,'html.parser')\n",
        "  row = soup.find_all('tr',attrs={'class':'css-gfsdx9'})\n",
        "\n",

Esta parte do código cria a lista denominada "Netflix TV shows", sendo preenchida por um looping (for) que terá um alcance (range) até 501, com a requisição de acesso à URL sendo realizada a cada 5 segundos. Como o código da página do Reelgood é em HTML, o comando BeautifulSoup utilizado transforma a informação obtida em HTML em formato próprio para o JSON. A variável row corresponde as fileiras em que estão os títulos do catálogo na página indicada, sendo identificada no código HTML com a classe css-1u11l3y. 

"  for t in row:\n",
        "        title = t.find(attrs={'class':'css-1u7zfla e126mwsw1'}).text\n",
        "        year = t.find(attrs={'class':'css-1u11l3y'}).text\n",
        "        age = t.find(attrs={'class':'css-1u11l3y'}).findNext('td').text\n",
        "        IMDb = t.find(attrs={'class':'css-1u11l3y'}).findNext('td').findNext('td').text\n",
        "        rottenTomatoes = t.find(attrs={'class':'css-1u11l3y'}).findNext('td').findNext('td').findNext('td').text\n",
        "        records.append([title, year, age, IMDb, rottenTomatoes])\n",
        "  i =  i + 1\n",
        "\n",

Dentro da fileira designada é realizado for que vai obter os dados a serem preenchidos. Podemos perceber que as informações estão identificadas com a mesma classe css-1u11l3y, e portanto é utilizado o comando findNext tantas vezes quanto se faz necessário para obter a informação desejada. O número de vezes que se usa o comando é relativo a ordem em que essas infomações se apresentam na página do Reelgood: primeiro temos o título, em seguido o ano e assim por diante. Por fim, para interromper o for, é redefinida a varável i para i +1.

"df1 = pd.DataFrame(records) \n",
        "df1.rename(columns = {0:'Title', 1:'Year', \n",
        "                              2:'Age', 3:'IMDb', 4:'Rotten Tomatoes'}, inplace = True) \n",
        "df1['Netflix'] = 1\n",
        "df1['Hulu'] = 0\n",
        "df1['Prime Video'] = 0\n",
        "df1['Disney+'] = 0\n",
        "df1"

Para colocar as informações no formato de data frame foram utilizados os comandos de Panda, criando as colunas "Title", "Year", "Age", "IMDB", "Rotten Tomatoes" de acordo com as variáveis anteriormente atribuídas.

Esse processo se repetiu para cada serviço de streaming, formando o dataset que usamos neste trabalho.


### Limpeza de dados


















# Dicionário de dados 

| Nome da coluna    |   Conteúdos       
|-------------------|--------------------------------------------------------------------------------
|    Title          |
|    Year           |
|    Age            |
|    IMDB           |
|    Rotten         |
|    Tomatoes       |
|    Netflix        |
|    Hulu           |
|    Prime Video    |
|    Disney         |
|    Type           | 
|    Title          |
|    Director       |


| Nome da Coluna    |  Conteúdos 
|-------------------|----------------------------------------------------------------------------------
|    Cast           |
|    Country        |
|    Date_added     |
|    Release_year   |
| Rating duration   |
