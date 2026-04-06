# 🚐 Locridea Transfer: B2B Booking & Fleet Management System

> 👨‍💻 **AUTORE E PROPRIETÀ INTELLETTUALE**
> Questo ecosistema gestionale SaaS è stato interamente progettato, ingegnerizzato e sviluppato da me su commissione per *Locridea Transfer*, un'azienda reale operante nel settore NCC (Noleggio Con Conducente). 
>
> ⚠️ **AVVISO DI RISERVATEZZA E NDA**
> Il codice sorgente dell'applicazione in produzione è proprietario ed è rigorosamente protetto da NDA (Accordo di Non Divulgazione). Questo repository pubblico funge esclusivamente da **Case Study Tecnico e Panoramica Architetturale** per dimostrare le mie competenze nello sviluppo Full-Stack, nel System Design e nel Cloud FinOps.
>
> 📸 **ALLEGATI E PROVE VISIVE**
> Per dimostrare la reale complessità della UI/UX e le architetture descritte, all'interno della cartella `docs/` di questo repository sono consultabili:
> 1. **Screenshot del Pannello Admin** (Smart Dispatch Dashboard) in produzione (con dati sensibili oscurati per rispetto del GDPR).
> 2. **Documento di Analisi Tecnica** e Architetturale (formato PDF) redatto in LaTeX durante la fase di progettazione.

---

## 🎯 Il Problema di Business e l'Impatto (ROI)

**Lo scenario iniziale:** Prima del mio intervento, il cliente gestiva la flotta tramite un flusso puramente analogico. La preventivazione veniva fatta a mente o con fogli di calcolo rudimentali, il booking avveniva via WhatsApp e non esisteva un controllo centralizzato delle disponibilità. Questo generava enormi colli di bottiglia, errori di calcolo sui chilometraggi a vuoto e un grave rischio di overbooking nei periodi di alta stagione.

**La soluzione:** Ho ingegnerizzato una piattaforma B2B end-to-end che ha digitalizzato e automatizzato l'intero processo aziendale, dal primo tocco del cliente finale fino all'assegnazione della corsa all'autista.

**📊 Risultato in Produzione:** Nel suo primo mese di messa live, il sistema ha gestito autonomamente un elevato volume di richieste e transazioni senza alcun intervento umano nella fase di quotazione. L'automazione del processo ha garantito al cliente un ROI (Return on Investment) positivo a tempo record, ammortizzando i costi di sviluppo, azzerando i tempi di data-entry e massimizzando i margini operativi su ogni singola corsa.
---

## 🛠 Stack Tecnologico Full-Type-Safe

L'applicazione è stata costruita adottando i paradigmi più moderni dell'ecosistema React e Serverless, puntando a scalabilità, zero debito tecnico e sicurezza dei dati.

* **Core Framework:** Next.js 16 (App Router) + React 19.
* **API & Data Fetching:** Sostituzione delle classiche API REST con **Next.js Server Actions**. Questo permette mutazioni dirette e strettamente tipizzate tra il client e il database, eliminando boilerplate inutile e riducendo la superficie di attacco.
* **Database & Auth:** Supabase (PostgreSQL) blindato con policy di **Row Level Security (RLS)**. Le RLS garantiscono che i dati sensibili dei passeggeri siano inaccessibili a chiunque non sia l'amministratore autenticato.
* **Data Validation End-to-End:** Zod e React Hook Form integrati in ogni layer del sistema. Nessun dato entra nel DB senza una validazione rigorosa degli schemi.
* **Integrazioni Esterne (Blindate):** Google Maps API (Distance Matrix, Geocoding, Places) eseguite in ambiente Node.js rigorosamente *server-side* per impedire manipolazioni dei percorsi e dei prezzi da parte del client.
* **Styling:** TailwindCSS e Radix UI (Componenti Headless).

---

## 🧠 Architettura Logica e Design Pattern

Per gestire l'elevata complessità logistica e tariffaria, ho rifiutato l'approccio "spaghetti code", ingegnerizzando il core applicativo con pattern architetturali di livello enterprise. Tutta la logica di dominio è isolata nella directory `@/lib/patterns/`:

### 1. Strategy Pattern (`/strategy`)
Il motore di preventivazione (`calculateTrip`) è il cuore pulsante del sistema. Il business richiedeva di gestire due flussi di vendita paralleli: tratte dinamiche (da un punto A a un punto B) e **Pacchetti Turistici** a costo fisso. 
Tramite il pattern Strategy, il sistema inietta a runtime la logica corretta:
* **`StandardPricingStrategy`**: Interroga le API di Google Maps, calcola la distanza reale e applica un algoritmo proprietario basato su scaglioni chilometrici, supplementi notturni, maggiorazioni per i festivi e, soprattutto, **i costi di rimessa** (il dead mileage, ovvero i chilometri a vuoto necessari per l'avvicinamento del veicolo al punto di pick-up).
* **`FlatPricingStrategy`**: Utilizzata per l'istanziazione dei Pacchetti Turistici, bypassa completamente l'overhead di rete di Google Maps, applicando una tariffa flat persistita a database.

### 2. Repository Pattern (`/repository`)
Tutte le interazioni con il database PostgreSQL (Supabase) sono completamente astratte. I componenti UI React e le Server Actions dialogano esclusivamente tramite interfacce Repository (es. `BookingRepository`, `PricingRepository`), garantendo una separazione netta tra la logica di business e il Data Access Layer.

### 3. Observer & Singleton (`/observer`, `/singleton`)
* **Singleton (`Logger`):** Implementazione di un Logger centralizzato per il tracciamento degli audit in produzione, capace di spawnare istanze figlie per tracciare con precisione l'origine dei log.
* **Observer (`NotificationManager`):** Gestione disaccoppiata degli eventi di sistema. Permette a funzioni non-React (come middleware o funzioni utility) di emettere eventi che l'interfaccia UI cattura per mostrare feedback reattivi e non bloccanti all'utente.

---

## 🚀 UX Avanzata: Smart Dispatch & Quick Actions
*(Consulta la cartella `docs/` per la documentazione visiva)*

La **Dispatch Dashboard** (Area Admin) si discosta dalla classica concezione di "tabella dati". L'ho concepita come un vero e proprio **Hub Operativo Mobile-First** progettato per un autista in mobilità. 

La singola card di prenotazione integra funzioni avanzate di **Deep Linking** con il sistema operativo del dispositivo, trasformando i dati in azioni immediate (Quick Actions):

* **🗺️ Click-to-Navigate:** Cliccando sulla stringa dell'itinerario, il sistema genera un URI Scheme dinamico che apre istantaneamente il navigatore nativo (Google Maps / Apple Maps), preimpostando le coordinate GPS di partenza e arrivo. Tempo di data-entry azzerato.
* **📅 Click-to-Calendar:** Interagendo con la data o l'orario, viene generato on-the-fly un payload di evento compatibile con l'app Calendario nativa di iOS/Android, automatizzando la schedulazione del turno lavorativo.
* **💬 Click-to-Chat:** L'azione di "Conferma Corsa" esegue un redirect verso le API di WhatsApp, precompilando un template con i dati logistici del passeggero, l'importo e le istruzioni di incontro.
* **📈 Business Intelligence in Real-Time:** La card incrocia gli orari dei voli aeroportuali con gli orari di pick-up per segnalare anomalie, e calcola in tempo reale il **margine di profitto effettivo**, scorporando matematicamente i costi della rimessa.

---

## 💸 Cloud FinOps: Il Bypass Infrastrutturale
Scrivere codice scalabile è solo metà del lavoro di un Software Engineer; l'altra metà è proteggere i budget aziendali.

L'infrastruttura originale su Vercel, in un contesto collaborativo, avrebbe comportato costi fissi mensili per "Team Seat" insostenibili per una piccola impresa locale. Per ovviare al problema, ho ingegnerizzato una **Ghost CI/CD Pipeline**:
1. Ho rimosso gli account sviluppatore a pagamento dall'organizzazione del cliente.
2. Ho sviluppato una GitHub Action custom (`.github/workflows/deploy.yml`) che comunica via Webhook crittografato direttamente con i Deploy Hooks API di Vercel. 
3. **Il Risultato:** Questo bypass architetturale ha tagliato i costi fissi del server cloud del **50%**, mantenendo inalterata l'automazione dei rilasci in produzione (al push sul branch `main`) e garantendo deployment continui con zero downtime.