# Medical-Literacy-Agent
Repository per la consegna del prodotto finale realizzato per il progetto bioergotech agentic-ao

Nome del progetto e descrizione 
Medical Literacy Agent è un agente di intelligenza artificiale in grado di analizzare e spiegare in maniera più semplice i risultati delle analisi del sangue a chi li riceve senza un immediato riscontro medico.  

Il problema che risolve 

Chi riceve un referto delle analisi del sangue spesso non ha un riscontro medico immediato. Senza un sistema come questo, tende a informarsi online e a trovare fonti non attendibili, con il risultato di disinformazione e allarmismi. L’agente offre una spiegazione semplice dei valori, basata su fonti attendibili, in attesa del confronto con il medico. 

Come funziona 

Il flusso segue lo schema input → elaborazione → output, con una parte di memoria a lungo termine per chi riutilizza il servizio.
Input

Form (Tally.so): l’utente indica il sesso e l’età, i parametri su cui vuole un chiarimento e l’indirizzo email a cui ricevere il resoconto. 
Elaborazione 

Orchestratore (Make): separa le informazioni ricevute dal form e le raccoglie in un unico testo. 
Associazione ai range (Google Sheets): ogni parametro viene associato alla tabella di database in cui sono scritti i valori entro cui dovrebbero rientrare i risultati. 
Punto decisionale 1 · richiesta di diagnosi? Se l’utente chiede una diagnosi, la domanda viene ignorata. [DA COMPLETARE: come avviene il controllo (regola in Make o istruzione nel prompt) e se l’utente riceve un avviso.] 
Resoconto (API Google Gemini, prima chiamata): confronta i valori inseriti con i range della tabella ed elabora il resoconto finale. 
Output 

Invio (Gmail): il resoconto viene inviato all’indirizzo email indicato, sempre con il disclaimer sulla possibilità di errore e sulla necessità di rivolgersi al medico. 
Memoria a lungo termine 

Registro (Google Sheets): i dati inseriti vengono memorizzati in una seconda tabella che funge da registro. 
Punto decisionale 2 · utente già presente? Se l’email è già nel registro, l’utente viene riconosciuto e si passa al punto 9; altrimenti il flusso termina. 
Confronto (API Google Gemini, seconda chiamata): confronta i vecchi dati con i nuovi ed elabora un testo che mostra l’andamento dei parametri nel tempo. 
Invio (Gmail): una seconda email porta all’utente il confronto nel tempo. 

Fonti e dati API utilizzate

Tally.so: raccoglie in tempo reale i dati inseriti dall'utente. Il piano gratutito garantisce moduli e risposte illimiatate

Google Sheets-tabella di database: Contiene i valori di riferimento dei parametri, tratti da fonti attendibili (es. Istituto Superiore di Sanità); i campi senza fonti attendibili sono lasciati vuoti. Dati inseriti manualmente dal team (statici)

Google Sheets registro: fonte di dati in tempo reale, è la memoria a lungo termine dei dati inseriti. Nel piango gratutito può essere effetuato un numero illimitato di chiamate al giorno 

API Google Gemini (due chiamate):fonte di dati dal vivo, la prima elabora il resoconto; la seconda confronta vecchi e nuovi risultati. Lo sfruttamento a uso commerciale è consentito solo con i piani a pagamento. Nei Termini generali delle API di Google è specificato che l'uso delle API standard non crea obblighi ai sensi della normativa sanitaria HIPAA e non va usato per trasmettere informazioni sanitarie protette senza consenso scritto.  
API Gmail: fonte di dati dal vivo, recapita i resoconti all'utente. Servizio consentito con limiti giornalieri sul numero di invii. Il limite è di 500 mail inviabili al giorno

Make: fonte di dati dal vivo, è l'orchestratore di tutto il sistema. Il piano gratuito (Free) di Make include 1.000 crediti al mese, un massimo di 2 scenari attivi contemporaneamente e un intervallo minimo di esecuzione di 15 minuti, regolati dal Master Service Agreement di Make

Limitazioni note e casi limite

Ingresso vuoto:Il resoconto arriva via email ma non risulta analizzato nulla, perché i parametri erano vuoti;

Formato inatteso (parametri in inglese): I parametri vengono tradotti e il resoconto viene spedito in italiano;

Input avversario (richiesta di diagnosi): La domanda viene completamente ignorata dall’agente che non riceve nessun output relativo a questa;

Strumento non disponibile: L’agente usa Google Sheets per recuperare e aggiornare i dati, Gemini per elaborarli e Gmail per inviare i risultati. Se uno di questi servizi non è disponibile, l’operazione non viene completata e tutti i moduli successivi non vengono eseguiti. Non esistono rami che inviino all’utente un messaggio di errore;

Verifica di correttezza (input con risposta già nota): L’agente ha elaborato correttamente i dati e l’output è risultato corretto. È un solo test: non esclude errori in altri casi.

Limitazioni note

Nessuna gestione degli errori quando un servizio esterno non è disponibile: l’utente non riceve alcuna comunicazione. 

Per i parametri privi di fonti attendibili i campi del database sono vuoti. Questi confronti sono segnalati all’utente come quelli che più necessitano di un riscontro medico. 

Il margine di errore del resoconto è la limitazione più grande. Con più tempo avremmo addestrato l’agente con più dati per ridurlo. 

Revisione etica finale
Le due preoccupazioni etiche principali che ci sono venute in mente durante la creazione del nostro prototipo sono: la divulgazione involontaria dei dati sanitari sensibili inseriti dall’utente e la possibilità di ricevere un resoconto errato. È importante considerare la divulgazione involontaria dei dati inseriti dall’utente come un problema etico perché, se l’indirizzo e-mail inserito viene letto male o viene modificato durante il lavoro dell’agente il resoconto potrebbe arrivare a terze persone che quindi verrebbero a conoscenza di eventuali problematiche della persona che ha effettuato le analisi. Per proteggere la privacy quindi abbiamo deciso di non chiedere mai all’utente dati sensibili come nome e cognome in modo che, qualora si verificasse una situazione del genere, non si potrebbero attribuire questi dati a una persona fisica. Riguardo la seconda preoccupazione etica descritta in precedenza, è da tenere in considerazione perché se il resoconto è errato e i dati che l’agente usa non sono del tutto attendibili allora potrebbe suscitare allarmismi e disinformazione (che sono le conseguenze che ci hanno portato a strutturare un agente del genere). Per questo abbiamo cercato di addestrare l’agente con dati proveniente solo da fonti attendibili, come l’Istituto Superiore di Sanità e quando non siamo riusciti a trovare siti attendibili da cui prendere questi dati, abbiamo deciso lasciare vuoti questi campi nella tabella di database e segnalare all’utente che questi confronti sono quelli che più necessitano un riscontro medico perché l’attendibilità delle fonti è dubbia. Riguardo il resoconto errato abbiamo deciso di fornire sempre il disclaimer in cui viene detto che i risultati potrebbero essere sbagliati e bisogna sempre rivolgersi al medico per un resoconto più attendibile. Questa riteniamo essere una delle limitazioni più grandi. Se avessimo avuto più tempo ci sarebbe piaciuto provare ad addestrare il nostro agente con più dati in modo da diminuire il margine di errore. 
