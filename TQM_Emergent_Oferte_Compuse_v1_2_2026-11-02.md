---
title: "TQM Achiziții — Oferte compuse și scenarii de selecție"
subtitle: "Corectarea separării dintre alternative și componente; totaluri, comparație și continuitate până la comandă"
date: "2 noiembrie 2026 · versiunea 1.2"
lang: ro-RO
---

# 1. Scop și defectul de corectat

**Destinație:** specificație de transmis în proiectul Emergent TQM existent, împreună cu Excelul original „Intend_TQM_Services OIP.xlsx” și capturile ecranelor. Se păstrează tehnologiile, designul general, datele și funcționalitățile acceptate. Se inspectează codul existent înainte de alegerea modificărilor.

**Relația cu v1.1 din 30.10.2026:** acest document detaliază și corectează implementarea R03–R06 și partea aferentă din R10. Are prioritate pentru ofertele compuse, calculul comparației, selecție și transmiterea configurației mai departe. Celelalte cerințe v1.1 rămân valabile. Nu se reimplementează integral aplicația și nu se elimină posibilitatea ofertelor cu produse alternative.

**Ce s-a verificat aici:** structura și formulele Excelului, precum și comportamentul vizibil în cele cinci capturi ale utilizatorului. Nu am avut acces la sursele sau baza de date a aplicației Emergent și nu am executat aplicația. Cauza exactă din cod trebuie confirmată de implementator. Acest document și verificarea aritmetică nu constituie o remediere deja instalată în aplicație.

Din capturi rezultă două probleme funcționale:

1. În fiecare ofertă au fost introduse switchul și suportul ca două „variante” ale aceleiași poziții. Suportul este o componentă care se adaugă echipamentului în configurația respectivă, nu un produs care poate îndeplini singur necesarul de switch.
2. Comparația și scenariul afișează costul suportului drept cost al poziției. Valorile în RON corespund conversiei prețurilor serviciilor din captură. Nu se poate stabili numai din imagine dacă implementarea alege minimul, ultimul rând sau altă variantă implicită; toate aceste reguli sunt greșite pentru componente cumulative.

În plus, capturile arată numai două rânduri pe configurație, în timp ce Excelul are **12 rânduri pe configurație**, inclusiv o licență tarifată. Adunarea celor două rânduri deja introduse ar rămâne incompletă. Sunt necesare atât o introducere completă, cât și un calcul corect.

**Rezultat urmărit:** utilizatorul poate introduce o ofertă simplă, un produs cu componente, configurații alternative ale aceluiași produs și oferte pentru mai multe materiale. Vede conținutul și costul integral al configurației, poate construi scenarii valide, iar comanda păstrează exact selecția aprobată.

Excelul reprezintă un caz de test, nu un șablon fix pentru toate produsele. Nu se codifică reguli specifice Cisco, două configurații, 12 componente ori durate de 12/36 luni. Valorile comerciale efective se citesc din originalul atașat separat; exemplele numerice publicate în acest document sunt fictive.

# 2. Ce conține efectiv exemplul Excel

Fișierul are o foaie, „Price Estimate”, cu prețuri în USD. Conține un document cu două configurații alternative pentru același echipament. Etichetele pozițiilor sunt texte din document, nu identificatori interni sau numere zecimale.

| Element | Configurația 1 | Configurația 2 |
|---|---|---|
| Echipament principal | 1.0, rândul 14 | 2.0, rândul 29 |
| Suport separat | 1.0.1, rândul 15, 12 luni | 2.0.1, rândul 30, 36 luni |
| Accesorii și licențe incluse | Rândurile 16–23 și 25, preț zero explicit | Rândurile 31–38 și 40, preț zero explicit |
| Licență tarifată | 1.8.1, rândul 24, 36 luni | 2.8.1, rândul 39, 36 luni |
| Total configurație completă | I26 = SUM(I14:I25) | I41 = SUM(I29:I40) |
| Total fără suport | I27 = SUM(I14,I24) | I42 = SUM(I29,I39) |

Echipamentul și licența tarifată sunt identice valoric în cele două configurații; diferența totalurilor provine din suportul de durate diferite. **Licența de 36 luni apare în ambele configurații**, chiar dacă suportul primei durează numai 12 luni. „Fără servicii” din formulele acestui Excel păstrează licența plătită. Nu elimina toate liniile care au o durată.

Rândul 1.0 contribuie economic cu prețul echipamentului chiar dacă are subcomponente. Rândul 1.8 are preț zero și copilul 1.8.1 are preț separat. O relație părinte–copil nu decide singură dacă părintele este facturabil sau un subtotal. Totalurile I26/I41 sunt agregări și nu se adaugă din nou liniilor componente.

Unele componente au cantitate 2 pentru un echipament. Cantitățile trebuie păstrate din document. „Smart Account Mandatory = Yes” reprezintă o condiție de cont a producătorului, nu stabilește automat că o componentă este opțională, obligatorie sau separabilă comercial.

Sursa se declară estimare informativă pentru planificare, fără caracter de ofertă fermă Cisco. Regimul TVA și data-limită de valabilitate nu sunt precizate. Păstrează aceste incertitudini. Înainte de comandă este necesară confirmarea comercială potrivit fluxului existent; nu inventa o cotă TVA, un termen ori o confirmare a furnizorului.

**Reconciliere obligatorie în Emergent:** citește toate cele 24 de linii, recalculând cantitate × preț, și reconciliază cele patru celule de total. Raportează valorile și eventualele diferențe de rotunjire în proiectul privat. Nu valida implementarea doar cu echipamentul și suportul, fără licența plătită.

