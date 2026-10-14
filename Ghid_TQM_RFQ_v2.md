# Folosirea versiunii TQM RFQ v2

Datele păstrate în fișier sunt exemple fictive. Nu sunt o bază istorică și nu sunt folosite pentru probabilități sau predicții. Comparația ofertelor se face din cantitățile, prețurile și condițiile introduse pentru cererea respectivă.

## Ce înseamnă cele trei niveluri

- **Cerere comună RFQ**: un document, de exemplu `RFQ-2026-0001`, care poate conține 10 materiale. Numărul este afișat în formularul cererii și în comparație.
- **Poziție a cererii**: fiecare material are un ID intern stabil. Aceste ID-uri rămân în registrul existent, pentru legăturile cu comenzile, recepțiile și facturile. Nu sunt 10 documente de cerere separate.
- **Document de ofertă al furnizorului**: un număr de ofertă, un furnizor și condiții comune, cu răspuns numai pentru pozițiile pe care le poate furniza.

O ofertă înregistrată nu devine automat aprobată. Aprobarea este o decizie cu furnizor, cantitate, persoană și motiv. O cerere poate avea mai multe atribuiri și mai multe comenzi.

## Exemplu: o cerere cu 10 materiale

1. În **06_NOMENCLATOARE**, verifică materialele și activează numai înregistrările verificate. Datele existente de test nu au fost activate automat. Înregistrează și proiectele, furnizorii și locațiile pe care le vei folosi.
2. În **02_CERERI_SI_OFERTE**, apasă **Cerere noua**. Completează antetul și cele 10 rânduri de materiale. Folosește **Selecteaza material** pe rândul respectiv. Cantitatea și termenul necesar se completează pe fiecare rând. Proiectul poate fi diferit între poziții; dacă este gol, la salvare se preia proiectul implicit. Salvează cererea, apoi salvează fișierul principal cu **Ctrl+S**.
3. Selectează o poziție a acestei cereri în registru și apasă **Compara**. Raportul arată întreaga cerere. Poți exporta cererea comună în PDF și ține lista furnizorilor invitați. Exportul nu trimite mesaje; marchezi manual invitațiile trimise, refuzurile sau lipsa răspunsului.
4. Închide comparația. Selectează o poziție și apasă **Oferta noua**. Completează furnizorul, antetul documentului, livrarea și conformitatea tehnică. Dacă A are numai materialele 1–6, completează cantități și prețuri numai la aceste poziții. Cantitatea goală sau 0 înseamnă **fără ofertă pe poziție**. Prețul 0 este acceptat numai dacă îl introduci explicit pentru o cantitate pozitivă. O linie cu conformitate **De verificat** sau **Nu** nu poate fi aprobată; marchează **Da** numai după verificare.
5. Repetă pentru B, care poate avea materialul 1 și materialele 7–10. **Modifica oferta** deschide întregul document al furnizorului pornind de la oricare dintre liniile sale.
6. Deschide **Compara**. Vezi necesarul, cantitatea atribuită, restul, cantitatea oferită, prețul net pe unitate, minimele, livrarea și eligibilitatea. Sub liniile ofertelor se află condițiile documentelor și costul selecției încă necomandate, inclusiv transportul și celelalte costuri comune.
7. Selectează linia dorită și apasă **Aproba linia selectata**. Pentru același material poți atribui 60 dintr-un necesar de 100 lui A și restul de 40 lui B. Programul verifică necesarul rămas și disponibilul ofertat. Introdu persoana și motivul alegerii. Alegerea nu este automată după prețul pe unitate: verifică și transportul, livrarea și condițiile.
8. Apasă **Genereaza comenzi**. Rezultă câte o comandă **Ciorna** pentru fiecare furnizor selectat, cu pozițiile și cantitățile atribuite. Costurile comune se adaugă o singură dată pentru fiecare document de ofertă inclus în acea comandă. Repetarea generării nu dublează atribuiri deja comandate.
9. În **03_COMENZI_FURNIZORI**, selectează și deschide fiecare comandă. Verifică documentul, responsabilul și informațiile administrative, apoi schimbă starea în **Trimisa** sau **Confirmata**, conform situației reale. Poți folosi și butonul existent **Din oferte aprobate**, introducând numărul comun RFQ.
10. Înregistrează recepțiile în **03_COMENZI_FURNIZORI**, apoi folosește **Preia receptii** în **04_LIVRARI_SI_STOC**, cu cantitatea preluată și locația reală. Recepția comenzii și intrarea în stoc sunt două operații distincte.
11. În **05_FACTURI_SI_PLATI**, pornește factura din comanda sau recepția corectă. Introdu valorile și TVA din factura reală. Pentru transport sau alte costuri, folosește o repartizare separată, legată de **ID_comanda**, fără ID de poziție sau recepție; nu le adăuga încă o dată la prețul materialelor. Înregistrează plățile separat, conform documentelor de plată.
12. Verifică **08_PROIECTE_BUGETE** și **09_DOCUMENTE**. Bugetele țin cont de facturi și de angajamentele nefacturate ale comenzilor trimise/confirmate. O ciornă nu este un angajament bugetar activ. Costurile comune ale comenzilor RFQ cu mai multe proiecte se repartizează pe proiectele pozițiilor din documentul furnizorului, proporțional cu valoarea materialelor; dacă toate valorile sunt zero, se repartizează egal. Rotunjirile păstrează totalul costurilor.

