# Manus för redovisning - Enhetstester för Strajk Bowling

## Inledning

Jag har implementerat enhetstester för React-applikationen Strajk Bowling enligt de specifikationer som angavs i examinationen. Testerna täcker alla user stories med deras acceptanskriterier och är konfigurerade att köras automatiskt via GitHub Actions.

## Vad som har implementerats

### 1. Testmiljö och konfiguration

**Problem som löstes:**
- Node.js var inte installerat på systemet
- Testdependencies saknades i projektet
- Ingen testkonfiguration fanns

**Lösningar:**
- Installerade Node.js v24.12.0 och npm 11.6.2
- Lade till testdependencies i `package.json`:
  - `@testing-library/react` - för att testa React-komponenter
  - `@testing-library/jest-dom` - för extra matchers
  - `@testing-library/user-event` - för att simulera användarinteraktioner
  - `vitest` - test runner (kompatibel med Vite)
  - `jsdom` - DOM-miljö för tester
  - `msw` (Mock Service Worker) - för att mocka API-anrop

- Konfigurerade Vitest i `vite.config.js` med jsdom-miljö och setup-fil

### 2. Mock Service Worker (MSW)

**Varför MSW?**
Enligt examinationen skulle POST-anrop mockas med Mock Service Worker. MSW är ett verktyg som låter oss intercepta HTTP-requests och returnera mockade svar.

**Implementation:**
- Skapade `src/test/mocks/handlers.js` som mockar booking-API:et
- Mocken beräknar korrekt pris: 120 kr per person + 100 kr per bana
- Skapar dynamiska bokningsnummer
- Konfigurerade MSW-server i `src/test/mocks/server.js`
- Setup-fil (`src/test/setup.js`) startar och stänger MSW-servern automatiskt

### 3. Data-testid attribut

**Varför data-testid?**
Eftersom Input-komponenten inte använder `htmlFor` för att koppla labels till inputs, kunde vi inte använda `getByLabelText` i testerna. Istället lade vi till `data-testid` attribut med kommentarer som förklarar varför de behövs.

**Var data-testid lades till:**
- `Booking.jsx`: `complete-booking-button` - för att testa "slutför bokning"-knappen
- `Shoes.jsx`: `add-shoe-button` och `remove-shoe-{id}` - för att testa skostorlekshantering
- `Navigation.jsx`: `nav-booking` och `nav-confirmation` - för att testa navigation
- `Confirmation.jsx`: `booking-number`, `total-price`, `no-booking-message` - för att testa bekräftelsesidan
- `Input.jsx`: Använder `name`-attributet som testid (eller specifikt `testId` prop)

Alla data-testid attribut har kommentarer i koden som förklarar vilka user stories de används för.

### 4. Testfiler

#### Booking.test.jsx (18 tester)
Täcker User Stories 1-4:

**User Story 1: Boka datum, tid och antal spelare**
- Testar att användaren kan välja datum från kalendervalssystem
- Testar att användaren kan välja tid från tidvalssystem
- Testar att användaren kan ange antal spelare (minst 1)
- Testar att användaren kan reservera ett eller flera banor

**User Story 2: Välja skostorlek för varje spelare**
- Testar att användaren kan lägga till skostorleksfält
- Testar att användaren kan ange skostorlek för varje spelare
- Testar att användaren kan ändra skostorlek
- Testar att det är möjligt att välja skostorlek för alla spelare

**User Story 3: Ta bort skostorleksfält**
- Testar att användaren kan ta bort skostorleksfält via "-"-knapp
- Testar att bokningen uppdateras när skostorlek tas bort

**User Story 4: Slutföra bokning**
- Testar att användaren kan slutföra bokningen
- Testar att bokningsnummer genereras och visas
- Testar att totalsumman beräknas korrekt (120 kr/person + 100 kr/bana)
- Testar att totalsumman visas tydligt

**Valideringstester:**
- Testar felmeddelanden för tomma fält
- Testar felmeddelanden för felaktigt antal skor
- Testar felmeddelanden för ofyllda skor
- Testar felmeddelanden för för många spelare per bana

#### Confirmation.test.jsx (7 tester)
Täcker User Stories 4-5:

**User Story 4: Visa bokningsnummer och totalsumma**
- Testar att bokningsnummer visas
- Testar att totalsumman beräknas och visas korrekt
- Testar att totalsumman visas tydligt med uppdelning

**User Story 5: Navigera mellan vyer**
- Testar navigation från bokningsvyn till bekräftelsevyn
- Testar att "Ingen bokning gjord" visas när ingen bokning finns
- Testar att bokning visas från sessionStorage
- Testar att korrekt data visas i bekräftelsen

#### Navigation.test.jsx (2 tester)
Täcker User Story 5:
- Testar navigation till bokningsvyn
- Testar navigation till bekräftelsevyn

### 5. Testkommentarer

Varje test har en kommentar som förklarar vilka acceptanskriterier som testas. Exempel:

```javascript
it('ska kunna välja ett datum från kalendervalssystem', async () => {
  // Acceptanskriterier: Användaren ska kunna välja ett datum och en tid från ett kalender- och tidvalssystem
  // ... testkod ...
});
```

### 6. GitHub Actions

**Implementation:**
- Skapade `.github/workflows/test.yml`
- Workflow triggas automatiskt vid push till `main` eller `master` branch
- Workflow:
  1. Checkout code
  2. Setup Node.js 18.x
  3. Install dependencies med `npm ci`
  4. Kör tester med `npm run test:run`

**Resultat:**
- När koden pushas till GitHub körs testerna automatiskt
- En grön bock visas i GitHub-repot när alla tester passerar
- Detta uppfyller kravet att "testerna triggas via en Github actions på main-branchen"

## Tekniska utmaningar och lösningar

### Problem 1: Label-koppling
**Problem:** Input-komponenten använder inte `htmlFor` för att koppla labels till inputs, vilket gjorde att `getByLabelText` inte fungerade.

**Lösning:** Använde `data-testid` attribut istället, vilket är en acceptabel lösning enligt examinationen (där det står att man får lägga till data-testid med kommentarer).

### Problem 2: Dynamiska ID:n för skostorleksfält
**Problem:** Skostorleksfälten använder nanoid för att generera unika ID:n, vilket gör det svårt att hitta specifika fält.

**Lösning:** Använde `getAllByRole('textbox')` och filtrerade på `maxlength="2"` för att hitta skostorleksfälten.

### Problem 3: MSW setup
**Problem:** MSW behövde konfigureras korrekt för att fungera med Vitest.

**Lösning:** Skapade en setup-fil som startar MSW-servern innan tester körs och stänger den efteråt.

## Testresultat

**Slutgiltigt resultat:**
- ✅ 28 tester passerar
- ✅ 3 testfiler
- ✅ Alla user stories täckta
- ✅ Alla acceptanskriterier testade
- ✅ MSW mockar POST-anrop korrekt
- ✅ GitHub Actions workflow fungerar

## Sammanfattning

Jag har framgångsrikt implementerat enhetstester för Strajk Bowling-applikationen enligt alla krav i examinationen:

1. ✅ Tester skrivna i React Testing Library
2. ✅ Alla user stories och acceptanskriterier täckta
3. ✅ POST-anrop mockade med Mock Service Worker
4. ✅ GitHub Actions triggar tester automatiskt vid push
5. ✅ Kommentarer i varje test som förklarar acceptanskriterier
6. ✅ Data-testid attribut lagda till med kommentarer som motiverar varför

Testerna är nu redo för redovisning och GitHub Actions kommer automatiskt att köra testerna varje gång kod pushas till main-branchen.
