# UFSCar-CC-So-PMD2025-Grupo07

### Tema: Aplicação de recomendação de medicamentos

Integrantes:
- Daniella Yuka Hirosue - 813008
- Renan Yugo Ueda - 813346

## Fase Intermediária – Planejamento: Definição da proposta

### Resumo e Introdução:

  O seguinte projeto será desenvolvido pelos membro Daniella e Renan, e tem como finalidade aplicar conceitos aprendidos durante as aulas de Processamento Massivos de Dados, tais como Apache Spark, Python, Manipulação e Transformação de Dados, Relacionamentos e Consultas em um Banco de Dados orientado a Grafos (Neo4j).
Para isso, a dupla escolheu trabalhar o projeto sobre dois datasets de medicamentos, aplicando os conhecimentos acima referidos.
Nos tópicos a seguir, elaboramos mais detalhes do trabalho em questão.

### Minimundo:

  Devido à grande competição no setor farmacêutico, empresas passaram a desenvolver cada vez mais medicamentos com o objetivo de se destacar no mercado. Como consequência, houve um aumento acelerado na quantidade de remédios com diversas finalidades, ampliando significativamente as opções disponíveis aos consumidores.

Diante desse cenário, torna-se interessante e necessário oferecer ao público uma forma eficiente de filtrar essas opções, sejam por tratamentos de algum sintoma, efeitos colaterais ou até mesmo fabricante (se disponibilizado esta informação). Para isso, foram utilizados dois conjuntos de dados disponíveis no site Kaggle.

### Fonte de Dados:
  Os dados do projeto são provenientes de dois arquivos csv, disponibilizados nos seguintes *links*:

- dataset1: https://www.kaggle.com/datasets/singhnavjot2062001/11000-medicine-details?select=Medicine_Details.csv
  
- dataset2: https://www.kaggle.com/datasets/shudhanshusingh/250k-medicines-usage-side-effects-and-substitutes

Embora os dois datasets possuam atributos diversos, o projeto em questão não fará uso de todos eles. Logo, a dupla aplicará uma etapa de transformação utilizando o Spark para sintetizar e adaptar os dados em apenas um *dataset* com quatro atributos, sendo eles `drugName`, `manufacturer`, `sideEffects` e `conditions` (este último seria o sintoma que o remédio tratará).

OBS: no dataset2, o campo `manufacturer` (fabricante) não está presente. 
O grupo procurou outras bases de dados que possuíssem os mesmos atributos principais do projeto, mas não obtiveram sucesso. 
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

### Tecnologias:
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
- Adição de um novo *dataset* para complementar o *dataset* inicial e aumentar a complexidade do projeto.
- Mudança do Diagrama para acompanhar as alterações acima.

### Desenvolvimento:
A seguir serão apresentadas as ações tomadas pela dupla para realização do projeto.
Você pode conferir a execução e detalhamento do mesmo em formato de notebook no arquivo NOME_DO_ARQUIVO presente no Github do projeto. 
Enfatizamos que, faremos um breve resumo abaixo de como o grupo planejou a execução, para mais detalhes acesse o arquivo disponibilizado, por gentileza.

**1) Upload de dados**: Inicialmente, com os datasets em mãos, faremos o upload dos mesmos na plataforma Databricks, onde utilizaremos a tecnologia do PySpark(Apache Spark).

**2) Tratamento individual do df1(dataset1)**:
    
- **Valores Nulos**: Verificamos se existe alguma tupla com valores nulos em pelo menos um dos campos principais: Medicine Name (drug), Uses(conditions) e sideEffects (manufacturer como dito antes pode conter campos nulos em nosso projeto, por isso não é verificado).  
   
- **Dados Duplicados**: Para esta verificação, basta analisarmos o nome do medicamento. Isso porque podem existir medicamentos com a mesma composição (não analisada em nosso projeto) e com a mesma finalidade.  

- **Remodelar coluna Uses com uma lista de usos (as finalidades dos medicamentos)**: Separar a cadeia de caracteres em uma lista que conterá cada uso(finalidade) do medicamento. Isso será útil para passar os dados ao Neo4j posteriormente

- **Remodelar coluna de efeitos colaterais com uma lista de Side_effects**: o motivo é o mesmo que o item amterior.

**3) Tratamento individual do df2(dataset2)**:    

- **Dados Duplicados**: Para esta verificação, basta analisarmos o nome do medicamento. Isso porque podem existir medicamentos com a mesma composição (não analisada em nosso projeto) e com a mesma finalidade.

- **Remodelar colunas de efeitos colaterais**: Os efeitos colaterais estão separados por colunas e estão bem estruturados. Dessa maneira, criaremos uma lista contendo todos os efeitos

- **Remodelar colunas de usos**: O motivo é o mesmo que o item anterior.

- **Remover Dados Nulos**: Analisamos se alguma coluna possui algum campo nulo.

**4) Junção dos dois DataFrames**:  

- **Adição da coluna “Manufacturer” em df2**: adição desta coluna para padronização de DataFrames. Os campos foram preenchidos com valores nulos

- **Renomear colunas de df2**: novamente, para padronizar os dados, e possibilitar um cruzamento de dados, mantemos nomes de colunas iguais para os dois DataFrames

- **Remover medicamentos de df2 que já estão contidos em df1**: Mesmo que df1 tenha menos dados, ele terá prioridade já que contém os campos de Manufacturer. Aqui removeremos de df2 medicamentos já presentes em df1 para evitar duplicidade.

- **União dos dados**: Com df1 e df2 tratados, cruzamos os dados.

**5) Verificações Finais antes da etapa de carga**:

- **Remoção de Espaços**: Remoção de espaços antes e depois de cada string, em todos os campos de todas as colunas  

- **Eliminar Duplicatas**: Verificar se existem medicamentos duplicados novamente

**6) Carga de dados Databricks para Neo4j** Preparação de nós e relacionamentos no próprio Pyspark para alimentarmos a base de dados diretamente para o Neo4j  

- **Renomeação de Colunas**: Redefinir os nomes das colunas para corretude do projeto, já que agora criaremos nós e arestas, os quais terão relacionamentos.  
**a) Medicine Name -> drug**  
**b) Manufacturer -> manufacturer**  
**c) Side_Effects -> sideEffects (com os atributos id e name)**  
**d) Uses -> conditions**  

- **Atribuição de IDs**: para realizar a etapa de carga no Neo4j, precisamos atribuir um ID para cada nó do nosso banco de dados

- **Criar dataframes separados apenas com id e nome de cada nó**: criamos os dataframes df_drugs, df_condition, df_sideEffects e df_manufacturers que continham apenas o id e nome de cada nó para utilizarmos posteriormente.

- **Criação do banco de dados no Neo4j Sandbox**

- **Estabelecer a conexão entre o Databricks e o Neo4j**: realizamos a conexão entre o Databricks e o Neo4j utilizando a biblioteca `neo4j-contrib:neo4j-connector-apache-spark_2.12:4.0.1_for_spark_3`no cluster utilizado para executar o projeto.  

- **Criar os nós no Neo4j**: Criamos os nós no ?Neo4j a partir dos dataframes criados no terceiro item desta seção.

- **Criação das arestas**: Com os nós criados, fizemos uma manipulação dos nossos dataframes para obter os ids dos nós de origem e destino da nossa arestas, e assim criarmos as arestas  

