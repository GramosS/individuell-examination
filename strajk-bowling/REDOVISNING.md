# Guide för redovisning av examination

## Steg 1: Installera dependencies

Först måste du installera alla dependencies (inklusive testdependencies):

```bash
cd individuell-examination/strajk-bowling
npm install
```

## Steg 2: Testa lokalt

Kör testerna lokalt för att verifiera att allt fungerar:

```bash
# Kör testerna i watch-läge (för utveckling)
npm run test

# Eller kör testerna en gång (som i CI)
npm run test:run
```

Alla tester ska gå igenom (gröna). Om något test misslyckas, kontrollera felmeddelandena.

## Steg 3: Pusha till GitHub

1. **Kontrollera att du är i rätt branch:**
   ```bash
   git branch
   # Om du inte är på main/master, byt branch:
   git checkout main
   # eller
   git checkout master
   ```

2. **Lägg till alla ändringar:**
   ```bash
   git add .
   ```

3. **Commit:**
   ```bash
   git commit -m "Lägger till enhetstester för alla user stories med MSW och GitHub Actions"
   ```

4. **Pusha till GitHub:**
   ```bash
   git push origin main
   # eller
   git push origin master
   ```

## Steg 4: Verifiera GitHub Actions

1. Gå till ditt GitHub-repository i webbläsaren
2. Klicka på fliken **"Actions"** (högst upp i repot)
3. Du ska se en workflow som körs med namnet **"Run Tests"**
4. Vänta tills workflow:en är klar (kan ta 1-2 minuter)
5. Du ska se en **grön bock** ✅ om alla tester gick igenom
6. Om det är en röd X ❌, klicka på workflow:en för att se felmeddelanden

## Steg 5: Redovisning

I din redovisning ska du inkludera:

### 1. Screenshot av GitHub Actions
- Ta en screenshot av GitHub Actions-fliken där du ser den gröna bocken ✅
- Detta visar att testerna triggas automatiskt vid push

### 2. Testresultat
- Kör `npm run test:run` lokalt och ta en screenshot av resultatet
- Eller visa testresultatet från GitHub Actions

### 3. Förklaring av teststruktur
Förklara kort:
- Var testfilerna finns (`src/views/Booking.test.jsx`, `src/views/Confirmation.test.jsx`, etc.)
- Hur MSW används för att mocka API-anrop (`src/test/mocks/`)
- Vilka user stories som testas i vilka testfiler

### 4. Exempel på testkommentarer
Visa ett exempel på hur du har kommenterat testerna med acceptanskriterier, t.ex.:

```javascript
it('ska kunna välja ett datum från kalendervalssystem', async () => {
  // Acceptanskriterier: Användaren ska kunna välja ett datum och en tid från ett kalender- och tidvalssystem
  // ... testkod ...
});
```

### 5. Data-testid attribut
Förklara varför du lade till `data-testid` attribut (med hänvisning till kommentarerna i koden):
- I `Booking.jsx` - för att testa "slutför bokning"-knappen
- I `Shoes.jsx` - för att testa "lägg till sko" och "ta bort sko"-knapparna
- I `Navigation.jsx` - för att testa navigation mellan vyer
- I `Confirmation.jsx` - för att testa bokningsnummer, totalsumma och "ingen bokning"-meddelande

## Checklista innan inlämning

- [ ] Alla dependencies är installerade (`npm install`)
- [ ] Alla tester går igenom lokalt (`npm run test:run`)
- [ ] Koden är pushat till GitHub
- [ ] GitHub Actions visar grön bock ✅
- [ ] Alla testfiler har kommentarer som förklarar acceptanskriterier
- [ ] Alla `data-testid` attribut har kommentarer som förklarar varför de behövs

## Ytterligare tips

- Om GitHub Actions misslyckas, klicka på workflow:en och läs felmeddelandena
- Vanliga problem:
  - Fel branch (måste vara `main` eller `master`)
  - Saknade dependencies (kontrollera `package.json`)
  - Syntaxfel i testfiler (kör `npm run test:run` lokalt först)

## Teststruktur

```
strajk-bowling/
├── src/
│   ├── views/
│   │   ├── Booking.test.jsx      # User Stories 1-4
│   │   └── Confirmation.test.jsx  # User Stories 4-5
│   ├── components/
│   │   └── Navigation/
│   │       └── Navigation.test.jsx # User Story 5
│   └── test/
│       ├── setup.js               # Test setup med MSW
│       └── mocks/
│           ├── server.js          # MSW server setup
│           └── handlers.js        # Mock API handlers
└── .github/
    └── workflows/
        └── test.yml                # GitHub Actions workflow
```

## Kontakt

Om du stöter på problem, kontrollera:
1. Att alla dependencies är installerade
2. Att testfilerna har korrekt syntax
3. Att GitHub Actions workflow-filen finns på rätt plats (`.github/workflows/test.yml`)
