# Clarificarea introducerii cantității — TQM RFQ v2.2

În captura trimisă, câmpul cantității există în coloana G, cu antetul „Necesar”, dar celulele G15, G16 etc. sunt goale. Mesajul anterior nu indica locul care trebuie completat.

Modificări limitate la completarea cantității în cererea RFQ:

- Antetul coloanei G devine **Cantitate**, iar celulele de introducere sunt evidențiate cu galben.
- Instrucțiunea de deasupra tabelului indică explicit coloana G.
- După **Preia in formular**, cursorul ajunge la cantitatea materialului selectat, pe același rând.
- Validarea celulei explică faptul că se introduce un număr mai mare decât 0, în UM din coloana F.
- Salvarea identifică rândul, materialul și celula cantității lipsă sau invalide și selectează celula pentru corectare, înainte de a scrie datele cererii.
- Cantitățile fracționare rămân permise, cu maximum 6 zecimale. Nu se introduce automat o cantitate implicită.
- Datele, foile principale, legăturile dintre module și regulile comerciale sunt păstrate. Corecția selectorului din v2.1 este inclusă.

## Verificări executate

Au trecut toate cele 22 de verificări de structură, integritate și sursă din `Audit/verification.json`: pachet ZIP/XML, relații, cele 48 de module VBA, referințele butoanelor, schemele tabelelor și păstrarea foilor existente.

Compararea directă cu v2.1 confirmă că se schimbă numai proiectul VBA și marcajul versiunii din foaia auxiliară RFQ. Toate datele stocate și celelalte componente sunt identice.

Cele șapte proceduri modificate sau adăugate au trecut analiza sintactică ANTLR VisualBasic6, fără erori. Analiza sintactică nu înlocuiește compilarea în Microsoft VBA.

`RFQ_TesteCircuit` include verificări noi pentru poziționarea cursorului, celula editabilă, identificarea erorii și cantitățile fracționare; rulează numai într-o copie temporară. **Microsoft Excel nu este disponibil aici: compilarea VBA, rularea testului și interacțiunea cu formularul pe Windows nu au fost executate.**

## Continuarea formularului din captură

În formularul deja deschis se pot completa direct cantitățile în G15, G16 etc., sub antetul Necesar. Nu este necesară reintroducerea materialelor pentru această corecție. Noul fișier păstrează datele versiunii livrate anterior; nu poate include modificările introduse ulterior pe calculatorul utilizatorului.

Fișier: `TQM_Cereri_Oferte_v2_2_RFQ.xlsm`, 731048 bytes.

SHA-256: `89eb7bbcde1fbf6febc25789f2fffa91e9b05e80dfbd41b56325d680864e371f`.
