---
title: "TQM Achiziții — Cerințe pentru Emergent"
subtitle: "Completări punctuale ale aplicației existente, pe baza caietului și a exemplului Excel"
date: "30 octombrie 2026 · versiunea 1.1"
lang: ro-RO
---

# Scop, surse și reguli de implementare

**Destinație:** document de transmis în proiectul TQM deja realizat în Emergent. Se implementează completările de mai jos în aplicația existentă, folosind arhitectura, modelele și componentele ei actuale. Nu este o solicitare de reconstrucție a aplicației sau de schimbare a tehnologiilor.

**Actualizare v1.1:** precizează explicit că modelul de configurație este general și adaugă R10 pentru adăugare, editare, eliminare, arhivare și restaurare. Introduce testele E37–E50. Această versiune înlocuiește specificația v1.0. Dacă o parte din v1.0 este deja implementată, păstrează rezultatele acceptate și implementează diferențele; nu reface întreaga aplicație.

**Surse:** captura aplicației în Emergent, cele două fotografii ale caietului și fișierul `Intend_TQM_Services OIP.xlsx`, foaia `Price Estimate`. Fișierul Excel a fost citit pentru structura configurațiilor și verificarea formulelor; originalul nu a fost modificat. Nu am avut acces la codul ori instanța aplicației Emergent. Comportamentele raportate în caiet sunt cerințe de verificat în proiect, nu defecte reproduse aici.

**Clarificare confirmată de Andrei:** punctele tăiate despre editarea cererilor, schimbarea stării și editarea pozițiilor sunt deja rezolvate. Nu se reconstruiesc. Intră numai în verificările de regresie. Cerința cu stea despre afișarea câmpurilor „Sursă” și „Observații” rămâne activă.

**Ce se păstrează:** identitatea vizuală din captură, navigarea și denumirile existente, datele și legăturile între documente, regulile de acces existente, calculele corecte, reviziile, auditul și mecanismele de protecție la dublare. Schimbările de model se fac prin migrare compatibilă; datele existente nu sunt șterse sau resetate pentru a implementa cerințele.

Captura arată remedieri declarate pentru încărcarea paginii Necesare, Coș după ștergerea datelor de test, restaurarea fără documentul părinte și ștergerea unei recepții de înlocuire. Aceste afirmații sunt sursă pentru teste de regresie, nu dovadă că testele au trecut în această sesiune. La verificare, identifică în cod Coșul de elemente șterse/restaurare și deosebește-l de coșul comercial pe furnizor.

**Livrabil cerut lui Emergent:** modificări funcționale pe frontend și backend, migrații dacă sunt necesare, teste automate relevante și probe în browser. Pentru fiecare cerință, raportează ce ai modificat, ce ai verificat și ce a rămas de confirmat. Nu marca drept implementată o funcție numai pentru că apare un buton.

Prețurile comerciale efective se citesc din Excelul original, care se atașează separat în Emergent. Exemplele numerice din acest document sunt demonstrative. Ele nu înlocuiesc valorile ori condițiile din documentul furnizorului.

# Cerințele active și corespondența cu notițele

| ID | Cerință de implementat | Sursa | Rezultat așteptat |
|---|---|---|---|
| R01 | Afișarea și păstrarea „Sursă/Observații” | Caiet, Flux 1, punctul cu stea | Datele introduse la necesar se văd în fișa lui și rămân după redeschidere |
| R02 | Invitarea furnizorilor din Cereri de ofertă | Caiet, punctul 4 | Acțiune accesibilă din contextul COF, cu aceeași logică existentă |
| R03 | Mai multe oferte/configurații ale aceluiași furnizor | Caiet, punctele 1 și 5 | Două variante ale aceluiași produs se păstrează și se compară distinct |
| R04 | Tabel comparativ pe rânduri și coloane | Caiet, punctul 3 rescris | Materialele sunt pe rânduri, ofertele/variantele pe coloane |
| R05 | Alegere manuală independentă de recomandare | Caiet, punctul 2 | Poți selecta o ofertă eligibilă chiar dacă nu este recomandarea calculată |
| R06 | Configurații cu accesorii, licențe și servicii | Caiet, punctul 5 și Excel | Comparația folosește configurația dorită și condițiile comerciale reale |
| R07 | Facturi cu prețuri cu/fără TVA și documente fără TVA | Caiet, Flux 5 | Baze de introducere distincte, calcule și regim fiscal explicite |
| R08 | Factură introdusă direct | Caiet, Flux 5 | Poți porni factura fără toate etapele anterioare, fără operații fictive |
| R09 | Asocierea facturii la proiecte | Caiet, Flux 5 | Proiectul sau repartizările pe proiecte se păstrează în control și rapoarte |
| R10 | Adăugare, editare, eliminare și restaurare contextuală | Clarificarea ulterioară a utilizatorului | Conținutul poate fi gestionat în stările permise, cu dependențe și istoric păstrate |

R03–R06 formează aceeași funcție de comparație și selecție. R07–R09 formează traseul de factură/control. Implementarea lor trebuie să folosească modele și servicii comune, fără două mecanisme divergente pentru aceeași operație.

R10 se aplică transversal obiectelor gestionate de aceste fluxuri. Funcțiile de editare deja confirmate ca rezolvate rămân în utilizare și la regresie; R10 completează lipsurile, fără a cere reconstruirea lor.

Numerele și numele de câmpuri din exemplele tehnice sunt orientative; adaptează-le la codul existent. Identitatea furnizorului, a ofertei, a variantei și a documentului sursă trebuie păstrată distinct.

# Flux 1 — Contextul cererii și invitarea furnizorilor

## R01 — Sursă și Observații vizibile în cererea potrivită

**Problema raportată:** la „Necesar nou” se pot introduce Sursa și Observațiile, dar informațiile nu mai sunt vizibile unde utilizatorul le caută ulterior.

Comportament cerut:

