# UFSCar-CC-So-PMD2025-Grupo07

### Tema: Aplicação de recomendação de medicamentos

Integrantes:
- Daniella Yuka Hirosue - 813008
- Renan Yugo Ueda - 813346

## Fase Intermediária – Planejamento: Definição da proposta

### Resumo e Introdução:

  O seguinte projeto será desenvolvido pelos membro Daniella e Renan, e tem como finalidade aplicar conceitos aprendidos durante as aulas de Processamento Massivos de Dados, tais como Apache Spark, Python, Manipulação e Transformação de Dados, Relacionamentos e Consultas em um Banco de Dados Orientado a Grafos (Neo4j).
Para isso, a dupla escolheu trabalhar o projeto sobre dois *datasets* de medicamentos, aplicando os conhecimentos acima referidos.
Nos tópicos a seguir, elaboramos mais detalhes do trabalho em questão.

### Minimundo:

  Devido à grande competição no setor farmacêutico, empresas passaram a desenvolver cada vez mais medicamentos com o objetivo de se destacar no mercado. Como consequência, houve um aumento acelerado na quantidade de remédios com diversas finalidades, ampliando significativamente as opções disponíveis aos consumidores.

Diante desse cenário, torna-se interessante e necessário oferecer ao público uma forma eficiente de filtrar essas opções, sejam por tratamentos de algum sintoma, efeitos colaterais ou até mesmo fabricante (se disponibilizado esta informação). Para isso, foram utilizados dois conjuntos de dados disponíveis no site Kaggle.

### Fonte de Dados:
  Os dados do projeto são provenientes de dois arquivos csv, disponibilizados nos seguintes *links*:

- *dataset1*: https://www.kaggle.com/datasets/singhnavjot2062001/11000-medicine-details?select=Medicine_Details.csv
  
- *dataset2*: https://www.kaggle.com/datasets/shudhanshusingh/250k-medicines-usage-side-effects-and-substitutes

Embora os dois *datasets* possuam atributos diversos, o projeto em questão não fará uso de todos eles. Logo, a dupla aplicará uma etapa de transformação utilizando o Spark para sintetizar e adaptar os dados em apenas um *dataset* com quatro atributos, sendo eles `drugName`, `manufacturer`, `sideEffects` e `conditions` (este último seria o sintoma/doença/infecção que o remédio tratará).

**OBS**: no *dataset2*, o campo `manufacturer` (fabricante) não está presente. 
O grupo procurou outras bases de dados que possuíssem os mesmos atributos principais do projeto, mas não obtiveram sucesso. 
Ainda assim, mesmo que alguns medicamentos fiquem sem essa informação, a dupla considera o fabricante um atributo interessante para análise e, por esse motivo, decidimos manter em nosso projeto.

A partir deste novo *dataset* formaremos os nós no Neo4j. Resultando no seguinte *schema*:

<img width="981" height="440" alt="image" src="https://github.com/user-attachments/assets/a81c4d7d-1ea4-4d0a-bd6d-7fd3a5ba608e" />
Imagem 1: Schema do Neo4j.

Serão 4 tipos de nós: 
- `Manufacturer` (com os atributos `id` e `name`)
- `Drug` (com os atributos `id`, `name`)
- `sideEffect` (com os atributos `id`, `name`) 
- `Condition` (com os atributos: `id` e `name`)

E as arestas serão:
- `DEVELOPS` (aresta entre `manufacturer` e `drug`)
- `HAS_SIDE_EFFECT` (aresta entre `drug` e `sideEffects`)
- `TREATS` (aresta entre `drug` e `condition`)

![Web (4)](https://github.com/user-attachments/assets/e1fa67bc-b29a-4d4d-a967-da7c3ffae82d)
Imagem 2: Fluxograma de como os dados transitarão pela aplicação.

### Objetivos:

  O objetivo principal do projeto é possibilitar a visualização de medicamentos com a **mesma finalidade terapêutica** no Neo4j.
Além disso, é possível que o usuário filtre os resultados com base em outros critérios como **fabricante** e **efeitos colaterais**, os quais podem ser evitados pelo consumidor.

Alguns exemplos de consulta que o sistema oferece:
  - Quais medicamentos tratam condições relacionadas à uma condição "x"?
  - Quais medicamentos são do mesmo fabricante?
  - Quais medicamentos possuem o efeito colateral “y”?
  - Quais fabricantes produzem medicamentos que causam um efeito colateral "y"?

### Tecnologias:
Para este projeto, as tecnologias escolhidas foram:

Apache Spark (na plataforma Databricks) para extração e transformação, pois:
- Possui alta performance para processar grandes volumes de dados, pois ele executa as transformações de dados em memória principal. No cenário do projeto onde novos medicamentos são desenvolvidos constantemente, essa característica é bem útil para uma futura atualização da aplicação.
- Possui escalabilidade e flexibilidade, pois o Apache Spark tem a capacidade de ler arquivos de vários formatos diferentes.

Neo4j para a carga e as consultas, pois:
- O modelo em grafos permite que consultas que utilizam relacionamentos possam ser processadas de forma direta sem depender de junções complexas como em bancos de dados relacionais.
- É mais eficiente navegar entre relacionamentos em um modelo em grafos do que em bancos de dados relacionais.
- Possui estrutura de dados flexível que permite a adição de novos nós como modo de consumo ou principal composto ativo, por exemplo, sem a necessidade de reestruturar todo o banco de dados.

### Ajustes da Etapa Anterior:
- Remoção dos atributos `price` e `description`:
    - O motivo dessas remoções se dá por conta da indisponibilidade de *datasets* com os mesmos atributos para realização de cruzamento.
- Alteração do nome `category` para `condition`, e `belongs_to` para `treats`
- Adição de um novo *dataset* para complementar o *dataset* inicial e aumentar a complexidade do projeto.
- Mudança do Fluxograma (Imagem 2) para acompanhar as alterações acima.

### Desenvolvimento:
A seguir serão apresentadas as ações tomadas pela dupla para realização do projeto.
Você pode conferir a execução e detalhamento do mesmo em formato de notebook no arquivo `Databricks_ETL` presente no Github do projeto. 
Enfatizamos que, faremos um breve resumo abaixo de como o grupo planejou a execução, para mais detalhes acesse o arquivo disponibilizado, por gentileza.

**1) Upload de dados**: Inicialmente, com os *datasets* em mãos, faremos o *upload* dos mesmos na plataforma Databricks, onde utilizaremos a tecnologia do PySpark(Apache Spark).

