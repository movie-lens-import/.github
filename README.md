
# Movie Lens Import


## Referências ao Dataset Utilizado
O dataset utilizado para este projeto é baseado em um conjunto de dados de avaliações de filmes extraído do GroupLens.

- Fonte: https://grouplens.org/datasets/movielens/20m/
- Descrição: Contém informações sobre filmes, avaliações, tags e outros metadados relacionados a filmes."
- Formato: .CSV

## Descrição do Projeto
Este projeto tem como objetivo analisar e otimizar consultas e inserções de dados no banco de dados. Utiliza um banco de dados PostgreSQL para armazenar dados relacionados a filmes e avaliações, e fornece uma interface para consultar e manipular esses dados.

## Explicação Detalhada do Projeto
O projeto é dividido em várias partes principais:

### Banco de Dados
Utiliza PostgreSQL para armazenar dados sobre filmes e avaliações. As tabelas principais incluem:

- Movies: Contém informações sobre os filmes, como ID, título e gêneros.
- Ratings: Armazena as avaliações dos filmes, incluindo o ID do filme, a avaliação dada e a data da avaliação.

### Backend
Utiliza Python com Flask para fornecer uma API para acessar e manipular os dados do banco de dados.

#### Endpoints principais
- `/tasks` para listar tarefas
- `/tasks/{id}` para detalhes de uma tarefa
- `/movies/` para listar uma lista detalhada de filmes
- `/convert/` para receber o caminho de um arquivo .csv e gerar um job para conversão

### Frontend
Desenvolvido usando Next.js para fornecer uma interface de usuário para consultar e visualizar os dados. Além disso, sendo possível enviar um arquivo para o back-end converter.

### Processos de Importação de Dados
1. O front-end divide o arquivo em chunks
2. Ao final do upload, junta as chunks em um arquivo novamente e faz uma requisição para `/convert/` com o caminho do arquivo gerado
3. Um novo job é cadastrado em uma fila redis no back-end
4. Quando disponível, o worker irá iniciar o processo de conversão
5. Através do `copy_expert`do `psycopg` os dados do arquivo .csv serão copiados para o banco de dados
6. Ao final, a MATERIALIZED VIEW responsável pela consulta de filmes é atualizada com os novos dados.   

## Processo de Desenvolvimento

### 1. Divisão e Envio de Arquivos
#### Divisão de Arquivos em Chunks

- Implementação de funcionalidade no front-end para dividir grandes arquivos em chunks menores.
- Utilização das Server Actions do Next.js para enviar esses chunks de forma eficiente para o servidor.

#### Recepção e Processamento Inicial

- Preparação do back-end para receber os chunks enviados.
- Implementação de um processamento primitivo e síncrono para testar a recepção dos dados.


### 2. Implementação de Processamento Assíncrono
#### Detecção de Lentidão e Transição para Processamento Assíncrono

- Identificação de problemas de lentidão devido ao processamento síncrono.
- Introdução de uma fila Redis para gerenciar o processamento assíncrono dos chunks.

#### Integração com Redis

- Configuração de tarefas assíncronas na fila Redis para melhorar a eficiência.
- Desenvolvimento de uma estratégia para salvar e atualizar o estado das tarefas (queued, started, completed, failed).
#### Atualização do Front-End

- Implementação de uma interface no front-end para mostrar o progresso da conversão em tempo real, refletindo o status das tarefas.
### 3. Desenvolvimento da Listagem de Filmes
#### Implementação Inicial

- Criação de uma listagem simples dos filmes processados para verificar a integridade dos dados.
#### Melhorias de Desempenho

- Identificação de problemas de desempenho devido ao grande volume de dados.
- Introdução de Views Materializadas para otimizar as consultas e reduzir o tempo de resposta.
#### Paginação e Filtros

- Implementação de paginação para melhorar a navegação pelos dados.
- Adição de filtros para permitir uma busca mais refinada e eficiente entre os filmes.
