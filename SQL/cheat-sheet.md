# Cheat Sheet SQL - Terminale NSI

## 📋 Structure de base

### CREATE TABLE - Créer une table
```sql
CREATE TABLE eleves (
    id INTEGER PRIMARY KEY,
    nom TEXT NOT NULL,
    prenom TEXT,
    age INTEGER,
    moyenne REAL
);
```

**Types de données courants :**
- `INTEGER` : nombres entiers
- `REAL` : nombres décimaux
- `TEXT` : chaînes de caractères
- `BLOB` : données binaires

**Contraintes :**
- `PRIMARY KEY` : clé primaire (unique + non null)
- `NOT NULL` : valeur obligatoire
- `UNIQUE` : valeur unique
- `DEFAULT valeur` : valeur par défaut
- `FOREIGN KEY` : clé étrangère

### DROP TABLE - Supprimer une table
```sql
DROP TABLE eleves;
```

## ➕ Insertion de données

### INSERT - Ajouter des lignes
```sql
-- Toutes les colonnes
INSERT INTO eleves VALUES (1, 'Dupont', 'Marie', 17, 15.5);

-- Colonnes spécifiques
INSERT INTO eleves (nom, prenom) VALUES ('Martin', 'Lucas');

-- Plusieurs lignes à la fois
INSERT INTO eleves VALUES 
    (2, 'Martin', 'Lucas', 16, 14.2),
    (3, 'Durand', 'Emma', 17, 16.8);
```

## 🔍 Consultation de données

### SELECT - Lire des données

#### Sélection simple
```sql
-- Toutes les colonnes
SELECT * FROM eleves;

-- Colonnes spécifiques
SELECT nom, prenom FROM eleves;

-- Avec alias
SELECT nom AS "Nom de famille", prenom AS "Prénom" FROM eleves;
```

#### WHERE - Filtrer les résultats
```sql
-- Égalité
SELECT * FROM eleves WHERE age = 17;

-- Comparaisons
SELECT * FROM eleves WHERE moyenne > 15;
SELECT * FROM eleves WHERE moyenne >= 14 AND moyenne <= 16;
SELECT * FROM eleves WHERE age < 18;

-- Opérateurs logiques
SELECT * FROM eleves WHERE age = 17 AND moyenne > 15;
SELECT * FROM eleves WHERE nom = 'Dupont' OR nom = 'Martin';
SELECT * FROM eleves WHERE NOT age = 16;

-- LIKE - Recherche de motifs
SELECT * FROM eleves WHERE nom LIKE 'Dup%';      -- Commence par "Dup"
SELECT * FROM eleves WHERE nom LIKE '%ont';      -- Finit par "ont"
SELECT * FROM eleves WHERE nom LIKE '%ar%';      -- Contient "ar"

-- IN - Liste de valeurs
SELECT * FROM eleves WHERE nom IN ('Dupont', 'Martin', 'Durand');

-- BETWEEN - Intervalle
SELECT * FROM eleves WHERE moyenne BETWEEN 12 AND 16;

-- IS NULL / IS NOT NULL
SELECT * FROM eleves WHERE moyenne IS NULL;
SELECT * FROM eleves WHERE moyenne IS NOT NULL;
```

#### ORDER BY - Trier les résultats
```sql
-- Ordre croissant (par défaut)
SELECT * FROM eleves ORDER BY moyenne;
SELECT * FROM eleves ORDER BY moyenne ASC;

-- Ordre décroissant
SELECT * FROM eleves ORDER BY moyenne DESC;

-- Tri multiple
SELECT * FROM eleves ORDER BY age DESC, nom ASC;
```

#### LIMIT - Limiter le nombre de résultats
```sql
-- Les 5 premiers
SELECT * FROM eleves LIMIT 5;

-- Avec décalage (skip les 10 premiers, prend les 5 suivants)
SELECT * FROM eleves LIMIT 5 OFFSET 10;
```

#### DISTINCT - Supprimer les doublons
```sql
SELECT DISTINCT age FROM eleves;
```

## 🔢 Fonctions d'agrégation