# 3. Modelul general: ce se adună și ce se alege

## P01 — Distincții obligatorii

| Noțiune | Semnificație și comportament |
|---|---|
| Document sursă | Excel/PDF primit. Poate conține una sau mai multe oferte/configurații. Fișierul nu este duplicat pentru fiecare componentă. |
| Ofertă comercială și familie de revizii | Identitate comercială cu furnizor, număr extern dacă există, monedă și condiții. Revizia actualizează aceeași ofertă și păstrează istoricul. |
| Configurație/pachet ofertat | Ansamblul care răspunde uneia sau mai multor poziții de necesar. Are conținut, preț calculat, disponibilitate și condiții proprii. |
| Alternativă | O altă soluție pentru aceeași cantitate/scop. Alegerea uneia nu cere cumpărarea celorlalte. |
| Componentă | Echipament, accesoriu, licență, suport, montaj ori alt element care aparține configurației. Liniile facturabile selectate se adună. |
| Rând informativ/subtotal | Explică ori însumează alte valori; nu generează încă o contribuție economică. |
| Scenariu | Selecție proprie a cumpărătorului, cu cantități și opțiuni. Nu modifică documentul original și nu emite singur comandă. |

În exemplu, **un document → două configurații alternative → fiecare cu echipament și componente cumulative**. Suportul nu poate fi ales singur ca înlocuitor al switchului. Dacă aplicația păstrează două înregistrări de ofertă pentru cele două configurații, ele pot rămâne, cu sursa comună și grupul de alternative identificate corect. Nu este obligatorie comasarea distructivă într-o singură înregistrare.

Se păstrează și cazul valid „o ofertă → două produse alternative pentru o poziție”. În interfață, acțiunile **Adaugă alternativă** și **Adaugă componentă** sunt distincte. Alegerea tipului produce relații și validări reale în backend, nu doar etichete sau indentare vizuală.

## P02 — Date și relații

Adaptează entitățile existente; nu crea un al doilea motor de ofertare. Identificatorii interni sunt stabili și separați de numerele pozițiilor furnizorului. Nu grupa exclusiv după furnizor + material și nu folosi un dicționar cu o singură valoare care suprascrie configurațiile aceleiași poziții.

Pentru configurație: ID, ofertă/revizie, denumire, grup de alternative, poziții de necesar acoperite, cantitate de referință, UM, produs principal, monedă, condiții comerciale și tehnice, disponibilitate, termen, valabilitate, sursă și stare. Legătura cu necesarul arată separat **cantitatea îndeplinită de produs**, nu suma cantităților componentelor.

Pentru fiecare linie: ID, configurație, părinte opțional, ordine, etichetă originală ca text, cod, descriere, tip, UM, cantitate, baza cantității, preț, moneda, modul de contribuție la total, rol în selecție, durată, bază tarifară, eligibilitate și referință la document/celulă. Permite descrieri fără cod și componente care nu există în nomenclator; nu condiționa orice componentă de crearea unui material de stoc.

**Contribuția la preț** și **obligativitatea** sunt două lucruri diferite. O linie poate fi tarifată separat și obligatorie; alta poate fi inclusă gratuit, dar opțională. Păstrează cel puțin aceste stări:

- tarif separat, inclus la zero explicit, inclus într-un preț global fără valoare separabilă, informativ/subtotal;
- obligatoriu, opțional, de confirmat; produsul principal este identificat explicit;
- cantitate per produs, per lot/comandă sau per perioadă, cu unitatea perioadei;
- tarif pentru întreaga durată, pe lună/an sau plată unică; durata serviciului este câmp separat;
- preț cunoscut, zero explicit sau preț lipsă/de confirmat; niciunul nu se confundă cu celelalte.

Pentru subansambluri, interzice ciclurile și părinții dintr-o altă configurație. Relația tehnică nu implică automat însumarea unui subtotal sau excluderea prețului părintelui. Ordinea etichetelor se păstrează fără conversie la float: 1.0, 1.0.1, 1.8.1 și 1.10 trebuie să rămână distincte.

Un pachet poate acoperi mai multe poziții: păstrează o mapare de acoperire cu cantitate și UM pentru fiecare necesar. Pachetul se plătește o singură dată. Repartizarea valorii pentru raportare nu creează copii facturabile ale pachetului pe fiecare poziție.

Alternativele care folosesc același disponibil au o sursă comună de capacitate; nu dubla stocul furnizorului prin multiplicarea configurațiilor. „Disponibil: DA” nu devine automat o cantitate numerică nelimitată.

# 4. Înregistrarea și administrarea ofertelor

## P03 — Formular simplu, extins la nevoie

Păstrează traseul rapid pentru un produs simplu. Pentru fiecare poziție ofertată, permite alegerea „Produs simplu” sau „Produs/pachet cu componente”. Un produs simplu are o configurație cu o singură linie facturabilă; folosește același calcul.

La pachet, utilizatorul introduce denumirea configurației și produsul principal, apoi adaugă componente într-un tabel extensibil. Datele avansate se deschid când sunt necesare. Numărul de linii, tipuri, produse și durate nu este fixat după exemplul Excel. Se păstrează stilul vizual actual și derularea utilă, cu antet și identificarea produsului vizibile.

