# Café Dib v2 — Tlemcen (refonte + tableau de bord)

Refonte du site une page de **Café Dib** (café · pizzeria · crèmerie, Bd Pasteur, Tlemcen) — noir & or modernisé, animations, réservation de table / message, et espace patron.
Version précédente conservée intacte : `/workspace/terrasse/cafedib/site` (+ `cafedib_site.zip`).

- Site : https://sayler991.github.io/Cafe_Dib_v2/
- Espace patron : https://sayler991.github.io/Cafe_Dib_v2/admin/

## Faits réels utilisés (fiche Google Maps relevée le 2026-10-10, voir `terrasse/cafedib/maps_cafedib.md`)
Café Dib · Café / Crèmerie / Pizzeria · Bd Pasteur, Tlemcen 13000 · 0559 82 67 81 (+213 559 82 67 81) ·
samedi→jeudi 06:00–00:00, vendredi 12:00–00:00 · 4,3/5 sur 36 avis Google · description du propriétaire (citée mot pour mot) ·
accessibilité fauteuil roulant (entrée, parking, places assises, toilettes) · 3 avis Google cités tels quels avec leur auteur.

## Éléments provisoires / choix de la maquette (à confirmer avec le client)
- **Carte complète (17/10/2026)** : 88 articles en 14 rubriques, transcrits des 5 photos de la carte papier (`menu/menu.json`, `menu/menu.md`, points incertains notés).
  Stockée dans `cafedib.site_content` (clé `menu`) + `content.json` ; modifiable dans l'admin « Carte & prix » (articles et rubriques).
- Réservation : créneaux de 30 min de l'ouverture à 23h00, 1 à 20 personnes, jusqu'à 60 jours — paramètres de la maquette.
- Numéro : la description Google cite aussi « 0559.926.781 » ; le site utilise 0559 82 67 81 (numéro de la fiche) — à confirmer.
- Champ « Occasion » et textes de liaison (titres de sections) : rédaction de la maquette, sans promesse.

## Photos
- **Photos du café** (reprises du site v1, issues de la fiche Google de Café Dib) : salle, comptoir, pizzas, gâteau, jus de fruits, jus rouges ; plan © OpenStreetMap.
- **Photos d'illustration — licence Unsplash** (usage libre, commercial compris), légendées « Illustration » sur le site, converties en WebP + AVIF 800/1600 px :
  - Tasses de café (espresso / cappuccino) : Olena Bohovyk — https://unsplash.com/photos/i_Mfo3UUtTo
  - Machine à espresso : https://unsplash.com/photos/3b1mi9obsgA
  - Café crème, fond sombre : https://unsplash.com/photos/h8okJrDFE1Y
  - Grains de café torréfiés : https://unsplash.com/photos/wV8Rju13Vpc
  - Croissants et viennoiseries : Phil Hearing — https://unsplash.com/photos/iKExZ008Zh0
  (auteurs non affichés pour 3 photos : la page Unsplash, protégée anti-robot, n'a pas pu être lue — attribution facultative sous la licence Unsplash ; liens fournis.)
- Polices : Fraunces + Outfit (SIL OFL), auto-hébergées.

## Structure
- `site/` : site publié (index.html, config.js, content.json de secours, assets/, admin/). Scripts classiques : fonctionne aussi en `file://` (contenu statique).
- `site/assets/js/live.js` : contenu Supabase (`cafedib.site_content`) → `content.json` → HTML ; formulaire réservation / message ; animations
  (diaporama Ken Burns + parallaxe, titre lettre par lettre, apparitions au défilement en cascade, traits dorés dessinés, bandeau défilant, onglets animés, barre de progression, `prefers-reduced-motion` respecté).
- `site/admin/` : tableau de bord — Réservations, Messages (statuts nouveau / confirmé / honoré / annulé), Clients (notes), Stats, Contenu (ouvert/fermé, réservations on/off), Carte & prix, Horaires, Photos, Historique + restauration.
  `alerts.js` : temps réel + relève 30 s, son, bannière rouge, pastilles, titre de l'onglet, notification du navigateur.
- `supabase/schema.sql`, `supabase/seed.sql` : schéma `cafedib` (RLS ; propriétaire via `public.owners`/`public.is_owner()` ; anti-abus par trigger : pot de miel, statut forcé, date ≤ 60 j, heure dans les horaires (Africa/Algiers), réservations fermables, 20 demandes/min, 3 par téléphone/10 min ; historique 50 versions ; publication realtime).
- `tools/e2e/` : `e2e.js` (site en ligne 1440 + 390 px, envoi réel puis suppression), `admin_harness.html` + `admin_test.js` + `fixture.json` (interface patron avec faux client Supabase, données fictives).
- `netlify.toml` : publish = `site`, /admin → /admin/.

## Supabase
Projet `rvreusugrtiubqtafgbk`. Appliquer `schema.sql` puis `seed.sql`, puis schémas exposés : `public, graphql_public, ibis, dupre, cafedib` (fait).
Propriétaire : benduy0@gmail.com déjà présent dans `public.owners` (partagé). Photos de l'admin : bucket public `images`, préfixe `up-cafedib_`.

## Tests
```
node tools/e2e/e2e.js https://sayler991.github.io/Cafe_Dib_v2/ /workspace/cafe_dib_v2_shots/
python3 -m http.server 8894 --bind 127.0.0.1 (racine du dépôt) && node tools/e2e/admin_test.js /workspace/cafe_dib_v2_shots/ 8894
```
Les demandes de test s'appellent « TEST E2E … » et sont supprimées après coup (requests + customers).

## Allègement (17/10/2026)
Une seule photo en haut de page (srcset), plus de diaporama / bandeau défilant / parallaxe / barre de progression / badge tournant ;
photos d'illustration Unsplash retirées ; supabase-js remplacé par des appels REST directs (`fetch`) sur le site public (l'admin garde supabase-js).
Retour arrière : tags `pre-menu` (gh-pages) et `pre-menu-main` (main).
