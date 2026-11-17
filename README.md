# Project-standaard

De centrale kwaliteitsstandaard voor al mijn projecten. Eén herbruikbare GitHub Actions-workflow controleert bij elke pull request de toegankelijkheid, kwaliteit en veiligheid van een project. Een set standaardbestanden legt de afspraken vast, voor mensen en voor AI-codeerassistenten.

Een project sluit aan met één workflowbestand van een paar regels. Hieronder staat per thema wat de standaard afdwingt, en daarna hoe je een project aansluit.

```text
.github/
  workflows/kwaliteit.yml      herbruikbare workflow (workflow_call)
  workflows/zelftest.yml       controleert deze repo zelf
  dependabot.yml               houdt de gepinde actions van deze repo actueel
standaard/
  CLAUDE.md                    regels voor AI-gegenereerde code
  dependabot.yml               Dependabot-configuratie voor projecten
  pull_request_template.md     PR-checklist: tests, toegankelijkheid, security, documentatie
  SECURITY.md                  beleid voor het melden van kwetsbaarheden
  HANDMATIGE-CHECKS.md         toetsenbord- en screenreaderchecklist per release
  workflows/kwaliteit.yml      voorbeeld van de aanroep vanuit een project
```

## Wat de standaard afdwingt

De workflow draait zeven checks naast elkaar. Elke check staat standaard aan en is per project uit te zetten met een `run-*`-input. Een achtste job, **Kwaliteitspoort**, faalt zodra een ingeschakelde check faalt of wordt geannuleerd. Dat is de enige check die je in de branch protection verplicht hoeft te maken.

### Toegankelijkheid

| Eis | Hoe | Blokkeert merge |
| --- | --- | --- |
| Geen overtredingen van WCAG 2.2 AA | axe-core via Playwright, op elke opgegeven URL, in een desktop- en een mobiele viewport. Elke overtreding laat de check falen, ongeacht de ernst. | Ja |
| Lighthouse-score voor toegankelijkheid van minstens 90 | Lighthouse CI op elke URL | Ja |
| Toegankelijke code: semantische HTML, toetsenbordbediening, zichtbare focus, contrast, labels, alt-teksten | Regels in `CLAUDE.md` en de PR-checklist | Via review |
| Bruikbaar met toetsenbord en screenreader | `HANDMATIGE-CHECKS.md`, bij elke release | Releasecriterium |

Automatische tests vinden maar een deel van de toegankelijkheidsproblemen. Of een alt-tekst klopt, of de focusvolgorde logisch is en of een screenreader iets begrijpelijk voorleest, beoordeelt een mens. Daarom hoort de handmatige checklist bij elke release.

### Kwaliteit

| Eis | Hoe | Blokkeert merge |
| --- | --- | --- |
| Code voldoet aan de lintregels | `lint-command` | Ja |
| Geen typefouten | `typecheck-command` | Ja |
| Unit-tests slagen, met minstens 70% lijndekking | `test-command`; de workflow leest het lcov-rapport en vergelijkt het met `coverage-threshold` | Ja |
| End-to-end-tests slagen | Playwright-tests van het project (`e2e-command`) | Ja |
| De app bouwt | `build-command`, in de e2e- en Lighthouse-jobs (of in de lint-job als die allebei uitstaan) | Ja |
| Lighthouse-score voor best practices van minstens 90 | Lighthouse CI op elke URL | Ja |
| Elke functionele wijziging krijgt een test; geen merge met rode checks | `CLAUDE.md`, PR-checklist en de Kwaliteitspoort als verplichte check | Ja |

### Veiligheid

| Eis | Hoe | Blokkeert merge |
| --- | --- | --- |
| Geen secrets in de code of in de historie | gitleaks scant alle commits; gevonden secrets worden in de log gemaskeerd | Ja |
| Geen dependencies met bekende kwetsbaarheden | `npm audit`, vanaf niveau high | Ja |
| Geen onveilige codepatronen | Semgrep met de regelset `p/default` | Ja |
| Een veilige pipeline | Actions gepind op commit-SHA, alleen leesrechten per job, geen bewaarde tokens na checkout, tools met vaste versie of checksumcontrole, installaties zonder install-scripts | Ingebouwd |
| Invoer valideren, geen secrets, geen nieuwe dependency zonder melding | Regels in `CLAUDE.md` en de PR-checklist | Via review |
| Kwetsbaarheden zijn privé te melden | `SECURITY.md`, met de meldfunctie van GitHub | n.v.t. |

