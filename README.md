# 👑 Palace Market

> **Premium Marketplace Web3 · Delta Mini App**  
> Achetez, vendez et livrez en crypto — directement depuis Delta.

![Version](https://img.shields.io/badge/version-1.20-gold)
![Platform](https://img.shields.io/badge/platform-Delta%20Mini%20App-black)
![Stack](https://img.shields.io/badge/stack-HTML%20%7C%20Supabase%20%7C%20Delta%20SDK-blue)
![License](https://img.shields.io/badge/license-Private-red)

---

## 📱 Aperçu

Palace Market est une **mini application Delta** qui connecte acheteurs, vendeurs et livreurs au sein d'un écosystème marketplace premium. Les paiements s'effectuent en **DTC / USDT** via le wallet Delta, avec conversion automatique en FCFA.

---

## ✨ Fonctionnalités

### 🛍 Acheteur
- Marketplace produits avec filtres par catégorie
- Food Palace — restaurants et commandes en ligne
- Panier multi-vendeurs avec paiement Delta Wallet
- Tracking commande en temps réel (4 étapes)
- Historique des commandes
- Messagerie avec vendeurs et livreurs
- Gestion des adresses de livraison

### 🏪 Vendeur
- Création de restaurant (Food Palace) ou boutique (Marketplace)
- Upload logo + photos produits/plats (Supabase Storage)
- Dashboard avec statistiques revenus et commandes en temps réel
- Gestion du catalogue (ajout, modification, rupture de stock)
- Gestion des commandes entrantes (Accepter / Refuser / Prêt)

### 🛵 Livreur
- Dashboard des demandes de livraison
- Gestion des courses en cours

### 🎰 Palace Jackpot
- Tirage hebdomadaire
- Système de tickets de participation
- Tableau des derniers gagnants (Supabase live)

### 🤖 Assistant Ema
- Assistant IA intégré avec humour

---

## 🏗 Architecture

```
palace-market/
├── index.html          # Application complète (single file)
├── .version            # Numéro de version (ex: 1.20)
└── supabase/
    └── functions/
        └── update-balance/
            └── index.ts    # Edge Function sécurisée
```

### Screens (16)
| Screen | Description |
|---|---|
| `screen-splash` | Écran de chargement |
| `screen-auth` | Connexion Delta |
| `screen-home` | Accueil marketplace |
| `screen-search` | Recherche & Explorer |
| `screen-orders` | Commandes & Tracking |
| `screen-chat` | Messagerie |
| `screen-chat-conv` | Conversation individuelle |
| `screen-profile` | Profil utilisateur |
| `screen-cart` | Panier |
| `screen-seller` | Dashboard vendeur |
| `screen-seller-setup` | Configuration établissement |
| `screen-delivery` | Dashboard livreur |
| `screen-jackpot` | Palace Jackpot |
| `screen-notifs` | Notifications |
| `screen-addresses` | Adresses de livraison |
| `screen-settings` | Paramètres |

---

## 🛠 Stack Technique

| Composant | Technologie |
|---|---|
| Frontend | HTML5 · CSS3 · JavaScript ES2022 |
| Auth | Delta SDK (`authByIdentToken`) |
| Base de données | Supabase (PostgreSQL) |
| Storage | Supabase Storage (buckets `logos` + `products`) |
| Backend | Supabase Edge Functions (Deno) |
| Paiements | Delta Wallet SDK (`walletPayment`) |
| Auth bridge | Supabase Anonymous sign-ins |

---

## 🗄 Base de données Supabase

### Tables

| Table | Description |
|---|---|
| `users` | Profils utilisateurs liés au DID Delta |
| `products` | Produits et plats des vendeurs |
| `stores` | Restaurants et boutiques |
| `orders` | Commandes avec statut et tracking |
| `messages` | Messagerie acheteur ↔ vendeur ↔ livreur |
| `addresses` | Adresses de livraison des acheteurs |
| `jackpot_winners` | Historique des gagnants du jackpot |

### Storage Buckets

| Bucket | Accès | Usage |
|---|---|---|
| `logos` | Public | Photos de profil des établissements |
| `products` | Public | Photos des produits et plats |

---

## 🔐 Sécurité

### Architecture d'authentification
```
Delta DID (signé cryptographiquement)
    ↓
window.delta.authByIdentToken()
    ↓
supabase.auth.signInAnonymously()  →  UUID Supabase unique
    ↓
DB.setAuthToken(jwt)  →  auth.uid() valide dans RLS
    ↓
RLS : auth.uid() = user_id  →  chaque user ne touche que ses données
```

### Row Level Security (RLS)
- ✅ Activé sur toutes les tables sensibles
- `users` — lecture/modification par propriétaire uniquement
- `stores` — lecture publique, write par propriétaire (`auth.uid() = user_id`)
- `orders` — acheteur et vendeur concernés uniquement
- `products` — lecture publique, write par vendeur propriétaire
- `addresses` — propriétaire uniquement
- `jackpot_winners` — lecture publique, write backend uniquement

### Edge Function `update-balance`
- Modification du solde `dtc_balance` **uniquement côté serveur**
- Vérification JWT obligatoire (`auth.getUser()`)
- Montant max par appel : 10 000
- Empêche les soldes négatifs

### Protections client-side
- Sanitisation anti-XSS sur toutes les données utilisateur (`sanitize()`)
- Validation des formulaires (longueur, type, limites)
- Cache mémoire avec TTL 60s pour réduire les requêtes

---

## ⚙️ Configuration

```javascript
// index.html — CONFIG object
const CONFIG = {
  DELTA_APP_ID:   29,
  SUPABASE_URL:   'https://onnzwrglpfkezuzimlba.supabase.co',
  SUPABASE_KEY:   'eyJ...',        // Clé anon publique (normale dans un frontend)
  DTC_TO_FCFA:    650,             // Taux de conversion — à mettre à jour
  ESCROW_ADDRESS: 'palace-escrow-29',
};
```

---

## 🚀 Déploiement

### 1. Mini App Delta
```
Delta Developer Portal
  → App ID : 29
  → Upload : palace-market-delta.zip (index.html + .version)
  → Publier
```

### 2. Edge Function
```bash
# Installer Supabase CLI
npm install -g supabase

# Lier le projet
supabase login
supabase link --project-ref onnzwrglpfkezuzimlba

# Déployer
supabase functions deploy update-balance
```

### 3. Supabase — Prérequis
- Authentication → Providers → **Anonymous** → Enable ✅
- Exécuter les scripts SQL (`/sql/`)
- Storage buckets `logos` et `products` créés et publics

---

## 🔄 Versioning

| Version | Changements majeurs |
|---|---|
| 1.20 | Sécurité RLS stores · Bridge Delta↔Supabase Auth |
| 1.19 | Fix "Session expirée" · saveSellerConfig sans blocage dur |
| 1.18 | Pont JWT Delta DID ↔ Supabase anonymous session |
| 1.17 | Corrections uploadées depuis corrections manuelles |
| 1.16 | Clé anon JWT · Edge Function update-balance · fetchUserData |
| 1.15 | Renommage `type` → `store_type` (compatibilité Supabase) |
| 1.14 | Solde réel · restaurants dynamiques · tracking live · images produits |
| 1.13 | Fix bucket Storage SQL |
| 1.12 | Audit sécurité · cache mémoire · sanitize XSS · adresses dynamiques |
| 1.11 | `.maybeSingle()` · upsert · guard DID |
| 1.10 | Structure `div#app` correcte · splash/auth hors app |
| 1.09 | Connexion Supabase SDK · `loadProducts()` dynamique |

---

## 📋 Checklist de mise en production

- [ ] Anonymous sign-ins activé dans Supabase
- [ ] RLS activé sur toutes les tables
- [ ] Edge Function `update-balance` déployée
- [ ] Buckets Storage `logos` et `products` créés publics
- [ ] Taux `DTC_TO_FCFA` mis à jour dynamiquement
- [ ] Adresse escrow `ESCROW_ADDRESS` vérifiée
- [ ] Tests paiement Delta Wallet en environnement réel

---

## 👤 Auteur

**Palace Market** — Mini App Delta premium  
App ID : `29` · Supabase Project : `onnzwrglpfkezuzimlba`