```sql
-- Compter
SELECT COUNT(*) FROM eleves;                    -- Nombre total de lignes
SELECT COUNT(moyenne) FROM eleves;              -- Compte les valeurs non NULL
SELECT COUNT(DISTINCT age) FROM eleves;         -- Nombre d'âges différents

-- Somme
SELECT SUM(moyenne) FROM eleves;

-- Moyenne
SELECT AVG(moyenne) FROM eleves;

-- Minimum et Maximum
SELECT MIN(moyenne) FROM eleves;
SELECT MAX(moyenne) FROM eleves;
```

### GROUP BY - Regrouper les résultats
```sql
-- Nombre d'élèves par âge
SELECT age, COUNT(*) FROM eleves GROUP BY age;

-- Moyenne par âge
SELECT age, AVG(moyenne) AS moyenne_par_age 
FROM eleves 
GROUP BY age;
```

### HAVING - Filtrer après regroupement
```sql
-- Âges avec plus de 3 élèves
SELECT age, COUNT(*) AS nb_eleves
FROM eleves
GROUP BY age
HAVING COUNT(*) > 3;

-- Différence entre WHERE et HAVING :
-- WHERE filtre AVANT le regroupement
-- HAVING filtre APRÈS le regroupement
```

## 🔗 Jointures (JOIN)

### INNER JOIN - Jointure interne
```sql
-- Ne garde que les lignes avec correspondance dans les deux tables
SELECT e.nom, e.prenom, n.note, m.nom_matiere
FROM eleves e
JOIN notes n ON e.id = n.id_eleve
JOIN matieres m ON n.id_matiere = m.id;
```

### LEFT JOIN - Jointure externe gauche
```sql
-- Garde toutes les lignes de la table de gauche
SELECT e.nom, e.prenom, n.note
FROM eleves e
LEFT JOIN notes n ON e.id = n.id_eleve;
-- Résultat : tous les élèves, même ceux sans notes (note = NULL)
```

### Exemple complet de double jointure
```sql
-- 3 tables : eleves, notes, matieres
SELECT 
    e.nom,
    e.prenom,
    m.nom_matiere,
    n.note
FROM eleves e
JOIN notes n ON e.id = n.id_eleve
JOIN matieres m ON n.id_matiere = m.id
WHERE n.note > 15
ORDER BY n.note DESC;
```

## ✏️ Modification de données

### UPDATE - Modifier des lignes
```sql
-- Modifier une colonne
UPDATE eleves SET moyenne = 16.5 WHERE id = 1;

-- Modifier plusieurs colonnes
UPDATE eleves 
SET moyenne = 15, age = 18 
WHERE nom = 'Dupont';

-- Modifier toutes les lignes (attention !)
UPDATE eleves SET age = age + 1;
```

### DELETE - Supprimer des lignes
```sql
-- Supprimer une ligne spécifique
DELETE FROM eleves WHERE id = 1;

-- Supprimer selon une condition
DELETE FROM eleves WHERE moyenne < 10;

-- Supprimer toutes les lignes (attention !)
DELETE FROM eleves;
```

## 🔧 Commandes Terminal SQLite3

```bash
# Lancer SQLite3
sqlite3 ma_base.db

# Commandes dans SQLite3 (commencent par un point)
.tables                    # Liste les tables
.schema nom_table          # Affiche la structure d'une table
.mode column               # Affichage en colonnes
.headers on                # Affiche les en-têtes
.width 10 15 20            # Définit la largeur des colonnes
.quit                      # Quitter SQLite3

# Exécuter un fichier SQL
.read mon_script.sql

# Exporter les résultats
.output resultats.txt
SELECT * FROM eleves;
.output stdout             # Revenir à l'affichage normal
```

## 📊 Opérateurs et fonctions utiles

### Opérateurs arithmétiques
```sql
SELECT moyenne * 20 / 100 AS note_sur_20 FROM eleves;
SELECT (note1 + note2 + note3) / 3 AS moyenne FROM notes;
```

### Fonctions sur les chaînes
```sql
-- Concaténation
SELECT nom || ' ' || prenom AS nom_complet FROM eleves;

-- Majuscules/Minuscules
SELECT UPPER(nom) FROM eleves;
SELECT LOWER(nom) FROM eleves;

-- Longueur
SELECT LENGTH(nom) FROM eleves;

-- Sous-chaîne (position commence à 1)
SELECT SUBSTR(nom, 1, 3) FROM eleves;  -- Les 3 premiers caractères
```

