# Conversione database Mysql Mongo

Facciamo un test di conversione dei database da Mysql a Mongo. Come database di prova utilizziamo il DB dell'Hotel Flora
La struttura è composta da 3 database differenti:
- flmdbedoc_files: tot. righe 1.247 - 89.6 MiB - 
- flmdbedoc_indici: tot. righe 537 - 15 MiB
- flmdbedoc_tabstampe: tot. righe 0 - 16 KiB

Come si evince dalle dimensioni del DB andiamo a lavorare principalmente sul Db dei files per ottimizarlo e provare a spostarlo successivamente su Mongo.

## Mysql
Seguendo la guida di ottimizzazione delle tabelle presente su HTML.it come prima cosa analizziamo il DB _information_schema_ e controlliamo su questo db la corrispondenza tabella schema e nome tabella.
### Iniziamo l'analisi del db
Supponendo di volere analizzare il contenuto di una tabella, per capire quanto spazio stia occupando su disco e verificare che InnoDB la stia effettivamente comprimendo, possiamo adoperare il comando sotto riportato:
```sql
SELECT
    table_name AS 'Table',
    data_length + index_length 'Size (Bytes)',
    ROUND(((data_length + index_length) / 1024 / 1024), 2) `Size (MB)`
FROM information_schema.TABLES
WHERE table_schema = " flmdbedoc_files"
    AND table_name = "filesdati";
```
oppure per vederle raggruppate
```sql 
SELECT 
    table_schema "DB Name",
    Round(Sum(data_length + index_length) / 1024 / 1024, 1) "DB Size in MB"
 FROM information_schema.tables
 GROUP BY table_schema;
```
Con questa query invece vediamo la dimensione delgi indici
```sql
SELECT table_schema as database_name,table_name,round(index_length/1024/1024,2) as index_size
FROM information_schema.tables
WHERE table_type = 'BASE TABLE'
     and table_schema not in ('information_schema', 'sys','performance_schema', 'mysql')
     and table_schema = '<your database name>'
ORDER BY index_size desc;
```
### Iniziamo ad eliminare le righe che non ci interessano
Ora possiamo iniziare ad eliminare tutte le tabelle che occupano dello spazio ma che in tutto questo periodo non son utilizzate.
```sql
SELECT table_schema,table_name,create_time,update_time from information_schema.tables 
WHERE table_schema not in ('information_schema','mysql') 
	and engine is not null and ((update_time < (now() - interval 1 day)) or update_time is NULL) 
LIMIT 5;
```
Con la query sopra indicata andiamo ad ottenere un risultato in cui si vedono tutte le tabelle che non anno ricevuto un update, quindi non sono state utilizzate. Nell'esempio viene indicato `interval 1 day` ma questa voce puo essere agiornata in `interval 1 month` oppure `interval 1 year` per modificare i valori.

#### Abilitiamo quindi le colonne Comprsse
The row format of a table determines how its rows are physically stored, which in turn can affect the performance of queries, DML operations, and disk usage.

Il passaggio da poter eseguire è la duplicazione della tabella
- Creo quindi una nuova tabella:
    ```sql
    CREATE TABLE nuova LIKE filesdati
    ```
- ne modifico il formato di compressione 
    ```sql
    ALTER TABLE nuova ROW_FORMAT=COMPRESSED;
    ```
    potremmo ottenere come risposta
    _Query OK, 0 rows affected (0,07 sec)_
    _Records: 0  Duplicates: 0  Warnings: 2_
    ed nei warning
    ```sql
    SHOW WARNING;
    ```

    | Level   | Code | Message                                                               |
    |---------|------|-----------------------------------------------------------------------|
    | Warning | 1478 | InnoDB: ROW_FORMAT=COMPRESSED requires innodb_file_format > Antelope. |
    | Warning | 1478 | InnoDB: assuming ROW_FORMAT=COMPACT.                                  |
    
- Controlliamo quindi che versione di file format abbiamo con 
    ```sql
    show variables like "%innodb_file%";
    ```
    Ottenendo qualcosa di simile a
    
    | Variable_name            | Value    |
    |--------------------------|----------|
    | innodb_file_format       | Antelope |
    | innodb_file_format_check | ON       |
    | innodb_file_format_max   | Antelope |
    | innodb_file_per_table    | ON       |
    
    Quindi per risolvere questo problema dobbiamo modificare il file format in Barracuda:
    ``` sql
    SET GLOBAL innodb_file_format = barracuda;
    SET GLOBAL innodb_file_format_max = barracuda;
    ```
    quindi possiamo ripetere il comando del punto precedente.
    Il formato di riga COMPRESSO è simile al formato di riga COMPATTO, ma le tabelle che utilizzano il formato di riga COMPRESSO possono memorizzare ancora più dati nelle pagine di overflow rispetto alle tabelle che utilizzano il formato di riga COMPATTO.
    Ciò consente di memorizzare i dati in modo più efficiente rispetto alle tabelle che utilizzano il formato di riga COMPACT, soprattutto per le tabelle contenenti colonne che utilizzano i tipi di dati VARBINARY, VARCHAR, BLO e TEXT.
- Controlliamo lo stato del DB Engine
    ```sql
    SHOW ENGINE INNODB status;
    ```
- copiamo i dati nella nuova tabella
    ```sql
    INSERT INTO nuova SELECT * FROM filesdati;
    ```

##### Risorse Utilizzate
> https://www.forknerds.com/reduce-the-size-of-mysql/
> https://www.html.it/pag/65479/analisi-e-ottimizzazione-della-memoria/
> https://stackoverflow.com/questions/30635603/what-does-table-does-not-support-optimize-doing-recreate-analyze-instead-me
> https://dba.stackexchange.com/questions/14246/innodb-file-format-barracuda

## MongoDb



