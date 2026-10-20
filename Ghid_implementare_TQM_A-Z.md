---
title: "TQM Achiziții — Ghid de implementare de la A la Z"
subtitle: "Plan practic pentru realizarea aplicației locale, urmat de scrierea codului pe loturi"
author: "Document de lucru pentru Andrei și Mara"
date: "20 octombrie 2026 · versiunea 1.0"
lang: ro-RO
---

# Cum folosești acest ghid

Aplicația TQM Achiziții îi ajută pe Andrei și Mara să urmărească aceeași achiziție, simultan, de la necesar până la recepție, verificarea facturii și predarea documentelor către facturare/contabilitate.

Acest ghid transformă PDF-ul **TQM_Proiectare_Aplicatie_v1.pdf**, 30 de pagini, într-un plan de implementare. PDF-ul este sursa cerințelor funcționale. Comenzile, structura de proiect, modelul detaliat și ordinea sarcinilor de aici sunt propuneri tehnice pentru a-l realiza. Texte din documentul sursă despre instalare ori despre fișiere mai vechi sunt tratate ca informații de proiectare, nu ca operații deja executate.

**În această etapă primești instrucțiunile și contractele de implementare. Aplicația, serviciile Windows și scripturile finale vor fi scrise ulterior, pe loturile din capitolul 20.** Comenzile de pregătire pot fi urmate când ajungi la etapa respectivă. Comenzile care folosesc fișiere ale aplicației se execută numai după ce acele fișiere au fost livrate în lotul indicat.

Ordinea recomandată de citire:

1. Citește capitolele 1–5 pentru a înțelege ce construim și cum pregătești calculatorul.
2. Păstrează capitolele 6–15 ca specificație în timpul scrierii codului.
3. Implementează loturile în ordinea din capitolul 20; fiecare lot are un rezultat verificabil.
4. Folosește capitolele 16–19 pentru instalare, backup, migrare și lansare.
5. Bifează scenariile din capitolul 21 pe o instanță de test, înaintea utilizării reale.

Fișierele de cod vor fi livrate cu calea completă în proiect, conținut integral pentru fișierele noi, modificări clare pentru cele existente, comenzi de rulare și rezultat așteptat. O captură de ecran reușită nu înlocuiește verificarea calculelor și a bazei de date.

# Ce este stabilit și ce se configurează

## Cerințe preluate din PDF

- Aplicație accesată prin browser, instalată pe un calculator al firmei.
- Django 5.2 LTS, PostgreSQL și interfață proprie HTML/CSS/JavaScript.
- Andrei și Mara au conturi distincte, aceleași drepturi funcționale și aceleași date comune. Oricare poate valida sau aproba; nu introducem o regulă obligatorie creator diferit de aprobator.
- Meniu cu exact șapte secțiuni principale: **Astăzi, Cereri, Oferte, Comenzi, Livrări, Control, Administrare**.
- Cererea internă și solicitarea de ofertă sunt documente distincte.
- O poziție poate fi acoperită de mai mulți furnizori, mai multe comenzi ori stoc confirmat.
- Comenzile au versiuni aprobate și termene originale păstrate. Ciornele nu consumă necesarul.
- Recepțiile, retururile, înlocuirile, serviciile și transferurile au efecte distincte.
- Controlul include facturi, alocări, stornări, proforme, avansuri și predare documentată.
- Plățile, înregistrarea contabilă și e-Factura rămân la compartimentul de facturare/contabilitate. Nu construim un registru bancar paralel.
- Software și servicii externe: buget de licențe/abonamente 0 EUR, folosind calculatoarele și rețeaua existente.
- Import/export Excel și PDF local. Trimiterea efectivă a e-mailurilor se face prin instrumentele firmei.
- Nu presupunem recunoașterea automată a oricărui PDF, funcționare offline sincronizată sau acces public de pe internet.
- Cele 24 de teste T01–T24 sunt criterii de acceptare de implementat, nu teste deja trecute.

## Propuneri tehnice ale acestui ghid

- Python 3.13 x64 și PostgreSQL 17.x, folosind corecții de securitate suportate și compatibilitatea confirmată la instalare. Alegerea majorului PostgreSQL nu obligă folosirea celei mai noi versiuni majore.
- Un singur proiect Django, împărțit în module de domeniu, fără frontend separat și fără microservicii.
- Pagini HTML randate de Django; JavaScript simplu pentru tabele, căutare, salvare de ciorne și dialoguri.
- ReportLab pentru PDF, openpyxl pentru XLSX și psycopg 3 pentru PostgreSQL.
- UUID pentru identificatori interni, numere lizibile pentru documente și `Decimal` pentru calcule.
- Waitress ca server al aplicației pe Windows, Caddy pentru HTTPS și fișiere statice, WinSW pentru găzduirea proceselor ca servicii Windows.
- Instanțe și configurări distincte pentru dezvoltare, test și producție.

Versiunile exacte ale pachetelor se fixează după instalarea și verificarea lotului 01. Nu alegem aici numere de corecție presupuse. Accesul la site-urile oficiale a fost refuzat de proxy în mediul în care a fost scris ghidul; referințele din capitolul 24 trebuie reverificate la instalare.

## Date de completat înaintea pilotului

| Decizie | Propunere inițială | Ce confirmăm în firmă |
|---|---|---|
| PC server | Windows 11 x64 existent, pornit în orele de lucru | Nume, RAM, spațiu, drepturi de instalare și repaus |
| Rețea | Rețea locală privată, adresă stabilă | Adresă rezervată în router și rezoluția numelui |
| Resurse | Orientativ 8 GB RAM minimum practic, 16 GB preferabil, SSD | Măsurăm pe PC-ul real; nu este o garanție de performanță |
| Utilizatori | Conturi Andrei și Mara | Identitate, parole inițiale și procedura de recuperare |
| Documente | Antetul firmei și numere CER/COF/COM | Date comerciale, siglă, prefixe și început de numerotare |
| Calcule | Cantități 4 zecimale, factori/prețuri/cursuri 6 | Rotunjire, toleranțe și monede folosite |
| Backup | Zilnic, pe alt suport/PC existent | Calea, accesul, capacitatea și perioada păstrată |
| Migrare | Import cu previzualizare și corespondențe | Fișierul V5 exact și datele acceptate ca reale |

**Diferență de sursă:** PDF-ul menționează un XLSM V5 cu 66 foi și 57 tabele. În această cerere a fost atașat PDF-ul; acel XLSM V5 nu este disponibil. Excelul v2.1 analizat anterior avea altă structură. Nu folosim automat maparea v2.1 pentru V5 și nu pretindem că lista de 22 materiale menționată în PDF a fost primită aici. Această lipsă afectează migrarea exactă și proba pe acea listă, nu realizarea aplicației.

# Ordinea de construcție și limitele primei versiuni

## Trei praguri de funcționare

**Pragul A — probă internă:** două conturi pot crea o cerere, înregistra două oferte parțiale, selecta cantități și aproba o comandă fără dublare. Acesta dovedește arhitectura și regulile centrale. Nu este lansare completă.

**Pragul B — pilot complet:** același caz continuă prin confirmare, recepții, documente, factură, diferențe și predare. Se adaugă apoi cazurile speciale: conversii, pachete, retururi, servicii, stornări și avansuri.

**Pragul C — utilizare reală:** toate funcțiile obligatorii din PDF și T01–T24 sunt verificate, backupul a fost restaurat, iar datele de pornire sunt reconciliate.

Implementarea graduală reduce riscul de a construi toate ecranele înainte să avem un circuit funcțional. Funcțiile avansate sunt implementate în loturi ulterioare; nu sunt eliminate din cerința finală.

## Ce rămâne pentru extinderi separate

Integrarea e-mail, importul automat din e-Factura, OCR general pentru PDF, aplicație mobilă nativă, stoc contabil complet, acces prin internet și lucru offline sincronizat cer proiectare suplimentară. Nu sunt dependențe ale lansării descrise în PDF.

# Pregătirea calculatorului, pas cu pas

## Pasul 1 — Alege calculatorul care găzduiește aplicația

Poate fi unul dintre cele două calculatoare existente. Celălalt accesează aplicația prin browser. PC-ul server trebuie să rămână pornit și conectat cât timp lucrează oricare dintre voi. Verifică în setările Windows repausul pe alimentare și planifică repornirile pentru actualizări.

Pentru început lucrezi pe date de probă. Nu modifici fișierul Excel original și nu îl folosești drept bază de date comună a aplicației.

## Pasul 2 — Instalează instrumentele

În această ordine:

1. **VS Code**, pentru fișiere și terminal; extensia Python este suficientă pentru început.
2. **Git for Windows**, pentru istoricul codului.
3. **Python 3.13 x64**, o versiune de corecție suportată. Activează launcherul `py`; verifică instalarea în PowerShell.
4. **PostgreSQL 17.x**, inclusiv uneltele `psql`, `pg_dump` și `pg_restore`; pgAdmin este util, dar nu obligatoriu.
5. **Caddy și WinSW**, la etapa de instalare în rețeaua firmei, după ce aplicația funcționează local.

Descărcările se fac de pe site-urile oficiale, fără instalatoare neoficiale. Drepturile de administrator Windows sunt necesare pentru unele instalări, servicii, certificat local și reguli firewall; acestea se fac pe PC-ul ales, prin persoana care îl administrează.

În PowerShell verifici:

```powershell
py -3.13 --version
git --version
& "C:\Program Files\PostgreSQL\17\bin\psql.exe" --version
```

Dacă PostgreSQL este instalat în alt loc sau alt major a fost ales și verificat, ajustezi calea. În rezultat trebuie să apară versiunile instrumentelor, fără eroare „not recognized”.

## Pasul 3 — Creează directoarele

Exemplul folosește `C:\TQM`. Dacă politica firmei cere altă cale, se schimbă unitar în configurări și servicii.

```powershell
New-Item -ItemType Directory -Force C:\TQM\proiect
New-Item -ItemType Directory -Force C:\TQM\config
New-Item -ItemType Directory -Force C:\TQM\date\media
New-Item -ItemType Directory -Force C:\TQM\date\backup
New-Item -ItemType Directory -Force C:\TQM\logs
New-Item -ItemType Directory -Force C:\TQM\scripts
Set-Location C:\TQM\proiect
git init
```

| Director | Conținut |
|---|---|
| `C:\TQM\proiect` | Codul aplicației și fișierele de dependențe |
| `C:\TQM\config` | Configurări locale și secrete, în afara Git |
| `C:\TQM\date\media` | Documentele încărcate, cu nume interne generate |
| `C:\TQM\date\backup` | Copii intermediare; nu este singurul loc de backup |
| `C:\TQM\logs` | Jurnale tehnice și rezultatele întreținerii |
| `C:\TQM\scripts` | Scripturile de serviciu, backup și restaurare |

Nu este necesar să publici codul sau datele pe GitHub pentru a folosi Git local. Documentele firmei, parolele, copiile bazei și fișierele Excel reale nu intră în Git. `.gitignore` va fi livrat în lotul 01.

## Pasul 4 — Creează mediul Python

```powershell
Set-Location C:\TQM\proiect
py -3.13 -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install "Django>=5.2,<5.3"
.\.venv\Scripts\python.exe -m pip install "psycopg[binary]>=3.2,<4"
.\.venv\Scripts\python.exe -m pip install "waitress>=3,<4"
.\.venv\Scripts\python.exe -m pip install "openpyxl>=3.1,<4"
.\.venv\Scripts\python.exe -m pip install "reportlab>=4,<5"
.\.venv\Scripts\python.exe -m pip install "python-dotenv>=1,<2"
.\.venv\Scripts\python.exe -m django --version
.\.venv\Scripts\python.exe -m pip check
```

