# UFSCar-CC-So-PMD2025-Grupo07

### Tema: Aplicação de recomendação de medicamentos

Integrantes:
- Daniella Yuka Hirosue - 813008
- Renan Yugo Ueda - 813346

## Fase Intermediária I – Planejamento: Definição da proposta

### Resumo e Introdução:

  O seguinte projeto será desenvolvido pelos membros Daniella e Renan, e tem como finalidade aplicar conceitos aprendidos durante as aulas de Processamento Massivos de Dados, tais como Apache Spark, Python, Manipulação e Transformação de Dados, Relacionamentos e Consultas em um Banco de Dados orientado a Grafos (Neo4j).
Para isso, a dupla escolheu trabalhar o projeto sobre um dataset de medicamentos, aplicando os conhecimentos acima referidos.
Nos tópicos a seguir, elaboramos mais detalhes do trabalho em questão.

### Minimundo e Objetivos:

  Por conta da grande competição farmacêutica, empresas elaboraram seus próprios medicamentos para ampla concorrência no mercado. Devido a isso a quantidade de remédios com a mesma finalidade existentes no mundo cresceu de maneira acelerada, possibilitando diversas opções de escolhas para os consumidores.

  Nesse sentido, este projeto foi elaborado com a principal finalidade de recomendar medicamentos criados para um mesmo sintoma ou doença, podendo filtrar os medicamentos de acordo com preço e efeito colateral que o usuário deseja evitar. Consultas complementares como “medicamentos de uma mesma empresa” também podem ser realizadas, mas o foco é a filtragem de medicamentos com finalidades semelhantes e efeitos colaterais.

![Web (1)](https://github.com/user-attachments/assets/147d9562-75da-4811-a6e1-f6e049b4ff25)
Imagem 1: Fluxograma de como os dados transitarão pela aplicação.

### Planejamento da execução do projeto

  Começando pelos medicamentos, a dupla encontrou a base de dados no site disponibilizado pela professora, o kaggle, no *link* (https://www.kaggle.com/datasets/pritipoddar/inventory-data-for-pharmacy-website-in-json-format). Nele, estão presentes informações diversas sobre medicamentos, tais como:
- `drugName` (nome do remédio)
- `manufacturer` (nome do fabricante)
- `image` (imagem do remédio)
- `description` (descrição do remédio)
- `consumeType` (modo de consumo, ex: oral)
- `expirydate` (data de validade)
- `price` (preço)
- `sideEffects` (efeitos colaterais)
- `disclaimer` (orientação)
- `category` (categoria, finalidade)
- `countInStock` (quantidade no estoque)

  No entanto, o projeto em questão não fará uso de todos os atributos presentes no dataset. Logo, será realizada uma etapa de transformação de dados utilizando o Spark para selecionar apenas os atributos `drugName`, `manufacturer`, `description`, `price`, `sideEffects` e `category` do *dataset* do kaggle e, a partir desses atributos, criaremos o seguinte esquema no Neo4j. 

Serão 4 tipos de nós: 
- `manufacturer` (com os atributos `id` e `name`)
- `drug` (com os atributos `id`, `drugName`, `description`, `price`)
- `sideEffects` (com os atributos `id`, `name`) 
- `category` (com os atributos: `id` e `name`)

E os relacionamentos serão:
- `develops` (relacionamento entre `manufacturer` e `drug`)
- `has_side_effect` (relacionamento entre `drug` e `sideEffects`)
- `belongs_to` (relacionamento entre `drug` e `category`)

Com esses nós, um consumidor pode requisitar, por exemplo, uma consulta que retorne medicamentos úteis (ou relacionados) a pressão sanguínea (blood pressure), trazendo diversas opções de consumo.

### Tecnologias
Para este projeto, as tecnologias escolhidas foram:

Apache Spark para extração e transformação, pois:
- Possui alta performance para processar grandes volumes de dados, pois ele executa as transformações de dados em memória principal. No cenário do projeto onde novos medicamentos são desenvolvidos constantemente, essa característica é bem útil para uma futura atualização da aplicação.
- Possui escalabilidade e flexibilidade, pois o Apache Spark tem a capacidade de ler arquivos de vários formatos diferentes.

Neo4j para a carga e as consultas, pois:
- O modelo em grafos permite que consultas que utilizam relacionamentos possam ser processadas de forma direta sem depender de junções complexas como em bancos de dados relacionais.
- É mais eficiente navegar entre relacionamentos em um modelo em grafos do que em bancos de dados relacionais.
- Possui estrutura de dados flexível que permite a adição de novos nós como modo de consumo ou principal composto ativo, por exemplo, sem a necessidade de reestruturar todo o banco de dados.

### Fonte de Dados
Os dados do projeto foram retirados de um *dataset* do Kaggle disponível em: (https://www.kaggle.com/datasets/pritipoddar/inventory-data-for-pharmacy-website-in-json-format ). O arquivo `.json` disponibilizado pelo *dataset* foi carregado no Apache Spark onde fizemos as transformações necessárias para o projeto.