## Condiții comerciale și modificări

**Independent** permite atribuirea unor poziții sau cantități din document. **Integral** înseamnă că toate liniile active și toate cantitățile ofertate se acceptă împreună. Aprobarea unei linii dintr-un astfel de document aprobă întregul pachet, dacă încape în necesarul rămas. Discountul de pachet în RON este disponibil numai pentru Integral.

Pragul minim al documentului se verifică la generarea comenzilor, pe valoarea mărfii după discount, fără transport și alte costuri. Discountul de pachet se repartizează în prețurile nete ale pozițiilor comandate, cu rotunjiri la bani, astfel încât recepțiile, facturile și bugetele să folosească aceleași valori. Costurile comune se aplică fiecărei comenzi noi care utilizează documentul; o comandă ulterioară nu primește automat transport gratuit.

Pentru o poziție existentă, folosește **Modifica cerere**. Nu șterge rândul: marchează-l **Anulata** dacă nu are comenzi. Modificarea cantităților, prețurilor sau condițiilor invalidează atribuirile necomandate afectate; acestea trebuie reconfirmate. Aprobările expirate nu permit generarea sau trimiterea unei ciorne noi.

**Retrage aprobare** arată ID-urile atribuirilor pentru cererea selectată. Poți retrage o atribuire necomandată; la Integral se retrage întregul pachet necomandat. După generarea comenzii, anularea se face din **Comenzi furnizori → Anuleaza restul**. Cantitățile deja primite rămân în istoric, iar restul anulat poate fi atribuit din nou. Anularea unei părți dintr-un pachet deja comandat necesită verificarea condițiilor renegociate cu furnizorul; Excel nu inventează prețuri noi.

Datele comerciale ale documentelor deja folosite în comenzi rămân fixe. Pentru condiții noi, înregistrează o ofertă nouă cu numărul reviziei. Nu modifica manual tabelele auxiliare ascunse sau ID-urile interne.

## Rolul fiecărei foi

| Foaie | Rol și legături |
|---|---|
| 00_DASHBOARD | Acces la module și situația dosarelor/documentelor din Control. |
| Control_Achizitii | Registru al documentelor primite și al completitudinii dosarelor; nu înlocuiește cererea RFQ, recepția sau factura financiară. |
| 02_CERERI_SI_OFERTE | Necesarul, cererile comune, documentele ofertelor, comparația și atribuirea cantităților. |
| 03_COMENZI_FURNIZORI | Comenzi pe furnizor, poziții, recepții și anularea restului. Păstrează sursele RFQ. |
| 04_LIVRARI_SI_STOC | Preluarea recepțiilor în locații, stocuri, transferuri, consumuri și retururi. |
| 05_FACTURI_SI_PLATI | Facturi, repartizări pe proiecte/comenzi/poziții/recepții, plăți, solduri și storno. |
| 06_NOMENCLATOARE | Identitatea materialelor, furnizorilor, proiectelor și locațiilor folosite de module. |
| 07_SETARI_LISTE | Parametri, liste și regulile documentelor necesare în dosare. |
| 08_PROIECTE_BUGETE | Bugete aprobate, facturat, angajamente nefacturate, disponibil, plăți și solduri. |
| 09_DOCUMENTE | Arhiva și legăturile documentelor cu operațiile, inclusiv cererea RFQ comună și documentul ofertei. |

Foile de backup existente rămân copii de lucru anterioare; operațiile circuitului se fac în foile principale de mai sus.

## Verificarea pe Windows

Deschide fișierul în Excel desktop pe Windows și permite macrocomenzile pentru acest fișier. Dacă Windows îl marchează ca descărcat de pe internet și Excel blochează macrocomenzile, verifică sursa, închide Excel și folosește **Proprietăți fișier → Deblocare**, apoi redeschide-l.

Apasă **Alt+F8**, selectează **RFQ_TesteCircuit** și rulează. Testul creează o copie temporară, introduce date fictive numai acolo și lasă rezultatele într-un registru nou. Fișierul principal nu primește datele testului. Rezultatul trebuie să se încheie cu `FINAL: toate verificarile executate de acest test au trecut.` Orice `FAIL / OPRIT` trebuie analizat înainte de folosirea circuitului pentru date reale.

Testul acoperă cererea cu 10 materiale, răspunsurile parțiale, împărțirea 60+40, depășirea necesarului, invalidarea aprobărilor, generarea repetată, transportul comun, recepția parțială, stocul, factura și plata parțiale, expunerea bugetară, reatribuirea restului anulat, pachetul integral, pragul minim, costurile pe două proiecte și restaurarea după o eroare de scriere. El validează funcțiile și calculele cu înregistrări de test; nu simulează clicurile și completarea tuturor formularelor.

Verifică separat pe o copie: deschiderea/salvarea/modificarea formularelor, selectarea din Nomenclatoare, discountul introdus ca procent, sortarea și filtrele, trimiterea ciornei, preluarea recepției în stoc și exportul PDF. În acest mediu de lucru nu există Microsoft Excel; testul VBA și verificările vizuale pe Windows nu au fost executate aici.