**2) Tratamento individual do df1(*dataset1*)**:
    
- **Valores Nulos**: Verificamos se existe alguma tupla com valores nulos em pelo menos um dos campos principais: `Medicine Name` (`drug`), `Uses`(`conditions`) e `sideEffects` (`manufacturer` como dito antes pode conter campos nulos em nosso projeto, por isso não é verificado).  
   
- **Dados Duplicados**: Para esta verificação, basta analisarmos o nome do medicamento. Isso porque podem existir medicamentos com a mesma composição (não analisada em nosso projeto) e com a mesma finalidade.  

- **Remodelar coluna `Uses` com uma lista de usos (as finalidades dos medicamentos)**: Separar a cadeia de caracteres em uma lista que conterá cada uso (finalidade) do medicamento. Isso será útil para passar os dados ao Neo4j posteriormente

- **Remodelar coluna de efeitos colaterais com uma lista de `Side_effects`**: o motivo é o mesmo que o item anterior.

**3) Tratamento individual do df2(*dataset2*)**:    

- **Dados Duplicados**: Para esta verificação, basta analisarmos o nome do medicamento. Isso porque podem existir medicamentos com a mesma composição (não analisada em nosso projeto) e com a mesma finalidade.

- **Remodelar colunas de efeitos colaterais**: Os efeitos colaterais estão separados por colunas e estão bem estruturados. Dessa maneira, criaremos uma lista contendo todos os efeitos

- **Remodelar colunas de usos**: O motivo é o mesmo que o item anterior.

- **Remover Dados Nulos**: Analisamos se alguma coluna possui algum campo nulo.

**4) Junção dos dois *dataframes***:  

- **Adição da coluna `Manufacturer` em df2**: adição desta coluna para padronização de *dataframes*. Os campos foram preenchidos com valores nulos.

- **Renomear colunas de df2**: novamente, para padronizar os dados, e possibilitar um cruzamento de dados, mantemos nomes de colunas iguais para os dois *dataframes*.

- **Remover medicamentos de df2 que já estão contidos em df1**: Mesmo que df1 tenha menos dados, ele terá prioridade já que contém os campos de `Manufacturer`. Aqui removeremos de df2 medicamentos já presentes em df1 para evitar duplicidade.

- **União dos dados**: Com df1 e df2 tratados, cruzamos os dados.

**5) Verificações Finais antes da etapa de carga**:

- **Remoção de Espaços**: Remoção de espaços antes e depois de cada string, em todos os campos de todas as colunas.  

- **Eliminar Duplicatas**: Verificar se existem medicamentos duplicados novamente.

**6) Carga de dados Databricks para Neo4j** Preparação de nós e relacionamentos no próprio Pyspark para alimentarmos a base de dados diretamente para o Neo4j.

- **Renomeação de Colunas**: Redefinir os nomes das colunas para corretude do projeto, já que a partir deles criaremos nós e arestas.  
  - `Medicine Name` -> `drug` 
  - `Manufacturer` -> `manufacturer`  
  - `Side_Effects` -> `sideEffect` 
  - `Uses` -> `condition`  

- **Atribuição de *IDs***: para realizar a etapa de carga no Neo4j, precisamos atribuir um *ID* para cada nó do nosso banco de dados.

- **Criar *dataframes* separados apenas com *id* e nome de cada nó**: criamos os *dataframes* `df_drugs`, `df_condition`, `df_sideEffects` e `df_manufacturers` que contém apenas o *id* e nome de cada nó para utilizarmos posteriormente.

- **Criação do banco de dados no Neo4j Sandbox**

