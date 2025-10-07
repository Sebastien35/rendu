# Rendu EDD Groupe 7 


## Utilisation : 

Lancer 
```
docker compose up -d
```
Accéder à l'interface PhpMyAdmin sur  localhost:8081



## Présentation

Ce document présente 5 KPIs (Key Performance Indicators) calculés à partir des données du projet. Chaque KPI est décrit et accompagné de la requête SQL utilisée pour l'obtenir.



### 1. Chiffre d'Affaires Mensuel

**Description :** Somme totale des ventes réalisées chaque mois.

**Requête SQL :**
```sql
DROP TABLE IF EXISTS ca_mensuel;
CREATE TABLE ca_mensuel AS
SELECT LEFT(order_date, 7) AS mois, SUM(total_amount) AS chiffre_affaires
FROM dim_orders
GROUP BY mois
ORDER BY mois;
```

---

### 2. Top 5 Produits Vendus

**Description :** Les 5 produits ayant généré le plus de ventes.

**Requête SQL :**
```sql
    DROP TABLE IF EXISTS top_ventes;
    CREATE TABLE top_ventes AS 
    SELECT dp.id_product, dp.name, COUNT(*) AS total_ventes
    FROM dim_products dp
    JOIN order_items oi ON dp.id_product = oi.id_product
    JOIN dim_orders o ON o.id_order = oi.id_order
    GROUP BY dp.id_product, dp.name
    ORDER BY total_ventes DESC
    LIMIT 5;
```

---

### 3. Panier Moyen

**Description :** Montant moyen dépensé par commande.

**Requête SQL :**
```sql
    DROP TABLE IF EXISTS avg_cart_mensuel;
    CREATE TABLE avg_cart_mensuel AS
    SELECT LEFT(order_date, 7) AS mois, ROUND(AVG(total_amount), 2) AS panier_moyen FROM dim_orders GROUP BY mois ORDER BY mois;
```

---

### 4. Taux de Fidélisation

**Description :** Pourcentage de clients ayant passé au moins une commande.

**Requête SQL :**
```sql
DROP TABLE IF EXISTS taux_fideliation;
CREATE TABLE taux_fideliation AS
SELECT ROUND(100.0 * COUNT(DISTINCT o.id_customer) / (SELECT COUNT(*) FROM dim_customers), 2) AS pct
FROM dim_orders o;
```

---
### 5. Commandes par mois & par catégories
```SQL
CREATE OR REPLACE VIEW vue_commandes_par_mois_et_categorie AS
SELECT 
  LEFT(dim_orders.order_date, 7) AS mois,
  dim_products.category AS category,
  COUNT(*) AS total_commandes
FROM dim_orders
JOIN order_items ON dim_orders.id_order = order_items.id_order
JOIN dim_products ON dim_products.id_product = order_items.id_product
GROUP BY mois, category
ORDER BY mois;
````