### Onderhoudbaarheid

| Eis | Hoe |
| --- | --- |
| Dependencies blijven actueel | Dependabot, wekelijks, voor npm en GitHub Actions. Minor- en patch-updates komen gebundeld, majors los. Nieuwe releases worden pas na een week overgenomen. |
| Leesbare code | `CLAUDE.md`: kleine functies, duidelijke namen, de bestaande stijl volgen, geen dode code |
| Een herleidbare geschiedenis | Conventional Commits, ook voor de PR's van Dependabot |
| Actuele documentatie | De README bijwerken bij elke relevante wijziging, met een vinkje in de PR-checklist |

## Een project aansluiten

### 1. Zorg dat het project deze scripts heeft

De standaard is gemaakt voor Node.js-projecten. Met de standaardinstellingen roept de workflow deze scripts aan:

| Commando | Doel | Overslaan met |
| --- | --- | --- |
| `npm ci` | dependencies installeren | |
| `npm run lint` | lint, bijvoorbeeld ESLint | `lint-command: ""` |
| `npm run typecheck` | typecheck, bijvoorbeeld `tsc --noEmit` | `typecheck-command: ""` |
| `npm test -- --coverage` | unit-tests met een lcov-rapport | `run-unit-tests: false` |
| `npm run build` | productiebuild | `build-command: ""` |
| `npm start` | de gebouwde app starten op `http://localhost:3000` | `start-command: ""` |

Ontbreekt een script, dan faalt de check. Iets overslaan is dus een bewuste keuze in het workflowbestand van het project. Gebruik je pnpm of yarn, of andere scripts, stel dan de commando's in via de inputs.

### 2. Laat de unit-tests een lcov-rapport schrijven

De workflow controleert de coveragedrempel zelf, met het rapport in `coverage/lcov.info`. Zo werkt de drempel hetzelfde voor elke testrunner.

**Vitest** (met `@vitest/coverage-v8`):

```ts
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    coverage: { provider: 'v8', reporter: ['text', 'lcov'] },
  },
});
```

**Jest** schrijft met `--coverage` standaard al een `coverage/lcov.info`.

### 3. Voeg de workflow toe

Kopieer [`standaard/workflows/kwaliteit.yml`](standaard/workflows/kwaliteit.yml) naar `.github/workflows/kwaliteit.yml` in het project en vul de URL's in:

```yaml
jobs:
  kwaliteit:
    name: Kwaliteit
    uses: sjuppuh/project-standaard/.github/workflows/kwaliteit.yml@v1
    permissions:
      contents: read
    with:
      urls: |
        http://localhost:3000/
        http://localhost:3000/contact
```

Zet alleen de inputs die afwijken van de standaard. Zet je een check uit, schrijf dan de reden erbij als commentaar.

### 4. Kopieer de standaardbestanden

| Bestand in deze repo | Plaats in het project |
| --- | --- |
| `standaard/CLAUDE.md` | `CLAUDE.md` (bestaat die al, voeg ze dan samen) |
| `standaard/dependabot.yml` | `.github/dependabot.yml` |
| `standaard/pull_request_template.md` | `.github/pull_request_template.md` |
| `standaard/SECURITY.md` | `SECURITY.md` |
| `standaard/HANDMATIGE-CHECKS.md` | `HANDMATIGE-CHECKS.md` |

Vanuit de root van het project kan dat ook in één keer. Let op: dit overschrijft bestaande bestanden.

```bash
bron=https://raw.githubusercontent.com/sjuppuh/project-standaard/v1/standaard
mkdir -p .github/workflows
curl -fsSL "$bron/CLAUDE.md"                -o CLAUDE.md
curl -fsSL "$bron/dependabot.yml"           -o .github/dependabot.yml
curl -fsSL "$bron/pull_request_template.md" -o .github/pull_request_template.md
curl -fsSL "$bron/SECURITY.md"              -o SECURITY.md
curl -fsSL "$bron/HANDMATIGE-CHECKS.md"     -o HANDMATIGE-CHECKS.md
curl -fsSL "$bron/workflows/kwaliteit.yml"  -o .github/workflows/kwaliteit.yml
```

### 5. Zet de repo-instellingen goed

- **Branch protection** (Settings → Rules → Rulesets, voor `main`): pull request verplicht, status check **`Kwaliteit / Kwaliteitspoort`** verplicht, geen force pushes.
- **Dependabot** (Settings → Advanced Security): zet *Dependabot alerts* en *Dependabot security updates* aan.
- **Secret scanning**: zet *Secret scanning* en *Push protection* aan. Dan houdt GitHub een secret al tegen bij de push, nog voordat gitleaks het in de pull request vindt.
- **Private vulnerability reporting**: zet dit aan, anders werkt de meldroute uit `SECURITY.md` niet.