### Fonctions sur les nombres
```sql
SELECT ROUND(moyenne, 1) FROM eleves;     -- Arrondi à 1 décimale
SELECT ABS(-5);                            -- Valeur absolue
```

## 🎯 Ordre d'exécution SQL (important !)

```sql
SELECT nom, AVG(note) AS moyenne     -- 5. Sélection des colonnes
FROM eleves                          -- 1. Table source
JOIN notes ON eleves.id = notes.id   -- 2. Jointures
WHERE age > 16                       -- 3. Filtrage des lignes
GROUP BY nom                         -- 4. Regroupement
HAVING AVG(note) > 12                -- 6. Filtrage des groupes
ORDER BY moyenne DESC                -- 7. Tri
LIMIT 10;                            -- 8. Limitation
```

## ⚠️ Erreurs courantes à éviter

```sql
-- ❌ Oublier WHERE dans UPDATE/DELETE (modifie toute la table !)
UPDATE eleves SET moyenne = 20;

-- ✅ Toujours mettre un WHERE
UPDATE eleves SET moyenne = 20 WHERE id = 1;

-- ❌ Utiliser des noms/prénoms pour les jointures
JOIN appreciation ON eleves.nom = appreciation.nom;

-- ✅ Utiliser des ID
JOIN appreciation ON eleves.id = appreciation.id_eleve;

-- ❌ Utiliser une colonne agrégée dans WHERE
SELECT nom FROM eleves WHERE AVG(note) > 12;

-- ✅ Utiliser HAVING après GROUP BY
SELECT nom FROM eleves GROUP BY nom HAVING AVG(note) > 12;
```

## 💡 Astuces pour le BAC

1. **Toujours nommer les clés primaires `id` ou `id_nom_table`**
2. **Utiliser des alias courts** : `FROM eleves e` plutôt que `FROM eleves`
3. **Indenter ton code** pour le rendre lisible
4. **Tester tes requêtes étape par étape** : d'abord le SELECT simple, puis ajoute WHERE, puis JOIN, etc.
5. **Faire attention à l'ordre** : WHERE avant GROUP BY, HAVING après GROUP BY
6. **Pour compter, utiliser `COUNT(*)`** plutôt que `COUNT(colonne)` si tu veux tout compter

## 📝 Exemple complet - Base de données lycée

```sql
-- Création des tables
CREATE TABLE eleves (
    id INTEGER PRIMARY KEY,
    nom TEXT NOT NULL,
    prenom TEXT NOT NULL,
    classe TEXT
);

CREATE TABLE matieres (
    id INTEGER PRIMARY KEY,
    nom TEXT NOT NULL,
    coefficient REAL
);

CREATE TABLE notes (
    id INTEGER PRIMARY KEY,
    id_eleve INTEGER,
    id_matiere INTEGER,
    note REAL,
    date TEXT,
    FOREIGN KEY(id_eleve) REFERENCES eleves(id),
    FOREIGN KEY(id_matiere) REFERENCES matieres(id)
);

-- Insertion de données
INSERT INTO eleves VALUES 
    (1, 'Dupont', 'Marie', 'TG1'),
    (2, 'Martin', 'Lucas', 'TG1'),
    (3, 'Durand', 'Emma', 'TG2');

INSERT INTO matieres VALUES 
    (1, 'NSI', 4),
    (2, 'Mathématiques', 7),
    (3, 'Philosophie', 3);

INSERT INTO notes VALUES 
    (1, 1, 1, 16, '2024-01-15'),
    (2, 1, 2, 15, '2024-01-16'),
    (3, 2, 1, 14, '2024-01-15'),
    (4, 2, 2, 18, '2024-01-16'),
    (5, 3, 1, 17, '2024-01-15');

-- Requête complexe : moyenne pondérée par élève
SELECT 
    e.nom,
    e.prenom,
    ROUND(SUM(n.note * m.coefficient) / SUM(m.coefficient), 2) AS moyenne_ponderee
FROM eleves e
JOIN notes n ON e.id = n.id_eleve
JOIN matieres m ON n.id_matiere = m.id
GROUP BY e.id, e.nom, e.prenom
ORDER BY moyenne_ponderee DESC;
```