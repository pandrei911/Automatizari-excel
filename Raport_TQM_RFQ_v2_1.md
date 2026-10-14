# Corectarea selectorului de materiale — TQM RFQ v2.1

Problema raportată: Selecteaza material deschidea o listă goală în cererea RFQ.

Cauza verificată în fișierul livrat: există 51 de materiale cu ID, toate cu starea De verificat. Selectorul anterior includea numai starea Activ.

Corecția este limitată la selecția materialelor și validarea cererii:

- Selectorul cererii arată materiale Activ și De verificat, cu starea reală în coloana Stare.
- Preia in formular copiază codul, denumirea, specificația și UM în rândul cererii.
- Cererea se poate salva cu materiale De verificat.
- Materialele Inactiv sunt excluse din selecția pentru cereri noi.
- Aprobarea ofertelor și generarea comenzilor cer în continuare materiale Activ. Mesajul precizează materialul și starea care trebuie verificată.
- Selecțiile pentru comenzile manuale, stoc, parteneri și proiecte păstrează regulile lor anterioare.
- O listă fără înregistrări eligibile arată o explicație.
- Materialele existente nu sunt activate automat. Cele 51 de stări De verificat sunt păstrate.
- Limita cererii rămâne 100 de poziții.

## Verificări executate

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
- PASS — RFQ picker and request saving accept pending materials; approval still requires Active.
- PASS — Empty selection lists explain missing eligible records.
- PASS — Regression test covers real picker, field transfer, pending request saving and approval gate.
- PASS — Windows acceptance runner uses a guarded disposable workbook copy.
- PASS — No stored broken worksheet formula references.
- PASS — Compararea cu v2 confirmă păstrarea foilor și datelor existente; în foaia auxiliară RFQ se schimbă numai marcajul versiunii.
- PASS — Cele șapte proceduri afectate au trecut analiza sintactică ANTLR VisualBasic6.

## Testul pentru Excel Windows

RFQ_TesteCircuit include acum un test care deschide selectorul real, selectează un material De verificat, preia câmpurile în cerere și verifică salvarea acesteia. Verifică și excluderea materialelor Inactiv, păstrarea stării după selecție și respingerea aprobării până la activarea explicită în copia de test.

Microsoft Excel nu este disponibil în acest mediu: **testul VBA, compilarea în Microsoft VBA și interacțiunea cu selectorul pe Windows nu au fost executate aici**. Verificările executate sunt de structură, integritate, sursă și sintaxă. Testul inclus rulează într-o copie temporară; nu schimbă registrele principale.

Pentru utilizare: Cerere noua → selectează rândul materialului → Selecteaza material → selectează un material din listă → Preia in formular. Consultă Ghid_TQM_RFQ_v2_1.md.

## Fișier

TQM_Cereri_Oferte_v2_1_RFQ.xlsm — 729688 bytes.

SHA-256: `ba0bec9afaec69d3d21441db14907b08bc2d338e6ad119952d9a2e9de80e1ee4`.