## Inputs

| Input | Standaard | Betekenis |
| --- | --- | --- |
| `node-version` | `lts/*` | Node.js-versie |
| `cache` | `npm` | pakketbeheerder voor de dependency-cache; leeg betekent geen cache |
| `install-command` | `npm ci` | dependencies installeren |
| `lint-command` | `npm run lint` | lint; leeg betekent overslaan |
| `typecheck-command` | `npm run typecheck` | typecheck; leeg betekent overslaan |
| `test-command` | `npm test -- --coverage` | unit-tests die een lcov-rapport schrijven |
| `coverage-file` | `coverage/lcov.info` | pad naar het lcov-rapport |
| `coverage-threshold` | `70` | minimale lijndekking in procenten |
| `build-command` | `npm run build` | build; leeg betekent overslaan |
| `start-command` | `npm start` | start de app op de achtergrond; leeg betekent dat de URL's al bereikbaar zijn (bijvoorbeeld een preview-deployment) |
| `e2e-command` | leeg | eigen Playwright-tests van het project; leeg betekent overslaan |
| `urls` | `http://localhost:3000/` | te testen URL's, gescheiden door spaties of regeleinden |
| `start-timeout` | `120` | seconden wachten tot de eerste URL reageert |
| `lighthouse-min-score` | `90` | minimale Lighthouse-score voor toegankelijkheid en best practices |
| `audit-command` | `npm audit --audit-level=high` | dependency-audit |
| `semgrep-config` | `p/default` | Semgrep-regelsets of -bestanden, gescheiden door spaties |
| `run-lint` | `true` | job lint en typecheck |
| `run-unit-tests` | `true` | job unit-tests en coverage |
| `run-e2e` | `true` | job e2e-tests en axe-core-scan |
| `run-lighthouse` | `true` | job Lighthouse CI |
| `run-secrets-scan` | `true` | job gitleaks |
| `run-dependency-audit` | `true` | job dependency-audit |
| `run-semgrep` | `true` | job Semgrep |

Het `e2e-command` draait voordat de workflow de app start. Laat Playwright de server dus zelf starten via `webServer` in `playwright.config`. De workflow installeert vooraf de Playwright-browsers van het project.

## Uitzonderingen

Soms is een melding onterecht of niet direct op te lossen. Leg een uitzondering dan zo smal mogelijk vast, met de reden erbij, zodat die in de review zichtbaar is.

- **gitleaks.** Is een gevonden secret echt gelekt, trek hem dan eerst in en vervang hem. Een vals positief of een al ingetrokken secret in de historie neem je op in `.gitleaksignore`, met de fingerprint uit de log.
- **Semgrep.** Zet een vals positief uit met `// nosemgrep: <regel-id>` en de reden op dezelfde regel, of sluit bestanden uit via `.semgrepignore`.
- **npm audit.** Werk de dependency bij, of dwing een veilige versie van een indirecte dependency af met `overrides` in `package.json`. Kan dat nog niet, beschrijf de uitzondering dan in de PR en maak er een issue van.
- **axe-core.** Los de overtreding op. Een check uitzetten om een merge door te drukken is precies wat deze standaard wil voorkomen.

## Versies en onderhoud

- Projecten verwijzen naar een versie van de standaard, bijvoorbeeld `@v1`. Bij elke release komt er een tag als `v1.2.0`, en de tag `v1` schuift mee naar de nieuwste `v1.x`. Wie strikter wil pinnen, gebruikt de commit-SHA met de versie als commentaar. Dependabot werkt die dan bij.
- Een wijziging die bestaande projecten rood kan maken, zoals een nieuwe check die standaard aanstaat of een strengere drempel, krijgt een nieuwe majorversie.
- Dependabot houdt de actions van deze repo actueel. De versies van Playwright, axe-core, Lighthouse CI, Semgrep en gitleaks staan in het `env`-blok bovenaan de workflow; die werk je met de hand bij. Kies daarbij een release die minstens een week oud is, en vervang bij gitleaks ook de checksum.
- De workflow [`zelftest.yml`](.github/workflows/zelftest.yml) controleert deze repo bij elke wijziging met actionlint en shellcheck, en past de eigen secrets- en Semgrep-scan toe op deze repo.
