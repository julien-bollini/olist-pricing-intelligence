# 📦 Olist Pricing Intelligence

> Analyse complète de la stratégie tarifaire d'un e-commerce B2C brésilien — de la préparation des données à la modélisation de l'élasticité-prix, avec dashboard Power BI interactif.

<br>

[![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-yellow?logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)
[![Dataset](https://img.shields.io/badge/Dataset-Olist%20Kaggle-blue?logo=kaggle&logoColor=white)](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

---

## 🎯 Contexte & Objectif

Ce projet simule la mission d'un **Pricing Analyst** au sein d'un e-commerce multi-catégories.

À partir du dataset public **Olist Brazilian E-commerce** (100 000+ commandes, 2017–2018), j'ai construit une chaîne analytique complète :

1. **Préparation des données** — nettoyage, jointures, feature engineering pricing
2. **Analyse exploratoire** — positionnement prix, marges, saisonnalité, géographie
3. **Modélisation statistique** — élasticité-prix par régression log-log
4. **Visualisation décisionnelle** — dashboard Power BI 3 pages avec simulation tarifaire

**Toutes les valeurs monétaires sont converties en euros** (taux configurable).

---

## 📊 Aperçu du Dashboard

> 👉 [Voir la maquette interactive](outputs/dashboard_mockup.html) — s'ouvre directement dans le navigateur.

| Page | Contenu |
|---|---|
| 📈 Performance Produits | CA, marge, évolution mensuelle, segments de prix |
| 💰 Pricing & Concurrence | Positionnement prix, saisonnalité, satisfaction client |
| 📐 Élasticité & Recommandations | Élasticités par catégorie, simulation tarifaire, recommandations |

---

## 🗂 Structure du projet

```
olist-pricing-intelligence/
│
├── 📓 notebooks/
│   ├── 01_data_preparation.ipynb      # Phase 1 — Nettoyage, jointures, features
│   ├── 02_pricing_analysis.ipynb      # Phase 2 — Analyse exploratoire pricing
│   └── 02b_price_elasticity.ipynb     # Phase 2b — Modèle d'élasticité-prix
│
├── 📊 powerbi/
│   ├── olist_pricing_dashboard.pbix   # Dashboard Power BI (3 pages)
│   ├── POWERBI_GUIDE.md               # Guide de construction pas-à-pas
│   └── dax_measures.md                # Mesures DAX prêtes à copier
│
├── 📁 data/
│   ├── raw/                           # CSV Olist (non versionnés — voir .gitignore)
│   └── processed/
│       ├── olist_pricing_dataset.csv  # Dataset final (output Phase 1)
│       └── olist_pricing_dataset.xlsx # Export multi-onglets
│
├── 🖼 outputs/
│   ├── dashboard_mockup.html          # Maquette interactive du dashboard
│   ├── phase1_data_quality.png        # Visualisations Phase 1
│   ├── phase2_*.png                   # Visualisations Phase 2
│   └── phase2b_*.png                  # Courbes élasticité & heatmap scénarios
│
├── requirements.txt
├── .gitignore
└── README.md
```

---

## ⚙️ Installation & Exécution

### 1. Cloner le dépôt

```bash
git clone https://github.com/<username>/olist-pricing-intelligence.git
cd olist-pricing-intelligence
```

### 2. Créer l'environnement virtuel

```bash
python -m venv .venv
source .venv/bin/activate        # macOS / Linux
.venv\Scripts\activate           # Windows
```

### 3. Installer les dépendances

```bash
pip install -r requirements.txt
```

### 4. Télécharger le dataset

Télécharger les CSV depuis [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) et les placer dans `data/raw/` :

```
data/raw/
├── olist_orders_dataset.csv
├── olist_order_items_dataset.csv
├── olist_products_dataset.csv
├── product_category_name_translation.csv
├── olist_order_reviews_dataset.csv
├── olist_sellers_dataset.csv
├── olist_customers_dataset.csv
└── olist_order_payments_dataset.csv
```

> ⚠️ Le fichier `olist_geolocation_dataset.csv` n'est pas utilisé (coordonnées GPS, hors scope pricing).

### 5. Exécuter les notebooks dans l'ordre

```bash
jupyter notebook
```

| Ordre | Notebook | Durée estimée |
|---|---|---|
| 1 | `01_data_preparation.ipynb` | ~2 min |
| 2 | `02_pricing_analysis.ipynb` | ~3 min |
| 3 | `02b_price_elasticity.ipynb` | ~2 min |

---

## 🔬 Méthodologie

### Phase 1 — Préparation des données

**Tables jointes :** `orders` + `order_items` + `products` + `categories` + `reviews` + `sellers` + `customers` + `payments`

**Filtres appliqués :**
- Commandes `delivered` uniquement (statut fiable)
- Prix entre 0 et 10 000 R$ (outliers exclus)
- Paiements `not_defined` exclus

**Features engineering créées :**

| Feature | Description |
|---|---|
| `total_price_eur` | Prix produit + fret, converti en € |
| `freight_ratio` | Part du fret dans le prix total |
| `estimated_margin_eur` | Marge estimée (hypothèse COGS = 55%) |
| `price_segment_eur` | Segmentation en 5 gammes de prix en € |
| `is_installment` | Achat en plusieurs fois (booléen) |
| `same_state_delivery` | Vendeur et client dans le même état |

> Le taux de change R$ → € est configurable dans la cellule de configuration (Section 1 du notebook).

---

### Phase 2 — Analyse exploratoire pricing

5 axes d'analyse, chacun conclu par un insight business actionnable :

| Section | Question business |
|---|---|
| 2.1 Prix par catégorie | Quelles catégories sont en position premium / sous-pricées ? |
| 2.2 Analyse marge | Quelles catégories contribuent le plus à la marge totale ? |
| 2.3 Tendances | Existe-t-il une saisonnalité des prix exploitable ? |
| 2.4 Comportement client | Prix et satisfaction sont-ils corrélés ? |
| 2.5 Géographie | Quel est le surcoût logistique inter-état ? |

---

### Phase 2b — Modèle d'élasticité-prix

**Modèle utilisé :** Régression log-log (OLS)

$$\ln(Q_t) = \alpha + \varepsilon \cdot \ln(P_t) + \epsilon$$

Le coefficient $\varepsilon$ est l'élasticité-prix estimée par catégorie.

**Pipeline :**
1. Agrégation mensuelle par catégorie (prix moyen, volume vendu)
2. Transformation logarithmique des deux variables
3. Régression OLS par catégorie (via `sklearn` + test Student via `scipy`)
4. Filtrage des résultats significatifs (p < 0.05, n ≥ 6 mois)
5. Classification : élastique / inélastique / Veblen / non significatif
6. Simulation de 6 scénarios tarifaires (-20% à +20%)

**Interprétation :**

| Élasticité ε | Type | Recommandation |
|---|---|---|
| ε < -1 | Élastique | Baisser le prix augmente le CA |
| -1 ≤ ε < 0 | Inélastique | Hausse de prix possible sans perte de volume significative |
| ε ≥ 0 | Veblen / anomalie | Vérifier la cohérence des données |

> ⚠️ **Limites :** Le modèle ne contrôle pas les facteurs exogènes (saisonnalité, promotions, concurrence). Les estimations sont directionnelles, non causales.

---

## 📐 Design des visualisations

Toutes les visualisations appliquent les principes du **design inclusif** :

- **Palette Okabe-Ito** — 8 couleurs distinguables par les personnes daltoniennes ([source](https://jfly.uni-koeln.de/color/))
- **Annotations systématiques** — chaque valeur est affichée en chiffre sur les graphiques
- **Axes explicites** — unité toujours présente dans le label d'axe
- **Taille de police augmentée** — lisibilité sur tous les supports

---

## 📦 Dépendances

```
pandas>=2.0
numpy>=1.24
matplotlib>=3.7
seaborn>=0.12
scipy>=1.10
scikit-learn>=1.3
openpyxl>=3.1
jupyter>=1.0
```

---

## 📁 Dataset

| Champ | Valeur |
|---|---|
| Source | [Olist Brazilian E-commerce — Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) |
| Période | Janvier 2017 — Août 2018 |
| Volume | ~100 000 commandes, ~110 000 articles |
| Tables utilisées | 8 sur 9 (geolocation exclue) |
| Licence | CC BY-NC-SA 4.0 |

---

## 👤 Auteur

Ce projet a été développé pour illustrer mes compétences en analyse de données et en tarification, afin de soutenir ma candidature à des postes de **Data Analyst** ou **Pricing Analyst**.

---

## 📄 Licence

Ce projet est distribué sous licence MIT. Voir le fichier [LICENSE](LICENSE).

Le dataset Olist est distribué sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).