1. Verifică mai întâi dacă sunt câmpuri separate, un câmp comun ori metadate deja salvate. Repară maparea, salvarea și afișarea înainte de a crea câmpuri duplicate.
2. În fișa fiecărui necesar/cereri interne există un bloc clar „Sursă” și „Observații”. Afișează numai informațiile acelei înregistrări.
3. Păstrează textul integral, diacriticele și rândurile noi. Dacă sursa poate fi fișier/link, păstrează mecanismul existent și indică tipul sursei.
4. După salvare, reîncărcare, închidere/redeschidere și consultarea de celălalt utilizator, valorile rămân identice.
5. Listele pot avea previzualizare scurtă ori coloană opțională; fișa păstrează textul complet. Nu încărca toate listele cu texte foarte lungi.
6. Dacă există Observații pe poziție și Observații generale, etichetează-le separat. Nu copia aceeași observație generală în toate pozițiile și nu le suprascrie.
7. În documentele descendente există acces la cererea sursă. Observațiile interne nu se trimit automat furnizorilor: reutilizează sau adaugă un mesaj extern explicit pentru COF.

**Criteriu de acceptare:** două cereri cu surse și observații diferite afișează fiecare informațiile proprii, inclusiv după relogare și consultare de pe alt cont. Editarea deja reparată continuă să funcționeze.

## R02 — Invită furnizori din Cereri de ofertă

În lista sau fișa unei cereri de ofertă, utilizatorul poate selecta furnizori existenți, adăuga unul prin mecanismul existent și pregăti invitațiile. Acțiunea trebuie disponibilă în contextul „Cereri de ofertă”, conform notiței, nu doar într-un alt ecran COF.

- Reutilizează aceeași componentă și aceeași acțiune backend. Dacă „COF” și „Cereri de ofertă” sunt două intrări către aceeași entitate, păstrează o singură sursă de date.
- Preia COF-ul și revizia lui; nu cere ID-uri introduse manual și nu creează alt COF doar pentru a invita.
- Afișează furnizorii deja invitați, starea și termenul. Reapăsarea nu dublează invitația pentru același furnizor și aceeași revizie.
- Permite adăugarea unui furnizor nou la o solicitare existentă, păstrând invitațiile și răspunsurile precedente.
- Un răspuns poate conține mai multe variante ale aceluiași furnizor; nu este necesar să dublezi furnizorul în catalog.
- „Pregătește invitația”, generarea documentelor și transmiterea efectivă au sensuri distincte. Nu declara e-mail trimis dacă funcția reală nu a trimis unul. Păstrează consemnarea manuală atunci când acesta este mecanismul aplicației.

# Flux 2 — Variante ale aceluiași furnizor, matrice și alegere

## R03 — Oferte/configurații distincte pentru același furnizor

Modelul trebuie să permită: **un furnizor → mai multe oferte/răspunsuri → mai multe variante pentru aceeași poziție de necesar**.

Se păstrează pentru fiecare variantă: ID intern, documentul sursă și revizia, numărul extern dacă există, denumirea variantei, produsul principal, componentele, prețurile, moneda, disponibilul, livrarea, durata serviciului, starea tehnică și condițiile comerciale.

Exemple valide:

- același furnizor, două numere de ofertă pentru același material;
- un singur document comercial, două configurații alternative ale aceluiași produs;
- o ofertă revizuită care înlocuiește versiunea anterioară.

Aceste situații sunt distincte. Două configurații alternative pot fi comparate simultan. O revizie care a înlocuit oferta veche păstrează vechea versiune în istoric, dar nu o reactivează automat pentru aprobare.

Nu folosi drept cheie unică doar combinația „furnizor + material”. Nu suprascrie a doua variantă peste prima și nu o elimina din comparație prin grupare/deduplicare după furnizor.

Configurațiile alternative ale aceleiași cantități nu dublează disponibilul. Se păstrează o grupare de alternative și sursa comună când documentul o indică. Pentru aceeași unitate necesară, alegerea uneia nu înseamnă cumpărarea simultană a ambelor.

**Criteriu de acceptare:** un furnizor cu două variante apare cu ambele înregistrări identificabile în comparație; alegerea păstrează exact varianta și documentul selectate până în comandă.

## R04 — Tabel comparativ real

Locul recomandat: **Comparații și scenarii → Tabel comparativ**, cu acces și din ofertele înregistrate/COF. Dacă aplicația are deja altă organizare utilă, adaugă o vedere reutilizabilă, fără două implementări ale comparației.

Structură demonstrativă:

| Element comparat | Furnizor A / oferta 1 / varianta 12 luni | Furnizor A / oferta 1 / varianta 36 luni | Furnizor B / oferta 2 |
|---|---|---|---|
| Produs și configurație | Produs + componente | Același produs, alt suport | Produs/echivalent identificat |
| Cantitate comparată | Cantitate și UM | Cantitate și UM | Cantitate și UM |
| Preț normalizat | Net/UM dacă baza este confirmată | Net/UM dacă baza este confirmată | Net/UM dacă baza este confirmată |
| Cost configurație | Total componente selectate | Total componente selectate | Total componente selectate |
| Servicii și durată | Tip și 12 luni | Tip și 36 luni | Conținutul ofertei |
| Livrare și eligibilitate | Termen, stare, motiv | Termen, stare, motiv | Termen, stare, motiv |
| Alegere | Cantitate/selectează | Cantitate/selectează | Cantitate/selectează |

Pentru mai multe materiale, fiecare poziție de necesar este un rând sau un grup de rânduri extensibile; fiecare ofertă/variantă este o coloană distinctă. Costurile comune și totalul coșului sunt separate de liniile de material.

Comportament minim:

1. Antete care includ furnizorul, oferta și varianta; două coloane cu același furnizor nu sunt ambigue.
2. Fixarea antetului și a coloanei materialului în timpul derulării; scroll orizontal când sunt multe oferte.
3. Filtre după necesar/COF/proiect/material și acces la documentul original.
4. Cantitatea efectiv comparată și configurația de referință sunt vizibile. Nu compara tacit 1 bucată cu 10 bucăți sau suport de durate diferite ca și cum ar fi identice.
5. Preț sursă și monedă, preț normalizat și cost total al selecției, cu discount, transport și alte costuri aplicabile o singură dată.
6. Lipsa unei oferte apare „Neofertat”, cu preț absent, nu zero. Prețul zero explicit rămâne distinct.
7. Celulele indisponibile/neeligibile indică motivul. Recomandarea și selecția utilizatorului au indicatoare separate, cu text și culoare.
8. Schimbarea cantității sau a componentelor recalculează matricea și coșul prin aceeași logică server. Cantitățile deja angajate și regulile de concurență existente rămân respectate.
9. Compararea nu creează automat comenzi și nu modifică în fundal ofertele originale.

Nu se cere aici un nou export Excel al matricei dacă acesta nu există deja. Prioritatea este analiza și selecția funcțională pe ecran; un export existent trebuie să folosească aceleași calcule.

