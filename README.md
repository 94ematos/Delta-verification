# 👑 PALACE MARKET — Documentation Technique

> **Version:** 1.0.0 · **Mini-App Delta** · Web3 Marketplace

---

## 🚀 DÉPLOIEMENT DELTA

### Structure du package
```
palace-market/
├── index.html      ← Application complète (single-file)
├── .version        ← Requis par Delta (contient: 1.0.0)
└── README.md       ← Ce fichier
```

### Étapes de soumission
1. Zipper le dossier `palace-market/`
2. Aller dans le **Delta Developer Center**
3. Créer une nouvelle app → **Type: Mini (Native)**
4. Upload du package ZIP
5. Tester en mode **Debug** avant publication
6. Passer le statut → **Beta publique** → **Publié**

---

## 🔐 AUTHENTIFICATION DELTA

```javascript
// Intégration complète dans l'app
let res = await window.delta.authByIdentToken();
localStorage.setItem("identToken", JSON.stringify(res));
// DID récupéré: res.dAppIdentToken.did
```

**Mode dégradé:** Si `window.delta` n'est pas disponible (browser normal), l'app passe en mode démo automatiquement.

---

## 💰 PAIEMENT (ESCROW)

```javascript
// Flux de paiement sécurisé
await window.delta.walletPayment(
  coinCode,         // 'USDT' | 'DTC' | 'ICP'
  'palace-escrow-wallet',  // Adresse escrow Palace
  amount,
  'Palace Market Order'
);
```

**Logique Escrow:**
1. Acheteur paie → Fonds en escrow
2. Commande = `pending`
3. Vendeur prépare → Livreur livre
4. Acheteur reçoit un **code 4 caractères** unique
5. Livreur saisit le code → Paiement libéré au vendeur

---

## 🎰 SYSTÈME JACKPOT

- Tirage **chaque dimanche à 20h00 GMT**
- Tickets gagnés via **publicités Delta** (`window.delta.showAd`)
- Récompense par pub: **+1 Ticket** + **0.01 USDT en DTC**
- Countdown en temps réel jusqu'au prochain tirage
- Historique des gagnants précédents affiché

---

## 🛠 BACKEND SUPABASE (À CONNECTER)

### Tables à créer:
```sql
-- Utilisateurs
CREATE TABLE users (
  id UUID PRIMARY KEY,
  did TEXT UNIQUE NOT NULL,
  nickname TEXT,
  avatar_url TEXT,
  role TEXT DEFAULT 'buyer',
  tickets INTEGER DEFAULT 0,
  dtc_balance DECIMAL DEFAULT 0,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Boutiques
CREATE TABLE stores (
  id UUID PRIMARY KEY,
  owner_did TEXT REFERENCES users(did),
  name TEXT NOT NULL,
  category TEXT,
  rating DECIMAL DEFAULT 0,
  is_restaurant BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Produits
CREATE TABLE products (
  id UUID PRIMARY KEY,
  store_id UUID REFERENCES stores(id),
  name TEXT NOT NULL,
  description TEXT,
  price_dtc DECIMAL NOT NULL,
  price_fcfa INTEGER,
  emoji TEXT,
  category TEXT,
  stock INTEGER DEFAULT 0,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Commandes
CREATE TABLE orders (
  id UUID PRIMARY KEY,
  buyer_did TEXT REFERENCES users(did),
  seller_did TEXT REFERENCES users(did),
  delivery_did TEXT REFERENCES users(did),
  items JSONB NOT NULL,
  total_amount DECIMAL NOT NULL,
  coin_code TEXT DEFAULT 'USDT',
  status TEXT DEFAULT 'pending',
  delivery_code TEXT,
  escrow_tx TEXT,
  address TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Paiements
CREATE TABLE payments (
  id UUID PRIMARY KEY,
  order_id UUID REFERENCES orders(id),
  payer_did TEXT,
  amount DECIMAL NOT NULL,
  coin_code TEXT,
  tx_hash TEXT,
  status TEXT DEFAULT 'escrow',
  created_at TIMESTAMP DEFAULT NOW()
);

-- Messages
CREATE TABLE messages (
  id UUID PRIMARY KEY,
  sender_did TEXT,
  receiver_did TEXT,
  order_id UUID REFERENCES orders(id),
  content TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Tickets Jackpot
CREATE TABLE jackpot_tickets (
  id UUID PRIMARY KEY,
  user_did TEXT REFERENCES users(did),
  week_date DATE NOT NULL,
  ticket_count INTEGER DEFAULT 0,
  earned_dtc DECIMAL DEFAULT 0
);
```

### Connexion Supabase dans l'app:
```javascript
import { createClient } from '@supabase/supabase-js';
const supabase = createClient(
  'https://YOUR_PROJECT.supabase.co',
  'YOUR_ANON_KEY'
);
```

---

## 🌍 MULTILANGUE

```javascript
// Détection automatique
const lang = await window.delta.languageCode();

// Traduction
const translated = await window.delta.translateText("Hello", lang);
```

---

## 💱 CONVERSION PRIX

```
1 DTC ≈ 650 FCFA (à ajuster selon taux en temps réel)
Prix vendeur (FCFA) ÷ 650 = Prix en DTC
```

---

## 📱 FONCTIONNALITÉS IMPLÉMENTÉES

| Fonctionnalité | Status |
|---|---|
| Authentification Delta | ✅ |
| Rôles (Acheteur/Vendeur/Livreur) | ✅ |
| Marketplace produits | ✅ |
| Palace Food (restaurants) | ✅ |
| Panier & Paiement escrow | ✅ |
| Suivi commande (étapes) | ✅ |
| Chat intégré | ✅ |
| Assistante Ema (IA) | ✅ |
| Jackpot dimanche 20h GMT | ✅ |
| Système tickets pub | ✅ |
| Récompense 0.01 USDT/DTC | ✅ |
| Countdown jackpot temps réel | ✅ |
| Conversion FCFA ↔ DTC | ✅ |
| Multicoins (USDT/DTC/ICP) | ✅ |
| Code livraison sécurisé | ✅ |
| Tableau de bord vendeur | ✅ |
| Notifications | ✅ |
| Mode sombre luxe (Or/Rouge/Blanc) | ✅ |

---

## 🎨 DESIGN SYSTEM

**Couleurs:**
- Or principal: `#D4AF37`
- Rouge accent: `#C0392B`
- Fond: `#0D0A06` (noir profond)
- Surface: `#221C0E`

**Typographie:**
- Display: `Cormorant Garamond` (élégant, luxe)
- Body: `DM Sans` (moderne, lisible)

---

## 📞 SUPPORT

Pour toute question technique, contacter l'équipe Palace Market.