Coloane utile în editor: eticheta sursă, cod/descriere, rol/tip, cantitate, UM, bază, preț unitar, valoare linie, perioadă și mod de preț. Detaliile despre obligativitate, separabilitate, disponibilitate și document pot fi în panoul liniei. Arată permanent totalul configurației și numărul de linii cu preț ori rol neconfirmat.

Acțiuni cerute: adaugă componentă, adaugă subcomponentă, adaugă alternativă, duplică configurația ca punct de pornire, reordonează, editează și elimină. Duplicarea creează ID-uri noi și păstrează separat sursa; nu consumă disponibilul și nu aprobă nimic. Eliminarea unui părinte cu copii cere alegerea explicită a efectului și revalidarea întregului pachet.

În lista ofertelor se afișează cardul configurației cu totalul complet, produsul principal, serviciile și duratele, starea comercială, revizia și accesul la sursă. Componentele se pot extinde. Prețul hardware-ului, prețul suportului și totalul pachetului au etichete distincte; nu se numesc toate „Preț unitar” fără baza la care se referă.

**Asistentul de mapare a sursei:** introducerea manuală trebuie să funcționeze complet. Dacă există deja import, extinde-l cu previzualizare, alegerea configurației/părintelui și confirmarea tipului de linie. Nu este necesar un importator universal de PDF/OCR pentru a închide această remediere. Copierea din Excel poate ajuta, dar nu inventează clasificarea liniilor necunoscute.

La redeschiderea unei configurații sau la pornirea unei revizii, precompletează datele complete potrivit acțiunii alese. Captura unui formular gol nu dovedește singură pierderea lor, dar aceasta trebuie verificată. Nu înlocui configurația existentă cu valori goale prin salvarea unui formular incomplet.

## P04 — Corecție, revizie, eliminare

Corectarea unei transcrieri se deosebește de o ofertă comercială nouă și de o revizie a furnizorului. Păstrează istoricul, autorul și relațiile cu sursa. Ciorna este editabilă; după utilizare în aprobare/comandă, aplică mecanismul de revizie sau corecție controlată existent.

Excluderea unei componente într-un scenariu nu o șterge din ofertă. Eliminarea unei configurații neutilizate o mută în Coș potrivit regulilor existente. Documentele și configurațiile cu dependențe nu se șterg definitiv pentru a rezolva o eroare de model. Restaurarea verifică oferta/revizia părinte și nu reactivează automat aprobări sau comenzi.

Modificarea ofertei marchează scenariile dependente drept „De recalculat/de verificat”. O comandă aprobată își păstrează snapshotul; un calcul nou nu rescrie acordurile istorice. Versiunile se verifică pe server la salvare, astfel încât două sesiuni să nu se suprascrie neobservat.

# 5. Calculul complet și verificabil

## P05 — Un singur calcul pe server

Editorul ofertei, comparația, scenariul, comanda și exporturile folosesc același serviciu de calcul. Interfața poate oferi previzualizare, dar serverul validează și recalculează; nu acceptă un total transmis de browser drept adevăr contabil. Nu modifica numai afișarea totalului în comparație.

Pentru configurația selectată:

1. Stabilește cantitatea efectivă a fiecărei componente din cantitatea produsului și baza liniei. O componentă cu factor 2 per produs devine 6 la 3 produse; una per lot rămâne la cantitatea lotului. Durata nu multiplică prețul dacă tariful sursă acoperă deja întreaga perioadă.
2. Calculează valoarea fiecărei linii care contribuie economic: cantitate facturabilă × tarif × factorul de perioadă aplicabil. Prețul unui pachet global este o contribuție unică, cu componentele incluse neadunate din nou.
3. Aplică reducerile de linie și reducerile comune numai în ordinea, pe baza și la nivelul definite de oferta sursă. Nu aplica încă o reducere dacă prețurile introduse sunt deja nete după discount. Reducerile fixe nu se multiplică automat pe componente.
4. Adună o singură dată contribuțiile economice selectate. Păstrează liniile la zero explicit în conținut și în documentele următoare. Prețul lipsă face calculul incomplet, nu zero.
5. Recalculează condițiile comune: transport, montaj per lot, reduceri condiționate, minime, cantități pe ambalaj, taxe cunoscute. Un minim de comandă este o restricție, nu un cost suplimentar inventat până la atingerea pragului.
6. Convertește valorile cu același set de cursuri și aceeași politică de rotunjire în toate ecranele. Salvează cursul, data, sursa și baza scenariului; un curs actualizat nu rescrie o comandă aprobată.
7. Separă valoarea fără taxe, taxele cunoscute și totalul cu taxe când regimul este confirmat. TVA necunoscut rămâne necunoscut. Nu prezenta o estimare fără regim fiscal ca total brut ferm.

Componentele în monede diferite se tratează prin conversie explicită și validare. Dacă modelul existent impune o monedă per ofertă, păstrează această regulă și solicită separarea ori conversia confirmată, fără însumarea directă a USD cu RON.

Prețurile și factorii folosesc aritmetică zecimală, nu float. Păstrează precizia legitimă din document; nu calcula din etichetele afișate cu două zecimale. Excelul conține prețuri cu mai multe zecimale și artefacte de reprezentare binară. Citește valoarea ca zecimală, normalizează numai artefactele la precizia acceptată și reconciliază totalul sursă. Nu trunchia automat un tarif cu trei zecimale la două.

Politica de rotunjire trebuie explicită: la fiecare linie sau la total, conform documentului. Pentru acest Excel se verifică totalurile din formulele sursă. O diferență de rotunjire se afișează și se justifică; nu se ascunde prin schimbarea arbitrară a unui preț. Sumele în RON pot diferi dacă se convertesc valori deja rotunjite în locul valorilor sursă, de aceea baza conversiei trebuie identică în toate ecranele.