## R05 — Recomandată nu înseamnă singura selectabilă

Utilizatorul poate alege orice ofertă/variantă **eligibilă**, chiar dacă nu este prima în clasamentul de preț sau nu este marcată de sistem ca recomandată/câștigătoare.

- Elimină condiția greșită de tip „se poate selecta doar oferta recomandată”, dacă este identificată în interfață ori backend.
- Păstrează recomandarea calculată distinct de alegerea explicită a utilizatorului.
- Pentru o alegere mai scumpă, păstrează/solicită motivul comercial conform mecanismului aplicației: livrare, disponibilitate, tehnic, condiții sau alt motiv.
- Recalculează coșul pentru alegerea efectivă, fără a reseta selecția la oferta cea mai ieftină la reîncărcare.
- Comanda și istoricul păstrează oferta, varianta, configurația, cantitatea, motivul și utilizatorul real.
- Selecția parțială și împărțirea între furnizori rămân posibile în limitele necesarului și condițiilor ofertelor.

Această libertate nu dezactivează verificările de disponibil, cantitate, valabilitate, conformitate tehnică, pachet integral, versiune sau confirmare comercială. O ofertă cu o condiție neîndeplinită se clarifică, nu se transformă în eligibilă printr-un checkbox.

# R06 — Accesorii, licențe și servicii în configurația comparată

## Regulă generală, fără cod particularizat pe Excel

Excelul atașat este **un singur caz de probă**, nu șablonul fix al produselor permise. Funcția trebuie să poată reprezenta orice produs/configurație relevantă activității firmei: echipament simplu, tablou electric, corp de iluminat cu driver și kituri, cablu cu accesorii, aparat cu montaj, ansamblu cu licențe ori servicii.

- Un produs poate avea zero, una sau mai multe componente. Nu impune existența accesoriilor pentru un produs simplu.
- Numărul de oferte, variante, componente și niveluri nu este fixat după cele două configurații din Excel. Eventualele limite tehnice sunt explicite și justificate, fără trunchiere ascunsă.
- Denumirile, codurile, tipurile, UM, cantitățile, prețurile, perioadele și regulile componentelor provin din date. Introducerea unui produs nou nu cere modificarea codului ori o nouă publicare a aplicației.
- Categoriile de componente pot fi extinse controlat din date sau prin etichete/descrieri; nu este o listă închisă de accesorii ale unui anumit producător.
- Cantitatea unei componente poate fi pe produs, pe lot sau pe perioadă, cu baza explicită. Schimbarea cantității produsului principal recalculează numai componentele dependente de acea bază.
- Sunt posibile subansambluri, dacă oferta le conține; păstrează părintele, ordinea și regulile. Interzice ciclurile și referințele către o altă configurație fără legătură.
- Duratele de 12 și 36 luni sunt valori ale exemplului. Alte durate ori servicii fără durată fixă se înregistrează conform ofertei, fără condiții hardcodate pentru aceste două valori.
- Regula comercială poate fi diferită de la ofertă la ofertă: componentă separabilă, inclusă fără reducere, obligatorie, opțională, necunoscută ori parte dintr-un pachet integral.

Comparația trebuie să funcționeze pe acest model comun. Exemplul Excel verifică o instanță a regulilor; testele suplimentare verifică produse cu alte structuri. Nu construi o pagină specială valabilă doar pentru produsul, codurile sau rândurile acestui fișier.

## Datele necesare

O variantă are produs principal și componente legate: accesoriu, licență, serviciu ori informație de configurație. Pentru fiecare componentă se păstrează:

- identificator și cod/descriere, ordinea și părintele în configurație;
- cantitate și bază: pe produs, pe pachet sau pe perioada indicată;
- mod de preț: tarif separat, inclus fără tarif separat ori rând informativ/subtotal;
- valoare sursă și moneda, inclusiv zero explicit;
- durată dacă este serviciu/licență;
- aplicabilitate: obligatoriu, opțional ori de confirmat;
- selecția din scenariu, motivul excluderii și starea confirmării tehnice/comerciale.

Numere precum `1.0`, `1.0.1` și `1.8.1` rămân texte. Nu se convertesc în numere zecimale și nu devin ID-uri interne. Relația părinte–copil se confirmă la maparea datelor; ierarhia nu este suficientă pentru a decide dacă o componentă este obligatorie.

## Cum se configurează și se calculează

1. Pornești de la oferta originală, care rămâne păstrată ca sursă. O corectare a transcrierii sau o revizie se face explicit și versionat, potrivit R10.
2. Deschizi „Configurează varianta” și vezi componentele incluse, prețul și condițiile fiecăreia.
3. Poți selecta/deselecta componente opționale. O componentă obligatorie ori cu statut neclar poate fi exclusă într-o simulare, dar configurația cere soluționare/reconfirmare înaintea comenzii ferme.
4. Costul se calculează din liniile care contribuie economic, fără dublarea părintelui și a copiilor dacă unul este doar subtotal informativ.
5. Eliminarea unei componente incluse la preț zero nu reduce automat prețul produsului principal.
6. Eliminarea unui serviciu tarifat separat reduce costul simulat cu acel serviciu pentru cantitatea/baza corectă. Dacă furnizorul condiționează pachetul, costul rămâne simulare până la reconfirmare.
7. Dacă accesoriile sunt incluse într-un preț global și nu au prețuri separabile, aplicația nu inventează reducerea. Se înregistrează „Preț de reconfirmat” și, ulterior, o ofertă/variantă revizuită.
8. Licențele și condițiile tehnice se verifică explicit. O licență cu cost nu se elimină automat pentru că utilizatorul a ales „fără servicii”.
9. Transportul, minimele, discounturile și pachetul integral se reevaluează pentru configurația și cantitățile efective.
10. Alegerea aprobată păstrează un snapshot al configurației. Modificarea ulterioară a scenariului nu rescrie o comandă aprobată.

Pe ecran se disting **valoare ofertă originală**, **cost configurație selectată**, **diferență de conținut** și **stare de confirmare**. O diferență obținută prin scoaterea serviciilor nu este prezentată automat ca negociere sau economie pentru același conținut.

## Exemplu aritmetic demonstrativ

Aceste valori sunt fictive și sunt folosite numai pentru testarea logicii:

| Componentă | Varianta A | Varianta B |
|---|---|---|
| Echipament principal | 1.000,00 | 1.000,00 |
| Licență păstrată | 300,00 | 300,00 |
| Suport separat | 120,00 / 12 luni | 300,00 / 36 luni |
| Accesoriu inclus | 0,00 | 0,00 |
| Total inițial | 1.420,00 | 1.600,00 |
| Simulare fără suport | 1.300,00 | 1.300,00 |

Scoaterea accesoriului cu preț zero lasă totalurile inițiale neschimbate. Scoaterea suportului separat produce totalurile de 1.300,00 numai ca simulare sau pe baza condițiilor comerciale confirmate. Licența de 300,00 rămâne.

Nu transforma un preț pentru întregul suport de 12/36 luni în preț lunar fără o bază contractuală care o spune. Durata nu este multiplicator automat al unui preț deja calculat pe întreaga perioadă.

## Cum se folosește Excelul real atașat

Fișierul conține o singură foaie, `Price Estimate`, cu două grupuri de câte 12 linii: configurația A la rândurile 14–25 și configurația B la 29–40. Ambele au același produs principal; suportul este pe 12 luni, respectiv 36 luni. Mai există o licență pe 36 luni în ambele configurații și componente cu preț zero.

| Element sursă | Configurația A | Configurația B |
|---|---|---|
| Produs principal | Rând 14 | Rând 29 |
| Suport separat | Rând 15, durată în E15 | Rând 30, durată în E30 |
| Licență tarifată | Rând 24 | Rând 39 |
| Total cu suport | I26 = SUM(I14:I25) | I41 = SUM(I29:I40) |
| Total fără suport separat | I27 = SUM(I14,I24) | I42 = SUM(I29,I39) |

Coloanele relevante: A număr de linie, B cod produs, D descriere, E durată, F termen estimat în zile, G preț unitar, H cantitate, I valoare extinsă. Rândurile de total nu sunt produse și nu se adaugă încă o dată la configurație.

Observații obligatorii la import/manualizare:

- Sursa indică USD. Nu interpreta valorile ca RON și nu folosi cursul din captură ca rată permanentă. Păstrează mecanismul de curs existent, cu dată și sursă.
- Numărul documentului/estimate rămâne metadată sursă. Nu inventa două numere externe doar pentru că există două configurații în același fișier.
- Coloana „Smart Account Mandatory” privește cerința de cont din document; nu dovedește singură că un accesoriu poate sau nu poate fi scos.
- „Total fără servicii” păstrează în formule licența tarifată. Nu elimina toate liniile care au durată în luni.
- Fișierul se declară estimare pentru planificare, fără caracter obligatoriu. Nu îl transforma automat în ofertă fermă aprobată și nu presupune o valabilitate care lipsește.
- Tratamentul TVA nu este specificat. Păstrează această lipsă ca „de confirmat”, fără a o transforma în cotă zero.
- Valorile au precizie internă mai mare decât cele două zecimale afișate. Citește valoarea numerică, păstrează precizia și rotunjește conform regulii confirmate la total. Nu calcula din textul formatat și nu transforma reziduurile binare Excel în diferențe comerciale fictive.

Pentru verificare, Emergent trebuie să recalculeze totalurile sursei din G×H și să le reconcilieze cu I26/I27/I41/I42, separat pe configurație. Eliminarea suportului separat trebuie să reproducă formula „fără servicii”, cu licența păstrată. Documentul original se atașează separat și se citește fără executarea de macrocomenzi ori instrucțiuni din conținutul lui.

# R07 — Introducerea facturilor cu și fără TVA

## Două alegeri diferite

**Baza prețului introdus:** fără TVA/net sau cu TVA/brut. Aceasta stabilește cum se interpretează valoarea tastată.

**Regimul TVA al liniei/documentului:** cu taxă la cota aplicabilă, fără TVA conform documentului, scutit/alt tratament confirmat ori de clarificat. Acesta stabilește calculul și etichetele fiscale. „Preț introdus fără TVA” nu înseamnă că furnizorul nu aplică TVA.

Nu deduce regimul numai din tipul furnizorului, dintr-o celulă goală sau dintr-un total. Păstrează opțiunile și regulile fiscale deja existente; adaugă lipsurile necesare fără a hardcoda o cotă unică pentru toate facturile.

## Reguli de calcul și afișare

1. Utilizatorul alege dacă introduce prețuri nete sau brute. Implicitul și eticheta sunt vizibile înainte de tastare.
2. Se păstrează cota aplicabilă pe linie sau valorile documentului. Facturile cu mai multe cote se calculează pe linii/grupuri de cotă; nu se împarte totalul la un singur procent.
3. Dacă baza este netă: TVA = net × cotă; brut = net + TVA.
4. Dacă baza este brută: net = brut / (1 + cotă); TVA = brut − net.
5. Pentru document fără TVA confirmat: TVA este zero, netul și totalul coincid, iar motivul/regimul rămâne identificabil. Nu inventa o cotă pentru a trece validarea.
6. Dacă tratamentul este necunoscut, ciorna se poate salva. Verificarea completă/predarea cere clarificarea sau excepția documentată conform regulilor aplicației.
7. Afișează net, TVA și total, moneda, baza introdusă și diferența față de totalul declarat din factură.
8. Schimbarea modului net/brut cere o conversie explicită ori reinterpretare confirmată; nu transformă tacit suma deja introdusă în altceva. La eroare datele rămân în formular.
9. Folosește Decimal sau mecanismul monetar exact disponibil în backend, fără calcule finale în float JavaScript. Păstrează regulile de rotunjire/toleranță existente dacă sunt confirmate.

Exemplu exclusiv aritmetic, cu o cotă de test de 21%: 100,00 net + 21,00 TVA = 121,00 brut. Introducerea a 121,00 în modul brut trebuie să producă aceleași rezultate. Cota de test nu devine cotă implicită pentru toate documentele sau perioadele.

Controlul față de comandă compară prețuri în aceeași bază, monedă, UM și configurație. Un preț brut mai mare decât un preț net egal economic nu produce o falsă diferență de preț.

# R08–R09 — Factură directă și proiectele ei

## R08 — Traseu direct, fără documente fictive

Din Flux 5/Control trebuie să existe „Factură nouă” cu două căi clare: din documentele sursă existente sau introducere directă. Calea directă nu cere să creezi mai întâi un necesar, COF, ofertă, comparație și comandă.

