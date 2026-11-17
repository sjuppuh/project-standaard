## Wat en waarom

<!-- Wat verandert er en waarom? Verwijs naar een issue met "Closes #123". -->

## Hoe getest

<!-- Welke tests heb je toegevoegd of aangepast? Wat heb je handmatig gecontroleerd, en met welke browser of screenreader? -->

## Checklist

Vink alleen aan wat je echt hebt gecontroleerd. Is een punt niet van toepassing, zet dan "n.v.t." met een korte reden achter het punt.

### Tests

- [ ] Elke functionele wijziging heeft een test. Een bugfix begint met een test die de bug reproduceert.
- [ ] Alle checks zijn groen.
- [ ] Er zijn geen drempels, tests of lintregels versoepeld of overgeslagen (geen `.skip`, `eslint-disable`, `@ts-ignore` of lagere coverage), of dat is hieronder gemotiveerd.

### Toegankelijkheid

- [ ] Nieuwe of gewijzigde pagina's staan in de `urls` van de kwaliteitsworkflow.
- [ ] Alles is met alleen het toetsenbord te bedienen, in een logische volgorde en met zichtbare focus.
- [ ] Formuliervelden hebben gekoppelde labels, afbeeldingen hebben passende alt-teksten en het contrast voldoet.
- [ ] Wat handmatig gecontroleerd moet worden staat onder "Hoe getest" (zie `HANDMATIGE-CHECKS.md`).

### Security

- [ ] Er staan geen secrets, tokens of persoonsgegevens in code, tests, logs of commits.
- [ ] Alle nieuwe invoer wordt aan de serverkant gevalideerd.
- [ ] Nieuwe dependencies staan hieronder, met motivatie. Of: er zijn geen nieuwe dependencies.

### Documentatie

- [ ] De README en andere documentatie zijn bijgewerkt, of dat is niet nodig.
- [ ] De commits volgen Conventional Commits.

## Nieuwe dependencies

<!-- Per dependency: naam en versie, waarom nodig, waarom bestaande code niet volstaat, licentie. Verwijder deze sectie als er geen nieuwe dependencies zijn. -->
