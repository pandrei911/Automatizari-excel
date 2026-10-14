# Verificarea TQM RFQ v2 — 14.10.2026

Fișier: **TQM_Cereri_Oferte_v2_RFQ.xlsm**. Versiune actualizată, pregătită pentru validare în Excel desktop pe Windows.

## Modificări în circuit

- Cerere comună RFQ cu până la 100 de poziții, peste ID-urile interne existente.
- Documente de ofertă pe furnizor, răspunsuri parțiale și cantități disponibile/minime.
- Atribuiri cantitative, inclusiv împărțirea unui material între furnizori; blocarea depășirii necesarului sau a disponibilului ofertat.
- Costuri comune pe document, prag minim și condiție Integral; discountul de pachet se reflectă în valorile pozițiilor.
- Comenzi Ciorna grupate pe furnizor, protecție împotriva generării repetate și păstrarea ID-urilor de poziție pentru recepții, stoc și facturi.
- Invalidarea atribuirilor necomandate la schimbarea surselor sau la pierderea eligibilității; sursele deja comandate rămân fixe.
- Restul anulat poate fi reatribuit; cantitățile primite rămân în istoric.
- Costurile comune se repartizează în bugete pe proiectele pozițiilor, cu păstrarea totalului la rotunjire.
- Export PDF al cererii comune, registru manual al invitațiilor și surse RFQ/document ofertă în arhivă.

Datele existente sunt păstrate ca date fictive. Nu au fost introduse estimări statistice, probabilități istorice sau exemple noi în registrele fișierului livrat.

## Verificări executate în acest mediu

- PASS — ZIP integrity.
- PASS — Every original package part retained.
- PASS — All original 39 sheets are byte-identical to corrected v1.
- PASS — Original formatting, buttons, charts, table schemas, print settings and test data retained.
- PASS — Every XML part parses.
- PASS — All package relationships are unique and resolve.
- PASS — Six RFQ data sheets appended and very hidden; original sheet order retained.
- PASS — 35 original schemas and six RFQ schemas have unique IDs and expected columns.
- PASS — OLE metadata and source offsets retained; no obsolete compiled caches.
- PASS — All 48 VBA source streams decompress to the exact reviewed sources.
- PASS — Packaged RFQ code includes the latest implementation fragments.
- PASS — No duplicate public routines; VBA line lengths within limit.
- PASS — Every one of the 100 existing button assignments resolves to a public routine.
- PASS — Every added form button resolves to a public routine.
- PASS — Every literal RFQ table reference resolves.
- PASS — Managed order guards and downstream calculation entry points present.
- PASS — Windows acceptance runner uses a guarded disposable workbook copy.
- PASS — No stored broken worksheet formula references.

Analiza sintactică a procedurilor noi a fost executată cu gramatica ANTLR VisualBasic6; procedurile schimbate ulterior au fost analizate din nou, fără erori semnalate. Aceasta este o verificare de sintaxă, nu compilarea Microsoft VBA. Revizuirea simbolurilor locale nu a identificat variabile nedeclarate în codul nou.

Cele 39 de foi preexistente, cele 35 de tabele originale, formele butoanelor, stilurile, formulele stocate și datele de test sunt identice cu versiunea v1 corectată. Sunt adăugate șase foi auxiliare VeryHidden. Se păstrează toate cele 48 de module VBA; modificările circuitului sunt în modulele existente. Codul se recompilă din sursă în Excel; cache-urile compilate vechi nu sunt incluse.

## Verificări neexecutate aici

Mediul nu are Microsoft Excel. **Nu au fost executate compilarea proiectului în Microsoft VBA, macrocomanda RFQ_TesteCircuit, interacțiunea cu formularele/butoanele, sortarea/filtrele și exportul PDF pe Windows.** Fișierul nu trebuie confundat cu o versiune certificată prin teste în Excel.

În fișier există RFQ_TesteCircuit, care lucrează exclusiv într-o copie temporară marcată pentru test. Acoperă cererea cu 10 materiale, oferte parțiale, atribuirea 60+40, depășirea necesarului, schimbarea condițiilor, generarea repetată, transportul comun, recepția și stocul, factura/plata parțiale, bugetul fără dublare, reatribuirea restului, pachetul integral, pragul minim, două proiecte și restaurarea după eroare. Acest test însămânțează înregistrări valide în copia temporară și cheamă funcțiile/calculul; verificările de interfață se fac separat.

Pentru validarea finală în Windows: Alt+F11 → Debug → Compile VBAProject, apoi Alt+F8 → RFQ_TesteCircuit și pașii de interfață din Ghid_TQM_RFQ_v2.md. Nu este necesară activarea accesului programatic la proiectul VBA pentru a rula testul inclus.

## Identificarea fișierului

Dimensiune: 726980 bytes.

SHA-256: `f854b5da1c3e88fd96c36ecbf1717a254dea1ae811fd33315bd158e1b738b17c`.