Date minime pentru ciornă: cele disponibile. Pentru document înregistrat/verificat se cer furnizorul, numărul și data facturii, moneda, sumele/TVA conform documentului și asocierea la proiect ori repartizările confirmate. Originalul și dovezile de control se gestionează prin mecanismul de documente existent.

- Păstrează legăturile la necesar/comandă/recepție nule când nu există. Nu genera în fundal documente tehnice „placeholder”.
- Afișează „Factură introdusă direct — fără comandă asociată” și traseul de verificare manuală.
- Permite documentul să ajungă la pregătire/predare după verificarea manuală a identității, sumelor, proiectului și dovezilor relevante, conform regulilor aplicației. Lipsa unei comenzi nu trebuie să blocheze permanent acest traseu intenționat.
- Nu declara „comandă–recepție–factură verificate” când nu există comandă ori recepție. Componentele de control fără sursă sunt marcate ca atare.
- Factura directă nu produce recepție, stoc, acoperire de necesar ori capacitate de facturare inventată.
- Verificarea duplicatelor se aplică între toate căile de intrare: aceeași factură nu poate fi introdusă o dată direct și apoi încă o dată „din comandă”.
- Dacă documentele anterioare sunt adăugate ulterior, factura existentă se poate asocia explicit după verificare. Nu se clonează factura și nu se dublează valorile în rapoarte.
- Dacă factura este deja predată, asocierea/corecția ulterioară urmează istoricul/revizuirea existentă, cu diferențele vizibile.

Traseul direct înseamnă mai puține documente obligatorii de parcurs, cu evidență transparentă a verificării reale. Nu înseamnă că aplicația presupune recepția ori aprobarea tuturor sumelor.

## R09 — Asociere și repartizare pe proiecte

La factura directă, proiectul se alege din catalogul existent. Pentru factura pornită din comandă/recepție, se preia proiectul sursă și se confirmă. Utilizatorul nu tastează un ID și nu creează alt proiect printr-o variantă de nume.

Comportament cerut:

1. Caz uzual: un proiect ales în antet; liniile/repartizările preiau proiectul fără reintroducere.
2. Dacă factura acoperă mai multe proiecte, folosește mecanismul de repartizare existent; dacă lipsește, adaugă subalocări explicite. Nu duplica factura pentru fiecare proiect.
3. Subalocările au valori net, TVA și total, păstrând exact totalurile facturii. Pe linii cu cote diferite se păstrează tratamentul acelei linii, fără TVA mediu inventat.
4. Permite salvare de ciornă cu proiect/repartizare incompletă; verificarea finală cere repartizarea completă sau traseul de excepție explicit existent.
5. În listă, fișă și rapoarte se vede proiectul sau „Mai multe proiecte”, cu acces la defalcare.
6. Factura directă intră în facturatul proiectului o singură dată. Nu creează și o comandă angajată artificială pentru aceeași valoare.
7. Asocierea ulterioară la o comandă existentă reconciliază angajamentul și facturatul prin logica existentă, fără a număra de două ori aceeași cheltuială.

Exemplu demonstrativ: factură 300,00 net, 63,00 TVA, total 363,00; proiect A primește 100,00/21,00/121,00, proiect B 200,00/42,00/242,00. Totalul facturii rămâne 363,00, nu 726,00. Rotunjirile se distribuie păstrând sursa exactă.

# R10 — Adăugare, editare, eliminare și restaurare

## Operații clare, accesibile în context

Utilizatorul trebuie să poată corecta date, adăuga și elimina conținut acolo unde starea documentului și legăturile permit. Folosește controalele și stilul aplicației existente: „Adaugă”, „Editează”, „Elimină din selecție”, „Șterge”, „Arhivează”, „Restaurează” sau „Revizuiește”, cu sensuri distincte.

Formularul de editare preia valorile actuale. Confirmarea arată ce se schimbă, salvează efectiv pe server și actualizează fișa, lista, matricea și totalurile relevante. „Șterge” nu trebuie să fie doar ascundere locală în browser.

Ștergerea/eliminarea are confirmare cu denumirea înregistrării, contextul și efectele. Dacă este blocată, afișează documentele sau cantitățile dependente și acțiunea corectă. Nu lăsa doar un buton dezactivat fără explicație și nu cere utilizatorului să repare baza de date manual.

## Ce se poate modifica, în funcție de conținut

| Conținut | Ciornă / încă nefolosit | Confirmat, aprobat sau cu dependențe |
|---|---|---|
| Necesare și poziții | Adăugare, editare, eliminare rânduri prin funcțiile deja existente | Se păstrează regulile existente de revizie/anulare și cantitățile deja angajate |
| Sursă, observații și texte | Corectare, completare și golirea textului opțional | Modificarea este urmărită; nu rescrie mesajele sau snapshoturile deja transmise |
| COF și invitații | Adăugare/eliminare furnizori și poziții înainte de transmitere | După transmitere: retragere/revizie explicită; istoricul trimiterii rămâne |
| Ofertă, variantă și componente | Adăugare, editare și ștergere a datelor nefolosite | Corectare de transcriere cu audit ori revizie comercială; comenzile aprobate păstrează termenii lor |
| Scenariu și selecție | Adăugare/eliminare oferte, cantități și componente; duplicare ca scenariu nou dacă funcția există | Modificarea unui scenariu nu rescrie comenzile create; noile alegeri se reverifică |
| Comandă și poziții | Adăugare, editare și eliminare în ciornă | Revizie/reaprobare, anularea restului confirmată sau operație nouă; partea executată rămâne stabilă |
| Recepție, retur și înlocuire | Editarea/eliminarea unei ciorne fără efect cantitativ confirmat | Corecție/anulare legată cu recalcularea efectelor și verificarea facturilor/transferurilor dependente |
| Factură și repartizări | Corectare date, sume, TVA și proiecte; adăugare/eliminare linii sau alocări | Corectare a transcrierii cu istoric ori document financiar de corecție real, după caz; predările vechi rămân identificabile |
| Furnizor, proiect, material, locație | Corectare; ștergere recuperabilă dacă nu sunt folosite | Corectare cu ID stabil și/sau dezactivare; documentele istorice nu pierd referința |
| Atașamente și asocieri | Adăugare, înlocuire explicită și eliminare a unei asocieri | Versiune nouă ori retragere justificată; protejarea fișierului folosit în alte dosare sau snapshoturi |

