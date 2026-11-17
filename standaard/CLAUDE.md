# Werkafspraken voor AI-gegenereerde code

Dit project volgt [project-standaard](https://github.com/sjuppuh/project-standaard). Deze regels gelden voor alle code die je schrijft of wijzigt. Twijfel je of iets mag, vraag het dan in plaats van te gokken. Kun je een regel niet naleven, meld dat dan expliciet met de reden.

## Toegankelijkheid (WCAG 2.2 AA)

- **Semantische HTML.** Gebruik `<button>` voor acties en `<a href>` voor navigatie. Zet koppen in logische volgorde: één `<h1>` per pagina en geen niveaus overslaan. Gebruik landmarks (`<header>`, `<nav>`, `<main>`, `<footer>`) en lijsten of tabellen voor inhoud die een lijst of tabel is. Gebruik ARIA alleen als er geen native element bestaat, en volg dan het patroon uit de ARIA Authoring Practices Guide.
- **Toetsenbordbediening.** Alles wat met de muis kan, kan ook met het toetsenbord, in een logische tabvolgorde. Gebruik nooit `tabindex` groter dan 0 en maak geen toetsenbordvallen. Een dialoog houdt de focus vast, sluit met Escape en zet de focus terug op het element dat hem opende. Voor sleepacties is er een alternatief met één klik of tik (2.5.7).
- **Zichtbare focus.** Elk focusbaar element heeft een duidelijke focusstijl, met minstens 3:1 contrast tegen de achtergrond. Gebruik `:focus-visible` en haal `outline` nooit weg zonder gelijkwaardige vervanging. Sticky headers, cookiebanners en andere overlappende inhoud mogen de focus niet verbergen (2.4.11).
- **Contrast.** Gewone tekst heeft minstens 4,5:1 contrast. Grote tekst (vanaf 24px, of 18,66px vet) heeft minstens 3:1, net als randen van invoervelden, iconen en andere grafische elementen die nodig zijn om de pagina te begrijpen. Breng informatie nooit alleen met kleur over.
- **Formulieren.** Elk veld heeft een zichtbaar `<label>` dat eraan gekoppeld is; een placeholder is geen label. Groepeer samenhangende velden met `<fieldset>` en `<legend>`. Een foutmelding staat als tekst bij het veld, is gekoppeld met `aria-describedby` en wordt aangekondigd. Gebruik `autocomplete` voor persoonsgegevens en vraag niet opnieuw om gegevens die de gebruiker al heeft ingevuld (3.3.7).
- **Alt-teksten.** Een informatieve afbeelding krijgt een `alt` die de inhoud of functie beschrijft, en een decoratieve afbeelding krijgt `alt=""`. Iconknoppen en links zonder zichtbare tekst hebben een toegankelijke naam.
- **Klikdoelen** zijn minstens 24 × 24 CSS-pixels, of hebben genoeg ruimte eromheen (2.5.8).
- **Statusberichten** zoals "opgeslagen", "3 resultaten" of foutmeldingen na versturen worden aangekondigd via `role="status"` of `role="alert"`, zonder de focus te verplaatsen.
- **Pagina's.** Zet `lang` op `<html>` en geef elke pagina een unieke, beschrijvende `<title>`. De inhoud blijft bruikbaar bij 200% zoom en bij een breedte van 320 CSS-pixels zonder horizontaal scrollen. Respecteer `prefers-reduced-motion`.
- **Inloggen** vraagt geen puzzel of andere cognitieve test, en plakken in wachtwoordvelden en wachtwoordmanagers blijven werken (3.3.8).

## Tests en checks

- Elke functionele wijziging krijgt een test die zonder de wijziging faalt en met de wijziging slaagt. Een bugfix begint met een test die de bug reproduceert.
- Voeg nieuwe pagina's en routes toe aan de `urls` in `.github/workflows/kwaliteit.yml`, zodat ze op toegankelijkheid worden gescand.
- Draai voor je een taak afrondt `npm run lint`, `npm run typecheck` en `npm test`, en meld eerlijk wat faalt.
- Er wordt niet gemerged met rode checks. Versoepel nooit een drempel, test of lintregel om een check groen te krijgen. Dat betekent: geen `.skip` of `.only`, geen `eslint-disable`, `@ts-ignore` of `nosemgrep`, en geen lagere coveragedrempel, tenzij je dat expliciet meldt en motiveert.
- Raakt een wijziging de gebruikersinterface, noem dan in de PR wat er handmatig gecontroleerd moet worden. Zie `HANDMATIGE-CHECKS.md`.

## Veiligheid

- **Nooit secrets in code**, tests, fixtures, logs of commits. Dat geldt voor API-sleutels, wachtwoorden, tokens, private keys en connection strings. Lees ze uit omgevingsvariabelen, zet voorbeeldwaarden in `.env.example` en controleer dat `.env` in `.gitignore` staat.
- **Valideer alle invoer** aan de serverkant met een schema (type, lengte, formaat, bereik), ook als de client al valideert. Weiger wat niet expliciet is toegestaan. Dat geldt ook voor queryparameters, headers, bestanden en data van externe API's.
- Bouw nooit SQL, shellcommando's, paden of HTML door invoer aan elkaar te plakken. Gebruik geparametriseerde queries en de escaping van het framework. Gebruik `innerHTML` of `dangerouslySetInnerHTML` niet met data die je niet zelf beheert.
- Toon gebruikers geen stacktraces of interne details. Log details aan de serverkant, zonder secrets of onnodige persoonsgegevens.
- **Gebruik geen nieuwe dependency zonder dit te melden.** Noem bij elke voorgestelde dependency de naam en versie, waarom die nodig is, waarom bestaande code of het platform niet volstaat, de licentie en hoe actief het pakket wordt onderhouden. Kies geen release die minder dan een week oud is.
- Pin GitHub Actions en herbruikbare workflows op een volledige commit-SHA, met de versie als commentaar. De enige uitzondering is de verwijzing naar project-standaard zelf (`@v1`), zodat verbeteringen aan de standaard direct in elk project gelden.

## Code

- Schrijf kleine functies met één verantwoordelijkheid. Is een functie langer dan ongeveer 40 regels of zit er meer dan drie niveaus nesting in, splits hem dan.
- Kies beschrijvende namen en vermijd afkortingen, behalve gangbare als `id` en `url`.
- Volg de bestaande structuur en stijl van het project. Introduceer geen nieuw patroon, framework of tool zonder overleg.
- Gebruik strikte typen. Gebruik `any` alleen met een motivatie in commentaar.
- Laat geen dode code, uitgecommentarieerde code of debug-logging achter.
- Commentaar legt uit waarom iets zo is, niet wat de code doet.

## Commits, pull requests en documentatie

- Gebruik Conventional Commits: `type(scope): beschrijving`, met als type `feat`, `fix`, `docs`, `test`, `refactor`, `perf`, `build`, `ci`, `chore` of `revert`. Markeer een brekende wijziging met `!` na het type en een `BREAKING CHANGE:`-footer. Eén logische wijziging per commit.
- Werk de README bij als installatie, configuratie, scripts, omgevingsvariabelen of gebruik veranderen.
- Vul de checklist in de PR-template eerlijk in en vink niets aan wat niet is gecontroleerd.