Acestea sunt intervale inițiale pentru proba de compatibilitate, nu fișierul final de versiuni. Lotul 01 va salva dependențele verificate în `requirements.lock.txt`. Actualizările ulterioare se fac controlat și se retestează.

Folosim calea directă către Python din `.venv`; nu este necesară relaxarea politicii PowerShell pentru a activa mediul. Dacă rețeaua firmei blochează descărcarea pachetelor, administratorul configurează accesul sau un set de pachete offline; nu dezactivezi verificarea certificatelor.

## Pasul 5 — Pregătește PostgreSQL și cele trei medii

La instalare, păstrezi portul 5432 dacă este liber, autentificare cu parolă și acces doar din calculatorul server. Reții parola administrativă în locul stabilit de firmă. Aplicația nu va rula cu utilizatorul PostgreSQL `postgres`.

În pgAdmin sau `psql`, administratorul creează:

| Mediu | Bază | Utilizare |
|---|---|---|
| Dezvoltare | `tqm_dev` | Construirea aplicației și date demonstrative |
| Test | `tqm_test` sau baze temporare prefixate `test_tqm_` | Teste automate, inclusiv două conexiuni concurente |
| Producție | `tqm_prod` | Datele reale după pilot |

Rolul de test poate crea/șterge numai bazele de test din mediul de dezvoltare conform procedurii. În producție separăm rolul de migrare de rolul aplicației: aplicația are drepturi de operare, fără privilegii administrative asupra serverului. Scriptul exact pentru roluri, proprietari, scheme și privilegii implicite va fi livrat și verificat în lotul 02/17.

`pg_trgm` se activează o singură dată în baza potrivită, de un rol autorizat. Nu se încearcă instalarea extensiei la fiecare căutare.

Lotul 02 livrează verificarea conexiunii și configurările. **Nu rula `migrate` pe configurarea implicită SQLite a unui proiect Django nou.** Mai întâi configurăm explicit PostgreSQL și demonstrăm conexiunea către `tqm_dev`.

## Pasul 6 — Completează fișierul local de configurare

Lotul 01 va livra un `.env.example` fără secrete și instrucțiuni pentru `C:\TQM\config\development.env`, `test.env` și `production.env`.

Datele necesare: mediul, cheia Django, numele bazei, utilizatorul, parola, gazda, portul, calea documentelor, fusul orar, numele serverului și configurarea HTTPS. Configurarea trebuie să refuze pornirea în producție dacă lipsesc secretele, dacă `DEBUG` este activ sau dacă este selectată din greșeală baza de dezvoltare.

Nu partajăm parola contului Windows pentru a crea conturile aplicației. Parolele TQM sunt stocate cu mecanismele Django; actorul unei aprobări vine din sesiunea autentificată.

# Structura codului și modul în care lucrăm

## Arhitectura

Browserul trimite cererile către Caddy prin HTTPS. Caddy servește resursele statice și transmite operațiile către Waitress, care rulează Django. Django citește și scrie în PostgreSQL și gestionează accesul la documentele locale.

**Flux:** browser → Caddy → Waitress/Django → PostgreSQL; documentele sunt pe disc, iar descrierile, versiunile și legăturile lor sunt în baza de date.

Nu avem nevoie inițial de React, Node ca server al aplicației, Redis, Celery, Kubernetes sau servicii AI. Sarcinile periodice se pornesc prin comenzi Django și Windows Task Scheduler. Dacă apar mai târziu operații lungi care o justifică, se poate adăuga un worker.

## Directoarele proiectului

```text
C:\TQM\proiect\
  manage.py
  config\
    settings\base.py
    settings\development.py
    settings\test.py
    settings\production.py
    urls.py
    wsgi.py
  apps\
    accounts\        conturi, autentificare, preferințe personale
    core\            numere, versiuni, operații repetabile, jurnal
    catalog\         furnizori, proiecte, locații, materiale, UM
    requests\        necesare, poziții, surse și revizii
    sourcing\        solicitări COF, invitații, oferte, comparație
    orders\          comenzi, versiuni, angajamente și planuri
    logistics\       recepții, stoc confirmat, transferuri, retururi
    control\         facturi, alocări, corecții, avansuri, predări
    dossier\         fișiere, aplicabilitate, probleme și verificări
    reporting\       Astăzi, căutare și rapoarte
    imports\         previzualizări, loturi, mapare ID și erori
  templates\
  static\css\
  static\js\
  static\fonts\
  tests\integration\
  tests\acceptance\
  docs\
  requirements.in
  requirements.lock.txt
  .env.example
  .gitignore
```

Fiecare modul are `models`, `forms`, `views`, `urls`, `services`, `selectors`, `tests` și `migrations`, după mărime. `services` conține acțiunile de afaceri; `selectors` conține citirea și agregările. Formularele și rapoartele folosesc aceleași servicii și formule. Nu copiem regula de cantitate în trei locuri.

Modelul utilizatorului se definește înaintea primei migrări: un model propriu bazat pe `AbstractUser`, indicat prin `AUTH_USER_MODEL`. Cheile străine către utilizatori folosesc acel model. Conturile reale sunt create prin procedura de instalare, cu parole stabilite local; datele demonstrative nu introduc parole implicite în producție.

## Contractul fiecărui lot de cod

1. Verificăm versiunea proiectului de la care pornim și facem un commit local.
2. Primești fișierele exacte, fără a înlocui în orb fișiere pe care le-ai modificat.
3. Primești migrațiile noi, comenzile și datele demonstrative strict separate.
4. Rulezi verificarea de structură și testele relevante pe PostgreSQL.
5. Verifici în browser pașii indicați; dacă apare o eroare, păstrezi mesajul și comanda executată.
6. Facem commit-ul lotului numai după verificări. În datele reale nu se rulează comenzi de resetare ori seed demonstrativ.

Comenzile de bază, **după livrarea lotului 01 și a configurării PostgreSQL**, vor avea forma:

```powershell
.\.venv\Scripts\python.exe manage.py check --settings=config.settings.development
.\.venv\Scripts\python.exe manage.py migrate --settings=config.settings.development
.\.venv\Scripts\python.exe manage.py runserver 127.0.0.1:8000 --settings=config.settings.development
```

Comanda de test va folosi explicit `config.settings.test`. Configurarea de test trebuie să refuze orice nume de bază de producție. Serverul Django de dezvoltare se folosește pentru dezvoltare locală, iar instalarea pentru ambele PC-uri folosește Waitress și Caddy.

# Modelul de date: ce înregistrăm și cum legăm

## Reguli comune tuturor documentelor

Fiecare document are UUID intern, număr vizibil, dată, stare de lucru, `version`, autor, momentul creării și al ultimei modificări. Momentele se păstrează UTC și se afișează Europe/Bucharest. Datele calendaristice de termen sunt `DateField`, fără conversii artificiale de fus orar.

Numărul vizibil se generează pe server pentru tip și an, de exemplu CER-2026-000123, COF-2026-000045 și COM-2026-000087. Se folosește secvență/counter blocat tranzacțional, nu `MAX(număr)+1`. Golurile de numerotare tehnică nu sunt ascunse prin renumerotarea documentelor existente.

Catalogul poate fi modificat, dar denumirile, adresele și condițiile folosite într-un document aprobat sunt păstrate în versiunea acelui document. Un raport istoric nu își schimbă furnizorul pentru că s-a editat ulterior catalogul.

## Cataloage și infrastructură

| Entitate | Date esențiale | Legături / regulă |
|---|---|---|
| Utilizator | nume, login, activ, preferințe | Andrei și Mara au acces funcțional egal; jurnalul folosește contul real |
| Furnizor | denumire, identificare fiscală opțională inițial, contacte, stare | Posibilele duplicate se sugerează; nu se unesc automat |
| Proiect | cod, denumire, stare, adrese/contacte | Aceeași lucrare are un ID unic |
| Locație | nume, tip, adresă, proiect opțional | Depozit, șantier sau altă destinație |
| Material | cod opțional, denumire, specificație, UM | Referință opțională: o poziție poate exista și ca text liber |
| Unitate | cod, denumire, precizie | Conversia comercială este explicită; o rolă nu are implicit 50 m |
| Monedă / curs | cod, rată, dată, sursă confirmată | Fără API extern obligatoriu; cursul folosit se păstrează |
| Operație repetabilă | tip, cheie, amprentă cerere, rezultat | Recunoaște reîncercarea aceleiași acțiuni |
| Eveniment de audit | actor, moment, tip, obiect, versiune, diferențe, motiv | Istoric accesibil din fișă; nu stocăm parole în jurnal |
| Vedere salvată | utilizator, secțiune, filtre, sortare | Preferințele unuia nu schimbă vederea celuilalt |

## Necesar, ofertare și comenzi

| Entitate | Date esențiale | Legături / regulă |
|---|---|---|
| Cerere internă | proiect, solicitant, termen, sursă | Are multe poziții și revizii |
| Poziție necesar | material/text, specificație, UM, cantitate, termen | Identitatea rămâne stabilă la revizie; anularea este explicită |
| Revizie necesar | număr, snapshot, motiv, validare | Ofertele păstrează versiunea la care au răspuns |
| Solicitare ofertă COF | termen, mesaj, versiune | Poate include poziții din una sau mai multe cereri |
| Poziție solicitată | cantitate și descriere trimise | Referă poziția și revizia sursă; consolidarea păstrează subalocările |
| Invitație furnizor | COF, furnizor, destinatar, stare, date | O invitație distinctă pentru fiecare furnizor |
| Ofertă / revizie | furnizor, număr extern, date, monedă, fișier | Reviziile nu suprascriu oferta veche |
| Variantă ofertată | poziție solicitată, produs oferit, cantitate, preț, factori, tehnic | Mai multe variante sunt posibile; nu amestecăm disponibilul lor |
| Cost / condiție ofertă | fix/variabil, bază, minim, pachet, discount | Condițiile se evaluează pentru coșul ales |
| Scenariu de selecție | autor, versiune, cantități propuse | Nu angajează necesarul; devine sursă pentru ciorne |
| Comandă | furnizor, monedă, destinație, stare | Grupare numai pentru condiții compatibile |
| Revizie comandă | snapshot complet, aprobare, trimitere, confirmare | PDF-ul indică exact revizia aprobată |
| Poziție comandată | un necesar sursă, o variantă ofertată, cantitate, preț | Un singur necesar intern pe poziție, chiar dacă PDF-ul agregă afișarea |
| Eveniment angajament | poziție comandă, cantitate semnată, cauză | Aprobarea angajează; anularea confirmată/returul definitiv eliberează |
| Plan de livrare | poziție, cantitate, termen inițial și actual | Revizuirea păstrează data inițială și motivul |

## Execuție, facturi și documente