La nivel de scenariu, grupează costurile comune după domeniul lor comercial real: configurație, ofertă, comandă ori furnizor, potrivit condițiilor. Două alternative ale aceleiași oferte nu generează două transporturi automat. Două oferte independente ale aceluiași furnizor nu își unifică automat pragurile și transportul.

**Totalul documentului**, **totalul fiecărei configurații alternative** și **totalul selecției** au sensuri diferite. Nu aduna toate alternativele unui singur produs într-un total de cumpărare. Pentru documentul care conține alternative, arată „Totaluri pe configurație; selecție necesară”. Pentru o ofertă cu materiale cumulative independente, adună toate liniile aplicabile, fără a pierde ofertele parțiale.

## Exemplu aritmetic fictiv pentru teste

| Contribuție | Configurația A | Configurația B |
|---|---|---|
| Echipament | 1.000,00 | 1.000,00 |
| Suport separat, tarif pentru toată perioada | 120,00 pentru 12 luni | 300,00 pentru 36 luni |
| Licență separată, 36 luni | 300,00 | 300,00 |
| Accesorii incluse la zero | 0,00 | 0,00 |
| Total configurație, 1 produs | 1.420,00 | 1.600,00 |
| Simulare fără suport, licența păstrată | 1.300,00 | 1.300,00 |
| Total configurație, 3 produse, toate liniile per produs | 4.260,00 | 4.800,00 |

Cu transport comun 50,00, totalul pentru un produs A devine 1.470,00. La trei produse A, transportul o singură dată produce 4.310,00. O componentă inclusă gratuit poate fi exclusă din simulare fără scăderea prețului hardware-ului. O modificare a unui pachet integral cere reconfirmare comercială, chiar dacă estimarea aritmetică poate fi afișată.

# 6. Comparație pe conținut și scop comun

## P06 — Matricea compară configurații complete

Fiecare coloană identifică furnizorul, oferta, revizia și configurația. În exemplu, cele două coloane sunt „INTEND / configurația 1 / suport 12 luni” și „INTEND / configurația 2 / suport 36 luni”, cu totalurile integrale. Un serviciu accesoriu nu ocupă o coloană ca ofertă de sine stătătoare pentru switch.

Rândul principal este poziția de necesar; valoarea celulei reprezintă costul configurației pentru cantitatea comparată. Subrândurile extensibile arată componentele, cantitățile și prețurile: echipament, suport, licență și accesoriile. Arată și lipsurile ori nepotrivirile de mapare. Componente cu coduri diferite pot îndeplini aceeași funcție; nu le declara identice numai după descriere. Maparea tehnică este confirmabilă de utilizator.

La pachetul care acoperă mai multe poziții, grupează rândurile sub pachet și afișează contribuția o singură dată în total. O împărțire analitică a valorii pe poziții este etichetată distinct de valorile facturabile.

Valorile vizibile trebuie să includă: cantitatea de referință, totalul configurației în moneda sursă, valoarea convertită, diferențele de conținut, durata serviciilor, taxele cunoscute/necunoscute, livrarea și eligibilitatea. Totalul coșului include selecția efectivă și costurile comune, nu o variantă aleasă tacit prin minim sau ordine.

„Ultima revizie” se aplică **familiei de ofertă**, cu configurațiile ei actuale, nu ultimului răspuns al furnizorului în ansamblu. O ofertă independentă și o alternativă curentă ale aceluiași furnizor nu dispar pentru că a fost înregistrată o altă ofertă. O revizie înlocuită se poate consulta în istoric; nu se recomandă implicit drept ofertă curentă.

## P07 — Bază de comparație și recomandări

Utilizatorul definește baza: poziții și cantități, cerințe tehnice, componente/funcții obligatorii, perioada necesară și costurile incluse. Setarea implicită preia necesarul existent; nu obligă completarea unui formular mare pentru achiziții simple. Diferențele și datele necunoscute rămân vizibile.

Sunt utile trei vederi ale aceleiași selecții:

- **Așa cum este ofertat:** configurația completă originală. Permite observarea diferenței 12/36 luni, fără a declara automat conținuturile identice.
- **Configurația dorită:** componentele selectate de cumpărător, cu confirmările comerciale și tehnice necesare. Nu alterează oferta originală.
- **Cost pe perioada analizată:** numai costurile și regulile pentru orizontul declarat. Lipsurile viitoare rămân necunoscute; nu se inventează prețuri de reînnoire.

În captura utilizatorului, necesarul menționează servicii pe 12 luni. Configurația cu 36 luni poate fi o alternativă mai extinsă, dacă îndeplinește cerințele și utilizatorul o acceptă. Costul suplimentar și perioada suplimentară se văd separat. Dacă baza cerută este 36 luni, oferta cu suport 12 luni are o lipsă de acoperire pentru lunile rămase până la o cotație de prelungire confirmată.

Nu împărți întregul pachet la 12 sau 36 și nu prezenta rezultatul drept preț lunar al suportului. Hardware-ul este cost distinct. Chiar și un cost mediu lunar al serviciului este doar indicator explicativ; nu dovedește că un tarif de 12 luni se poate repeta de trei ori la același preț sau că unul de 36 luni poate fi cumpărat parțial.

