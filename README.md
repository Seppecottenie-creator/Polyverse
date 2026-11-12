# Polyverse

Een 3D-wandeling door het Polygon-bureau, gebouwd met [three.js](https://threejs.org). Alles draait in de browser: er is geen build-stap en er hoeft niets geïnstalleerd te worden.

## Bediening

| Toets | Actie |
|---|---|
| W A S D of pijltjes | lopen |
| muis | rondkijken (klik eerst in het beeld) |
| Shift | sneller lopen |
| E | deur openen of sluiten, gaan zitten of opstaan |
| V | camera achter je personage of door je ogen |
| Enter of T | chatten |
| X | zwaaien |
| M | kaart groter of kleiner |
| P | spelmenu |
| K | kaarten (als je aan de eettafel zit) |
| G / F | beeldkwaliteit wisselen / beelden per seconde tonen |

## Mappen

```
index.html                    het volledige spel (gebouw, personages, besturing, multiplayer)
models/personage1.gltf.json   eerste personage (Meshy → Mixamo), met animaties idle, look, walk, wave, wave2
models/zit.anim.json          Mixamo-animaties Stand To Sit en Sit To Stand, gedeeld door alle personages
models/zetel.gltf.json        grijze hoekzetel (uit 3ds Max)
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

## Spellen

- **Arcadekast** (E bij de kast in de lounge): Polygon Defender, Kabelkluwen en Gevelbreker.
- **Whiteboard** (E in het sas): tekenen met de muis; iedereen ziet de tekening en ze blijft bewaard.
- **Kaarten** (K als je aan de eettafel zit, of via het menu): blackjack en patience.
- **Spelmenu**: E bij de tv op het middenblok, of P waar je ook bent. Daar staan ook de ranglijsten.

### Ranglijsten en whiteboard bewaren op GitHub Pages

Op claude.ai werkt dit vanzelf. Voor de GitHub-versie voer je één keer deze SQL uit in Supabase (SQL Editor → New query → Run). Zonder deze tabellen werkt alles ook, maar dan blijven scores op je eigen toestel en wordt het whiteboard alleen live gedeeld met wie op dat moment online is.

```sql
create table if not exists polyverse_scores (
  id bigint generated always as identity primary key,
  game text not null, name text not null, score integer not null,
  created_at timestamptz default now()
);
alter table polyverse_scores enable row level security;
create policy "scores lezen" on polyverse_scores for select using (true);
create policy "scores toevoegen" on polyverse_scores for insert
  with check (score >= 0 and score < 10000000 and char_length(name) <= 30 and char_length(game) <= 30);

create table if not exists polyverse_kv (
  key text primary key, value jsonb, updated_at timestamptz default now()
);
alter table polyverse_kv enable row level security;
create policy "kv lezen" on polyverse_kv for select using (true);
create policy "kv toevoegen" on polyverse_kv for insert with check (key = 'whiteboard');
create policy "kv bijwerken" on polyverse_kv for update using (key = 'whiteboard') with check (key = 'whiteboard');
```
