DATABASES - Mysql, Postgres e MongoDb
==========

Mysql
------------

Ip
- $user= root #solitamente
- $PWD=SYSDAT
- $HOST= localhost #solitamente
- $DB=nomedatabase #inserire il nome del database de utilizzare

Questi sono i comandi per accedere al database

	$mysql -u NOMEUTENTE -p -h IPHOST
	
una volta eseguito l'accesso

	SHOW DATABASES; #per mostrare i database presenti sul host
	USE nomedatabase; #per selezionare il database da utilizzare
	SHOW TABLES; # per mostrare l'elenco delle tabelle
	SELECT * FROM ..... WHERE LIMIT\G
	#quando faccio una select e richiedo una visualizzazione facilmente leggibile, devo inserire a fine riga il \G

### Phpmyadmin

Se voglio installare phpmyadmin per potervi accedere dal browser del sistema host devo eseguire i seguenti passaggi:

	yum install phpmyadmin

quindi configurare il file presente in `/etc/httpd/conf.d/phpMyAdmin.conf` e sostituire il contenuto tra `<Directory>....</Directory>` con 

	<Directory /usr/share/phpMyAdmin/>
		AddDefaultCharset UTF-8
		<IfModule mod_authz_core.c>
		 # Apache 2.4
		 <RequireAny>
			#Require ip 127.0.0.1
			#Require ip ::1
			Require all granted
		 </RequireAny>
		</IfModule>
		<IfModule !mod_authz_core.c>
			# Apache 2.2
			Order Deny,Allow
			Deny from All
			Allow from 127.0.0.1
			Allow from ::1
		</IfModule>
	</Directory>

quindi vado a modificare il file `/etc/phpMyAdmin/config.inc.php` andando ad inserire come variabili per User e Password quelle di Mysql.

	...
	$cfg['Servers'][$i]['user']          = 'root';          // MySQL user
	$cfg['Servers'][$i]['password']      = 'SYSDAT';          // MySQL password (only needed
                                                    // with 'config' auth_type)
	...



POSTGRESS
--------------
Per accedere al db postgres e piu facile riuscirci se sono un utente root.
	
	$su -
	
Quindi diventato root posso accedere all'utente postgres con il comando [link](#abilitiamo-i-repository)

	#su - postgres

Diventato infine utente postgress, me ne rendo conto se lancio il comando `$pwd` che restituisce _/var/lib/pgsq_ posso quindi accedere al database lanciando 

	-$psql

accedo quindi alla applicazione.
Noto di aver effettuato l'accesso perchè la label è diventata `postgres=#`

A questo punto per visualizzare quali DB sono presenti devo richiedere una lista

	postgres=# \l (\elle)

Questo è un elenco dei database presenti di base in postgres

|Nome      |Proprietario|Codifica| Ordinamento|Ctype       |Privilegi di accesso               |
|----------|------------|--------|------------|------------|-----------------------------------|
|crm       |postgres    | UTF8   |it_IT.UTF-8 |it_IT.UTF-8 |
|postgres  |postgres    | UTF8   |it_IT.UTF-8 |it_IT.UTF-8 |
|template0 |postgres    | UTF8   |it_IT.UTF-8 |it_IT.UTF-8 |=c/postgres + postgres=CTc/postgres|
|template1 |postgres    | UTF8   |it_IT.UTF-8 | it_IT.UTF-8|=c/postgres + postgres=CTc/postgres|


Pe elezionare il db crm come su Mysql uso \c NOMEDB

	postgres=# \c

Per visualizzare le tabelle uso \dt o \dt+ per visualizzare piu dati.

	crm=#\dt+

Per uscire uso il \q

	crm=#\q

Per cancellare un database
	
	postgres=# drop database NOMEDB;
	
Per creare un database
	
	postgres=# create database NOMEDB;

Posso evitare di diventare utente postgres ma fare il backup del db indicando nel compando di dump l'opzione utente.

	pg_dump -U postgres NOMEDB > LOCATION/NOMEDB.db

	psql NOMEDB < LOCATION/NOMEDB 
per l'upload dei file.

**Quando voglio vedere la password di postgress di un utente del crm, devo recuperarla dal fil di configurazione del db presente in _/var/www/html/crm-cliente/protected/config/cliente/db.php**


MongoDB
---------------

MongoDB si differenzia da gli altri DB poichè si tratta di un database NoSql, non è composto da tabelle con righe e colonno ma è composto da _collections_ ovvero collezioni di documenti.
Un database NoSql significa che, a differenza di Mysql e PostgreSQL non supporta le query SQL per gestire i dati registrati.
Le collections  ed i documenti in cui MongoDB salva i dati hanno una struttura similare a Json. Ogni documento infatti, è formato da una serie di elementi e loo si puo vedere con un oggetto di Bson (Json).
I dati vengono passati in Json, tramutati in Bson e ritornati in Json per essere elaborati. Ogni elemento è composto da due campi strutturato come <code>campo:valore (field:value).</code>

**Bisogna fare attenzione poichè non ci sono schemi e strutture predefinite.**

Per quel che riguarda MongoDB e la sua installazione/aggiornamento, fare riferimento alla [guida ufficiale di Marco](./Mongodb.md)