Marcajul „cel mai mic” se acordă numai unui cost total calculat pentru **aceeași cantitate și acoperire eligibilă**. Ofertele parțiale, cu componente obligatorii lipsă ori prețuri necunoscute, nu câștigă printr-un total artificial mic. Dacă diferența de scop nu a fost soluționată, afișează „conținut diferit/comparație condiționată”. Prețul cunoscut poate fi afișat, dar nu este recomandare automată de achiziție completă.

Nu este necesar un motor nou de scoruri statistice sau de predicții. Prețul, conformitatea, livrarea, disponibilitatea, durata, plata și condițiile comerciale sunt criterii observabile. Un scor ponderat, dacă există, își păstrează formula transparentă; datele fictive nu susțin probabilități istorice.

# 7. Scenarii cu selecție explicită și control

## P08 — Alegerea configurației întregi

Într-un scenariu nou, pe fiecare poziție se alege **oferta + revizia + configurația**, cantitatea și apoi opțiunile pachetului. Lista de selecție arată denumirea configurației, totalul corect și durata serviciului. Nu oferă suportul drept produs principal pentru necesarul de switch.

Se poate alege manual orice configurație eligibilă, inclusiv una mai scumpă, cu motiv potrivit regulilor actuale ale aplicației. Recomandarea rămâne distinctă de selecție. Reîncărcarea paginii și precompletarea nu înlocuiesc alegerea explicită cu varianta minimă ori ultima din listă.

Un scenariu poate avea stare „Simulare” cu lipsuri explicate, dar aprobarea/comanda fermă verifică eligibilitatea completă. Nu transforma o incertitudine în confirmare printr-un checkbox fără documentarea condiției. Afișează de ce un buton de salvare, validare ori aprobare este dezactivat; nu lăsa operatorul să ghicească motivul.

## P09 — Opțiuni, cantități și coș comercial

1. Componentele opționale se includ/exclud în scenariu. Cele obligatorii ori cu statut neconfirmat nu dispar în tăcere. Dacă sunt excluse pentru analiză, scenariul este marcat condiționat și nu se comandă până la rezolvare.
2. Un accesoriu gratuit exclus nu produce economie de preț. Un element inclus într-un pachet global nu primește un preț negativ inventat. Un serviciu separat poate fi scăzut în simulare numai cu baza corectă; separabilitatea comercială rămâne de verificat.
3. Grupurile de opțiuni permit, unde sursa o cere, alegerea unei singure alternative de componentă, de exemplu suport 12 sau 36 luni. Nu selecta ambele implicit. Opțiunile independente se pot cumula. Regulile de exclusivitate sunt date, nu condiții după un anumit cod de produs.
4. Pentru necesar 1 bucată, alegerea configurației A consumă o unitate de necesar; componentele ei nu mai consumă alte unități. Pentru necesar 10, pot fi alocate, de exemplu, 4 configurații A și 6 B numai dacă sunt compatibile cu cerințele și permise comercial. Nu impune un prag fix de 10 produse.
5. Împărțirea între furnizori ori configurații verifică disponibilul numeric și capacitățile comune, cantitatea rămasă de achiziționat și UM. Nu permite supra-alocarea și nu adună de două ori disponibilul comun alternativelor. Revalidarea este tranzacțională la emiterea comenzii, inclusiv pentru două sesiuni simultane.
6. Un pachet integral sau un lot indivizibil se selectează ca atare. Nu permite extragerea celei mai ieftine componente dintr-un pachet și combinarea ei cu alt furnizor fără confirmare tehnică și comercială. Combinarea permisă creează o configurație compusă cu sursele și dependențele fiecărei linii.
7. Ofertele parțiale pentru materiale independente rămân utile: se poate cumpăra ce este ofertat și completa din alte surse. O componentă lipsă dintr-un echipament obligatoriu nu echivalează cu o ofertă completă a acelui echipament.
8. Reducerile condiționate de pachet și transportul se recalculează pentru coșul efectiv. De exemplu, două prețuri unitare mai mici de la doi furnizori pot produce un coș mai scump după două transporturi. „Precompletează cu cel mai mic preț” trebuie evaluat la costul și regulile reale, cu metoda afișată.

Precompletarea ia numai configurații eligibile și complete. Dacă metoda existentă este o euristică pe poziții, spune acest lucru și recalculează totalul; nu o declara optim global fără evaluarea combinațiilor. Când există condiții comune pe care metoda nu le poate respecta, prezintă limitarea și lasă selecția manuală. Nu sări peste validări ca să obții întotdeauna o recomandare.

Scenariile se pot salva cu nume, duplica, modifica și compara înainte de aprobare. Pentru fiecare se păstrează conținutul, cantitățile, totalul, costurile comune, cursul și data, diferențele față de baza de comparație și confirmările lipsă. Un scenariu concurent nu consumă necesarul de două ori; angajarea efectivă urmează regula existentă de aprobare/comandă.

## Exemplu de scenarii pentru același necesar

| Scenariu | Conținut | Interpretare |
|---|---|---|
| S1 — Complet, suport 12 luni | Echipament, accesorii, licență și suport 12 luni | Configurație integrală conform primei alternative |
| S2 — Complet, suport 36 luni | Echipament, accesorii, licență și suport 36 luni | Conținut extins; se văd costul și perioada suplimentare |
| S3 — Fără suport separat | Echipament, accesorii și licență; suport exclus | Simulare sau configurație confirmată, după condițiile furnizorului |
| S4 — Alt furnizor | Produs/configurație echivalentă, verificată | Comparabilitate tehnică și de servicii documentată |
| S5 — Cantitate împărțită | Configurații/furnizori diferiți pentru părți din necesar | Numai cu cantități, capacități și condiții compatibile |

