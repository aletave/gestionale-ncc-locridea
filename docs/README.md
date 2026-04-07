# 🚐 Locridea Transfer: B2B Booking & Fleet Management System

> 🌐 **SERVIZIO LIVE**
> Il sistema è in produzione e accessibile all'indirizzo: **[ncc.locridea.it](https://ncc.locridea.it)**
>
> 👨‍💻 **AUTORE E PROPRIETÀ INTELLETTUALE**
> Questo gestionale è stato progettato e sviluppato da me come unico sviluppatore su commissione per *Locridea Viaggi*, un'azienda reale operante nel settore NCC (Noleggio Con Conducente).
>
> ⚠️ **AVVISO DI RISERVATEZZA E NDA**
> Il codice sorgente dell'applicazione in produzione è coperto da NDA (Accordo di Non Divulgazione). Questo repository pubblico ha lo scopo di condividere le scelte architetturali e lo stack tecnologico adottato.
>
> 📸 **ALLEGATI**
> Per mostrare la struttura della UI/UX e le architetture descritte, nella cartella `docs/` sono disponibili:
> 1. **Screenshot del Pannello Admin** (Smart Dispatch Dashboard) in produzione (con dati sensibili oscurati per rispetto della privacy).
> 2. **Documento di Analisi Tecnica** e Architetturale (formato PDF) redatto durante la fase di progettazione.

---

## 🎯 Il Contesto e l'Obiettivo

**Lo scenario iniziale:** Prima di questo progetto, il cliente gestiva l'organizzazione della flotta principalmente tramite WhatsApp e calcoli manuali per i preventivi. Questo approccio richiedeva molto tempo e aumentava il rischio di errori logistici o sovrapposizioni nei periodi di alta stagione.

**La soluzione:** Ho sviluppato una piattaforma web B2B per digitalizzare e automatizzare l'intero processo, dalla richiesta del cliente fino all'assegnazione della corsa all'autista.

**📊 Risultati:** L'automazione ha permesso di gestire il flusso di prenotazioni in modo più fluido e scalabile fin dal primo mese. Il sistema processa le richieste riducendo drasticamente il tempo dedicato al data-entry manuale e ottimizzando l'organizzazione della flotta.

---

## 🛠 Stack Tecnologico

L'applicazione è basata sull'ecosistema React, con attenzione alla type-safety e alla sicurezza dei dati:

* **Frontend/Backend:** Next.js 16 (App Router) + React 19.
* **API:** Utilizzo di **Next.js Server Actions** per una comunicazione tipizzata e diretta tra client e database, sostituendo le classiche API REST.
* **Database & Auth:** Supabase (PostgreSQL). La sicurezza dei dati è garantita dalle **Row Level Security (RLS)**, che assicurano che le informazioni personali dei passeggeri siano accessibili solo agli account autorizzati.
* **Validazione End-to-End:** Zod e React Hook Form per validare rigorosamente i dati in ingresso.
* **Integrazioni Esterne:** API di Google Maps (Distance Matrix, Geocoding, Places) eseguite lato server per garantire la correttezza dei calcoli tariffari senza manipolazioni client-side.
* **UI:** TailwindCSS e Radix UI (Componenti Headless).

---

## 🧠 Architettura e Design Pattern

Per mantenere la codebase organizzata e manutenibile, la logica di business è stata separata dalla UI utilizzando pattern architetturali (isolati nella directory `@/lib/patterns/`):

### 1. Strategy Pattern (`/strategy`)
Utilizzato per il modulo di preventivazione (`calculateTrip`). Il sistema gestisce due tipologie di servizio:
* **`StandardPricingStrategy`**: Calcola il prezzo basandosi sulla distanza reale (tramite Google Maps), applicando scaglioni chilometrici, supplementi notturni e includendo il costo della **rimessa** (i chilometri a vuoto necessari per l'avvicinamento del veicolo).
* **`FlatPricingStrategy`**: Utilizzata per i Pacchetti Turistici a costo fisso, bypassando le chiamate API esterne.

### 2. Repository Pattern (`/repository`)
Le interazioni con il database Supabase sono astratte in classi dedicate (es. `BookingRepository`, `PricingRepository`). Questo approccio separa la logica di business dalle query SQL.

### 3. Observer & Singleton (`/observer`, `/singleton`)
* **Singleton (`Logger`):** Un servizio di logging centralizzato per monitorare gli eventi in produzione.
* **Observer (`NotificationManager`):** Un gestore per le notifiche di sistema che permette di mostrare feedback visivi all'utente in modo disaccoppiato.

---

## 🚀 Funzionalità Admin e UX

La **Dispatch Dashboard** (Area Admin) è stata ottimizzata per l'utilizzo da smartphone, facilitando il lavoro dell'autista in mobilità. La card della singola prenotazione integra funzioni di **Deep Linking** con il sistema operativo:

* **🗺️ Navigazione rapida:** Cliccando sull'indirizzo, si apre automaticamente il navigatore nativo (Google/Apple Maps) con le coordinate preimpostate.
* **📅 Integrazione Calendario:** Cliccando su data o orario, viene generato un evento da salvare rapidamente sul calendario del telefono.
* **💬 Integrazione WhatsApp:** L'azione di "Conferma Corsa" apre una chat WhatsApp con un messaggio precompilato contenente i dettagli logistici per il passeggero.
* **📈 Analisi dei costi:** Il sistema incrocia orari dei voli e orari di pick-up per segnalare anomalie, calcolando il margine della corsa al netto dei chilometri a vuoto.