- **Estabelecer a conexão entre o Databricks e o Neo4j**: realizamos a conexão entre o Databricks e o Neo4j utilizando a biblioteca `neo4j-contrib:neo4j-connector-apache-spark_2.12:4.0.1_for_spark_3` no cluster utilizado para executar o projeto.  

- **Criar os nós no Neo4j**: Criamos os nós no Neo4j a partir dos *dataframes* criados no terceiro item desta seção.

- **Criação das arestas**: Com os nós criados, fizemos uma manipulação dos nossos *dataframes* para obter os *ids* dos nós de origem e destino da nossa arestas, e assim criarmos as arestas `DEVELOPS`, `HAS_SIDE_EFFECT` e `TREATS`.

### Estatísticas do Banco de Dados
Após a etapa de carga dos dados para o Neo4j, temos a seguinte quantidade de nós e arestas:

Nós
- `Drug`: 223152 nós
- `Condition`: 1195 nós
- `sideEffect`: 1190 nós
- `Manufacturer`: 759 nós

Arestas
- `DEVELOPS`: 11498 arestas
- `HAS_SIDE_EFFECT`: 909996 arestas
- `TREATS`: 328037 arestas

### Consultas realizadas
Após a etapa de carga dos dados para o Neo4j, pudemos realizar as consultas definidas na Seção Objetivos deste arquivo. A seguir, temos os *scripts* das consultas em Cypher que correspondem as consultas definidas anteriormente.

  - Quais medicamentos tratam condições relacionadas à pressão sanguínea(*blood pressure*)?
    ```cypher
    MATCH (d:Drug)-[:TREATS]->(c:Condition)
    WHERE toLower(c.name_condition) CONTAINS "blood pressure"
    RETURN d.name_drug AS medicamento, c.name_condition AS condicao
  
<img width="1318" height="461" alt="image" src="https://github.com/user-attachments/assets/6893068e-1cfd-4e4f-94c3-27e6ec0b214b" />


  - Quais medicamentos são do mesmo fabricante?
    ```cypher
    MATCH (m:Manufacturer)-[:DEVELOPS]->(d:Drug)
    WHERE m.name_manufacturer = "Pfizer Ltd"
    RETURN m.name_manufacturer AS fabricante, d.name_drug AS medicamento

  <img width="1417" height="762" alt="image" src="https://github.com/user-attachments/assets/a783426c-7c8b-4b83-90fb-faae745cff89" />

    
  - Quais medicamentos possuem o efeito colateral indigestão?
    ```cypher
    MATCH (d:Drug)-[h:HAS_SIDE_EFFECT]->(s:sideEffect)
    WHERE s.name_sideEffect = "Indigestion"
    RETURN d.name_drug AS medicamento, s.name_sideEffect AS efeito_colateral

  <img width="1779" height="693" alt="image" src="https://github.com/user-attachments/assets/a8c56da1-f4ea-4e81-9a32-0def34fc2c8a" />

  - Quais fabricantes produzem medicamentos que causam dor abdominal?
    ```cypher
    MATCH (m:Manufacturer)-[:DEVELOPS]->(d:Drug)-[h:HAS_SIDE_EFFECT]->(s:sideEffect)
    WHERE s.name_sideEffect = "Abdominal pain" 
    RETURN m.name_manufacturer AS fabricante, d.name_drug AS medicamento, s.name_sideEffect AS efeito_colateral
  <img width="1835" height="695" alt="image" src="https://github.com/user-attachments/assets/99275321-b00c-49b6-ae99-10e1a9125117" />


### Considerações Finais
Ao longo do desenvolvimento deste projeto, tivemos a oportunidade de aprofundar nossos conhecimentos práticos nas tecnologias estudadas em sala de aula, especialmente o Spark (via Databricks) e o banco de dados orientado a grafos Neo4j. A aplicação prática dessas ferramentas nos permitiu compreender de forma mais concreta as diferenças entre bancos relacionais e bancos de grafos, principalmente com a parte de modelagem e desempenho das consultas.

As principais dificuldades surgiram nas etapas de transformação e carga dos dados. Durante a transformação, enfrentamos o desafio de padronizar colunas como sideEffects e uses, que possuíam estruturas distintas nos diferentes dois *datasets*. Já na etapa de carga, o principal obstáculo foi o grande volume de dados: um dos relacionamentos possuía quase 1,5 milhão de arestas, o que tornou o processo de inserção extremamente demorado onde 10 mil tuplas demorava cerca de 20 minutos para ser inserida no Neo4j. Por conta disso, não onseguimos inserir todos os dados dentro do tempo disponível.

Apesar dessas dificuldades, o trabalho foi essencial para consolidar o entendimento das tecnologias utilizadas. E pudemos ver na prática, a eficiência do Neo4j em consultas baseadas em relacionamentos: uma consulta envolvendo aproximadamente 900 mil arestas — que exigiria até quatro joins em um banco relacional e resultaria em alto custo computacional — foi executada no Neo4j em apenas 31 milissegundos.

Em resumo, a experiência proporcionada por este projeto foi valiosa tanto do ponto de vista técnico quanto prático, contribuindo significativamente para o nosso aprendizado em processamento massivo de dados e modelagem em bancos orientados a grafos.