Diferența dintre S1 și S3 se numește diferență de conținut/cost, nu economie negociată pentru același lucru. Un scenariu pe 36 luni nu declară S1 complet pe 36 luni fără preț și condiții pentru perioada lipsă.

# 8. Continuitate către comandă, recepție și control

## P10 — Snapshot complet al selecției aprobate

Comanda generată din scenariu păstrează configurația, sursa și revizia, cantitatea produsului, componentele și cantitățile lor, prețurile, duratele, costurile comune, moneda/cursul și condițiile confirmate. Componentele gratuite rămân vizibile. Nu crea o comandă numai pentru suport pentru că acesta a avut prețul cel mai mic din listă.

Pentru același furnizor, grupează comenzile numai dacă regulile comerciale permit, păstrând sursa fiecărei linii și reducerile/transportul corect. Două oferte ale aceluiași furnizor nu devin automat o singură ofertă comercială.

Recepția distinge echipamentele și accesoriile fizice de licențe, servicii și documente de activare. Nu introduce 36 luni de suport ca 36 bucăți de switch în stoc. Dovada pentru un serviciu poate fi activarea, perioada de acoperire ori documentul acceptat de firmă, fără recepție fizică fictivă. Un tarif de serviciu facturat anticipat și durata executării sunt dimensiuni diferite.

Factura poate avea altă grupare a liniilor decât comanda, inclusiv un singur rând de pachet. Permite maparea la configurație sau componente fără a dubla valorile. Verificările de cantitate și valoare folosesc aceeași selecție; un pachet facturat global nu mai este adunat încă o dată cu detaliile sale.

În controlul achizițiilor trebuie să poți deschide traseul necesar → COF → document/ofertă → configurație → scenariu aprobat → comandă → recepție/activare → factură. Păstrează perioadele și dovezile existente. Nu se cere în acest lot un sistem complet nou de gestiune a abonamentelor sau un modul contabil; se păstrează metadatele și se integrează cu verificările actuale.

O ofertă simplă și factura introdusă direct, deja funcționale, rămân utilizabile. Nu obliga toate facturile la existența unei configurații ori a întregului flux anterior.

# 9. Migrarea înregistrărilor existente și protecția datelor

## P11 — Corectarea controlată a datelor deja introduse

Înregistrările vizibile sunt incomplete și au relații ambigue. Extinderea modelului singură nu le transformă automat în configurații complete. Este necesară o procedură verificabilă:

1. Creează un punct de restaurare folosind mecanismul proiectului. Inventariază ofertele, liniile, scenariile și comenzile afectate, fără resetarea bazei sau ștergerea în masă a datelor de test.
2. Prezintă o previzualizare a mapării: ofertă/revizie/linie veche → configurație/componentă nouă. Identifică în mod explicit hardware-ul, suportul și documentul comun. Asocierea sugerată poate folosi sursa, dar operatorul confirmă cazurile ambigue; nu reclasifica orice „variantă” drept componentă.
3. Pentru acest caz, păstrează cele două configurații alternative și identificatorii existenți unde este posibil. Mută suporturile în rol de componente ale configurațiilor potrivite și completează toate liniile lipsă din sursa atașată, inclusiv licența tarifată.
4. Reconciliază totalurile cu I26/I27/I41/I42 și păstrează raportul mapării. Nu îmbina două variante reale de produs și nu inventa că există 12 linii în alte oferte.
5. Rulează migrarea în mod idempotent: repetarea nu dublează ofertele, componentele ori documentul sursă. Păstrează auditul, legăturile și posibilitatea de revenire tehnică înainte de acceptare.
6. Scenariile neaprobate se marchează pentru verificare și se recalculează controlat. Comenzile/snapshoturile aprobate nu se rescriu; neconcordanțele se rezolvă prin corecție sau revizie conform fluxului existent.
7. Datele reale și conturile nu se confundă cu fixture-urile. Testele noi folosesc date marcate într-un mediu separat, când este disponibil, ori un set identificabil care poate fi curățat fără afectarea celorlalte înregistrări.

O corecție efectuată de operator trebuie să poată repara și alte configurații introduse greșit. Nu construi o migrare valabilă exclusiv pentru numele INTEND ori codul Cisco.

## P12 — Diagnostic și probe de acceptare

Verifică exact traseul actual din cod: structura payloadului de înregistrare, persistența liniilor și a relațiilor, alegerea variantei în agregarea comparației, selecția implicită din scenariu și generarea comenzii. Caută condiții care păstrează doar ultimul rând sau minimul unei poziții, ori ultima ofertă pe furnizor. Acestea sunt ipoteze de verificat, nu o cauză demonstrată din capturi.

Raportul final trebuie să arate pentru o configurație: liniile sursă, contribuția fiecăreia, cantitățile, totalul înainte/după reduceri, costurile comune și conversia. O captură cu un total plauzibil, fără acest traseu, nu dovedește că modelul a fost reparat.

Nu fixa totalurile în interfață și nu hardcoda răspunsurile fixture-ului. Adăugarea unei componente sau schimbarea cantității trebuie să producă noul rezultat în toate ecranele prin același calcul.

# 10. Implementare în loturi verificabile

Folosește loturi scurte și păstrează un punct funcțional după fiecare. Începe cu un rezumat al codului relevant și al mapării de date, apoi execută lucrul. Nu opri implementarea pentru alegeri tehnice de rutină. Clarifică doar o ambiguitate comercială care afectează prețul, conținutul sau eligibilitatea; restul poate continua.

