Su SAAS esistono diversi computer ai quali l'utene si collega tramite Desktop Remoto per poter eseguire il client Genius perchè tutto funziona solo se è all'interno della lan e con la stessa classe di IP.
Lesecuzione di Genius avviene tramite TS Plus che virtualizza il singolo programma e lo ripropone al cliente come se fosse un semplice software eseguito in locale. 

La finestra di Genius che viene eseguita è in realtà fisicamente eseguita sui computer Blu (rivedi lo schema di Mauro) che essendo in lan con i server Neri riesce a raggiungere i databse perchè si trovano nella stessa classe di IP.

I computer neri non sono altro che server ProxBox che fanno girare delle MV con installato linux. Ogni MV contiene al massimo 4/5 clienti[^1]. Le macchine 131 e 132 da 1TB con max 10 clienti.

Quando voglio configurare all'interno di una macchina virtuale un secondo cliente, il reparto HW creera gia la macchina virtuale con le cartelle dedicate al cliente ma vuote.
1. Sarà mio compito riempire le cartelle con rsync prendendole dalla Master.
2. Dovrò poi andare a configurare l'alias dedicato al cliente e la configurazione del programma per il cliente

### Creiamo un nuovo ambiente per un nuovo clinete.

La macchina master dalla quale partire è la 192.0.200.115


#### Configurare l'alias.

Per configuare l'alias del cliente devo usare il comando

	$/usr/Acucorp/Acucbl1020/bin/acurcl -alias
	
Si aprirà quindi un menu con le voci per creare un uovo alias. Aggiungo quindi il nuovo alias che deve avere lo stesso nome della directory dove dovrà lavorare e che comunque dovrò indicare.
Quindi come Command line inseriro il link con destinazione il file di configurazione _cblconf_
Questi valori vengono presi dalla wiki.




#### Configurare Genius7

Dovrò quindi aprire e configurare il file di Genius7 con i dati relativi alla nuova installazione.
Per varlo velocemente, quindi sostituire l'alias _/u/usr/genius7_ con quello nuovo ad esempio _/u/usr/hotelnuovo_

Lo faccio con il comando sed

	$sed -i 's/genius7/hotelnuovo/g' ../cblconfi
	
A questo punto se setto l'alias nella configurazione del client di Genius7 l'applicativo dovrebbe partire.

Quando facciamo un aggiornamento o un backup, sulla MV in SAAS gira _Ossec_ che al momento crea problemi.
QUindi bisogna terminarlo con

	systemctl stop ossec -hids


#### Configurare un nuovo CRM per i punti vendita (PMS) 

Per configurare un nuovo crm devo seguire tre passaggi

- copiare la cartella del crm e rinominarla per il cliente: crm to crm-hotelnome
- creare un nuovo db in postgress  con lo stesso nome della cartella crm-hotelnome e copiarci all'interno il dump del db crm
- posizionarmi nella cartella _/var/www/html/crm-hotelnome/protected/config_ e copiare il contenuto della cartella _cliente_ nella cartella _hotelnuovo_
- copiare il file _GeniusWeb.sh_ presente in _/var/www/html/Acucgi/_ con il nome del cliente _GeniusWeb_hotelnome.sh_ 
- configurare il crm nelle seguenti posizioni
	- _/var/www/html/crm-hotelnome/protected/config/hotelnuovo/params.php_ inserendo in tutte le password il valore FERRARI21 cd (usando sed 's/\*\*\*\*\*\*\*/FERRARI21/g').
	- nello stesso file modificare la variabile _path_ nell'oggetto _myUpload_ sostituendo la voce _../files/uploads/_ con _../files/hotelnuovo_
	- modificare il file _/var/www/html/crm-hotelnome/protected/config/hotelnuovo/db.php_ inserendo come database il nome del database del cliente _hotelnuovo_
	- modificare il file _/etc/httpd/conf/httpd.conf_ inserendo il valore cliente nelle varibili di ambiente

	SetEnv CRMNAME cliente
    SetEnv CRMCONFIG cliente
    SetEnv CRMLOGO 'crm.png'
	
	questa modifica è necessaria solo se su una macchina ho un solo ambiente. Se sulla stessa macchina ho piu ambienti, devo modificare il virtual host
	
	-  modifica all'interno dell'array (solo su SAAS) l'indirizzo Ip della macchina al posto di localhost.
	
	array(
		'active' => true,
        'type'=>'pms' ,    // 'ldap' oppure 'pms'
        'server' =>'http://localhost/Acucgi/GeniusWeb_hotelnome.sh',
        'params' =>array(
        'funzione'     => "verifica_utente_password",
		),
	),