| Entitate | Date esențiale | Legături / regulă |
|---|---|---|
| Recepție | comandă, document, dată, locație, confirmator | O recepție are mai multe poziții |
| Poziție recepție | poziție comandată, sosit, acceptat, contestat | Cantitățile sunt distincte și validate |
| Operație logistică | tip, sursă, destinație, cantitate, motiv | Transfer, retur definitiv, retur pentru înlocuire, înlocuire, corecție |
| Sursă/alocare stoc confirmat | sursă, locație, cantitate, persoană, dată | Nu pretinde stoc total exact dacă nu avem toate mișcările |
| Drept economic recepționat | poziție comandă, recepție inițială, cantitate eligibilă | Înlocuirea schimbă sursa fizică fără a dubla cantitatea facturabilă |
| Factură/document financiar | tip, furnizor, număr extern, date, monedă, total declarat | Factură, proformă, avans și corecții sunt distincte |
| Linie factură | descriere, UM, cantitate sau valoare, preț, TVA | Costurile fixe se păstrează separat de marfă |
| Alocare factură | linie factură → poziție comandă/drept recepționat | Multe alocări pentru o linie; păstrăm exact totalul sursei |
| Corecție | tip, factură/linie/alocare originală, motiv, valoare/cantitate | Plafon cumulativ; storno preț nu creează cantitate artificială |
| Avans / regularizare | sursă, furnizor, monedă, net, TVA, total | Regularizări cumulative plafonate; fără confirmare de plată inventată |
| Cerință documentară | context, tip, aplicabilitate, stare, dovadă | Confirmare la proiect/comandă/poziție, nu doar după tipul partenerului |
| Fișier / versiune | nume intern, nume original, mărime, hash, tip, stare | Fișierele finale sunt imuabile; linkurile externe sunt marcate ca atare |
| Asociere document | fișier → context, aplicabilitate confirmată | Reutilizare fără a presupune că acoperă orice produs |
| Problemă / excepție | context, titlu, termen, responsabil opțional, rezolvare | Excepția nu șterge diferența calculată |
| Predare / poziție predare | versiuni exacte, actor, dată, borderou | Repetarea aceleiași operații nu dublează predarea |
| Lot import / corespondență | sursă, hash, rând, ID vechi/nou, erori | Import repetabil și trasabil |

Modelul concret va avea tabele de legătură explicite acolo unde sunt necesare chei străine. De exemplu, alocarea unei facturi nu se păstrează ca text „COM-…”. Pentru legături de dosar cu mai multe tipuri de obiecte, implementarea trebuie să verifice existența și să interzică ștergerea sursei. Nu folosim legături generice nevalidate pentru cantități sau bani.

# Stările și acțiunile din aplicație

| Obiect | Stări principale | Regula de trecere |
|---|---|---|
| Cerere | Ciornă → de validat → validată → în lucru → finalizată | Validarea cere câmpurile complete; reviziile ulterioare păstrează sursa |
| Invitație | Pregătită → transmisă → răspuns parțial/complet; refuz/fără răspuns | Transmisă se marchează numai după expedierea reală |
| Ofertă | Ciornă → înregistrată; de reconfirmat/expirată/înlocuită | O revizie nouă păstrează oferta precedentă |
| Comandă | Ciornă → de aprobat → aprobată → transmisă → confirmată | Aprobarea angajează cantitatea în tranzacție |
| Revizie comandă | Ciornă revizie → de aprobat → aprobată → confirmată contractual | Partea recepționată nu se rescrie |
| Anulare rest | Cerută → în așteptare → confirmată/refuzată | Numai confirmarea eliberează obligația |
| Control factură | Înregistrată → de alocat/așteaptă recepție/cu diferențe → pregătită → predată | Excepția are traseu și dovadă proprii |
| Dosar | De confirmat/incomplet/complet verificat/excepție justificată | Este calculat pentru versiunea curentă |

Livrarea, întârzierea, returul și documentele sunt indicatori separați. „Confirmată” nu înseamnă „primită”, iar „primită” nu înseamnă „factură verificată”. Stările calculate provin din operațiile confirmate; nu se editează liber dintr-un dropdown.

Ciornele acceptă lipsuri. Fiecare acțiune finală validează toate lipsurile relevante într-un singur răspuns, cu legături către câmpuri. Mesajele au nivel: informare, avertizare sau blocare. Termenul depășit este vizibil și tratat prin decizia de afaceri stabilită; o cantitate peste disponibil este blocare de integritate.

# Ecranele și comportamentul interfeței

## Elemente comune

- Antet cu utilizatorul autentificat, căutare globală și acces la ajutor.
- Meniu lateral cu cele șapte secțiuni din PDF.
- Etichete persistente, câmpuri obligatorii indicate și culori însoțite de text.
- Furnizor/proiect/locație: căutare tastând și „Adaugă” într-un dialog, cu revenire în formular fără pierderea datelor.
- Câmpuri uzuale vizibile; conversiile, pachetele și excepțiile într-o zonă avansată.
- Fiecare fișă arată sursa, documentele următoare, problemele și istoricul.
- Autosalvarea se aplică ciornelor. Indicatorii sunt „Modificări nesalvate”, „Se salvează”, „Salvat la …”, „Eroare” ori „Conflict”.
- Un răspuns întârziat la autosalvare nu poate marca drept salvate schimbări mai noi. Cererile sunt serializate pe document, cu identificator local și versiune server.
- Validarea, aprobarea, transmiterea, recepția și predarea rămân butoane explicite. Enter într-un autocomplete nu aprobă accidental o comandă.

## Liste și căutare

Listele au 25/50/100 rezultate pe pagină. Filtrarea și căutarea se aplică în PostgreSQL întregului set înainte de paginare. Sortarea are criteriu secundar stabil, de exemplu data și ID-ul.

Caută după număr intern/extern, material/cod, proiect și furnizor. Numărul exact apare prioritar. Rezultatul explică potrivirea: material, poziție sau document. Căutarea globală include și închisele, cu starea afișată; filtrele listelor pot avea alte valori implicite.

Filtrele, sortarea și pagina sunt în URL și în starea de revenire. Vederile salvate aparțin utilizatorului. Pentru o căutare nouă pagina revine la 1. Rezultatele și numărul total folosesc aceleași filtre; o cerere cu trei materiale potrivite se numără ca o cerere, nu trei.

Indici: B-tree pentru numere, date și chei de filtrare; indici potriviți pentru `pg_trgm` pe câmpuri de căutare normalizate. Specificația originală rămâne exactă. Similitudinea textului nu stabilește echivalența tehnică.

## Secțiunile

| Secțiune | Listă și fișă | Acțiuni |
|---|---|---|
| Astăzi | Necesar neacoperit, răspunsuri restante, livrări, diferențe, documente | Deschide exact sursa problemei, cu filtre vizibile |
| Cereri | Cereri și poziții, sursă, revizii, acoperire | Creează, lipește, salvează ciornă, validează, cere oferte, acoperă din stoc |
| Oferte | COF, invitații, răspunsuri, comparație și coș | Generează PDF/tabel, marchează transmis, importă răspuns, selectează |
| Comenzi | Ciorne, aprobări, confirmări și planuri | Pregătește, aprobă, exportă, transmite, confirmă, revizuiește, cere anulare |
| Livrări | Recepții, resturi, transferuri, retururi și înlocuiri | Confirmă recepție/serviciu, contestă, returnează, înlocuiește |
| Control | Facturi, alocări, diferențe, avansuri, documente și predări | Verifică, corectează, justifică excepție, generează borderou, predă |
| Administrare | Cataloage, parametri și monitorizare tehnică permisă | Adaugă/editează, gestionează importuri și vede rezultatul backupului |

Interfața tehnică Django Admin este separată de aplicația de afaceri. Acces complet în TQM nu înseamnă cont PostgreSQL administrativ sau acces automat la toate setările tehnice Django.

# Fluxul 1 — Necesar, solicitare și ofertă

## Creează necesarul

1. Deschizi Cereri → Cerere nouă.
2. Alegi sau adaugi proiectul. Completezi solicitantul real și termenul; operatorul din sesiune nu este presupus solicitant.
3. Alegi destinația și sursa dacă sunt cunoscute.
4. Introduci materialele sau lipești un tabel din Excel.
5. La lipire, alegi coloanele, separatorii și sensul virgulei zecimale. Previzualizarea arată rândurile originale și erorile.
6. Alegi un material existent ori păstrezi reperul nou ca text. Specificațiile incerte sunt marcate de confirmat.
7. Salvezi ciorna; primești numărul generat de server. Confirmarea importului nu validează necesarul.
8. „Validează necesarul” cere proiect, solicitant, termen și poziții cu material, UM și cantitate pozitivă. Confirmarea tehnică păstrează persoana, data și sursa.

Nu se stabilește o limită de 10, 22 sau 100 de poziții ca regulă de afaceri. Listele și importurile folosesc paginare/loturi tehnice configurabile. Un fișier mare primește mesaje și procesare controlată, fără trunchiere ascunsă.

## Acoperă din stoc confirmat

Din poziția de necesar alegi sursa, locația, cantitatea, persoana și data confirmării. Serverul verifică disponibilul sursei în tranzacție. Doar alocarea confirmată reduce restul de cumpărat. O mențiune „cred că avem 100 m” rămâne informație de verificat.

O cantitate cumpărată deja printr-o comandă pentru același necesar nu este dedusă încă o dată ca stoc existent. Transferul ulterior schimbă locația fizică, nu creează o nouă acoperire economică.

## Pregătește COF și invitațiile

1. Selectezi pozițiile și cantitățile din necesarul rămas. Pot proveni din mai multe cereri, păstrând separat sursele.
2. Selectezi furnizorii; poți adăuga unul provizoriu fără un catalog complet.
3. Confirmi termenul de răspuns și textul către furnizor.
4. Serverul salvează COF și invitațiile. Generează PDF-ul și tabelul XLSX de răspuns din versiunea salvată.
5. Descarci și trimiți prin e-mailul firmei. Apoi marchezi efectiv transmiterea, destinatarul și data.
6. Urmărești răspuns parțial/complet, refuz sau lipsă de răspuns.

Dacă necesarul se schimbă, creezi revizie. Invitația trimisă nu se rescrie; păstrează snapshotul și legătura cu revizia veche. Aplicația indică ofertele care trebuie reconfirmate.

## Înregistrează răspunsul furnizorului

Deschizi invitația → Înregistrează răspuns. Furnizorul și pozițiile sunt preluate. Completezi numărul extern, datele, moneda, produsul oferit, prețul, disponibilul și condițiile. Poți importa un tabel cu mapare și previzualizare. PDF-ul se atașează; nu presupunem extragere universală.

Fiecare poziție are un răspuns explicit: ofertată, neofertată sau de clarificat. Neofertată are preț nul (`NULL`), nu zero. Pentru preț zero se cere confirmare explicită. O alternativă tehnică este identificată ca variantă și nu devine conformă doar pentru că denumirea seamănă.

# Fluxul 2 — Comparație și reguli de calcul

## Cantități și bani

Folosim `Decimal`, trimis ca text în JSON și reprezentat ca `NUMERIC` în PostgreSQL. Cantitățile au 4 zecimale, prețurile/factorii/cursurile până la 6, totalurile monetare uzuale 2. Dacă sursa are precizie mai mare, importul cere confirmare pentru rotunjire; nu trunchiază tacit.

Serverul deține calculele. Interfața poate arăta o previzualizare, dar confirmă totalurile returnate de aceeași logică server. Calculele intermediare folosesc un context Decimal suficient, de exemplu 28 de cifre; prețul normalizat afișat nu se rotunjește prematur și nu devine o nouă sursă de adevăr. Regula propusă de rotunjire monetară este `ROUND_HALF_UP`, configurată și înregistrată în documentele finalizate. Toleranța de 0,01 RON este propunere din PDF, nu adevăr universal și nu se propagă necontrolat pe sute de linii.

Notăm:

- `D`: cantitatea validată curentă a necesarului;
- `R`: renunțare confirmată la necesar;
- `S`: acoperire din stoc existent, confirmată, fără dublarea comenzilor;
- `C`: obligația netă activă din comenzi, inclusiv partea deja primită;
- `A`: cantitatea netă acceptată fizic în obligația respectivă.

```text
Rest de cumpărat = D - R - S - C
Rest de primit pe comandă = C - A
```

Recepționarea unei comenzi nu eliberează necesarul pentru a-l cumpăra din nou: `C` include și partea executată. O valoare negativă este neconcordanță de rezolvat; nu o ascundem prin `max(0, rezultat)`.

## Conversii de ofertă

Factorul UM și baza prețului sunt câmpuri diferite:

```text
Cantitate în UM necesar = cantitate ofertată × factor UM
Preț net în UM necesar = preț / baza prețului × (1 - discount / 100) / factor UM
```

Exemple demonstrative:

- 450 lei pentru 100 m: baza prețului este 100, factor UM este 1 → 4,50 lei/m.
- 225 lei/rolă de 50 m: baza este 1 rolă, factorul este 50 m/rolă → 4,50 lei/m.
- Pentru 125 m, ambalare în role de 50 m, selecția de 2,5 role nu este acceptată dacă furnizorul vinde doar role întregi. Se clarifică 150 m și se aprobă revizia necesarului sau repartizarea surplusului; nu cumpărăm automat peste necesar.
- Preț deja net: discountul suplimentar este zero sau inactiv. Nu aplicăm din nou reducerea din document.

Conversia necesită sursă și confirmare. Lungimea continuă minimă a cablului se verifică separat: două role de 50 m nu acoperă automat cerința unei bucăți continue de 100 m.

## Coșul furnizorului

Totalul selecției include marfa aleasă, costurile fixe/variabile aplicabile și discountul de pachet confirmat. Transportul fix de 50 lei nu devine 30 lei doar pentru că alegi 60% din material. O comandă viitoare din aceeași ofertă nu primește automat transport gratuit.

Coșul verifică: disponibil neconsumat, minime cantitative/valorice, multipli de ambalaj, valabilitate, produs tehnic acceptat și condiții de pachet. Pachetul integral cere toate componentele/volumele la care furnizorul condiționează oferta; selecția incompatibilă cere ofertă revizuită.

Costurile comune se repartizează propus după valoare; ajustările manuale păstrează totalul exact. Folosim distribuirea resturilor de rotunjire în unități monetare minime, cu ordine stabilă pentru egalități. Exemplu: 100,00 lei între trei ponderi egale devine 33,34 + 33,33 + 33,33, nu 99,99.

Pentru monede diferite, afișăm moneda contractuală și echivalentul RON pe un curs confirmat, cu dată și sursă. Valoarea RON de comparație nu înlocuiește prețul contractual în moneda sa.

## Selecția și recomandarea

Poți împărți o poziție între furnizori. Exemplu: necesar 500 m, A oferă 300 m, B 500 m. Alegerea 300 A + 200 B este posibilă dacă toate condițiile coșurilor sunt îndeplinite.

Aplicația afișează acoperirea și costurile, fără a declara un optim global pe baza celui mai mic preț unitar. Dacă explică „alegerea mai scumpă”, baza de comparație trebuie să fie comparabilă și vizibilă; motivul este confirmat de utilizator.

Scenariile și ciornele nu rezervă definitiv cantitatea. La aprobare, disponibilul se reverifică. Interfața arată separat cantitatea propusă în ciorne pentru a evita surpriza când lucrați amândoi.

# Fluxul 3 — Comandă, versiune și aprobare

1. Din comparație alegi Pregătește comenzile.
2. Vezi gruparea pe furnizor, monedă, condiții, destinații și alte compatibilități. Nu combinăm tacit condiții comerciale incompatibile.
3. Serverul creează ciorne și poziții cu legături stabile la necesar și ofertă. Ciornele nu consumă disponibilul.
4. Completezi identitatea comercială și destinația. Furnizorul provizoriu trebuie identificat din documentul comercial înainte de comanda fermă.
5. „Aprobă” verifică versiunea, necesarul, disponibilul ofertei, costurile și confirmările. În aceeași tranzacție salvează snapshotul aprobat și angajamentele.
6. După commit, se generează PDF-ul acelei versiuni. Dacă exportul eșuează, comanda rămâne identificabilă și exportul poate fi reluat. Nu primește status de PDF generat dacă fișierul nu există complet.
7. Marchezi transmisă după expediere și păstrezi dovada/documentul transmis.
8. Înregistrezi confirmarea furnizorului, termenul promis inițial și termenul actual. Termenul necesar al șantierului rămâne separat.
9. Pentru tranșe, cantitățile și datele se definesc pe poziție. Replanificarea păstrează originalul și motivul.
10. O schimbare după aprobare creează revizie și reaprobare. Efectul contractual se confirmă separat; versiunea deja transmisă rămâne accesibilă.

Partea recepționată nu se modifică retroactiv. Suplimentul sau schimbarea produsului se tratează prin poziție/comandă nouă legată de cea inițială. O cerere de anulare nu eliberează cantitatea până la confirmarea ei; întârzierea furnizorului nu este anulare automată.

PDF-ul păstrează numărul, revizia, data, condițiile și datele comerciale din snapshot. O regenerare din aceeași versiune păstrează conținutul comercial, chiar dacă data tehnică a generării fișierului este diferită.

# Fluxul 4 — Livrare, stoc confirmat, retur și serviciu

## Recepția unei tranșe

Din comanda confirmată sau transmisă alegi Înregistrează recepția. Formularul preia pozițiile și restul disponibil. Completezi data, documentul, locația reală, persoana care confirmă și cantitățile sosite/acceptate/contestate. În modelul de bază, pentru o recepție finalizată `sosit = acceptat + contestat`; o situație încă neclasificată rămâne ciornă ori caz de clarificare, nu acceptare implicită.

Recepția finală se confirmă printr-o operație tranzacțională. Se blochează sursele cantitative și se reverifică disponibilul. O depășire față de comanda aprobată nu devine automat cantitate acceptată: se clarifică și se aprobă sursa economică sau se consemnează contestat, conform deciziei reale.

Pentru o comandă de 500 m:

1. Prima tranșă: 200 m acceptați → rest de primit 300 m.
2. A doua tranșă: 300 m acceptați → rest 0 m.
3. Cele două recepții păstrează datele, documentele și identitățile lor.

Cantitatea contestată nu intră în disponibilul utilizabil. O corecție a recepției se face prin operație legată, cu motiv. Dacă există transferuri sau facturi dependente, se arată efectele și se blochează finalizarea până când dependențele sunt tratate prin operațiile corespunzătoare.

## Stoc și transfer

Recepția acceptată înregistrează o sursă fizică în locația aleasă. Dacă materialul ajunge direct pe șantier, locația este șantierul, fără un depozit intermediar inventat. Transferul din depozit la șantier referă sursa și cantitatea disponibilă; are două efecte fizice legate, într-o singură tranzacție.

Transferul nu creează o nouă recepție furnizor, o nouă cumpărare sau un nou drept de facturare. Dacă evidența consumurilor nu este completă, interfața spune „cantitate urmărită/confirmată”, fără a o prezenta drept stoc contabil total exact.

## Retur definitiv

Se pornește din recepția sursă. Completezi cantitate pozitivă, motiv, document, persoană și dovada acceptării soluției definitive. Până la confirmarea comercială poate exista un retur fizic în așteptare, fără eliberarea automată a obligației.

La confirmarea definitivă se reduc cantitatea fizică păstrată și obligația vechii comenzi. Dacă necesarul rămâne activ, se redeschide restul de cumpărat. Situația financiară se tratează distinct: dacă marfa fusese facturată, aplicația urmărește corecția necesară. Returul nu generează singur o factură storno pretins primită.

Exemplu: `D=500`, `C=500`, acceptat 200. Retur definitiv confirmat de 20 → `C=480`, acceptat net 180, rest de primit 300, rest de cumpărat 20.

## Retur pentru înlocuire

Păstrează obligația furnizorului și creează cazul „În așteptarea înlocuirii”. Nu eliberează necesarul pentru o altă achiziție. Primirea înlocuirii pornește din cazul respectiv și nu poate depăși cantitatea încă de înlocuit.

În același exemplu, retur pentru înlocuire de 20 → `C=500`, acceptat net fizic 180, rest total de primit 320: 300 din tranșa inițială și 20 înlocuire. După sosirea celor 20 de înlocuire, restul revine la 300.

**Controlul facturabilității este separat de mișcarea fizică.** Prima recepție acceptată creează dreptul economic recepționat. Înlocuirea se leagă de același drept, fără încă o cantitate facturabilă. Returul definitiv reduce dreptul net; dacă există facturare, apare diferența de rezolvat. T18/T19 trebuie să demonstreze că înlocuirea nu permite facturarea de două ori.

## Servicii

O poziție poate fi serviciu: manoperă, încercare, etapă de montaj ori alt serviciu definit. Acceptarea se face pe cantitate/etapă și proces-verbal/dovadă, referind comanda. Nu produce intrare într-un stoc fizic. Factura se corelează cu acceptarea serviciului.

# Fluxul 5 — Facturi, corecții, avansuri și predare

## Înregistrare și alocare

1. Deschizi Control → Factură nouă din comandă sau furnizor.
2. Introduci numărul extern, data, scadența dacă se urmărește, moneda, totalurile declarate și documentul original.
3. Aplicația propune poziții și recepții eligibile ale furnizorului; confirmi alocările.
4. Dacă factura sosește înaintea recepției, o salvezi și rămâne „În așteptarea recepției”. Nu se inventează o recepție și nu devine complet verificată.
5. Pozițiile se verifică în moneda contractuală, cu prețul din revizia aprobată a comenzii.
6. Transportul și alte costuri apar o singură dată; subalocările lor au exact totalul sursei.
7. Diferențele creează probleme concrete, cu documentul/poziția afectată și acțiunea necesară.

Unicitatea documentului se verifică pe furnizor, număr normalizat și tip, potrivit PDF-ului. Normalizarea trebuie conservatoare: spații marginale și diferențe de litere, fără a elimina arbitrar separatori care pot schimba identitatea. Numerele similare și același hash de fișier produc avertizări. Dacă furnizorii reali repetă legitim numerele între ani, regula se clarifică înainte de import; nu se adaugă tacit anul pentru a ocoli duplicatele.

O linie de factură poate avea subalocări către mai multe recepții/comenzi. Cantitatea, netul, TVA și costurile sunt reconciliate cu sursa. O linie încă nealocată nu este duplicată pentru fiecare recepție. Prețul de control se citește din snapshotul contractual, nu din cea mai nouă ofertă a furnizorului.

Exemplu din PDF: 200 m facturați la 4,80 față de 4,50 aprobat → diferență netă `200 × 0,30 = 60,00` în moneda contractului. Dacă moneda este RON, diferența este 60,00 lei. Cursul de raportare nu schimbă această diferență contractuală.

## Storno cantitate și storno preț

- **Storno cantitate:** pornește din documentul/linia/alocarea inițială. Introduci magnitudinea pozitivă. Serverul aplică semnul și plafonează cumulul corecțiilor față de sursa corectabilă.
- **Storno preț:** introduci suma ori diferența de preț justificată, fără o cantitate artificială. Nu eliberează o cantitate pentru a fi facturată din nou.
- Corecția păstrează furnizorul, moneda, sursa, documentul primit și motivul. Nu rescrie factura inițială.
- Factura storno se păstrează distinct de evenimentul logistic. Un retur și un storno pot avea momente diferite.

