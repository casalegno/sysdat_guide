# GIT comandi per aggiornare la macchina

Su gitbooks trovi una guida completa ed in Italiano su come funziona Git.
https://yunwuxin1.gitbooks.io/git/content/it/
Su github trovi i comandi principali da eseguire con Git
https://gist.github.com/tesseslol/da62aabec74c4fed889ea39c95efc6cc

## Procedimenti preliminari

Come prassi eseguire il backup delle cartelle da aggiornare prima di fare la git e il backup del database relativo al crm.

## Aggiorniamo Genius7

L'aggiornamento del PMS va effettuato dalla cartella dell'ambiente e si esegue con Git sequendo i passai indicati qui sotto.
_Questi passaggi valgono sia per qualsiasi applicativo gestito con git._

1. ottengo la versione attuale dell'applicazione installata

		git status

2. per ripristinare i file alla versione attuale

		git checkout . (percorso/nomefile) 

3. Scarico ed aggiorno i file in due modi differenti:
	a.	scarica ed aggiorna i file all'ultima versione disponibile e funzionante

			git fetch

	b.	si occupa di scompattare e di installare i file scaricati con fetch

			git merge

	c. con un unico comando eseguo sia il fetch che il merge. E plausibile che non aggiorni lo status, quindi successivamente devo eseguire un `git checkout versioneaggiornata`

			git pull


4. come ultimo passaggio eseguo un 

		git log
cosi faccio un check del lavoro svolto.
Per visualizzare i log con i vari tag esegue
		git log --tags --decorate=full
		
		
### Su che macchina sto lavorando

Per sapere su quale macchina sto lavorando con Git posso venirne a conoscenza richiedendo le informazioni di configurazione.

	git config --list
	git config --global --list

mi restituisce tutte le informazioni relative al branch ed all'indirizzo IP della macchina in uso.

### Cambio di branch

Se capita di cambiare il branch mi devo posizionare all'interno della directory e devo eseguire un pull per visionare il nuovo branch.

Quindi con il comando <code>git checkout [nuovobranch]</code> passo al nuovo branch e viene aggiornata la cartella dei file.
Quindi eseguo il migrate esportando prima la variabile di ambiente e successivamente eseguendo lo _YesItIsCommand_	

### Annullare le modifiche nella directory di lavoro
È possibile usare il comando git checkout per annullare le modifiche apportate a un file nella directory di lavoro. In questo modo il file tornerà alla versione presente nell'HEAD:

	git checkout -- NOME-FILE

Durante l'ultima `git pull` eseguita ho regitrato questo errore:

>error: Your local changes to the following files would be overwritten by merge:
>protected/behaviors/PrenotazioniPMSBehavior.php
>protected/modules/vendite/models/WaOrdineExt.php
>Please, commit your changes or stash them before you can merge.
>Aborting

la soluzione è stata disabilitare questi due file dalla pull. Li abbiamo quindi sospesi con il comando

	git checkout -- protected/behaviors/PrenotazioniPMSBehavior.php
	git checkout -- protected/modules/vendite/models/WaOrdineExt.php

per poi eseguire nuovamente la pull
		
Aggiorniamo il CRM
------------------

Per aggiornare il crm che ad oggi sulla master è di 2.14 all'ultima attiva bisogna farlo dalla cartella del crm _/var/www/html/crm/_  
Come primo passaggio devo eseguire un bkp della cartella quindi la eseguo ocn il comando

	cp -Rdpv crm/ crm_bkp

Prima di fare questo posso eseguire il backup del db del crm tramite Postgres.
Per fare il backup del DB con Postgress vedi file BackupBD

Ci sono due strade: **Delploy e Git**.

**La git la uso se faccio l'aggiornamento su ambiente cloud, quindi SASS o su interfaccia multiutente MULTI TENANT, come per il server Elite**

Se invece faccio l'aggiornamento sulla macchina virtuale On Premis (quindi sulla macchina del cliente) lo eseguo con il deploy (in realtà posso farlo con git, è smeplicemente piu lungo come processo ma decisamente piu sicuro).
Accedo alla cartella del CRM, quindi controllo lo stato.
Eseguo il pull della nuova versione ed una volta terminato eseguo il migrate

### Migrate: aggiorniamo il database del CRM

1. Per poter eseguire il migrate devo capire quale ambiente migrare: nella cartella
	
		/var/www/html/crm/protected/config/

2. trovo tutti gli ambienti esistenti: principalmente sono quelli degli sviluppatori (identificati come cartelle) quella dedicata al cliente (con il suo nuome) è quella da aggiornare.

		export SERVER=cliente

3. creo la variabile di ambiente SERVER quindi eseguo il programma di migrazione
	
		./yiic migrate

4. Una volta eseguito il check do conferma e porto a termine il processo.
		git log --tags --decorate=full


Sul server di Elite
-------------------

Nella macchina 192.168.250.9 dove trovo i crm di helite ho diversi crm tra questi, quelli con il numero sono quelli di backup. Le cartelle 

Per eseguire l'aggiornamento quando mi viene richiesto devo controllare lo spazio disponibile, eseguire il backup del crm attuale e cancellare il backup piu vecchio presente

``` bash	
rm -rf crm-helite_20230318
cp -Rdpv crm-helite crm-helite_20230525
```
Quindi, dalla cartella crm-helite seguendo i passaggi di Git eseguo l'aggiornamento.
Per quel che riguarda la migrate dentro questo ambiente e stato creato lo scritp cmd_global che si occupa di creare autonomamente le varibili di ambiente di ogni cliente ed eseguire la migrate.

	./cmd_global migrate

se non sono presenti delle migrate da eseguire restituisce per ogni ambiente il valore 

	---------- Ambiente statuto ----------
	Yii Migration Tool v1.0 (based on Yii v1.1.23-dev)
	No new migration found. Your system is up-to-date.