| Lot | Schimbare | Condiție de închidere |
|---|---|---|
| L01 | Diagnostic, model și maparea datelor existente | Alternativele și componentele sunt distincte în date; migrarea are previzualizare, istoric și revenire |
| L02 | Calcul comun pentru configurații și coșuri | Se reconciliază Excelul și fixture-urile generale; niciun rând minim/ultim nu înlocuiește totalul pachetului |
| L03 | Editor, listă și corectarea ofertelor | Se pot introduce, redeschide și edita configurații complete, cu acțiuni distincte pentru alternativă și componentă |
| L04 | Matrice și comparație pe bază comună | Configurațiile au coloane proprii, componente extensibile și marcaje corecte de acoperire/cost |
| L05 | Scenarii și transmitere către comandă | Alegerea păstrează întreaga configurație; opțiunile, cantitățile și costurile comune sunt revalidate |
| L06 | Migrarea cazului, regresii și probe în browser | Datele existente sunt păstrate, totalurile reconciliate, exporturile și traseul ulterior verificate |

L01 poate necesita o migrare de schemă compatibilă, dar transformarea definitivă a datelor ambigue se face numai după previzualizare și validarea calculului. Nu lăsa un ecran cu date pe vechiul model să trimită payloaduri interpretate greșit de noul model. Gestionează compatibilitatea, versiunile și mesajele în perioada migrării.

Nu se cere publicare automată. Nu se schimbă întregul stack și nu se implementează un ERP, OCR general ori optimizare matematică universală pentru închiderea acestui defect. Se cer modelul general, operațiile uzuale și validările de mai sus. Păstrează calculele și funcțiile deja corecte.

# 11. Teste de acceptare pentru Emergent

**Important:** acestea sunt verificări de executat în aplicația modificată, nu rezultate deja obținute. Valorile numerice demonstrative sunt fictive. Pentru N01–N03 se folosește Excelul original atașat privat. Fișierul JSON din pachet oferă exemple independente de acel produs; nu este un import de producție și nu conține prețurile comerciale originale.