O alocare de storno cantitativ reduce consumul net al dreptului economic inițial. Un retur definitiv reduce și capacitatea netă a acelui drept. Ambele efecte sunt urmărite explicit, pentru a evita eliberarea dublă a cantității.

## Proformă

Proforma este document de urmărire separat. Nu reprezintă acceptare de marfă, nu consumă recepția și nu intră în totalul mărfii facturate. Poate fi legată de comandă și de avansul ulterior, cu documentele aferente.

## Avans și regularizare

Avansul este legat de furnizor și monedă. Se păstrează netul, TVA și totalul, sursa și eventual dovada comunicată de compartimentul financiar. Înregistrarea unui document de avans nu este confirmare automată că s-a făcut plata.

Regularizarea are sursă explicită și sume distincte net/TVA/total. Cumulul folosit nu depășește avansul eligibil. Două persoane nu pot consuma simultan aceeași sumă: se blochează rândul sursă înaintea verificării și scrierii.

Factura finală afișează separat marfa/costurile și taxele, regularizarea avansului și restul documentului. Valoarea achiziției raportată ca marfă facturată nu scade încă o dată pentru că avansul a fost regularizat. Plățile nu se scad din valoarea facturată.

TVA este introdus conform documentului și regulilor confirmate cu compartimentul financiar. Nu fixăm în cod o cotă unică pentru toate produsele, perioadele și documentele.

## Predare către facturare/contabilitate

„Pregătită de predare” este rezultat de verificare, iar „Predată” consemnează acțiunea efectivă. Se salvează operatorul, momentul, documentele, versiunile și borderoul. Un fișier lipsă la export poate fi regenerat din snapshotul predării.

Predarea cu excepție păstrează diferența/lipsa, justificarea și dovada acceptării ei. Nu schimbă neconcordanța în „totul corect”. O modificare ulterioară a dosarului este vizibilă ca fiind după predare și poate necesita o predare suplimentară, fără rescrierea borderoului vechi.

# Concurență, integritate și contractele serverului

## Editarea aceleiași fișe

Fiecare formular primește `version`. La salvare trimite versiunea citită. Serverul face actualizare condiționată și incrementare atomică; o versiune veche primește răspuns de conflict, cu valorile curente și diferențele relevante.

Datele locale rămân în browser până când alegi ce păstrezi. Nu există salvare forțată implicită și nici revizie globală care să blocheze două documente independente.

Propunerea pentru răspunsuri: validare de câmp în corpul răspunsului 400/422, conflict 409, sesiune expirată tratată explicit, obiect negăsit 404. Pe succes se returnează ID, număr, versiune și mesaj. Un răspuns de eroare nu redirecționează tacit către o fișă goală.

## Aprobări și operații cantitative

Ordinea tehnică obligatorie:

1. Verifică autentificarea și forma datelor; citește cheia operației.
2. Începe `transaction.atomic()`.
3. Recunoaște operația repetată și verifică amprenta cererii.
4. Blochează sursele afectate cu `select_for_update`, în ordine stabilă după tip și UUID: necesare, ofertă/disponibil, surse de stoc sau recepție, după caz.
5. Recalculează disponibilul și toate constrângerile din datele confirmate.
6. Scrie documentul/versiunea, alocările, evenimentele și auditul.
7. Salvează rezultatul operației repetabile în aceeași tranzacție.
8. Commit. Apoi execută efectele externe regenerabile, precum PDF-ul.

Toate căile care consumă o sursă trebuie să blocheze același rând părinte. O singură funcție corectă nu ajută dacă alt endpoint scrie alocări fără aceeași blocare. Sumele alocărilor între mai multe rânduri necesită servicii tranzacționale și teste; un simplu `CHECK` SQL pe un rând nu verifică toate aceste totaluri.

Două persoane care încearcă să comande ultimii 100 m sunt testate cu două conexiuni PostgreSQL reale: prima confirmă, a doua recalculează și primește indisponibil/conflict. Testele pe SQLite sau doar un buton dezactivat în browser nu demonstrează regula.

## Dublu clic și reîncercare

Browserul generează o cheie UUID pentru o acțiune finală și o păstrează la reîncercare. Tabelul de operații are unicitate pe domeniul operației și cheie; include amprenta datelor.

- Aceeași cheie și același conținut: se întoarce rezultatul deja salvat.
- Aceeași cheie și alt conținut: conflict, fără altă operație.
- Chei diferite pentru aceeași sursă: verificarea disponibilului previne depășirea.
- Eșec înainte de commit: nu rămâne o operație comercială pe jumătate.

Dezactivarea vizuală a butonului ajută utilizarea, dar garanția este pe server. O pierdere a răspunsului după commit nu trebuie să producă încă o recepție la reîncercare.

## Constrângeri și istoricul schimbărilor

PostgreSQL trebuie să aibă chei străine, unicitate pentru ID/număr/revizie/cheie de operație, limite de semn și cantități valide. Documentele finalizate se corectează prin revizie sau operație legată. Ștergerea unei surse folosite este protejată.

Evenimentul de audit conține actorul autentificat, momentul, operația, sursa, versiunea și diferențele. Motivul este cerut pentru corecții, revizii, excepții și anulări. Auditul de afaceri și logul tehnic sunt separate. Accesul egal al utilizatorilor nu permite ștergerea tacită a istoricului.

# Documente, alerte, rapoarte și salvarea fișierelor

## Atașamente și PDF

Încărcarea folosește un fișier temporar în locație controlată, verificarea mărimii/tipului, hash și nume intern generat. Numele original este doar metadată; nu devine cale executabilă sau cale de scriere arbitrară. Formatele și limitele se configurează.

Stocarea finală se face prin publicare atomică pe același disc și stare explicită `în pregătire/gata/eșuat`. Fișierul și tranzacția PostgreSQL nu formează singure o tranzacție comună: mecanismul trebuie să recunoască erorile și fișierele orfane. Un fișier „gata” este imuabil; o modificare produce versiune nouă.

Pentru PDF: documentul aprobat este sursa, după commit; generarea poate fi reluată și are amprentă/revizie. Comanda nu este duplicată dacă generarea PDF eșuează. O comandă de întreținere detectează fișiere lipsă, temporare vechi și orfane și produce raport înaintea curățării.

Fișierele încărcate se descarcă printr-o vedere Django autentificată. Caddy servește doar `staticfiles`, fără a expune public directorul `media`. Linkurile externe sunt afișate ca externe și nu echivalează cu o copie păstrată local.

Documentele originale cu macrocomenzi se păstrează ca atașamente și nu se execută de aplicație. Importul XLSX/XLSM citește datele; nu rulează VBA. Fișierele CSV/XLSX exportate tratează textul utilizatorului ca text, astfel încât o descriere care începe cu `=` să nu devină o formulă executată accidental.

## Aplicabilitatea documentelor

Profilul se confirmă pe proiect/comandă și se completează pe poziție. Cerința are aplicabilitate: obligatoriu, opțional, de confirmat sau neaplicabil justificat. Starea dovezii este solicitat/primit/verificat; verificarea cere persoană, moment și dovadă.

Același fișier poate acoperi mai multe poziții doar prin legături cu aplicabilitate confirmată. Se păstrează versiunea folosită și eventual expirarea. O declarație primită nu este automat o declarație verificată.

O problemă are titlu concret, context, termen, stare, rezolvare și dovadă. Responsabilul poate lipsi, conform PDF-ului; lipsa lui nu restrânge accesul celuilalt utilizator.

## Astăzi

Cardurile deschid lista exact filtrată. Separăm număr de documente de număr de poziții și de număr de cerințe. Exemplu: „3 comenzi / 7 poziții întârziate”, nu un total fără unitate.

Acțiunile includ: necesar urgent neacoperit, răspuns de urmărit, confirmare lipsă, livrare întârziată, înlocuire restantă, factură cu diferență, avans de clarificat și document aplicabil lipsă/expirat. Termenul unei probleme și termenul documentului pot fi diferite și se afișează clar.

Alertele pot fi calculate la citire și prin comenzi periodice pentru stări persistate. O sarcină repetată nu creează probleme identice zilnic: are cheie de deduplicare. Nu presupunem trimiterea de e-mail automată.

## Rapoarte

Filtre: proiect, furnizor, perioadă, monedă și stare. Fiecare total se deschide până la sursele sale și se poate exporta.

- **Angajat net:** obligațiile active la termenii aprobați, cu anulări definitive tratate.
- **Recepționat:** cantitățile acceptate net, evaluate la prețul contractual potrivit; înlocuirile nu dublează valoarea economică.
- **Facturat net:** marfă/costuri minus corecții eligibile; avansurile și regularizările sunt prezentate separat.
- **Rest de cumpărat/primit:** conform formulelor din capitolul 10.

Pentru RON, raportul precizează cursul și data folosite. Nu adunăm EUR cu RON fără conversie explicită. Plățile nu sunt deduse din facturat. Indicatorii nu folosesc probabilități statistice din date demonstrative.

# Instalarea pentru ambele calculatoare

Această etapă începe după pilotul local și după livrarea fișierelor de instalare din lotul 17.

## Nume de rețea și adresă

Se propune `achizitii.tqm.internal`. Administratorul rezervă o adresă locală stabilă pentru PC-ul server și configurează rezoluția numelui prin DNS local sau fișierul `hosts` pe ambele PC-uri. Adresa IP se alege din rețeaua reală; nu se copiază un exemplu arbitrar.

Aplicația nu este publicată prin port forwarding. Accesul din exterior se tratează separat. Pentru început, browserul ambelor PC-uri deschide aceeași adresă HTTPS și aceeași bază centrală.

## Cele trei servicii

| Serviciu | Ascultă | Dependențe și configurare |
|---|---|---|
| PostgreSQL | Numai loopback pe server | Serviciul instalatorului, autentificare cu parolă/SCRAM |
| TQMApp / Waitress | `127.0.0.1:8000` | Cont de serviciu cu acces la cod/config/media/loguri și baza aplicației |
| TQMProxy / Caddy | HTTPS 443 în rețeaua locală | Certificat local, resurse statice și reverse proxy către Waitress |

WinSW va avea executabilul, directorul de lucru, variabilele de mediu, jurnalizarea, oprirea controlată și repornirea după eșec. Caddy are drepturi pe propriul spațiu de certificate. Conturile de serviciu și parolele nu se includ în Git. Versiunea WinSW se alege și se verifică din distribuția oficială, fără un număr de versiune presupus în acest ghid.

Comenzile de instalare/start/stop vor fi livrate în scripturi reproductibile. Verificăm pornirea după restart Windows, ordinea serviciilor și indisponibilitatea clară când aplicația ori baza sunt oprite.

## HTTPS și certificat local

Caddy poate emite un certificat prin autoritatea sa locală. Certificatul rădăcină public al acelei autorități se instalează ca rădăcină de încredere pe ambele PC-uri, prin administrator. Cheia privată a autorității rămâne pe server și în backupul protejat; nu se copiază la clienți.

Locul de stocare Caddy se configurează explicit, de exemplu `C:\TQM\date\caddy`, pentru a nu depinde de profilul interactiv al persoanei care l-a pornit prima dată. Verificăm certificatul și numele înaintea importului în magazinul de încredere. Nu folosim dezactivarea avertismentelor de certificat ca procedură de lucru.

