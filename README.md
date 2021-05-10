# Tabelas-otimizadas-Hive
O objetivo do projeto é trabalhar com a criação de tabelas otimizadas utilizando o framework Hive em conjunto com HDFS. Realizado em docker.

###✍️ 1- Iniciando os serviços.
* O primeiro passo do projeto é iniciar os serviços, com o comando `docker-compose up -d`. Como é possível analisar, todos os serviços estão disponíveis.
* Acessar o hive pelo `docker exec -it hive-server bash` depois acesso o cliente beeline com o `beeline -u jdbc:hive2://localhost:10000`   
* Estar 'logado' no banco requerido através do `use nomedobanco`, no caso criei o `use vandisney`.
* Verificando a disponibilidade dos dados através selecionando os 10 primeiros registros da tabela pop.
![imagem1](https://github.com/vandisney/Tabelas-otimizadas-Hive/blob/main/imagens/imagem1.png)

###✍️ 2- Criar a tabela pop_parquet no formato parquet e inserir os dados da tabela pop na pop_parquet.
* O que é Parquet? É possível entender Parquet como um formato de armazenamento baseado em coluna para o Hadoop.
* Para isso, vamos aplicar o código create table pop_parquet(zip_code, total_population int, median_age float, total_males int, total_females int, total_households int, average_households_size float) stored as parquet.
* Para inserir os dados da tabela pop na tabela pop_parquet foi utilizado a consulta `insert into pop_parquet select * from pop;`
![imagem2](https://github.com/vandisney/Tabelas-otimizadas-Hive/blob/main/imagens/imagem2.png)

###✍️ 3- Verificação da tabela parquet criada.
 Para isso foi necessário contar os registros da tabela pop `select count(*) from pop` e da tabela pop_parquet `select count(*) from pop_parquet`, estas apresentaram o mesmo valor como mostra a figura.
![imagem3](https://github.com/vandisney/Tabelas-otimizadas-Hive/blob/main/imagens/imagem3.png)

###✍️ 4- Selecionar os 5 primeiros registros da tabela pop_parquet.
O objetivo agora é saber como está a consistência dos dados da tabela criada, utilizando a consulta `select * from pop_parquet limit 5;` é possível observar os dados perfeitamenete encaixados.
![imagem4](https://github.com/vandisney/Tabelas-otimizadas-Hive/blob/main/imagens/imagem4.png)

###✍️ 5- Criando tabela com compressão tipo SNAPPY para ler os dados da tabela pop.
 Criação da tabela com a compressão, para isso vamos utilizar o comando `create table pop_parquet_snappy(zip_code int, total_population int, median_age float, total_males int, total_females int, total_households int, average_households_size float) stored as parquet tblproperties('parquet.compress'='SNAPPY');`
Após a criação, vamos analisar a descrição dessa tabela com o comando `desc formatted pop_parquet_snappy;`Como é possível observar na imagem abaixo, a última linha mostra que os parâmetros da tabela tem um parquet do tipo compressão SNAPPY.
![imagem5](https://github.com/vandisney/Tabelas-otimizadas-Hive/blob/main/imagens/imagem5.png)

###✍️ 6- Verificando a consistência da tabela pop_parquet_snappy.
 * Selecionar os 5 primeiros registros da tabela pop_parquet_snappy. `select * from pop_parquet_snappy limit 5;`
 * Para isso foi necessário contar os registros da tabela pop_parquet_snappy. `select count(*) from pop_parquet_snappy;`
 ![imagem7](https://github.com/vandisney/Tabelas-otimizadas-Hive/blob/main/imagens/imagem7.png)

###✍️ 7- Comparando as tabelas
* Comparar as tabelas pop, pop_parquet e pop_parquet_snappy no HDFS, para isso deve-se acessar o HDFS com o `docker exec -it namenode bash`.
> Para realizar essa questão vamos comparar as tabelas, verificando a consistência de cada uma e analisando o conteúdo com o comando `hdfs dfs -ls /user/hive/warehouse/vandisney.db`
Para verificar os dados e o seus tamanhos, vamos utilizar o comando `hdfs dfs -du -h /user/hive/warehouse/vandisney.db`
![imagem6](https://github.com/vandisney/Tabelas-otimizadas-Hive/blob/main/imagens/imagem6.png)
