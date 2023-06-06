I computer e la rete aziendale SAAS
===============================

Come sono definiti gli indirizzi IP delle varie reti
--------------------------------

Esistono principalmente differenti schemi di rete.

La rete di Saronno, quindi della sede risponde alla rete **192.0.200.xxx**
Questa rete viene definita **trusted** è una rete vecchia, viaggia a 1Gb/s
All'interno di questa rete troviamo mediamente tutti i computer aziendali e gli altri dispositivi.

La rete con protocollo **192.168.10.xxx** dovrebbe nei prossimi mesi prendere il posto dell'attuale rete principale 0.200.
Al momento su questa rete è presente una macchina definita _documentale_ nella quale possiamo trovare tutte le varie documentazioni: 192.168.10.190

Sempre a Saronno è attiva una seconda rete, la **192.168.235.xxx** che viene definita **untrusted**
e questa è la rete pubblica dove possono accedere gli eventuali clienti e dalla quale vengono presi gli indirizzi delle macchine virtuali create localmente.

La rete di Milano dei NAS risponde a **192.168.100.xxx**
Questa rete viene definita **sass**. Qui troviamo tutte le macchine virtuali dei vari clienti che hanno il servizio in cloud.

L'ultima rete utilizzata in azienda ma ospitata da Irideos Farm è la numero  **192.168.250.xxx**  dove troviamo delle altre macchine virtuale di alcuni clienti che hanno il servizio in cloud, ma che adrà a breve sostituita.

### Risorse di varia importanza.

- Accedo alle macchine di Milano e di Irideo con i miei dati personali
- Stesso discorso per il SU c'è il gruppo sudo.
- 192.0.200.190 NAS dove vengono salvati tutti i backup dei computer della rete **trusted** 
- 190.0.200.80 Macchina dove vengono conservate le macchine virtuali da utilizzare per l'installazione: nella cartella   `\vm\Front\*.vmdk`  trovo l'immagine dei dischi.
- 192.168.10.190 Documentale, macchina con tutti i documenti dei vari reparti.
Ho accesso a Frontoffice e Doctur.
- 192.168.250.9 e la macchina che contiene i CRM Elite chiamata Webcheck-in l'url per raggiungere il crm helite è helite.syshotel-crm.it/q=ambiente/index.php
- 192.168.250.10 è la macchina che contiene i db per i CRM Elite.
- 192.168.100.222 è la macchina Delta con i CRM
- 192.168.100.211 e la macchina Delta DB
- 192.0.200.205 Macchina di Mauro
- 192.0.200.118 **LA MIA MACCHINA**
- 192.0.200.140 Macchina di test con montato il CRM - Sto provando a fare gli aggiornamenti su questa macchina.

Server BI
------------------

Il servizio è gestito dai due dirimpettai, fanno analisi di tutti i dati che arrivano dai vari PMS e CRM.
Hanno un server dedicato 93.62.208.137 gestito direttamente da loro.
sysdatbi.sigesgroup.it:9503 la porta per accedere al loro serve.
Tendenzialmente non viene utilizzato ma il cliente puo richiedere di installare on primis un server per la gestione dei dati e viene fatto direttamente da Oracle.


Server PMS
----------
Il server del PMS 
Per info su autorizzazioni lato pc chiedere a Bruno 666 o Daninele 666


E' installato il soft COBIAN per il Backup che viene eseguito autonomamente ogni dì.


Crontab.guru per impostare l'uso di cron.

Cat /etc/os-release
more

con cut /etc/environement vedo quale versione di Acucobol è in esecuzione e quali librerie richiede.


Su ProxMox con ORACLE9, abbiamo come software di aggiornamento sia Yum che dnf

Per avere una storia di tutti i comandi dati precedentemente anche a login terminato uso il comando.
$history
$history |more per vederli dal primo.
$history |grep cio che cerco


Per effettuare una ricerca all'interno della macchina uso il find.
$find / -name Nagios (effettuo la ricerca partendo dalla root della partizione)
$find / -iname nagios  (case insensitive per il nome)
$find /u/usr -name COSACERCO.

top - per
df -h
free -h
du -h
who
w