În Django producție: `DEBUG=False`, gazde permise exacte, origini CSRF HTTPS exacte, cookie-uri securizate și cheia secretă din configurarea locală. Antetul de protocol HTTPS este acceptat numai prin proxy-ul local configurat; Waitress nu este expus direct în LAN. Conexiunea către aplicație este servită prin Caddy.

## Firewall și verificare de pe al doilea PC

Administratorul permite TCP 443 numai pe profilul privat și subnetul local. Exemplu de regulă, de aplicat numai pe serverul ales:

```powershell
$regulaTqm = @{
    DisplayName = "TQM HTTPS local"
    Direction = "Inbound"
    Action = "Allow"
    Protocol = "TCP"
    LocalPort = 443
    Profile = "Private"
    RemoteAddress = "LocalSubnet"
}
New-NetFirewallRule @regulaTqm
```

Porturile PostgreSQL și Waitress rămân inaccesibile din al doilea PC. Regula trebuie revizuită dacă rețeaua conține mai multe segmente ori politici existente.

Proba minimă pe ambele calculatoare: login, aceeași cerere vizibilă, editare concurentă, atașament, PDF, logout și restart al serverului. După primele descărcări de pachete, funcțiile de bază trebuie să funcționeze fără acces internet; fonturile și resursele UI sunt locale.

Rularea producției va include verificarea Django `check --deploy`, colectarea resurselor statice și migrațiile cu rolul potrivit. Descoperirile se rezolvă sau se justifică pentru arhitectura locală; nu se ascund prin dezactivarea globală a verificărilor.

# Backup, restaurare și actualizări

## Ce intră într-un backup recuperabil

- Dump PostgreSQL în format custom, cu versiunea uneltei compatibilă cu serverul.
- Documentele încărcate și metadatele/manifestul lor, cu hash-uri și mărimi.
- Configurările necesare restaurării, protejate și accesibile doar persoanelor desemnate.
- Versiunea codului, fișierul de dependențe și migrațiile folosite.
- Configurarea serviciilor și a certificatului local ori procedura de reemitere și reinstalare a încrederii.
- Raportul operației, momentul, dimensiunile și verificările.

`pg_dump` nu salvează automat fișierele de pe disc și nici toate rolurile globale PostgreSQL. Rolurile sunt reconstruite din procedura de instalare, cu parole gestionate separat. O copie a folderului `proiect` nu este backup de date.

## Consistența datelor și fișierelor

Pentru prima versiune alegem o fereastră scurtă de întreținere:

1. Anunță intervalul și împiedică operații noi.
2. Oprește controlat serviciul aplicației după terminarea scrierilor active. Caddy afișează indisponibilitatea/întreținerea.
3. Execută `pg_dump`, copiază documentele imuabile și generează manifestul verificabil.
4. Verifică reușita uneltelor și existența tuturor fișierelor, apoi publică arhiva finală prin redenumire atomică.
5. Repornește aplicația chiar dacă backupul eșuează, consemnează rezultatul și semnalează lipsa unei copii noi valide.

Aceasta este mai simplu de demonstrat decât a pretinde că două copieri făcute în paralel cu scrieri active sunt coerente. La volum mai mare se poate implementa snapshot coordonat și fișiere imuabile cu manifest aferent exact acelui snapshot.

Scriptul nu pune parola în linia de comandă sau log. Un fișier de credențiale PostgreSQL are ACL restrâns și este separat de cod. Windows Task Scheduler rulează sub contul desemnat, cu căi absolute și raportează eșecul.

## Copie separată și păstrare

Propunere din PDF: 14 zilnice și 8 săptămânale, dacă spațiul permite. Copia de pe discul serverului este intermediară; backupul valid se copiază și pe alt PC/suport existent, cu acces limitat. Dacă ținta lipsește, scriptul păstrează copia locală și raportează explicit că duplicarea nu a reușit.

Rotirea copiilor se face numai după un backup nou verificat și fără a șterge singura copie validă. Pentru documentele confidențiale se folosește protecția de acces și, dacă se adoptă criptare, cheia de recuperare se păstrează separat și se probează pe alt calculator.

## Restaurarea de probă

1. Pregătește o instanță separată, fără a atinge `tqm_prod`.
2. Instalează majorul PostgreSQL compatibil, rolurile și versiunea codului din manifest.
3. Restaurează dumpul cu `pg_restore` și documentele în directorul dedicat.
4. Completează configurarea de test a restaurării și reconciliază migrațiile; nu actualiza schema înainte de a identifica versiunea restaurată.
5. Verifică numerele de documente, cantitățile, totalurile, utilizatorii, hash-urile, fișierele și legăturile.
6. Deschide un dosar complet în browser și regenerează un PDF dintr-o versiune aprobată.
7. Salvează raportul restaurării și timpul măsurat. T24 este trecut numai cu această probă, nu doar cu existența unui fișier `.dump`.

## Actualizarea aplicației

Se face backup verificat, se testează noua versiune pe o copie, se programează întreținerea, se aplică migrațiile și se verifică un caz reprezentativ. Reîntoarcerea la cod vechi poate fi incompatibilă cu schema nouă; procedura de revenire este testată per versiune și poate cere restaurarea perechii bază+fișiere. Nu presupunem că un simplu `git checkout` anulează o migrare de date.

# Migrarea din Excel și importurile repetabile

## Inventarul exact al sursei

Pentru V5 este necesar fișierul efectiv menționat de PDF. Până atunci, livrăm cadrul importului, nu o mapare de coloane inventată. Fișierul se copiază și se identifică prin hash, nume, dată și versiune. Importul nu execută VBA.

Inventarul va arăta foile/tabelele, antetele, cheile, relațiile, numărul de rânduri semnificative, duplicatele, tipurile, formulele și erorile. Foile istorice/de backup rămân istorice; nu devin automat tranzacții active.

## Procesul de import

1. Încărcare și înregistrare lot, fără scriere în documentele operaționale.
2. Alegerea mapării pentru versiunea exactă a sursei.
3. Parsare cu păstrarea valorii originale și a adresei foii/rândului.
4. Previzualizare: corect, lipsă, duplicat posibil, relație necunoscută, eroare de cantitate/monedă/precizie.
5. Confirmare umană pentru specificații, unități, date și clasificarea documentelor.
6. Import în ordine: cataloage confirmate → documente → poziții → operații/alocări → documente și probleme → istoric.
7. Corespondențe unice `(sistem sursă, tip, ID vechi) → ID nou`; rândul fără ID primește cheie stabilă documentată.
8. Raport final cu rânduri acceptate, respinse și neimportate, plus reconciliere.

Același lot/hash reluat nu dublează datele. Un fișier nou care conține aceleași ID-uri sursă este verificat ca actualizare/revizie/conflict, nu considerat automat document nou. Pentru import mare, confirmarea poate fi pe grupuri tranzacționale documentate, cu stare reluabilă și raport; nu lăsăm un import parțial pretins complet.

## Criterii de reconciliere

Numărul documentelor și pozițiilor pe tip, necesar validat, angajamente active, recepții nete, retururi, drepturi facturabile, facturi/corecții, avansuri folosite/disponibile și numărul documentelor atașate trebuie să se potrivească cu sursa acceptată. Nu reconciliem tranzacții folosind simple totaluri care amestecă monede ori ciorne cu documente confirmate.

Datele demonstrative rămân în dezvoltare/test. Pornirea reală are dată de tranziție, listă de documente deschise, responsabil de verificare și copia Excelului păstrată pentru referință. După tranziție, operațiile noi au aplicația ca sursă oficială convenită; nu se introduc aceleași operații independent în două sisteme fără o regulă de reconciliere.

# Pilotul și utilizarea zilnică

## Un caz complet obligatoriu

Folosește un set demonstrativ controlat, nu prețuri considerate istorice:

1. Cerere cu 500 m cablu și două materiale auxiliare.
2. Trei furnizori invitați, unul fără răspuns, unul parțial, unul cu ofertă completă.
3. Preț pe 100 m și ofertă pe rolă, plus transport fix.
4. Selecție împărțită și comandă aprobată pe un coș valid.
5. PDF, trimitere consemnată și confirmare cu tranșe.
6. Recepții 200 m și 300 m; un caz separat pentru retur/înlocuire.
7. Factură cu diferența de 60 lei pentru prima tranșă și corecție legată.
8. Document aplicabil lipsă, problemă și rezolvare/excepție explicită.
9. Predare cu borderou, rapoarte reconciliate și restaurarea backupului.

Proba finală se face de pe cele două PC-uri, cu două conturi, inclusiv acțiuni simultane. Machetele din PDF nu sunt dovezi că acest caz a fost executat.

## Performanță

Se generează cel puțin 1.000 cereri, cu poziții, furnizori și documente suficiente pentru căutări reprezentative. Pragurile propuse în PDF: căutare obișnuită sub 1 secundă și salvare obișnuită sub 2 secunde, fără încărcări mari de fișiere. Se măsoară pe rețeaua și PC-urile reale, cu volumul și metoda de măsurare consemnate.

Se verifică numărul de interogări, planurile de căutare, paginarea și lipsa încărcării tuturor pozițiilor în browser. 1.000 de cereri este un set de probă, nu limită funcțională sau promisiune pentru orice configurație.

## Condiții pentru începerea lucrului real

- T01–T24 trecute și dovezile păstrate.
- Utilizatorii pot parcurge cazul complet fără introducerea ID-urilor interne.
- Calculele și importul sunt reconciliate, iar datele demonstrative sunt separate.
- HTTPS funcționează pe ambele PC-uri și serviciile repornesc corect.
- Backupul rulează, copia separată există și restaurarea a fost demonstrată.
- Sunt confirmate documentele necesare, persoanele tehnice, numerotarea și toleranțele.

În utilizarea zilnică, Astăzi este punctul de plecare. Se urmăresc întâi necesarele urgente, apoi confirmările/livrările, facturile cu diferențe și documentele. O problemă rezolvată are dovadă; o replanificare păstrează termenul inițial. Verificarea rezultatului backupului intră în rutina desemnată, fără a transforma utilizatorii în administratori ai bazei.

# Loturile de cod și tabla de sarcini

Estimările de mai jos sunt orientative pentru implementare și verificare pe un mediu deja pregătit. O sarcină este dimensionată aproximativ între 1 și 4 ore pentru cine cunoaște instrumentele; configurarea hardware, clarificările, corecțiile și învățarea pot prelungi lucrul. Nu reprezintă un termen contractual. Loturile pot ocupa mai multe sesiuni și nu sunt aprobate ca „gata” doar fiindcă s-a scris codul.

Priorități: **P0** fundație și circuitul vertical; **P1** completarea pilotului și a regulilor obligatorii; **P2** finisare/optimizare. P2 nu înseamnă că un test obligatoriu din PDF poate fi omis la lansare.

## Loturi și livrabile