Aceasta este o matrice de comportament, nu permisiunea de a falsifica un document al furnizorului. Originalul primit se păstrează; o eroare de tastare și o schimbare comercială reală au motive și trasee distincte. Câmpurile obligatorii pot lipsi într-o ciornă, dar nu se golesc pe un document final fără o corecție/revizie validă.

## Componente și conținutul ofertei

În editorul configurației trebuie să poți **adăuga, redenumi, corecta, reordona și elimina** componente, nu doar să bifezi o listă importată fixă. Se pot modifica descrierea, tipul, UM, cantitatea/baza, prețul și durata atunci când documentul permite, cu recalculare și istoric.

„Elimină din configurația aleasă” păstrează componenta în oferta sursă și schimbă doar scenariul. „Corectează componenta din ofertă” modifică datele de lucru/versionate și marchează scenariile afectate pentru reverificare. „Șterge rândul din ciornă” îl elimină efectiv din acea ciornă, fără pierderea istoricului documentelor deja folosite.

Dacă componenta are subcomponente, arată ce se întâmplă cu ele înaintea confirmării: eliminare din același scenariu ori reatașare validă. Nu crea orfani și nu șterge componente ale altei oferte/variante. Pachetul integral, componentele obligatorii și prețul global rămân supuse regulilor R06 chiar dacă editorul permite o simulare liberă.

## Ștergere recuperabilă, arhivare și Coș

Păstrează și completează mecanismul de Coș existent. Pentru documentele comerciale și înregistrările de lucru, ștergerea obișnuită este recuperabilă, cu utilizator, moment și motiv atunci când este necesar. Arhivarea/dezactivarea ascunde din listele active, păstrând căutarea și legăturile istorice.

Documentele cu efecte confirmate nu se șterg în cascadă pentru a „elibera” cantități sau bani. Interfața propune anularea, corecția sau revizia adecvată. Ștergerea definitivă nu este acțiunea implicită și nu se folosește asupra datelor reale ca procedură de curățare a testelor.

Restaurarea reverifică părinții, unicitatea, disponibilul și dependențele actuale. Un părinte șters nu produce un copil activ orfan: fie se restaurează explicit ansamblul valid, fie se explică blocarea. Restaurarea nu re-aprobă tacit o comandă, nu retransmite invitații și nu dublează recepții/facturi.

## Fișiere, sume și salvări concurente

Eliminarea unei legături la un fișier nu șterge fișierul necesar altei asocieri. Înlocuirea unui document aprobat creează versiune nouă și păstrează dovada folosită anterior sau retragerea ei justificată. Nu suprascrie un fișier original în toate dosarele simultan.

La editarea/eliminarea unei repartizări de factură, recalculează proiectele și soldul de repartizat fără dublarea sursei. O factură directă nu primește documente fictive doar pentru a permite editarea ei. Dacă sumele nu mai sunt integral repartizate, starea de verificare reflectă lipsa.

Editarea, ștergerea, anularea și restaurarea folosesc aceleași verificări server de versiune, tranzacție și repetabilitate ca salvările. Două sesiuni nu pot șterge/restaura aceeași operație cu efecte cantitative duble. Datele tastate rămân în formular la eroare sau conflict.

# Integrarea cu aplicația existentă

## Verificare inițială în cod

Emergent trebuie să identifice implementările actuale pentru necesare, COF/invitații, oferte/variante, scenarii/coș, comenzi, facturi/repartizări, documente și audit. Numele tehnologiilor, fișierelor și endpointurilor se constată în proiect; nu se deduc din captură sau din ghidul vechi.

Pentru fiecare cerință, stabilește dacă lipsa este în model, mapare API, validare backend, componentă UI ori doar navigare. Repară în stratul potrivit și reutilizează serviciile existente. Un buton care ocolește o validare corectă în backend nu este o soluție.

## Date și compatibilitate

- Ofertele/configurațiile au ID-uri proprii și referințe sursă. Diferențiază variante paralele și revizii înlocuite.
- Configurația selectată este stare a scenariului, nu modificare a documentului original.
- Orice nouă aprobare salvează configurația, cantitatea, prețul, costurile și versiunea folosită. Documentele aprobate existente rămân reproductibile.
- Factura directă are tip de proveniență explicit, legături opționale și proiect/repartizări. Nu are surse fictive obligatorii.
- Câmpurile noi primesc valori inițiale prudente pentru date vechi: „de confirmat” acolo unde informația nu există, nu „confirmat” ori taxă zero inventată.
- Modificările sunt migrate fără pierdere de date; numerele documentelor și legăturile existente rămân stabile.

## Salvare și concurență

Păstrează mecanismele de versiune, operație repetabilă și blocare/verificare a disponibilului existente în stack. La schimbarea configurației ori proiectului se reverifică datele pe server. Dublul clic nu creează încă o invitație/factură/comandă. Două sesiuni nu pot angaja aceleași cantități sau dubla alocările.

Adăugarea unui furnizor din dialog nu pierde formularul curent. O eroare de validare sau de rețea nu șterge sumele/configurația introduse și nu afișează succes fals. Se păstrează distincția între salvare de ciornă, verificare, aprobare și predare.

# Ordinea de implementare pentru Emergent

| Lot | Conținut | Dependență | Criteriu de finalizare |
|---|---|---|---|
| B01 | R01 contextul cererii și R02 invitații | Inspectarea codului existent | Date persistente și invitații fără duplicare |
| B02 | R03 variante, R04 matrice și R05 alegere | B01; infrastructura ofertelor | Două variante ale aceluiași furnizor se văd și se selectează distinct |
| B03 | R06 configurații și costuri | B02 | Excelul se reconciliază, componentele nu se dublează |
| B04 | R07 moduri net/brut și regim TVA | Infrastructura facturilor | Aceeași valoare economică produce aceleași net/TVA/total |
| B05 | R08 factură directă și R09 proiecte | B04 | Factură verificabilă și raportată fără operații fictive |
| B06 | R10: completarea operațiilor de gestionare și a Coșului | B01–B05 | Operații contextuale cu dependențe, totaluri și istoric păstrate |
| B07 | Integrare, generalizare și regresie | B01–B06 | Toate criteriile sunt probate și rezultatele raportate |

Lucrează în loturi mici, cu teste după fiecare schimbare relevantă. Înaintea unei migrări de date, prezintă efectul și păstrează posibilitatea de recuperare conform mecanismelor proiectului. Nu folosi ștergerea bazei sau a datelor de test ca metodă de migrare.

