# Lead sheet Aziza

Éditeur de *lead sheet* (grille d'accords + paroles + rythme pour guitaristes) à fichier unique, hors-ligne, pensé pour le mobile. Livré préchargé avec la chanson **Aziza**, mais le moteur accepte n'importe quel flux.

**En ligne** : ouvrir `decoupe_mesures_aziza.html` dans un navigateur — aucune installation. Si hébergé sur GitHub Pages : `https://nmulongo-sys.github.io/leadsheetproject/`.
**Statut** : révision 2026-07-03 (*theme-ready* portail ; v2 — refonte UI) • fichier HTML unique, sans dépendance externe, fonctionne hors ligne et sur Android.

## Utilisation

Tout se pilote depuis une seule page, et **tout est sauvegardé dans le navigateur** (rien n'est envoyé nulle part). Au premier lancement, un **guide pas à pas** (wizard, 6 écrans) présente l'app ; il se rouvre via le bouton **🎓 Guide**. Chaque fonction porte une **bulle d'aide « ? »** (tap/clic) qui l'explique en contexte. Un bouton **↶ Annuler** (Ctrl+Z) défait la dernière action structurelle (barre, accord, rythme, suppression).

1. **Découpe (étage 1).** Le champ de texte du haut est la source de vérité. Une ligne `[Nom de section]` ouvre une section ; dans une ligne, les mesures sont séparées par `|`. La grille se régénère à chaque frappe.
2. **Accords (étage 2).** Cliquer sur une mesure pour lui poser un accord. Trois affichages au choix (réel / formes capo / les deux), réglage du capo (V par défaut), et altérations ♭/♯.
3. **Rythme (étage 3).** Sous chaque mesure, une voie rythmique (strumming) affiche **4 noires** par défaut. Cliquer dessus ouvre le sélecteur : on empile des figures piochées dans toute la bibliothèque FWB, avec un indicateur d'appui souple `X/4`. Bascule globale du sens du premier coup (↓ bas / ↑ haut) ; possibilité de masquer la voie rythmique.
4. **Impression / PDF.** Le bouton **« Imprimer / PDF »** ouvre un aperçu propre (masquant l'interface d'édition) avec options, puis lance l'impression du navigateur (« Enregistrer en PDF » possible).
5. **Export.** « Copier (JSON) » et « Copier (texte) » recopient la grille complète (accords + rythme) dans le presse-papiers. « Repartir d'Aziza » restaure le flux d'origine.

## Architecture & conventions

Cœur de la reprise : une session ultérieure doit pouvoir continuer sans que le système de notation soit réexpliqué.

**Forme générale.** Un seul fichier HTML/CSS/JS, tout le script dans une IIFE. Thème « papier/crayon » via variables CSS (`--paper`, `--ink`, `--ink-soft`, `--pencil`, `--charcoal`) et polices `--serif` / `--display`. Aucune bibliothèque externe.

**Modèle « en étages ».** Les trois étages (découpe → accords → rythme) sont superposés *dans chaque cellule* de mesure, pas dans des vues séparées.

**Le flux est du texte** (source unique). Lignes `[Section]` ; mesures séparées par `|`. Un segment de mesure peut porter, **en tête**, un ou plusieurs tokens `{…}` :
- `{accord}` — l'accord, stocké en **réel**. Collé à la mesure, il **survit aux coupes et fusions**.
- `{r: fig fig …}` — la voie rythmique (strumming). Les figures sont des **clés sans espace** séparées par des espaces (`{r: blanche blanche}`). Écrit **seulement** si le rythme diffère du défaut.
- **Défaut implicite** : aucune balise rythme = **4 noires**.
- À la fusion de deux mesures, **tous** les `{…}` en tête de la mesure absorbée sont retirés (on conserve accord + rythme de la mesure de gauche).

**Accords.** Réel = tel quel ; **forme capo** = transposition de `−capo` (capo V ⇒ formes Am pour Aziza) ; altérations ♭/♯ (choix d'enharmonie).

**Bibliothèque de figures (FWB), embarquée.** Cinq familles :
- *Notes* : ronde, blanche, noire, croche, double, triple.
- *Silences* : pause, demi-pause, soupir, demi-soupir, quart de soupir.
- *Groupements binaires* : deux croches, quatre doubles, saute, saute inversée, syncopette, contretemps, chassé, syncope.
- *Ternaires — réf. solfège* et *Divisions artificielles — réf. solfège* : **sélectionnables mais rendues « — »** (pas de sens de coup défini), conformément à la doctrine de la bibliothèque.

**Notation flèches** (voie rythmique) : `↓` coup bas (premier coup), `↑` coup haut, `·` unité tenue, `–` silence, `—` référence solfège. La bascule « 1ᵉʳ coup ↓/↑ » n'affecte que les figures binaires. L'indicateur `appui X/4` est la somme des durées de la mesure (guide, non bloquant).

**Persistance.** `localStorage`, **isolée par navigateur — aucune synchro centrale**. Deux clés :
- `aziza_decoupe_v3` — le flux (donc aussi accords **et** rythme, puisqu'ils voyagent dans le texte ; pas de clé rythme séparée).
- `aziza_set_v1` — les réglages : mode d'affichage (réel/capo/les deux), capo, altérations, orientation du 1ᵉʳ coup, affichage de la voie rythmique.
- `aziza_wiz_v1` — drapeau « guide déjà vu » (le wizard ne se rouvre pas automatiquement).

**Module impression.** Bouton → panneau d'aperçu (`#printPanel`) qui rend une feuille propre (`#sheet`). Options : en-tête, formes capo, accords réel, paroles, flèches rythme, n° de mesure, saut de page par section, mesures par ligne. Le bloc `@media print` masque toute l'interface et n'imprime que la feuille.

**Export.** JSON par mesure (`accord`, `forme_capo`, `rythme` si ≠ défaut, `rythme_fleches`) et export texte.

**Chanson embarquée par défaut.** *Aziza* — ré mineur, ♩ = 140, 4/4, capo V (formes Am), **56 mesures réparties en 10 sections** (Intro, Couplet 1, Pré-refrain, Refrain, Couplet 2, Pré-refrain (2), Refrain (2), Explication (pont), Refrain final, Conclusion).

**Contrat de thème (portail « Formation Musicale »).** L'app est *theme-ready* sans être forkée. Toutes les variables de couleur du thème papier (`--paper`, `--paper-2`, `--panel`, `--card`, `--card-hi`, `--ink`, `--ink-soft`, `--ink-faint`, `--pencil`, `--pencil-soft`, `--charcoal`, `--line`, `--line-soft`) sont recâblées en **repli** : `var(--fm-x, <valeur d'origine>)`. Sans thème, le rendu papier d'origine est **strictement préservé** (le repli s'applique) ; avec un thème, les jetons partagés `--fm-*` prennent le dessus. Un **chargeur** synchrone dans le `<head>` (après le `<style>`) résout le thème dans l'ordre `?theme=clair|sombre` → `localStorage['fm-theme']` (partagé avec le portail, même origine) → défaut, pose `data-fm-theme` sur `<html>`, et accepte un thème externe via `?themeUrl=/portal-theme-tokens.css` ; repli try/catch silencieux. Le champ d'accord (jadis fond `#fff` codé en dur) passe sur `--card-hi` (sinon texte illisible en thème sombre). **Volontairement laissés intacts** : les polices serif (`--serif`/`--display`, identité de l'app) et la « feuille » imprimable blanc/noir (fac-similé papier, correct dans tous les thèmes).

## Journal de développement

### 2026-07-03 — theme-ready : adoption du thème du portail (clair/sombre)
- **Contrat de thème `--fm-*` par repli.** Toutes les couleurs du thème papier sont recâblées en `var(--fm-x, <valeur d'origine>)` : sans thème, rendu papier identique (fallback) ; avec thème (clair/sombre du portail), les jetons `--fm-*` s'appliquent.
- **Chargeur de thème** synchrone ajouté dans le `<head>` : `?theme=clair|sombre` → `localStorage['fm-theme']` → défaut ; pose `data-fm-theme` sur `<html>` (pas de flash) ; thème externe optionnel via `?themeUrl=`. Repli try/catch silencieux.
- **Correctif contraste** : le champ d'accord (fond `#fff` codé en dur) passe sur `--card-hi`, sinon son texte devenait illisible en thème sombre.
- **Choix assumés** : polices serif (`--serif`/`--display`) **inchangées** (identité de l'app) ; « feuille » imprimable blanc/noir conservée (fac-similé papier).
- **Inchangé (compatibilité totale)** : moteur de parsing, tokens `{…}`/`{r:…}`, bibliothèque FWB, module impression, exports JSON/texte, clés `aziza_decoupe_v3` / `aziza_set_v1` / `aziza_wiz_v1`.
- Validé au rendu headless dans les trois états (défaut, `?theme=clair`, `?theme=sombre`), modale du wizard comprise.

### 2026-07-02 — v2 : refonte de l'interface (design, ergonomie, aide intégrée)
- **Design.** Thème « papier raffiné » : même identité papier/crayon mais professionnalisée — jetons de design (rayons, ombres `--sh-1/2/3`), contrôles segmentés en pilules avec état actif en relief, boutons avec hiérarchie claire (primaire dégradé, fantômes), cartes de mesure avec ombre douce, en-tête avec pictogramme 𝄞, favicon.
- **Structure.** La page est organisée en **4 étapes numérotées** (① Texte & découpe → ② Réglages → ③ Grille → ④ Imprimer & partager) pour guider le non-initié ; la légende devient un panneau repliable `<details>` ; état vide accueillant si le flux est effacé.
- **Aide généralisée.** Bulles **« ? »** sur chaque fonction (16 sujets : flux, barres, compteurs, affichage, capo, altérations, 1ᵉʳ coup, voie rythmique, grille, éditeur d'accord, éditeur de rythme, appui X/4, bibliothèque, actions, impression) — popover tactile, fermé par tap ailleurs ou Échap. `title=` natifs en complément sur les contrôles.
- **Wizard.** Guide de démarrage en 6 écrans (bienvenue → découpe → accords → rythme → impression → sauvegarde), affiché au premier lancement (clé `aziza_wiz_v1`), rouvrable via **🎓 Guide**. Aide contextuelle au niveau mesure : en-têtes explicites « Accord · M{n} » et « Rythme · M{n} » dans les éditeurs, avec leurs propres bulles.
- **Ergonomie.** **Annuler** (↶ / Ctrl+Z) pour toutes les actions structurelles (pile de 60 états) ; **toasts** de confirmation (copie, suppression, restauration) au lieu du changement de libellé des boutons ; placeholder d'exemple dans l'éditeur d'accord (`ex. Dm — ou Dm:1 A:3`) ; bouton « ✓ Valider » explicite ; indicateur ✎ sur la voie rythmique ; libellés d'options d'impression documentés.
- **Inchangé (compatibilité totale).** Moteur de parsing, tokens `{…}` / `{r:…}`, bibliothèque FWB, module impression, exports JSON/texte, clés `aziza_decoupe_v3` et `aziza_set_v1` : les données existantes des utilisateurs sont conservées telles quelles.

### 2026-07-02 — révision initiale (étages 1–3 + module impression)
- Première documentation de l'app.
- **État couvert :** étage 1 (découpe en sections/mesures via le flux texte) ; étage 2 (grille d'accords, affichage réel/capo/les deux, transposition, ♭/♯) ; étage 3 (voies rythmiques — token `{r:…}` embarqué dans le flux, défaut 4 noires implicite, bibliothèque FWB complète, notation flèches, bascule du 1ᵉʳ coup, indicateur d'appui) ; module **impression / PDF** (aperçu propre, options d'affichage, `@media print`).
- **Décisions de conception actées :** le rythme est écrit **dans le flux** et survit aux coupes comme les accords ; défaut « 4 noires » implicite (aucun token) ; ternaires et divisions artificielles accessibles mais rendues « — » ; persistance locale, non synchronisée, une clé pour le flux (`aziza_decoupe_v3`) et une pour les réglages (`aziza_set_v1`).

## Contributions

Outil personnel. Les signalements de bug et suggestions (*issues*) sont bienvenus ; les propositions de code (*pull requests*) sont relues et intégrées au cas par cas.

## Licence

Aucun fichier `LICENSE` connu à ce jour : par défaut, **tous droits réservés**. Pour un partage libre en gardant la paternité, une licence **MIT** est recommandée (*à confirmer*).