| Lot | Livrabil concret | Dependență | Când este gata |
|---|---|---|---|
| 00 | Inventar PC, versiuni, căi, configurări și decizii | — | Datele tehnice sunt cunoscute, fără schimbarea Excelului |
| 01 | Proiect Django, dependențe, configurări, Git, pagină de pornire | 00 | Proiectul pornește local și refuză configurații incomplete |
| 02 | PostgreSQL, modele comune, versiuni, numere, audit și operații repetabile | 01 | Migrațiile și testele de integritate trec pe PostgreSQL |
| 03 | Login, conturi egale, meniu, cataloage și adăugare în context | 02 | Ambii văd aceleași date, fără pierderea formularului |
| 04 | Cereri, lipire/import și validare/revizii | 03 | Un necesar valid este căutabil și trasabil |
| 05 | COF, invitații și documente de solicitare | 04 | Trei invitații preiau aceleași surse corecte |
| 06 | Oferte, variante, import și comparație simplă | 05 | Răspunsuri parțiale și selecții corecte, fără prețuri zero presupuse |
| 07 | Ciorne de comandă, aprobare atomică și două sesiuni | 06 | Pragul A și testele de supracomandare trec |
| 08 | Conversii, costuri, praguri, pachete, monede și tehnic | 06–07 | Selecția avansată și aprobarea folosesc aceleași reguli |
| 09 | Revizii, PDF, transmitere, confirmare, tranșe și anulări | 07–08 | Snapshotul și obligațiile rămân corecte |
| 10 | Recepții și servicii | 09 | Tranșele și corecțiile au cantități reconciliate |
| 11 | Stoc confirmat, transfer, retur și înlocuire | 10 | Fizicul și capacitatea facturabilă nu se dublează |
| 12 | Facturi, alocări, diferențe și costuri | 10–11 | Comandă–recepție–factură se verifică exact |
| 13 | Stornări, proforme, avansuri și predări | 12 | Corecțiile sunt plafonate și borderourile reproducibile |
| 14 | Dosar, aplicabilitate, probleme și fișiere | 03; finalizare după 09–13 | Fișierele sunt protejate și lipsurile rămân vizibile |
| 15 | Căutare la volum, Astăzi, rapoarte și export | 04; finalizare după 14 | Totalurile au surse, filtre și performanță măsurată |
| 16 | Import V5, previzualizare, mapare și reconciliere | 04–15 + XLSM exact | Același import nu dublează operațiile |
| 17 | Instalare Windows, servicii, HTTPS și backup/restaurare | 01–15 | Două PC-uri funcționează și T24 trece |
| 18 | Pilot final, documentație, remediere și tranziție | 16–17 | T01–T24 și datele reale de pornire sunt acceptate |

Infrastructura fișierelor și căutarea de bază se construiesc devreme, deși finalizarea lor apare în loturile 14 și 15. Nu lăsăm securizarea documentelor ori accesul egal pentru sfârșit. La lotul 07 avem prima probă completă a nucleului, înainte de funcțiile speciale.

## Sarcini mici, în ordinea recomandată

| ID | Sarcină | Pr. | Efort | Depinde de | Gata când |
|---|---|---|---|---|---|
| S01 | Inventariază PC-ul și căile | P0 | 1 h | — | Fișa de configurare este completă |
| S02 | Verifică Python, Git și PostgreSQL | P0 | 1–2 h | S01 | Comenzile de versiune și conexiunea reușesc |
| S03 | Creează proiectul și configurările pe medii | P0 | 2–4 h | S02 | Configurația greșită oprește pornirea |
| S04 | Creează rolurile și conexiunea PostgreSQL | P0 | 2–3 h | S03 | Dev/test sunt izolate de producție |
| S05 | Adaugă UUID, versiuni și numere document | P0 | 2–4 h | S04 | Numere unice sub concurență |
| S06 | Adaugă operațiile repetabile și auditul | P0 | 3–4 h | S05 | Aceeași cheie nu dublează scrierea |
| S07 | Creează conturile și login/logout | P0 | 2–3 h | S04 | Conturi distincte, acces egal |
| S08 | Creează meniul și componentele de formulare | P0 | 2–4 h | S07 | Tab, etichete și mesaje funcționează |
| S09 | Creează cataloagele de bază | P0 | 2–4 h | S05 | Furnizor/proiect/locație au ID stabil |
| S10 | Adaugă căutare și creare în dialog | P0 | 3–4 h | S08–09 | Formularul rămâne completat |
| S11 | Creează stocarea protejată a fișierelor | P0 | 3–4 h | S06–08 | Se folosește din S19 înainte, fără media public |
| S12 | Creează ciorna de necesar și pozițiile | P0 | 3–4 h | S09 | Text liber și catalog coexistă |
| S13 | Adaugă autosalvare cu versiune | P0 | 2–4 h | S06,S12 | Eroarea/conflictul păstrează textul |
| S14 | Adaugă lipire Excel și previzualizare | P0 | 3–4 h | S12 | Rândurile ambigue sunt marcate |
| S15 | Implementează validarea și reviziile necesarului | P0 | 3–4 h | S12–14 | Lipsurile și sursele sunt păstrate |
| S16 | Creează lista/căutarea cererilor | P0 | 2–4 h | S15 | Filtrele se aplică înainte de paginare |
| S17 | Creează COF și legăturile pe poziții | P0 | 3–4 h | S15 | Subsetele păstrează necesarele originale |
| S18 | Creează invitațiile și transmiterea consemnată | P0 | 2–4 h | S17 | Fiecare furnizor are traseu propriu |
| S19 | Generează PDF și tabel XLSX pentru COF | P0 | 3–4 h | S11,S17–18 | Exportul folosește revizia salvată |
| S20 | Creează răspunsul furnizorului | P0 | 3–4 h | S18 | Neofertat este distinct de zero |
| S21 | Adaugă import de ofertă cu mapare | P1 | 3–4 h | S20 | Previzualizarea nu scrie încă tranzacții |
| S22 | Creează selecția simplă pe cantități | P0 | 3–4 h | S20 | Împărțirea 300+200 este posibilă |
| S23 | Generează ciorne grupate compatibil | P0 | 3–4 h | S22 | Pozițiile păstrează sursele |
| S24 | Aprobă atomic și salvează snapshot | P0 | 3–4 h | S06,S23 | Ciornele nu consumă; aprobarea consumă |
| S25 | Testează dublu clic și ultimul disponibil | P0 | 3–4 h | S24 | Două conexiuni nu supracomandă |
| S26 | Implementează conversia și precizia | P1 | 2–4 h | S20 | Preț/100 m și rolă sunt corecte |
| S27 | Implementează costurile și rotunjirea | P1 | 3–4 h | S26 | Costul fix și totalul alocat sunt exacte |
| S28 | Adaugă minime, ambalaje și pachete | P1 | 3–4 h | S27 | Aprobarea respinge selecția incompatibilă |
| S29 | Adaugă monede, curs și confirmare tehnică | P1 | 3–4 h | S26 | Echivalența nu este dedusă din text |
| S30 | Generează PDF din comanda aprobată | P1 | 2–4 h | S24,S27 | Regenerarea nu schimbă termenii |
| S31 | Adaugă transmiterea și confirmarea furnizorului | P1 | 2–4 h | S30 | Dovezile și datele sunt păstrate |
| S32 | Adaugă revizii și anularea confirmată | P1 | 3–4 h | S31 | Partea executată rămâne stabilă |
| S33 | Creează tranșele și replanificarea | P1 | 2–4 h | S31 | Termenul inițial rămâne vizibil |
| S34 | Creează recepția și cantitățile distincte | P1 | 3–4 h | S31 | 200+300 închide restul de 500 |
| S35 | Adaugă corecții de recepție și servicii | P1 | 3–4 h | S34 | Dependențele sunt validate |
| S36 | Creează sursele și alocarea stocului confirmat | P1 | 3–4 h | S15,S34 | Acoperirea nu dublează comenzile |
| S37 | Creează transferurile legate | P1 | 2–4 h | S36 | Intrare/ieșire atomică, fără cumpărare nouă |
| S38 | Creează retur definitiv și înlocuire | P1 | 3–4 h | S34–37 | Cele două tipuri au obligații diferite |
| S39 | Verifică dreptul economic și capacitatea facturabilă | P1 | 3–4 h | S38 | Înlocuirea nu se facturează din nou |
| S40 | Creează factura și verificarea duplicatelor | P1 | 3–4 h | S09,S31 | Identitate și document sursă păstrate |
| S41 | Creează alocările pe recepții și comenzi | P1 | 3–4 h | S39–40 | Totalul sursei nu se multiplică |
| S42 | Adaugă diferențe de preț/cantitate/cost | P1 | 3–4 h | S41 | Exemplul +60 lei este reproductibil |
| S43 | Creează storno cantitate și preț | P1 | 3–4 h | S42 | Corecțiile cumulative sunt plafonate |
| S44 | Creează proforme, avansuri și regularizări | P1 | 3–4 h | S40–43 | Avansul nu se folosește de două ori |
| S45 | Creează cerințele și aplicabilitatea documentelor | P1 | 3–4 h | S11,S31 | Verificat și neaplicabil au dovezi |
| S46 | Creează problemele și excepțiile | P1 | 2–4 h | S42,S45 | Lipsa rămâne vizibilă după excepție |
| S47 | Creează predarea și borderoul versionat | P1 | 3–4 h | S44,S46 | Aceeași operație nu dublează predarea |
| S48 | Creează Astăzi cu surse filtrate | P1 | 3–4 h | S33,S46 | Cardurile și listele au aceleași definiții |
| S49 | Creează rapoartele reconciliate | P1 | 3–4 h | S39–44 | Monedele/avansurile sunt distincte |
| S50 | Adaugă vederi personale și păstrarea filtrelor | P1 | 2–4 h | S16,S48 | Preferințele unuia nu îl afectează pe celălalt |
| S51 | Măsoară căutarea la volum și optimizează indici | P2 | 3–4 h | S49–50 | 1.000 cereri, timp și corectitudine consemnate |
| S52 | Inventariază și mapează XLSM V5 exact | P1 | 3–4 h | S01 + sursa V5 | Diferențele sunt raportate, fără presupuneri |
| S53 | Creează lotul de import și previzualizarea | P1 | 3–4 h | S52,S14 | Erorile au foaie/rând/sursă |
| S54 | Implementează importul repetabil | P1 | 3–4 h | S53,S06 | Hash/ID sursă nu dublează tranzacțiile |
| S55 | Reconciliază importul și separă istoricul | P1 | 3–4 h | S54 | Cantitățile și valorile acceptate se potrivesc |
| S56 | Configurează producția și rolurile restrânse | P1 | 2–4 h | S03–06 | Aplicația nu este administrator PostgreSQL |
| S57 | Instalează Waitress/WinSW și Caddy HTTPS | P1 | 3–4 h | S56 | Servicii și certificat corecte pe ambele PC-uri |
| S58 | Scrie backupul, manifestul și rotația | P1 | 3–4 h | S11,S57 | Eșecurile nu sunt raportate ca succes |
| S59 | Restaurează pe o instanță separată | P1 | 3–4 h | S58 | Datele și fișierele sunt reconciliate |
| S60 | Execută T01–T24 și cazul complet pe două PC-uri | P1 | sesiuni 1–4 h | S47–59 | Dovezi pentru fiecare test, defectele remediate |
| S61 | Scrie manualul de utilizare și procedura de tranziție | P1 | 2–4 h | S55,S60 | Cei doi pot lucra pe un caz verificat |
| S62 | Confirmă pornirea reală și suportul inițial | P1 | 1–4 h | S61 | Sursa oficială și documentele deschise sunt convenite |

Infrastructura fișierelor se construiește înaintea primului export. Dependențele au prioritate când lucrăm pe loturi. Funcțiile dintr-o sarcină mare, cum este S60, se desfășoară în sesiuni mici cu raport de progres.

# Testele de acceptare și dovezile de păstrat

Pentru fiecare test înregistrăm versiunea codului, baza de test, pașii, rezultatul așteptat, rezultatul obținut și dovada. Testele de business se scriu automat în Django; cele de UX și instalare au și probe în browser. Testele de concurență folosesc PostgreSQL și conexiuni distincte, cu sincronizare controlată, nu întârzieri aproximative.