- assegnare alla cartella creata in _/var/www/html/_ i permessi completi _777_
Per testare il funzionamento mi colleghero allora al crm con il collegamento
http://xxx.xxx.xxx.xxx/crm-hotelnome/index.php



#### Creare un ambiente Helite: crm documentale

Per creare un ambiete CRM helite che si occupa della parte documentale (vedi schema creato con Mauro) sono sufficienti alcuni passaggi:

- Mi collego alla macchina dedicata: 192.168.250.9
- Accedo alla cartella di helite: _/var/www/html/crm-helite_
- Accedo alla cartella _./files_ e creo una cartella con lo stesso nome dedicato all'hotel per l'upload dei files. Assegno alla cartella l'utente/gruppo _apache_
- Modifico all'interno della cartella _./protected/config/NOMEHOTEL/_ il file _mailer.php_ inserendo nelle variabili _'upload_dir'_ e _'path_upload_dir'_ l'indirizzo corretto per il caricamento dei files.
- Nella stessa cartella
- Accedo alla cartella _./tmp_
- Copio uno dei file gia esistenti *_inputInstall.nomecliente* e lo rinomino con il nome del nuovo ambiente. Lo devo fare **come utente root**
	
		cp _inputInstall.nomecliente _inputInstall.nuovoambiente  (come base Mauro consiglia ArtHotel)
	
- accedo al file e ne aggiorno i dati secondo lo schema seguente

		<?php return array (
		/*  --- parametri per ambiente --- */
		'db' => 'nuovoambiente', // ilnome dell'ambiente anche come db
		'profilo' => 'nuovoambiente', // il nome dell'ambiente
		'codi' => '111111', //il codice cliente preso dal pannello pianificazione ** vedi immagine
		'db_sol' => 'xxx',
		'id_hotel_sol' => '1',
		'nome_hotel' => 'Hotel Test Helite',  //il nome dell'hotel
		'sito_hotel' => 'https://hoteltesthelite.com', //i dati dell'hotel
		'email_hotel' => 'info@hoteltesthelite.com',
	
		/*  NON TOCCO NULLA  
		
		--- parametri standard --- */
		'host' => '192.168.250.10',
		'user' => 'postgres',
		'password' => 'B0C4MGliy3',
		'scenario' => 'M',
		'url_crm' => 'http://helite.syshotel-crm.it',
		); ?>

- ricopio nuovamente il file appena modificato con estensione _.php_

		cp _inputInstall.nuovoambiente _inputInstall.php

- mi posiziono nella cartella _/var/www/html/crm-helite/protected_
- lancio l'installazione di tutto l'ambiente
		
		sh install
		
- se l'ambiente è richiesto da SOL (Syshotelonnline - Agazzi/Hatem) non devo configurare il file params.php
- se l'ambiente è richiesto dal marketing devo impostare il params.php con l'utente Amministratore SU-FERRARI21 

- Una volta terminata l'installazione posso visionare il nuovo ambiente al link
https://helite.syshotel-crm.it/q=NUOVOAMBIENTE/index.php?r=site/login

- Mando una mail ad assistenza@syshotelonline.it ed in CC a Marco con il link per il nuovo ambiente.

- **UPLOAD DEI FILE**
	Potrebbe essere necessario 



	



[^1]: Ogni cliente puo avere in linea di massima fino a tre ambienti: Genius7 per la gestione hotel, Contab per la gestione della contabilita e Contab-maga per la gestione del magazzino.