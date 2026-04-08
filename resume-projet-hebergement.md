# Résumé — Hébergement & Stratégie Technique

## 1. Site Carron Deslarzes (site vitrine HTML statique)

### Recommandation : GitHub Pages (gratuit)
- Hébergement gratuit pour sites statiques (HTML/CSS/JS/images)
- SSL inclus, 100 Go de bande passante/mois
- Repo public (pas de problème de sécurité pour un site vitrine — le code est déjà visible dans le navigateur)
- Seul coût : le nom de domaine (~10-15 CHF/an)
- Alternative gratuite : Cloudflare Pages ou Vercel

### Infomaniak (si besoin)
- Hébergement Web : CHF 10.91/mois — surdimensionné et trop cher pour un simple site statique
- Pas recommandé pour ce cas d'usage

---

## 2. App Photo de Courses (projet Tiffany)

### Concept
Plateforme pour photographe sportif : upload de photos de courses → reconnaissance automatique des numéros de dossard (OCR) → les coureurs retrouvent leurs photos en tapant leur numéro → achat et téléchargement en haute résolution.

### Architecture recommandée

| Brique | Rôle | Solution recommandée |
|--------|------|---------------------|
| Frontend | Interface web | Next.js (déjà utilisé) |
| Backend/API | Logique métier | Next.js API Routes ou Node.js séparé |
| Base de données | Événements, photos, utilisateurs, commandes | PostgreSQL (Supabase, Neon, ou Infomaniak Database Service dès CHF 21.64/mois) |
| Stockage photos | Photos haute résolution | Cloudflare R2 ou Infomaniak Object Storage (CHF 0.01/Go/mois) |
| OCR dossards | Reconnaissance des numéros | Google Cloud Vision (le plus fiable) ou Qwen3-VL via Infomaniak AI Services |
| Paiement | Vente de photos | Stripe + TWINT (voir section paiement) |
| Hébergement app | Serveur | Vercel (front) + Railway (back) ou Infomaniak Jelastic Cloud / VPS Lite |

### Workflow utilisateur
1. Tiffany uploade ses photos d'une course
2. L'OCR analyse chaque photo et détecte les numéros de dossard
3. Les numéros sont indexés en base de données
4. Un coureur visite le site, tape son numéro de dossard
5. Il voit ses photos avec watermark
6. Il paie via Stripe (carte ou TWINT)
7. Il reçoit les photos en haute résolution sans watermark

---

## 3. Offres Infomaniak Cloud Computing (détail)

### Jelastic Cloud (PaaS)
- Plateforme de déploiement d'applications
- Supporte : Node.js, Python, PHP, Go, Ruby, Docker, Kubernetes
- Facturation à l'usage : CHF 0.024 à 0.183/heure
- Base de données intégrée (MySQL, PostgreSQL, MongoDB, Redis)
- Essai gratuit 14 jours

### Public Cloud (IaaS)
- Instances générales : dès CHF 2.93/mois (1 CPU, 2 Go RAM)
- Object Storage : CHF 0.01/Go/mois, compatible S3
  - Trafic entrant : gratuit
  - Trafic sortant : gratuit jusqu'à 10 To/mois, puis CHF 0.009/Go
- Database Service MySQL : dès CHF 21.64/mois (1 CPU, 4 Go RAM, 80 Go)
- Bande passante gratuite (hors Object Storage > 10 To)
- CHF 300 de crédits offerts pour démarrer (3 mois)

### VPS Lite
- CHF 2.70/mois : 1 vCPU, 2 Go RAM, 20 Go disque
- CHF 5.40/mois : 2 vCPU, 2 Go RAM, 40 Go disque
- CHF 7.20/mois : 2 vCPU, 4 Go RAM, 60 Go disque
- CHF 9.00/mois : 4 vCPU, 4 Go RAM, 80 Go disque
- 10% de réduction en engagement annuel

### AI Services
- API compatible OpenAI, modèles open source hébergés en Suisse
- LLM : Gemma, Mistral, Llama 3.3, Qwen3-VL (vision), Kimi-K2.5
- Embeddings : Bge Multilingual Gemma2, All MiniLM (gratuit)
- Audio : Whisper V3 à CHF 0.006/min
- Image : Photomaker V2, Flux Schnell à CHF 0.30/min
- 1M de crédits offerts pour démarrer
- Qwen3-VL pourrait servir pour l'OCR de dossards (à tester)

### Budget estimé Infomaniak (tout-en-un)
~CHF 30-50/mois après crédits gratuits + coûts IA à l'usage

---

## 4. Paiement

### Recommandation : Stripe
- Commission : ~1.5% + 0.30 CHF par transaction
- Supporte nativement : TWINT, cartes (Visa, Mastercard), Apple Pay, Google Pay
- API très bien documentée, intégration simple
- Gère factures, remboursements, litiges

### Stripe Connect (modèle marketplace)
- Permet de prendre une commission sur chaque transaction
- Exemple : photo vendue 15 CHF → Stripe prend ~0.53 CHF → toi 10% = 1.50 CHF → photographe reçoit ~12.97 CHF
- Reversements automatiques sur le compte du vendeur
- Pas de licence FINMA nécessaire (Stripe est l'intermédiaire financier)
- Scalable : possibilité d'ouvrir la plateforme à d'autres photographes

### Alternative : Datatrans
- Prestataire suisse, utilisé par des concurrents
- Plus complexe à intégrer, frais souvent plus élevés
- Adapté aux grandes entreprises

### Note sur les micro-transactions
- Le frais fixe de 0.30 CHF pèse lourd sur les petits montants
- Photo à 5 CHF → frais = 7.5% du total
- Photo à 25 CHF → frais = 2.7% du total
- Recommandation : encourager les achats groupés (packs de photos)

---

## 5. Idée SaaS — Plateforme de paiement multi-industrie

### Concept
Créer un SaaS basé sur Stripe Connect qui permet à n'importe quel business d'intégrer du paiement avec commission automatique.

### Approche réaliste (court terme)
- Surcouche sur Stripe Connect
- Interface simplifiée pour les marchands
- Commission sur chaque transaction
- Pas de licence bancaire nécessaire
- Spécialisation par niche pour se différencier

### Approche ambitieuse (long terme)
- Devenir PSP (Payment Service Provider)
- Licence FINMA requise (12-24 mois, centaines de milliers de CHF)
- Conformité PCI DSS, partenariats Visa/Mastercard
- Capital nécessaire : plusieurs millions de CHF

---

*Document généré le 10 mars 2026*
