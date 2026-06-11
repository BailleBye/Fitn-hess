# FitLog — PWA de musculation (Safari iOS)

App 100 % locale : aucune donnée ne quitte ton iPhone. Pas de compte, pas de backend.

## Fichiers
- `index.html` — toute l'application (HTML + CSS + JS)
- `manifest.json` — manifest PWA
- `sw.js` — service worker (fonctionnement hors-ligne)
- `icon.png` — icône d'écran d'accueil
- `sounds/` — clips audio (Ronnie Coleman), à uploader avec le reste

## Installation sur iPhone (5 minutes)

Une PWA doit être servie en **HTTPS**. Le plus simple : GitHub Pages (gratuit).

1. Crée un dépôt GitHub (public ou privé avec Pages activé), par ex. `fitlog`.
2. Upload les 4 fichiers à la racine.
3. Settings → Pages → Source : branche `main`, dossier `/ (root)` → Save.
4. Ouvre `https://<ton-user>.github.io/fitlog/` dans **Safari** sur l'iPhone.
5. Bouton **Partager** → **Sur l'écran d'accueil** → Ajouter.

L'app s'ouvre alors en plein écran, fonctionne hors-ligne, et iOS **ne purge pas**
le stockage des web apps installées sur l'écran d'accueil (contrairement aux
onglets Safari, purgés après 7 jours d'inactivité).

Alternative locale pour tester sur Mac : `python3 -m http.server` dans le dossier,
puis `http://localhost:8000` (l'installation PWA nécessite quand même HTTPS).

## Sauvegarde

Réglages (engrenage sur l'Accueil) → **Exporter (backup JSON)**.
Fais-le régulièrement : si tu supprimes l'app de l'écran d'accueil, les données partent avec.
L'import fusionne (pas d'écrasement, déduplication par id).

## Logique métier (rappels)

- **Tonnage** : normal = kg×reps · poids de corps = (BW+lest)×reps · assisté = (BW−assist)×reps
- Le poids de corps est **figé dans chaque workout** ; le modifier ne change pas l'historique.
- Unilatéral : interrupteur sur la carte exo, dédouble chaque série en G/D.
- Historique en **lecture seule** (choix assumé). Garde-fou : si aucune série n'est
  cochée ✓ en fin de séance, l'app demande confirmation avant d'enregistrer.
- % hebdo = muscles colorés (clair + foncé) / 18 zones, reset le lundi.
- Cocher ✓ une série lance le timer de repos (défaut 90 s, réglable).

## Modifier la liste d'exercices

Dans `index.html`, tableau `EX` : `t` = `n` (normal) / `bw` (poids de corps) / `as`
(assisté), `uni:1` = unilatéral par défaut, `P`/`S` = muscles principaux/secondaires
(clés dans `ZONES`).

## Le cri de Goggins / Ronnie Coleman 🔊

À chaque exercice ajouté pendant une séance, l'app tire au hasard dans un pool
commun : les 3 clips Ronnie Coleman « Light weight baby! » (dossier `sounds/`,
volume normalisé) **et** les phrases style Goggins en synthèse vocale (voix
anglaise grave, tableau `GOGGINS`). Pour ne garder que les clips : vide le
tableau `GOGGINS`. Pour ne garder que la synthèse : vide `GOGGINS_AUDIO`.
Ajoute d'autres clips en les déposant dans `sounds/` et en les listant dans
`GOGGINS_AUDIO`.

Pour sa **vraie voix** : je ne peux pas distribuer ses enregistrements (protégés),
mais pour ton usage perso tu peux déposer tes propres clips audio à côté
d'`index.html` (ex. dossier `goggins/`) et les lister dans le tableau
`GOGGINS_AUDIO` : `const GOGGINS_AUDIO=['goggins/stayhard.m4a','goggins/boats.mp3'];`
S'il n'est pas vide, l'app joue un clip au hasard au lieu de la synthèse vocale.

**Formats supportés par Safari iOS** : m4a/AAC (mémos vocaux iPhone, la plupart
des rips), mp3, wav, aiff. **Non supportés** : ogg, opus — convertis-les en m4a
ou mp3 (sur Mac : `afconvert input.ogg output.m4a -f m4af -d aac`, sinon
n'importe quel convertisseur en ligne).

Note iOS : le son marche car l'ajout d'exercice est un tap (geste utilisateur
requis par Safari pour l'audio). Vérifie que l'iPhone n'est pas en mode silencieux.

## Modifier le diagramme corporel

La silhouette est générée depuis `BODY_R` (liste de points du demi-contour droit,
mirroir automatique, lissage en boucle fermée). Les zones musculaires sont dans
`HALF_F`/`CENT_F` (face) et `HALF_B`/`CENT_B` (dos) — des paths SVG en
coordonnées locales (centre x=110). Déplace les points pour ajuster les formes.
