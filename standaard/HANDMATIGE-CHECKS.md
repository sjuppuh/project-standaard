# Handmatige toegankelijkheidschecks per release

Automatische tests zoals axe-core en Lighthouse vinden maar een deel van de toegankelijkheidsproblemen. Of de focusvolgorde logisch is, of een alt-tekst klopt en of een screenreader iets begrijpelijk voorleest, moet een mens beoordelen.

Loop deze checklist bij elke release door, op de belangrijkste routes door de app (bijvoorbeeld: home, zoeken, detailpagina, formulier versturen). Kopieer de checklist naar het release-issue of de release-PR en vink af wat je hebt gecontroleerd. Maak voor elk gevonden probleem een issue met het label `toegankelijkheid`. Een probleem dat een gebruiker blokkeert, houdt de release tegen.

| Release | Datum | Getest door | Routes |
| --- | --- | --- | --- |
| | | | |

## 1. Toetsenbord

Gebruik alleen het toetsenbord: Tab, Shift+Tab, Enter, Spatie, de pijltjestoetsen en Escape. Raak de muis en het trackpad niet aan.

- [ ] Er is een skiplink ("Naar de inhoud") die als eerste focus krijgt en werkt.
- [ ] Alle links, knoppen en velden zijn met Tab bereikbaar en met Enter of Spatie te bedienen.
- [ ] De tabvolgorde volgt de visuele en logische volgorde van de pagina.
- [ ] De focus is altijd zichtbaar, op elk element en in elk kleurthema.
- [ ] De focus valt nooit helemaal weg achter een sticky header, cookiebanner of chatwidget (2.4.11).
- [ ] Er is geen toetsenbordval: je komt overal weer weg met Tab of Escape.
- [ ] Menu's, uitklappers, tabbladen en keuzelijsten werken met de pijltjestoetsen zoals je verwacht.
- [ ] Een dialoog: de focus gaat erin bij openen en blijft erin, Escape sluit hem en de focus keert terug naar de knop die hem opende.
- [ ] Formulieren zijn volledig in te vullen en te versturen. Bij een fout gaat de focus naar de foutmelding of naar het eerste foute veld.
- [ ] Na navigatie in een single-page app staat de focus op een logische plek, zoals de nieuwe `<h1>`, en is de paginatitel veranderd.
- [ ] Focus of invoer leidt niet tot onverwachte gebeurtenissen, zoals automatisch versturen of naar een andere pagina gaan.
- [ ] Wat met slepen kan (sorteren, schuifregelaars, kaarten), kan ook zonder slepen (2.5.7).

## 2. Screenreader

Test per release met minstens één combinatie, en wissel tussen releases:

- NVDA met Firefox of Chrome (Windows)
- VoiceOver met Safari (macOS of iOS)
- TalkBack met Chrome (Android)

Combinatie deze release: …

- [ ] De paginatitel wordt voorgelezen en beschrijft de pagina.
- [ ] De uitspraak klopt: de taal van de pagina is goed ingesteld, net als die van anderstalige passages.
- [ ] De lijst met koppen (NVDA: H of Insert+F7, VoiceOver: rotor) geeft de structuur van de pagina weer, zonder overgeslagen niveaus.
- [ ] De landmarks (header, navigatie, main, footer) zijn aanwezig en herkenbaar.
- [ ] Links en knoppen hebben een naam die ook zonder context duidelijk is: geen losse "klik hier" of "lees meer".
- [ ] Iconknoppen worden met hun functie voorgelezen, niet als "knop" of als bestandsnaam.
- [ ] Informatieve afbeeldingen krijgen een zinvolle beschrijving; decoratieve afbeeldingen worden overgeslagen.
- [ ] Bij formuliervelden worden het label, of het veld verplicht is en de hulptekst voorgelezen. Foutmeldingen worden aangekondigd en bij het veld voorgelezen.
- [ ] Statusberichten zoals "opgeslagen", "12 resultaten" of "laden" worden aangekondigd zonder dat de focus verspringt (4.1.3).
- [ ] De toestand van componenten wordt voorgelezen: uitgeklapt of ingeklapt, geselecteerd, aangevinkt, huidige pagina.
- [ ] In tabellen worden de kolomkoppen voorgelezen als je door de cellen gaat.
- [ ] Een dialoog wordt met zijn titel aangekondigd, en de inhoud erachter is niet bereikbaar zolang hij openstaat.
- [ ] Hulp of contactgegevens staan op elke pagina op dezelfde plek (3.2.6).

## 3. Zoom en weergave

- [ ] Bij 200% zoom is alle inhoud leesbaar en bruikbaar.
- [ ] Bij een breedte van 320 CSS-pixels (of 400% zoom) hoef je niet horizontaal te scrollen en valt er geen inhoud weg (1.4.10).
- [ ] Met "Beweging verminderen" aan in het besturingssysteem stoppen animaties of worden ze rustiger.
- [ ] Inloggen werkt met een wachtwoordmanager en met plakken, zonder puzzel of andere cognitieve test (3.3.8).

## Resultaat

| Route | Toetsenbord | Screenreader | Zoom | Gevonden problemen (issues) |
| --- | --- | --- | --- | --- |
| | | | | |
