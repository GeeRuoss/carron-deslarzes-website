# Carron+Deslarzes — Notes de développement

## Projet
Site vitrine statique pour **Carron+Deslarzes**, entreprise de charpente & rénovation en Valais.
- Hébergé sur **GitHub Pages** : repo `GeeRuoss/carron-deslarzes-website`
- Domaine custom : **carron-deslarzes.ch**
- Pas de framework, pas de build — HTML/CSS/JS pur
- Font : NewBlack (Regular + UltraLight, woff2/woff)
- Preview local : `npx serve` (configuré dans `.claude/launch.json`)

## Structure des fichiers
```
index.html          — Page d'accueil (hero + présentation)
entreprise.html     — L'entreprise
savoir-faire.html   — Savoir-faire (liste de compétences)
realisations.html   — Galerie de réalisations (cards groupées + lightbox)
contact.html        — Formulaire de contact (mailto JS)
immobilier.html     — Section immobilier (VENDU/COMPLET/NOUVEAU)
grange-prarreyer.html — Plans architecturaux grange Prarreyer
styles.css          — Feuille de styles commune
favicon.svg         — Favicon avec dark/light mode
fonts/              — Polices NewBlack
photos/             — Images du site
logo/               — Fichiers SVG du logo
```

## Couleurs & branding
- Vert accent : `--accent: #30aa3b` (anciennement #3a7d44)
- Doré logo : `#cb7a11`
- Le vert est utilisé à la fois via `var(--accent)` et en inline dans certains SVG
- Favicon : adapte ses barres blanches selon `prefers-color-scheme` (light → #0a0a0a, dark → #ffffff)

## Contact
- Email : francois@cardes.ch
- Tél : +41 79 755 09 44
- Formulaire = handler JS `mailto:` (pas de backend)

## Système de galerie groupée (realisations.html)
- Classe `.gallery-card-group` : empile plusieurs images avec `position: absolute; opacity: 0/1`
- Navigation par dots + compteur badge
- Lightbox séparé pour groupes : `openGroupLightbox()` / `navigateLightbox()`
- Variable `lbMode` distingue 'normal' vs 'group'
- Chaque card groupe a un `data-titles` avec les titres séparés par virgule

## Workflow utilisateur
- Guigui communique en français, instructions courtes et directes
- Souvent envoie des screenshots pour montrer ce qu'il veut modifier
- Workflow type : screenshot → instruction rapide → implémentation immédiate → push
- Préfère les changements appliqués directement sans trop de questions

## Déploiement
```bash
git add <fichiers modifiés>
git commit -m "description"
git push origin main
```
GitHub Pages déploie automatiquement depuis `main`.

## URLs propres
- Les liens internes n'ont pas de `.html` (ex: `href="contact"` au lieu de `href="contact.html"`)
- GitHub Pages supporte nativement les URLs sans extension
- **ATTENTION** : les fichiers doivent garder leur extension `.html` sur le disque

## Problèmes rencontrés & solutions

### 1. Lightbox cassé après suppression des filtres
**Problème** : En supprimant le HTML des filtres de la galerie, le JS référençait encore `document.getElementById('count')` → `null.textContent` → erreur silencieuse qui empêchait tout le JS lightbox de fonctionner.
**Solution** : Supprimer aussi tout le bloc JS lié aux filtres, pas seulement le HTML.

### 2. Fichiers lock Git
**Problème** : `index.lock` et `HEAD.lock` dans `.git/` après un crash de processus git.
**Solution** : `rm -f .git/*.lock`

### 3. Token GitHub expiré
**Problème** : `git push` échoue avec erreur d'authentification.
**Solution** : `gh auth login` pour renouveler le token.

### 4. Push rejeté (remote ahead)
**Problème** : Le remote avait des commits plus récents.
**Solution** : `git pull --rebase` puis `git push`.

### 5. Conflit de merge sur favicon.svg
**Problème** : Le remote avait déjà une version du favicon.
**Solution** : `git checkout --theirs favicon.svg` puis commit de résolution.

### 6. Fichier renommé sans extension
**Problème** : `entreprise.html` renommé accidentellement en `entreprise` (sans .html) → lien cassé.
**Solution** : `mv entreprise entreprise.html`. Toujours vérifier que les fichiers gardent leur extension.

### 7. `git add -A` a inclus des fichiers indésirables
**Problème** : Commit accidentel de ~50 photos et fichiers .ai volumineux (58MB, 93MB).
**Solution** : Toujours utiliser `git add <fichiers spécifiques>` au lieu de `git add -A` ou `git add .`.

### 8. HTTPS / Certificat SSL
**Problème** : Le site n'était pas en HTTPS malgré le domaine custom.
**Solution** : DNS correctement configuré (A records + CNAME www → GitHub Pages). Il faut parfois re-sauvegarder la config du domaine dans les settings GitHub Pages pour déclencher la génération du certificat. Le provisioning peut prendre du temps.

## Bonnes pratiques retenues
- **Git add** : toujours nommer les fichiers explicitement, jamais `-A`
- **Supprimer du HTML** : vérifier que le JS associé est aussi nettoyé
- **Extensions fichiers** : ne jamais renommer un .html sans extension
- **Tester après chaque changement** : utiliser le preview pour vérifier avant de push
- **Couleurs** : vérifier à la fois les CSS variables ET les valeurs inline dans les SVG
- **CSS Grid mobile** : quand on empile des éléments, vérifier que chaque élément a sa propre grid-row
- **Boutons tactiles** : toujours visibles sans hover, min 44-48px, fond semi-transparent
- **UI interactive** : si un élément a l'air cliquable (dots, flèches), il DOIT avoir le JS associé
- **Images** : toujours stocker dans le repo, jamais d'URLs externes (WordPress, CDN tiers)
- **Nouvelles pages** : toujours mettre un max-width + centrage pour les grands écrans
- **Lightbox mobile** : swipe tactile + flèches visibles + X accessible
