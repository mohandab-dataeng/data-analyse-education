# Analyse des données de systèmes éducatifs — Projet Academy

Étude exploratoire de données (EDA) réalisée pour **Academy**, une start-up EdTech proposant des contenus de formation en ligne pour un public lycée et université. L'entreprise souhaite s'appuyer sur les données éducatives de la **Banque Mondiale** pour identifier les pays présentant un fort potentiel de clients dans le cadre de son projet d'expansion internationale.

## Objectifs

- Explorer les jeux de données EdStats de la Banque Mondiale et identifier les pays à fort potentiel pour les services d'Academy.
- Comprendre comment ce potentiel pourrait évoluer dans le temps.
- Répondre à la problématique : *que faut-il conserver et traiter pour fournir des insights permettant de décider du ou des pays où s'implanter ?*

L'analyse suit la méthodologie **CRISP-DM** (Business Understanding, Data Understanding, Data Preparation, puis Visualisation/Conclusion).

## Jeux de données

Les données sources proviennent du programme **EdStats** de la Banque Mondiale (dossier [data/](data/)) :

| Fichier | Rôle | Description |
|---|---|---|
| `EdStatsData.csv` | Table de faits principale | +800k lignes, ~70 colonnes : valeurs des indicateurs par pays et par année (1970–2100) |
| `EdStatsCountry.csv` | Table de dimension | Pays/régions et leurs attributs |
| `EdStatsSeries.csv` | Table de liaison | ~3665 codes séries/indicateurs et leurs définitions |
| `EdStatsCountry-Series.csv` | Table de liaison | Correspondance pays ↔ codes séries disponibles |
| `EdStatsFootNote.csv` | Métadonnées | +640k lignes : notes et sources sur les données |

> **Note** : le dossier `data/` n'est pas versionné dans ce dépôt (fichiers trop volumineux pour GitHub, notamment `EdStatsData.csv` qui dépasse 300 Mo). Téléchargez les fichiers EdStats depuis le [Data Catalog de la Banque Mondiale](https://datacatalog.worldbank.org/search/dataset/0038480) et placez-les dans `data/` sous les noms `EdStatsData.csv`, `EdStatsCountry.csv`, `EdStatsSeries.csv`, `EdStatsCountry-Series.csv` et `EdStatsFootNote.csv` avant d'exécuter le notebook.

## Démarche

### 1. Data understanding
Description de chaque table (dimensions, types de variables, contenu) et clarification du grain de chaque ligne selon la table (donnée brute, champ calculé ou agrégat régional).

### 2. Data preparation
- Nettoyage générique (déduplication, suppression des colonnes vides) via une fonction `df_clean` réutilisable sur chaque dataframe.
- Réduction du périmètre :
  - Sélection des années suffisamment renseignées (seuil de complétude ≥ 10 %).
  - Calcul de la complétude par indicateur et par pays pour ne retenir que les plus pertinents.
  - Croisement années × indicateurs pour affiner la sélection finale.
- Sélection finale des indicateurs combinant une **approche métier** (pertinence business) et une **approche data** (taux de renseignement élevé).

### 3. Visualisation
- Heatmaps de corrélation (Pearson et Spearman) pour repérer et écarter les indicateurs redondants.
- Statistiques descriptives et distributions par indicateur.
- Création d'indicateurs calculés (ex. taux de population hors parcours scolaire au lycée et dans le tertiaire).
- Scoring exploratoire des pays à partir des indicateurs retenus.

### Indicateurs retenus

| Code | Indicateur |
|---|---|
| `IT.NET.USER.P2` | Utilisateurs d'Internet (% de la population) |
| `NY.GDP.PCAP.PP.KD` | PIB par habitant en PPA ($ constants 2011) |
| `SE.TER.ENRL` | Effectifs inscrits dans le supérieur |
| `SE.TER.ENRR` | Taux brut de scolarisation dans le supérieur (%) |
| `SP.SEC.UTOT.IN` | Population en âge du lycée |
| `SP.TER.TOTL.IN` | Population en âge du supérieur |
| `UIS.NER.3` | Taux net de scolarisation au lycée (%) |

## Conclusion

Un premier scoring (non pondéré) des pays est proposé à partir des indicateurs retenus. La suite du travail (pondération des indicateurs, analyse de la concurrence par marché) relève d'une réflexion marketing complémentaire, ce document se limitant à l'EDA.

## Structure du projet

```
data-analyse-education/
├── data/                  # Jeux de données EdStats (.csv)
├── notebooks/
│   └── P2_notebook.ipynb  # Notebook principal de l'EDA
├── pyproject.toml         # Dépendances du projet
└── README.md
```

## Installation et exécution

Le projet utilise [uv](https://docs.astral.sh/uv/) et Python 3.13.

```bash
# Installer les dépendances
uv sync

# Lancer JupyterLab
uv run jupyter lab notebooks/P2_notebook.ipynb
```

### Dépendances principales

- `pandas`, `numpy` — manipulation de données
- `matplotlib`, `seaborn` — visualisation
- `missingno` — analyse des valeurs manquantes
- `jupyterlab`, `ipykernel` — environnement notebook