Regulile R10 se aplică în fiecare lot pe obiectele atinse; B06 completează operațiile încă lipsă. Editarea cererilor deja acceptată nu se reface. Testele pe alte produse sunt parte a acceptării R06, nu o extindere opțională după lansare.

# Teste de acceptare

Acestea sunt teste de implementat în Emergent. În această sesiune au fost verificate sursa Excel, formulele ei și documentația; aplicația Emergent nu a fost executată sau testată de autorul acestui document.

| ID | Scenariu | Rezultat obligatoriu |
|---|---|---|
| E01 | Două cereri au Sursă și Observații diferite | Fiecare fișă afișează propriile valori după redeschidere și relogare |
| E02 | Observații generale și pe poziție, cu diacritice | Se păstrează integral și distinct, fără propagare greșită |
| E03 | Pregătești COF dintr-o cerere cu note interne | Notele interne nu sunt trimise automat în mesajul furnizorului |
| E04 | Inviți furnizor din fișa Cererii de ofertă | Se reutilizează COF-ul curent și se păstrează revizia |
| E05 | Reapeși invitația și adaugi un furnizor nou | Invitația existentă nu se dublează; noul furnizor se adaugă corect |
| E06 | Un furnizor are două oferte pentru același material | Ambele rămân identificabile și apar în comparație |
| E07 | Același document are variante 12/36 luni | Coloane distincte; aceeași unitate necesară nu este angajată de două ori |
| E08 | O ofertă este înlocuită prin revizie | Vechea versiune rămâne în istoric, fără reactivare implicită |
| E09 | Tabel cu mai multe materiale și furnizori | Rânduri/coloane, antete fixe, filtre și surse corecte |
| E10 | Ofertă parțială și preț zero explicit | Neofertat este distinct de zero; totalurile nu inventează produse gratuite |
| E11 | Alegi oferta eligibilă care nu este recomandată | Selecția se salvează cu motivul și rămâne după reîncărcare |
| E12 | Încerci alegere peste disponibil ori pachet invalid | Serverul blochează; alegerea manuală nu dezactivează integritatea |
| E13 | Două sesiuni selectează ultimul disponibil | Aprobarea nu depășește cantitatea rămasă |
| E14 | Excluzi accesoriul inclus cu preț zero din exemplul demonstrativ | Totalul inițial rămâne 1.420,00 sau 1.600,00, după variantă |
| E15 | Excluzi suportul tarifat separat în exemplul demonstrativ | Simularea ajunge la 1.300,00; licența rămâne inclusă |
| E16 | Componentă obligatorie/necunoscută sau preț global | Se cere soluționare/reconfirmare; nu se inventează reducerea |
| E17 | Importezi sau mapezi Excelul atașat | Două configurații, 24 linii comerciale, fără totaluri importate ca produse |
| E18 | Recalculezi grupurile Excel cu/fără suport | Totalurile se reconciliază cu I26/I27/I41/I42 la regula de rotunjire confirmată |
| E19 | Elimini suportul real din rândurile 15/30 | Se reproduce formula fără suport; licențele din 24/39 rămân |
| E20 | Sursa reală are USD și regim TVA neprecizat | Moneda rămâne USD, taxa este de confirmat, nu zero presupus |
| E21 | Modifici scenariul după aprobarea comenzii | Comanda păstrează varianta, configurația și prețul aprobate |
| E22 | Introduci 100,00 net la cota de test 21% | TVA 21,00, total 121,00 |
| E23 | Introduci 121,00 brut la aceeași cotă de test | Net 100,00, TVA 21,00; nu se mai adaugă încă o dată taxa |
| E24 | Document fără TVA confirmat sau tratament necunoscut | Confirmat: net=total și TVA zero; necunoscut: ciornă/de clarificat |
| E25 | Factură cu mai multe cote sau schimbare net/brut | Se calculează pe linii; schimbarea modului nu reinterpretează tacit sumele |
| E26 | Factură nouă fără necesar/COF/comandă/recepție | Se salvează pe traseul direct, cu legături nule și verificare manuală |
| E27 | Înregistrezi factura directă cu un proiect | Proiectul persistă și apare în listă, fișă și raport o singură dată |
| E28 | Repartizezi exemplul 300,00 net pe două proiecte | 121,00 + 242,00 = 363,00; factura nu este duplicată |
| E29 | Introduci aceeași factură pe calea directă și din comandă | Mecanismul comun de duplicate împiedică dublarea |
| E30 | Asociezi ulterior o comandă la factura directă | Se păstrează aceeași factură; rapoartele și angajamentele se reconciliază |
| E31 | Dublu clic, timeout sau eroare de validare | Fără operații duplicate, date pierdute sau succes fals |
| E32 | Reiei editarea cererilor/stărilor deja reparată | Funcția existentă continuă să respecte regulile și legăturile |
| E33 | Deschizi pagina Necesare cu date relevante | Nu se blochează la încărcare; navigarea și filtrarea funcționează |
| E34 | Rulezi curățarea de test în mediu izolat | Datele de test sunt tratate și în Coș/restaurare, fără legături orfane; datele reale nu sunt afectate |
| E35 | Restaurezi o factură/COF cu părinte lipsă | Dependențele se tratează conform regulilor, fără document orfan |
| E36 | Ștergi/corectezi o recepție de înlocuire în test | Cantitatea încă așteptată ca înlocuire revine corect și nu se pierde |
| E37 | Configurezi un tablou electric și un corp de iluminat, fără codurile din Excel | Aceleași funcții gestionează alte produse și alte accesorii, fără modificarea codului |
| E38 | Introduci un produs fără componente și altul cu multe componente | Ambele funcționează; nu există obligație de accesorii ori limită derivată din exemplu |
| E39 | Creezi un subansamblu, modifici baza cantitativă și durata serviciului | Recalculare corectă, relații valide și respingerea ciclurilor; duratele nu sunt limitate la 12/36 |
| E40 | Adaugi un tip nou de componentă și mai mult de două variante | Date configurabile și comparație distinctă, fără programare pe fiecare produs |
| E41 | Editezi descrieri, cantități și prețuri într-o ciornă | Se salvează pe server, se recalculează și persistă după redeschidere |
| E42 | Adaugi și ștergi un rând de ciornă încă nefolosit | Rândul este eliminat/recuperabil conform modelului, fără totaluri sau referințe orfane |
| E43 | Elimini o componentă numai din scenariu | Oferta originală rămâne; costul și starea de confirmare se actualizează corect |
| E44 | Corectezi oferta după ce există o comandă aprobată | Comanda păstrează snapshotul; scenariile afectate cer reverificare și noua versiune este identificabilă |
| E45 | Încerci să ștergi o recepție confirmată cu factură/transfer dependent | Primești explicația și traseul de corecție; nu rămân cantități sau legături financiare invalide |
| E46 | Editezi și elimini repartizări ale unei facturi directe în ciornă | Proiectele, totalul și restul de repartizat sunt corecte; nu apar documente fictive |
| E47 | Elimini o asociere la un atașament folosit în două dosare | Celălalt dosar și dovada lui rămân accesibile; înlocuirea este versionată |
| E48 | Două sesiuni șterg/restaurează o înregistrare și părintele lipsește | Tranzacție/repetabilitate, fără efect dublu sau copil orfan; blocarea explică remedierea |
| E49 | Dezactivezi un material/furnizor/proiect deja folosit | Dispare din alegerea activă conform regulilor, dar documentele istorice își păstrează legăturile |
| E50 | Revizuiești ori anulezi restul unei comenzi parțial executate | Partea executată rămâne stabilă; numai efectele confirmate ajustează obligațiile și necesarul |