| Test PDF | Cum îl executăm | Dovada obligatorie |
|---|---|---|
| T01 | Andrei și Mara se autentifică și accesează toate secțiunile | Aceleași drepturi funcționale și date; actori diferiți în jurnal |
| T02 | Adaugi furnizor dintr-o ofertă deja completată | Datele rămân, furnizorul nou este selectat |
| T03 | Cauți material/cod/număr/proiect în 1.000 cereri | Rezultate corecte în întregul set, cu filtre și paginare |
| T04 | Cauți exact un număr și un dosar închis | Rezultatul global îl găsește și arată starea |
| T05 | Revii la listă dintr-o fișă | Aceeași pagină, sortare și filtre |
| T06 | Lipești lista de 22 materiale menționată în PDF, când este disponibilă | Previzualizare, ambiguități păstrate, ID-uri automate; până atunci folosim un set demonstrativ separat |
| T07 | Trimiți logic un COF la 3 furnizori | Invitații distincte și surse preluate exact; transmiterea se consemnează |
| T08 | Furnizorul oferă 5 din 22 poziții | 17 neofertate, fără preț zero presupus |
| T09 | Introduci preț pe 100 m și rolă de 50 m | Baza și factorul distincte, preț normalizat corect |
| T10 | Selectezi parțial oferta cu transport fix | Costul fix aplicabil rămâne și totalul este exact |
| T11 | Încerci pachet parțial sau minim neîndeplinit | Aprobare blocată cu explicație, până la selecție/ofertă validă |
| T12 | Alegi echivalent ori cablu fără lungimea continuă cerută | Confirmarea tehnică lipsă blochează aprobarea |
| T13 | Dublu clic și retransmitere după pierderea răspunsului | O comandă/aprobare/angajare pentru aceeași operație |
| T14 | Două conexiuni aprobă simultan ultimii 100 m | Totalul angajat nu depășește disponibilul |
| T15 | Două formulare salvează aceeași versiune | A doua primește conflict și păstrează schimbările locale |
| T16 | Editezi oferta după aprobarea comenzii | Comanda și PDF-ul păstrează termenii snapshotului aprobat |
| T17 | Recepționezi 200 și apoi 300 din 500 m | Rest 300, apoi 0; două recepții și surse distincte |
| T18 | Retur definitiv, înlocuire și transfer pe cazuri controlate | Fizic, obligație și capacitate facturabilă reconciliate, fără dublare |
| T19 | Factură pe două recepții și un singur transport | Alocări exacte, transport o dată, surse facturabile plafonate |
| T20 | Facturezi 200 m la 4,80 față de 4,50 | Diferență 60 lei și corecție legată, fără rescrierea originalului |
| T21 | Storno, proformă și două regularizări concurente | Plafoane respectate, proforma nu facturează marfa, avans nedublat |
| T22 | Document aplicabil lipsă și excepție justificată | Lipsa și dovada rămân vizibile; nu apare complet fără neconcordanțe |
| T23 | Injectezi erori DB, fișier, rețea, timeout și răspuns pierdut | Fără scrieri comerciale parțiale, duplicate ori „Salvat” fals |
| T24 | Restaurezi dump+media+config pe altă instanță | Date, fișiere, legături și totaluri recuperate |

Teste suplimentare necesare: rezervări de stoc concurente, cerere redusă sub cantitatea angajată, preț nul versus zero, reduceri repetate, rotunjire pe multe linii, monede diferite, alocări de storno peste original, corecție de recepție dependentă, fișier inaccesibil după predare și lipsa țintei de backup.

Pentru erori injectate folosim baza și directoarele de test. Nu întrerupem serviciul de producție pentru un test de avarie fără fereastra de întreținere stabilită.

# Trasabilitatea către PDF și automatizări

## Cele 24 de automatizări

| Cod | Automatizare din PDF | Lot / verificare |
|---|---|---|
| A01 | ID, număr și poziții stabile | 02,04; unicitate și revizii |
| A02 | Căutare furnizor/proiect | 03; T02 |
| A03 | Revenire cu furnizor nou selectat | 03; T02 |
| A04 | Lipire și rânduri neclare | 04; T06 |
| A05 | Preluarea pozițiilor în COF | 05; T07 |
| A06 | PDF și tabel din versiunea salvată | 05,09; T16,T23 |
| A07 | Preluarea contextului în ofertă | 06; T08 |
| A08 | Conversii UM/bază/monedă | 08; T09 |
| A09 | Alocare transport cu total exact | 08; T10,T19 |
| A10 | Acoperire și condiții ale coșului | 06,08; T10–T12 |
| A11 | Grupare comenzi compatibile | 07,09; cazul complet |
| A12 | Aprobare și reverificare cantități | 07; T13–T16 |
| A13 | Operație repetabilă | 02,07; T13,T23 |
| A14 | Conflict și diferențe | 02,04; T15 |
| A15 | Termene și acțiuni în Astăzi | 09,15; T03,T05 |
| A16 | Poziții și rest în recepție | 10; T17 |
| A17 | Actualizarea cantităților recepționate | 10; T17 |
| A18 | Surse pentru retur și înlocuire | 11; T18 |
| A19 | Propuneri de alocare a facturii | 12; T19 |
| A20 | Comparație preț/cantitate/cost | 12; T19,T20 |
| A21 | Corecții legate și solduri | 13; T20,T21 |
| A22 | Aplicabilitate documentară confirmată | 14; T22 |
| A23 | Reverificare și snapshot predare | 13–14; T22,T23 |
| A24 | Backup, jurnal și întreținere | 02,17; T24 |

## Acoperirea paginilor sursă

| Pagini PDF | Cerințe | Unde sunt dezvoltate în ghid |
|---|---|---|
| 1–3 | Obiectiv, flux, acces și UX | 1–3,7–8 |
| 4–5 | Căutare și formular necesar | 8–9,15 |
| 6–7 | COF, invitații și ofertă | 9 |
| 8–9 | Comparație și calcul | 10 |
| 10–11 | Comandă, aprobare și versiuni | 11,14 |
| 12–13 | Recepție, stoc, retur, serviciu | 12 |
| 14–15 | Factură, corecții, avans, predare | 13 |
| 16 | Documente și probleme | 15 |
| 17–18 | A01–A24 | 22 |
| 19 | Stări și validări | 7,9–14 |
| 20–22 | Model, calcule, concurență | 6,10,12–14 |
| 23 | Instalare și costuri | 2,4–5,16 |
| 24 | Backup și migrare | 17–18 |
| 25–26 | T01–T24 | 21 |
| 27–28 | Astăzi, rapoarte, etape, performanță | 15,19–20 |
| 29 | Decizii și ipoteze | 2,12–13,16–19 |
| 30 | Sursa V5 și referințe | 2,18,24 |

# Cum vom cere și livra codul ulterior

## Primul mesaj util

Poți porni cu următorul text, adaptând datele între paranteze:

> Începem loturile 00 și 01 din Ghidul TQM A–Z. Pe PC am Windows [versiune], RAM [valoare], spațiu liber [valoare]. Pot instala programe prin [eu/administrator]. Aplicația va rula pe acest PC și va fi accesată de încă un PC din aceeași rețea. Vreau verificarea mediului și apoi fundația Django/PostgreSQL. Dă-mi fișierele cu căi exacte, comenzile PowerShell și rezultatele așteptate. Nu presupune că există XLSM V5 sau lista de 22 de materiale. Păstrează drepturi funcționale egale pentru Andrei și Mara.

Nu este obligatoriu să cunoști încă adresa finală ori toate setările. Începem local și ținem acele valori ca decizii deschise până la instalarea LAN.

## Contract comun pentru fiecare lot

> Continuă cu lotul [număr] din Ghidul TQM A–Z, pornind de la versiunea verificată [commit/arhivă]. Livrează codul necesar, migrațiile, testele și comenzile Windows. Păstrează identificatorii stabili, calculele Decimal, versiunile, auditul, operațiile repetabile și blocarea surselor în PostgreSQL. Spune ce se verifică automat și ce trebuie verificat în browser. Nu modifica funcții deja acceptate fără să explici motivul și efectul.

## Fișa pe care o folosim la finalul lotului

| Câmp | Ce notezi |
|---|---|
| Lot și versiune | Număr, data și commit/arhivă |
| Fișiere aplicate | Lista fișierelor și migrațiilor |
| Comenzi executate | Exact ce ai rulat, fără parole |
| Rezultat automat | Teste trecute/eșuate și mesajul relevant |
| Rezultat în browser | Pașii și ce ai observat |
| Probleme deschise | Ce blochează continuarea și datele de reproducere |
| Următorul lot | Cel permis de dependențe |

La o eroare, trimite textul complet relevant, comanda executată și calea fișierului, fără parole sau chei. Nu este necesar să reconstruiești proiectul din zero pentru fiecare problemă. Codul va rămâne într-un proiect coerent și versionat.

# Referințe, limite și definiția livrării

**Sursa funcțională:** TQM_Proiectare_Aplicatie_v1.pdf, 30 pagini, proiectare v1.0, 20.10.2026. Ghidul nu certifică instalarea sau testarea aplicației încă nescrise.

**Surse tehnice de verificat la instalare:**

- Django, versiuni și suport: https://www.djangoproject.com/download/
- Django 5.2, instalare/Python: https://docs.djangoproject.com/en/5.2/faq/install/
- Tranzacții: https://docs.djangoproject.com/en/5.2/topics/db/transactions/
- Blocări de rând: https://docs.djangoproject.com/en/5.2/ref/models/querysets/#select-for-update
- Constrângeri: https://docs.djangoproject.com/en/5.2/ref/models/constraints/
- Lista de verificare pentru producție: https://docs.djangoproject.com/en/5.2/howto/deployment/checklist/
- PostgreSQL, suport: https://www.postgresql.org/support/versioning/
- PostgreSQL 17, pg_dump: https://www.postgresql.org/docs/17/app-pgdump.html
- PostgreSQL, pg_trgm: https://www.postgresql.org/docs/current/pgtrgm.html
- Python Windows: https://www.python.org/downloads/windows/
- Waitress: https://docs.pylonsproject.org/projects/waitress/en/stable/
- Caddy HTTPS: https://caddyserver.com/docs/automatic-https
- Caddy stocare: https://caddyserver.com/docs/conventions#file-locations
- WinSW: https://github.com/winsw/winsw
- ReportLab: https://www.reportlab.com/docs/reportlab-userguide.pdf
- openpyxl: https://openpyxl.readthedocs.io/

Acestea sunt referințe ale planului. În această sesiune, accesul online de verificare a fost refuzat de proxy; nu declar că am reconfirmat ultimele versiuni de corecție ori conținutul actual al tuturor paginilor. La lotul 00/01 verificăm compatibilitatea și fixăm versiunile concrete înainte de utilizare.

Bugetul zero se referă la licențele/abonamentele componentelor propuse și la lipsa găzduirii externe. Presupune resurse existente pentru calculator, energie, rețea, stocare, backup și timpul de implementare/întreținere. Nu presupune că funcțiile speciale sunt deja realizate.

**Definiția finală a proiectului realizat:** cod versionat și disponibil firmei; două conturi funcționale egale; circuit complet verificat; calcule și import reconciliate; documente și istoric recuperabile; servicii locale stabile; T01–T24 cu dovezi; backup restaurat; manual de utilizare și procedură de actualizare. Fișierul PDF de proiectare și acest ghid sunt baza de lucru pentru acea livrare.
