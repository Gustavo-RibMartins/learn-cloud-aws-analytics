# Amazon Athena

Serviço Serverless de queries interativos para S3 (SQL). Não é necessário carregar dados, eles permancem no S3.

Funciona com a engine do `prestoDB` e suporta muitos formatos de dados como CSV, TSV, JSON, ORC, Parquet, Avro e formatos comprimidos como Snappy, Zlib, LZO, Gzip.

Exemplos de uso:

* Queries ad-hoc
* Consulta de staging data antes de carregá-los no Redshift
* Analise de logs do CloudTrail, CloudFront, VPC, ELB, etc no S3
* Integração com Jupyter, Zeppelin, RStudio notebooks
* Integração com QuickSight
* Integração via ODBC / JDBC com outras ferramentas de visualização

---

## Athena Workgroups

Organiza usuários, times, aplicações e cargas de trabalho em workgroups.

Possibilita controlar o acesso a queries e controlar o custo por workgroup.

Integrado com IAM, CloudWatch, SNS.

Cada workgroup tem seu próprio:

* Query history
* Limites de dados (limite de quantos dados as queries podem scanear)
* IAM Policies
* Configurações de criptografia

---

## Custo

* Pay-as-you-go: $5 / TB scaneado.
* Queries com sucesso ou canceladas são cobradas, queries com falha não
* Sem custo para DDL (CREATE / ALTER / DROP)
* Economia usando formato colunar de dados
    * Parquet, ORC
    * Economize 30-90% e tenha uma performance melhor

---

## Anti-patterns

* Visualizações/ reports: utilize o quicksight
* ETL: utilize o glue

---

## Performance

* Utilize formato colunar
* Pequeno número de arquivos grandes performa melhor que um grande número de arquivos pequenos
* Utilize partições

---

## Athena ACID Transactions

Desenvolvido pelo `Apache Iceberg`.

Apenas adicione `'table_type' = 'ICEBERG'` no seu comando de CREATE TABLE.

Compatível com EMR, Spark, qualquer coisa que suporte o formato iceberg table.

**Time travel operations**: recupera dados recentemente deletados com um SELECT statement

---

## Create Table as Select (CTAS)

Cria uma nova tabela a partir do resultado de uma query. Pode ser usada para criar uam tabela nova como resultado de um subset de outra.

Também pode ser usada para converter o formato de dados.

```sql
-- Parquet table
CREATE TABLE new_table
WITH (
 format = 'Parquet',
 write_compression = 'SNAPPY')
AS SELECT *
FROM old_table;

-- ORC table
CREATE TABLE my_orc_ctas_table
WITH (
 external_location =
's3://my_athena_results/my_orc_stas_table/',
 format = 'ORC')
AS SELECT *
FROM old_table;
```

---

## Amazon Athena para Apache Spark

Permite executar Jupyter Notebooks com Spark no Athena Console. Totalmente serverless.