| ID | Scenariu | Rezultat obligatoriu |
|---|---|---|
| N01 | Introduce toate cele 24 de linii din Excel în cele două configurații | Fiecare configurație are 12 linii; I26 și I41 se reconciliază folosind precizia sursă; produsul principal și licența plătită sunt incluse |
| N02 | Simulează configurațiile fără suport separat | I27 și I42 se reconciliază; licența de 36 luni rămâne; separabilitatea comercială este explicită |
| N03 | Compară și selectează cazul real din capturi | Totalurile sunt ale configurațiilor, nu ale suporturilor; serviciul nu poate satisface singur necesarul de switch |
| N04 | Exemplul fictiv: echipament 1.000, licență 300, suport 120/300 | Total A 1.420 și B 1.600; trei produse A costă 4.260 înainte de costuri comune |
| N05 | Părinte tarifat cu copii tarifați; apoi părinte doar subtotal | Primul caz adună contribuțiile distincte; al doilea nu dublează subtotalul și copiii |
| N06 | Componentă inclusă la zero, apoi componentă fără preț | Zero rămâne în configurație; lipsa prețului marchează total incomplet și nu câștigă automat comparația |
| N07 | Trei produse, factor 2 accesorii per produs și montaj 200 per lot | Accesorii 6; montajul se plătește o dată; A cu montaj și transport 50 produce total 4.510 |
| N08 | Suport 120 pentru întreaga perioadă de 12 luni, apoi tarif 10/lună | Ambele pot totaliza 120 cu bazele corecte; primul nu devine 1.440 prin multiplicare nejustificată |
| N09 | Exclude accesoriu gratuit sau o componentă inclusă în preț global | Nu inventează scădere de preț; pentru pachetul inseparabil cere reconfirmare înainte de comandă |
| N10 | Reduceri de linie, apoi reducere comună, cu bazele declarate | Un singur calcul pe aceleași baze în editor, matrice și scenariu; prețul deja net nu este redus din nou |
| N11 | Schimbă cantitatea, transportul comun și minimul de comandă | Recalcul coerent; transportul nu se multiplică per componentă, minimul nu este adăugat ca taxă fictivă |
| N12 | Prețuri cu trei/șase zecimale, conversie și export | Precizia sursă și politica de rotunjire se păstrează; editorul, comparația, scenariul, PDF/XLSX au aceleași totaluri |
| N13 | Același furnizor cu două oferte independente și două alternative în una | Toate configurațiile curente rămân identificabile; ultima ofertă pe furnizor nu le ascunde |
| N14 | Revizia nouă a uneia dintre oferte | Numai familia potrivită este actualizată; istoricul și celelalte oferte/alternative rămân; selecțiile afectate cer verificare |
| N15 | Ofertă simplă și două produse alternative reale | Traseul rapid funcționează; alternativele rămân alternative, fără adunare automată într-un singur pachet |
| N16 | Creează componente prin „Adaugă componentă” și alternativă prin acțiunea separată | Datele persistate au relațiile corecte după redeschidere; nu este doar o schimbare vizuală |
| N17 | Suport 12/36 luni pe bază de nevoie 12 luni | Diferența de conținut/cost este explicită; utilizatorul poate alege eligibil varianta mai extinsă |
| N18 | Schimbă orizontul necesar la 36 luni fără preț de reînnoire | Varianta cu 12 luni are lipsă de acoperire; nu se extrapolează automat prețul și nu primește recomandare de acoperire completă |
| N19 | Elimină o componentă obligatorie ori neconfirmată în simulare | Apare motivul și starea condiționată; comanda fermă este blocată până la rezolvare |
| N20 | Două opțiuni de suport în același grup exclusiv și două accesorii independente | Se alege un suport potrivit regulii; accesoriile independente pot fi cumulate |
| N21 | Alege manual configurația eligibilă mai scumpă și reîncarcă | Configurația, motivul dacă se aplică, cantitatea și opțiunile rămân; recomandarea nu suprascrie selecția |
| N22 | Necesar 1, apoi necesar 10 împărțit 4 A + 6 B | Componentele nu consumă necesarul separat; cantitățile sunt validate; peste 10 produse sunt de asemenea permise când necesarul o cere |
| N23 | Două alternative cu disponibil comun 8; încearcă alocare totală 10 | Nu permite consum dublu al capacității comune; „DA” fără număr nu devine disponibil nelimitat |
| N24 | Pachet indivizibil și amestec de componente de la furnizori diferiți | Nu rupe tacit pachetul; combinarea condiționată păstrează sursele și cere confirmările relevante |
| N25 | Ofertă parțială pentru materiale independente | „Neofertat” nu devine zero; completarea din altă sursă respectă necesarul și costurile comune |
| N26 | Pachet 1.200 care acoperă două poziții, cu transport 50 | Total coș 1.250, nu 2.450; fiecare poziție are acoperirea corectă și contribuția este unică |
| N27 | Furnizor A: marfă 1.400 + transport 100; B: marfă 1.430 fără transport | Totalurile 1.500 și 1.430 sunt vizibile; prețul mic al mărfii nu este prezentat drept coș minim |
| N28 | Salvează, duplică și modifică două scenarii; emite comandă din unul | Scenariile sunt independente; comanda păstrează selecția aprobată, nu minimul implicit ori ultimul scenariu editat |
| N29 | Corectează oferta după existența unei comenzi aprobate | Comanda istorică rămâne identică; scenariile afectate sunt semnalate; modificarea comercială urmează revizia potrivită |
| N30 | Două sesiuni modifică oferta/scenariul sau emit comenzi pentru același rest | Conflictul de versiune și cantitatea rămasă sunt revalidate pe server; nu apare supracomandare |
| N31 | Previzualizează și repetă migrarea cazului curent | Maparea este verificabilă; toate liniile lipsă sunt completate; repetarea nu dublează date; alternativele reale nu sunt reclasificate automat |
| N32 | Editează, elimină și restaurează configurație/componentă | Sunt păstrate istoricul și dependențele; o sursă comună nu dispare odată cu o singură legătură; restaurarea nu aprobă automat |
| N33 | Redeschide editorul și pornește o revizie a unei configurații complete | Sunt preluate datele potrivite; salvarea unui formular incomplet nu șterge liniile existente |
| N34 | Comandă, recepție/activare și factură cu grupare diferită | Hardware-ul, accesoriile, licența și suportul păstrează legăturile; nu se dublează stocul sau valorile pachetului |
| N35 | Corp de iluminat cu driver/kit; tablou cu aparataj/montaj; cablu cu accesorii | Alte coduri, UM, cantități, durate și structuri funcționează fără modificarea codului; etichetele ierarhice rămân texte |
| N36 | Regresie: necesare, invitații, factură directă, proiecte, TVA necunoscut, audit și Coș | Funcțiile acceptate rămân corecte; niciun document fictiv, cotă fiscală sau confirmare comercială nu este introdus automat |

Prioritizează probele pentru defectul raportat și efectele lui. Repetă testele existente afectate; nu reexecuta întregul proiect fără justificare. Dacă un test nu a fost executat, marchează-l „Neexecutat” și explică motivul. „Implementat” și „Testat cu succes” sunt stări diferite.

# 12. Prompt de lucru și raportul final cerut

Mesajul integral gata de lipit este în fișierul TXT din pachet. Atașează acest PDF, Excelul original și capturile. Dacă originalul există deja și poate fi accesat în același proiect Emergent, indică-l explicit; nu este necesar să multiplici fișierul.

Solicită lui Emergent să înceapă prin a identifica modelele, endpointurile și ecranele actuale, apoi să execute L01–L06. Păstrează datele de test existente până la închiderea verificărilor relevante. Curățarea lor este o operație separată, controlată, după acceptare.

Raportul final trebuie să includă:

- cauza confirmată în cod și modificările efective de model, backend și frontend;
- maparea și rezultatul migrării datelor existente, inclusiv ce a necesitat confirmare;
- reconcilierea privată a celor patru totaluri Excel, cu precizia și politica de rotunjire;
- probe că lista ofertelor, matricea, scenariul, exportul și comanda folosesc aceeași configurație și aceleași totaluri;
- rezultatele reale N01–N36 și ale regresiilor relevante, cu teste neexecutate ori limite explicite;
- pașii scurți prin care Andrei poate introduce alt pachet, compara alternativele și selecta opțiunile;
- ce este finalizat, ce mai trebuie verificat și ce condiții comerciale trebuie confirmate în afara aplicației.

**Definiția finalizării:** cazul Excel funcționează integral, produse cu alte structuri funcționează fără cod nou, datele existente sunt păstrate, iar selecția aprobată ajunge corect în comandă și poate fi urmărită în fluxul ulterior. Simpla dispariție a unei erori vizuale sau afișarea unui total fix nu închide cerința.
