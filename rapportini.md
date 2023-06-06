Rapportini di giornata
=====================

Come compilare i rapportini per ogni giorno
------------------------------------------

Per compilare i rapportini bisogna aprire tramite putty il relativo programma ed inserire quindi il codice operatore/matricola 0386.
La psw è iniziali nome cognome maiuscolo. Tutti i comandi vanno dati con **UPPERCASE**
Con un doppio _INVIO_ visualizzo il menu quindi seleziono _Menu Rapportini_
Quindi nel nuovo menu eseguo un triplo _INVIO_ per selezionare _Gestione Rapportini_ e _Gestione dati per rapportini_
Entro in **RA1** il menu per la gestione dei rapportini giornalieri.
Da qui posso cliccando ancora _INVIO_ andiamo a scegliere se inserire il rapportino giornaliero o modificare un rapportino in base alla data.

### Inseriamo il rapportino giornaliero
Nella schermata che si apre inseriamo nuovamente il codice matricola 0386 e la data del giorno da gestire quindi con _INVIO_ o con _F3_ confermiamo e passiamo alla nuova schermata.

Bisogna ricordarsi che con il comando _F6_ si esegue la ricerca

Compiliamo quindi il rapportino andando a riepire i campi richiesti:

| Numero | Titolo | Input |
|--------|--------|-------|
|01|Data| Data odierna|
|02|Reparto| TU Front Office|
|03|Cliente|31993|
||Protocollo| Il protocollo da utilizzare|
||Attivita| COR per i corsi ... altrimenti quello che serve|
||Prodotto|AAFRXX SW Front non Assegnato|
|04|Citta|sede|
|05|Motivo Interno|ZZ - Descrizione del lavoro svolto|
|06|Ore lavorate|Mattina/Pausa/Pomeriggio|
|07|Uso Ticket[^1]|S|
|08|Ore Concordate|Solo in caso di Viaggio|
|09|Auto Ditta|--|
|10|Andata/Ritorno|--|
|12|Spese dipendente|--|
|13|Spese Ant. Ditta|--|
|14|km percorsi|--|
|15|Da Stampare separato|No|

Alla fine della compilazione posso salvare con _F3_ oppure duplicare il rapportino con _F5_ nel qual caso viene richiesta la data con la quale duplicare.
Poi tramite la funzione di ricerca dei rapportini per data posso andare a modificarlo.

Una volta tornato al menu iniziale con _ESC_ posso chiudere tutto con _F4_

[^1]:Posso utilizzare il Ticket se ho fatto almeno 5 ore di lavoro consecutive con 1/2 di pausa-



STAMPA RAPPORTINI
----------------------
A fine mese bisogna eseguire la stampa dei rapportini

Posso richiamare i comandi rapidi con F2

### La prima stampa da eseguire
RA3COLASV 
In questo programma uso sempre  Anteprima di Stampa 
Come stampante usare 04 Laser Back
Quindi confermare, inserire numero matricola e data di inizio
Stampa ropportini con protocollo STAMPA
Inivo x 4
**NON STAMPO NULLA**

### Seconda stampa da eseguire è RA2ra2
Spunta Anteprima di stampa cosi controllo il risultato
Seleziono la stampante
F11 proseguo
Matricola e Data (primo giorno del mese)
Controllo il rapportino con tutti i giorni ed eventuali avvisi
F6 per la stampa

### Terza Stampa
Ra5
Seleziono la stampante
F11 proseguo
Matricola e Data (primo giorno del mese)
Controllo il riepilogo di tutte le ore suddivise per protocollo e 
F6 per la stampa

### Quarta Stampa
RA7LAS

Generazione Nota Spese
Eventuali spese che ho sostenuto e sono da inserire nei rapportini.0105

Generazione Calcolo Straordinari
Ti calcola gli straordina in base alle ore inserite nei rapportini giornalieri

### Quinta Stampa
RA3COLAS
Ripeto tutte la procedura gia eseguita in RA3COLASV
Il programma stampa solo quello che ritiene necessario
La stampa non è garantita (potrebbe non stampare nulla)
 
 
 
 
 INSERIMENTO RAPPORTINO DA CRM PER INVIO CLIENTE
 -----------------------------------------------
 
 Quindi apro il crm e mi posiziono Attivita-> Rapportino Cliente
 Aggiungo + Crea Intervento
 Nome del Cliente.
 E Continua
 
 Completo i _Click to edit_  
 
 Luogo: SEDE
 Oggetto: PREVE DESCRIZIONE (come nelle email)
 Indirizzo Email - Vuoto
 Gli altri campi secondo indicazione da Pianificazione.
 Al momento ho abilitato solo TU come REPARTO. Se diverso modificare successivamente da Rapportini_PuTTY
 Quindi ADD
 Inserisco data e per le ore
 Se per SOL 1 ora
 Se per Marketing Automation 3 ore
Inserire la descrizione del lavoro svolto|
QUindi Genera e Salva (Pulsante BLU) 

### Scaricare i rapportini (non sempre funziona)

Si possono scaricare i rapportini manualmente, non sempre funziona.
Subito sotto la voce _cerca per data_

 
 
 