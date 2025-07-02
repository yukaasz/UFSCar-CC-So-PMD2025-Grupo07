# UFSCar-CC-So-PMD2025-Grupo07

### Tema: Aplicação de recomendação de medicamentos

Integrantes:
- Daniella Yuka Hirosue - 813008
- Renan Yugo Ueda - 813346

## Fase Intermediária – Planejamento: Definição da proposta

### Resumo e Introdução:

  O seguinte projeto será desenvolvido pelos membro Daniella e Renan, e tem como finalidade aplicar conceitos aprendidos durante as aulas de Processamento Massivos de Dados, tais como Apache Spark, Python, Manipulação e Transformação de Dados, Relacionamentos e Consultas em um Banco de Dados orientado a Grafos (Neo4j).
Para isso, a dupla escolheu trabalhar o projeto sobre dois datasets de medicamentos, aplicando os conhecimentos acima referidos.
Nos tópicos a seguir, será elaborado mais detalhes do trabalho em questão.

### Minimundo:

  Devido à grande competição no setor farmacêutico, empresas passaram a desenvolver cada vez mais medicamentos com o objetivo de se destacar no mercado. Como consequência, houve um aumento acelerado na quantidade de remédios com diversas finalidades, ampliando significativamente as opções disponíveis aos consumidores.

  Diante desse cenário, torna-se interessante e necessário oferecer ao público uma forma eficiente de filtrar essas opções, sejam por tratamentos de algum sintoma, efeitos colaterais ou até mesmo fabricante (se disponibilizado esta informação). Para isso, foram utilizados dois conjuntos de dados disponíveis no site Kaggle.

### Fonte de Dados
  Os dados do projeto são provenientes de dois arquivos csv, disponibilizados nos seguintes *links*:

dataset1: https://www.kaggle.com/datasets/singhnavjot2062001/11000-medicine-details?select=Medicine_Details.csv
  
dataset2: https://www.kaggle.com/datasets/shudhanshusingh/250k-medicines-usage-side-effects-and-substitutes

  Embora os dois datasets possuam atributos diversos, o projeto em questão não fará uso de todos eles. Logo, a dupla aplicará uma etapa de transformação utilizando o Spark para sintetizar e adaptar os dados em apenas um dataset com quatro atributos, sendo eles `drugName`, `manufacturer`, `sideEffects` e `conditions` (este último seria o sintoma que o remédio tratará).

  OBS: no dataset2, o campo manufacturer (fabricante) não está presente. 
O grupo procurou outras bases de dados que possuíssem os mesmos atributos principais do projeto, mas não obtivemos sucesso. 
Ainda assim, mesmo que alguns medicamentos fiquem sem essa informação, a dupla considera o fabricante um atributo interessante para análise e, por esse motivo, decidimos manter em nosso projeto.

A partir deste novo *dataset* formaremos os nós no Neo4j.

Serão 4 tipos de nós: 
- `manufacturer` (com os atributos `id` e `name`)
- `drug` (com os atributos `id`, `name`)
- `sideEffect` (com os atributos `id`, `name`) 
- `condition` (com os atributos: `id` e `name`)

E as arestas serão:
- `develops` (aresta entre `manufacturer` e `drug`)
- `has_side_effect` (aresta entre `drug` e `sideEffects`)
- `treats` (aresta entre `drug` e `condition`)

![Web (4)](https://github.com/user-attachments/assets/e1fa67bc-b29a-4d4d-a967-da7c3ffae82d)
Imagem 1: Fluxograma de como os dados transitarão pela aplicação.

### Objetivos:

  O objetivo principal do projeto é possibilitar a visualização de medicamentos com a **mesma finalidade terapêutica** no Neo4j.
Além disso, é possível que o usuário filtre os resultados com base em outros critérios como **fabricante** e **efeitos colaterais**, os quais podem ser evitados pelo consumidor.

  Alguns exemplos de consulta que o sistema oferece:
    - Quais medicamentos são relacionados à pressão sanguínea(*blood pressure*)? 
    - Quais medicamentos são do mesmo fabricante?
    - O medicamento “x” possui o efeito colateral “y”? 

### Tecnologias
Para este projeto, as tecnologias escolhidas foram:

  Apache Spark para extração e transformação, pois:
    - Possui alta performance para processar grandes volumes de dados, pois ele executa as transformações de dados em memória principal. No cenário do projeto onde novos medicamentos são desenvolvidos constantemente, essa característica é bem útil para uma futura atualização da aplicação.
    - Possui escalabilidade e flexibilidade, pois o Apache Spark tem a capacidade de ler arquivos de vários formatos diferentes.

  Neo4j para a carga e as consultas, pois:
    - O modelo em grafos permite que consultas que utilizam relacionamentos possam ser processadas de forma direta sem depender de junções complexas como em bancos de dados relacionais.
    - É mais eficiente navegar entre relacionamentos em um modelo em grafos do que em bancos de dados relacionais.
    - Possui estrutura de dados flexível que permite a adição de novos nós como modo de consumo ou principal composto ativo, por exemplo, sem a necessidade de reestruturar todo o banco de dados.

### Ajustes da Etapa Anterior:

- Remoção dos atributos ´price´ e ´description´:
    - O motivo dessas remoções se dá por conta da indisponibilidade de datasets com os mesmos atributos para realização de cruzamento.
- Alteração do nome ´category´ para ´condition´, e ´belongs_to´ para ´treats´
- Adição de um novo *dataset* para complementar o dataset inicial e aumentar a complexidade do projeto.
- Mudança do Diagrama para acompanhar as alterações acima.


