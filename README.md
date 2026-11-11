# Polyverse

Een 3D-wandeling door het Polygon-bureau, gebouwd met [three.js](https://threejs.org). Alles draait in de browser: er is geen build-stap en er hoeft niets geïnstalleerd te worden.

## Bediening

| Toets | Actie |
|---|---|
| W A S D of pijltjes | lopen |
| muis | rondkijken (klik eerst in het beeld) |
| Shift | sneller lopen |
| E | deur openen of sluiten |
| V | camera achter je personage of door je ogen |
| Enter of T | chatten |
| X | zwaaien |
| M | kaart groter of kleiner |
| G / F | beeldkwaliteit wisselen / beelden per seconde tonen |

## Mappen

```
index.html                    het volledige spel (gebouw, personages, besturing, multiplayer)
models/personage1.gltf.json   eerste personage (Meshy → Mixamo), met animaties idle, look, walk, wave, wave2
```

Een nieuw personage voeg je zo toe:

1. Zet het bestand als `models/<naam>.gltf.json` in de map `models`.
2. Voeg bovenaan de lijst `AVATARS` in `index.html` een regel toe, met `glb: 'models/<naam>.gltf.json'` en de lengte `h`.

## Op GitHub Pages zetten

1. Maak een nieuwe repository aan en upload de inhoud van deze map, met `index.html` in de hoofdmap.
2. Ga naar **Settings → Pages**, kies **Deploy from a branch** en daarna `main` met `/ (root)`.
3. Na een minuut staat het spel op `https://seppecottenie-creator.github.io/Polyverse/`.

Lokaal testen kan met `python3 -m http.server` in deze map. Ga dan naar http://localhost:8000. Gewoon dubbelklikken op `index.html` werkt niet, omdat de browser de modellen dan niet mag laden.

## Multiplayer met Supabase

Op claude.ai gebruikt het spel de ingebouwde functies van de pagina. Op GitHub Pages gebruikt het **Supabase Realtime**. Daarvoor zijn geen tabellen of database nodig: het spel gebruikt alleen *Presence* (wie er is) en *Broadcast* (posities, chat, zwaaien en deuren).

1. Maak een gratis project aan op [supabase.com](https://supabase.com).
2. Ga naar **Project Settings → API** en kopieer de **Project URL** en de **anon / publishable key**.
3. Zoek in `index.html` deze twee regels en vul ze in:
   ```js
   const SUPABASE_URL = 'https://xxxx.supabase.co';
   const SUPABASE_ANON_KEY = 'eyJ...';
   ```
4. Zet de gewijzigde `index.html` op GitHub.

De anon-sleutel mag publiek in een website staan; daar is hij voor bedoeld. Gebruik **nooit** de `service_role`-sleutel.

Iedereen die de link opent, kiest zijn eigen collega-personage. Die naam staat boven zijn hoofd en in de lijst rechtsboven. Er is geen login: iedereen met de link kan binnen. Wil je dat afschermen met echte accounts, dan kan dat later met Supabase Auth.

Zonder ingevulde Supabase-gegevens start het spel als solo-wandeling.