Pentru E17–E20 se atașează Excelul original în Emergent. Pentru E13 și E31 se folosesc teste reale de concurență/reîncercare pe backendul aplicației, nu doar dezactivarea vizuală a butonului. Pentru E34–E36 se folosesc exclusiv date de probă izolate.

E37–E40 trebuie executate cu date diferite de Excelul atașat. E41–E50 verifică gestionarea conținutului și se rulează pe date de probă, inclusiv după reîncărcare și cu două sesiuni acolo unde este relevant.

# Prompt gata de trimis în Emergent

Textul următor poate fi copiat împreună cu PDF-ul și Excelul original:

> Continuă aplicația TQM Achiziții existentă, fără reconstrucție și fără schimbarea stackului ori a designului general. Citește PDF-ul „Cerințe pentru Emergent”, versiunea 1.1 din 30.10.2026, și Excelul „Intend_TQM_Services OIP.xlsx”. Notițele tăiate despre editarea cererilor/stărilor sunt confirmate ca rezolvate; verifică-le doar la regresie.
>
> Implementează R01–R10: Sursă/Observații vizibile și persistente; invitații din Cereri de ofertă prin aceeași logică COF; mai multe oferte/configurații pentru același furnizor; matrice comparativă material × ofertă/variantă; alegerea manuală a oricărei oferte eligibile; configurații cu componente, licențe și servicii, fără reducere inventată pentru accesorii gratuite; facturi cu mod de introducere net/brut și regim TVA distinct; facturi directe fără documente fictive; asociere/repartizare pe proiecte fără dublarea valorilor; operații contextuale de adăugare, editare, eliminare, arhivare și restaurare.
>
> Excelul este un singur caz de test. Construiește modelul general pentru alte produse, componente, structuri, cantități, durate și variante, fără condiții hardcodate pe codurile sau rândurile acelui fișier. Execută E37–E40 cu alte produse. R10 cere și gestionarea efectivă a conținutului, nu doar selectare/deselectare: ciorne editabile, revizii/corecții pentru documente finalizate și Coș/restaurare cu verificarea dependențelor. Dacă v1.0 a fost deja aplicată, implementează diferențele v1.1 și păstrează funcțiile acceptate.
>
> Inspectează mai întâi modelele, endpointurile și componentele actuale. Prezintă pe scurt maparea cerințelor și fișierele afectate, apoi implementează în loturile B01–B07. Cere clarificare numai pentru ambiguități care schimbă efectiv prețul, eligibilitatea ori datele; nu opri lucrul pentru alegeri de implementare de rutină.
>
> În Excel sunt două configurații alternative ale aceluiași produs, cu suport pe 12/36 luni, linii la preț zero și o licență tarifată păstrată în totalurile „fără servicii”. Citește valorile din fișier și reconciliază I26/I27/I41/I42. Sursa este o estimare în USD, iar regimul TVA nu este precizat. Nu inventa prețuri, taxe, echivalențe, valabilitate ori confirmări comerciale.
>
> Păstrează ofertele originale și snapshoturile comenzilor aprobate. Selecția manuală nu ocolește cantitățile disponibile, condițiile de pachet, validările tehnice și versiunile. Factura directă nu produce automat necesar, comandă, recepție ori stoc. Folosește aceleași calcule și reguli pe server și în toate ecranele.
>
> Livrează frontend și backend funcționale, migrații compatibile și testele E01–E50 relevante, cu rezultate reale. Nu șterge/resetă datele existente și nu publica automat schimbările. Raportează cerințele realizate, fișierele schimbate, testele executate, limitele și pașii de verificare în browser.

# Ce trebuie să livreze și să demonstreze Emergent

La încheierea fiecărui lot și la final, raportul trebuie să conțină:

1. Cerințele R01–R10 acoperite și comportamentul efectiv rezultat.
2. Modelele/câmpurile, componentele și endpointurile modificate, cu migrarea datelor vechi.
3. Traseul demonstrat: necesar → COF/invitație → două variante ale aceluiași furnizor → matrice → alegere → comandă cu configurația corectă.
4. Traseul demonstrat: factură directă → mod TVA → proiect/repartizări → verificare manuală și predare, fără operații fictive.
5. Reconcilierea Excelului, cu valori calculate în proiect, monedă și regula de rotunjire consemnate.
6. Lista E01–E50: trecut/eșuat/neexecutat, cu dovezi relevante; diferențiere între teste automate și probe în browser.
7. Confirmarea păstrării datelor, surselor și comenzilor deja aprobate. Defectele rămase sunt descrise concret.
8. Demonstrarea configurațiilor pe alte produse și a operațiilor de adăugare/editare/ștergere/restaurare pentru obiectele din matricea R10, în stările permise.

Pentru utilizator: atașează în aceeași conversație Emergent acest PDF și Excelul original, apoi folosește promptul din capitolul anterior. Nu este suficient să trimiți numai fotografia cu ecranul: Emergent trebuie să lucreze în proiectul în care aplicația există.

Documentul precizează ce trebuie implementat și verificat. El nu declară aceste completări ca fiind deja dezvoltate sau testate în aplicația Emergent.
