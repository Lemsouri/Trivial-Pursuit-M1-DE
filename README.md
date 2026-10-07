#  Trivial Pursuit — Benchmark de modèles IA

Projet réalisé dans le cadre du M1 Data Engineering & Intelligence Artificielle.

L'objectif est de comparer les performances de modèles de langage locaux sur un ensemble de questions de culture générale, tout en mettant en place un pipeline Data Engineering complet.

##  Objectifs

Le projet vise à :

- récupérer et nettoyer les questions de l'Open Trivia Database 
- tester plusieurs modèles de langage locaux 
- mesurer leur précision et leur temps de réponse 
- analyser leurs performances selon la difficulté et la catégorie 
- construire une architecture Data Engineering Bronze → Silver → Gold 
- utiliser dbt pour transformer et modéliser les données 
- créer un dashboard interactif avec Streamlit

##  Questions métier

Le projet cherche notamment à répondre à :

1. Quel modèle est le plus précis ?
2. Quel modèle est le plus rapide ?
3. Comment les performances évoluent-elles selon la difficulté ?
4. Dans quelles catégories les modèles sont-ils les plus performants ?
5. Quel modèle présente les temps de réponse les plus réguliers ?

## Dataset

Les données proviennent de Open Trivia Database.

Après nettoyage et déduplication :

5 295 questions sont utilisées pour le benchmark.

Les principales informations sont :

- catégorie 
- difficulté 
- question 
- réponse correcte 
- réponses incorrectes

##  Architecture

Le projet suit une architecture Medallion :

      text
Open Trivia Database
        ↓
     BRONZE
 questions_raw.csv
        ↓
     SILVER
 questions_clean.parquet
        ↓
   Benchmark Ollama
   ┌───────────────┐
   │ Gemma 3 4B    │
   │ LLaMA 3.2 3B  │
   └───────┬───────┘
           ↓
    Silver enrichie
           ↓
          dbt
           ↓
     STAGING
           ↓
    INTERMEDIATE
           ↓
        MARTS
           ↓
        DuckDB
           ↓
      Streamlit
           ↓
       Dashboard



1- Bronze

La Bronze contient les données brutes récupérées depuis Open Trivia Database.

data/bronze/questions_raw.csv

Nous conservons les données originales afin de pouvoir refaire les traitements.

2- Silver

La Silver contient les données nettoyées et normalisées au format Parquet.

data/silver/questions_clean.parquet

Après le benchmark, elle contient également les résultats des modèles :

questions_enriched_gemma3_4b.parquet
questions_enriched_llama3.2_3b.parquet

3- Gold

La Gold contient les données préparées pour l'analyse métier.

Elle est construite avec dbt + DuckDB.

Architecture dbt :
Nous avons trois niveaux de transformation :

STAGING
   ↓
INTERMEDIATE
   ↓
MARTS


1- Staging :

Les modèles stg_gemma et stg_llama récupèrent les données Silver.

2- Intermediate :

int_model_results regroupe les résultats des différents modèles dans une structure commune.

3- Marts :
Les Marts répondent directement aux questions métier :

mart_model_comparison : comparaison globale 
mart_accuracy_category : performance par catégorie 
mart_accuracy_difficulty : performance par difficulté 
mart_response_time : analyse des temps de réponse


# Modèles IA :
Les modèles sont exécutés localement avec Ollama :

1- Gemma 3 4B 
2- LLaMA 3.2 3B

Pour chaque question, nous enregistrons notamment :
la réponse du modèle 
si elle est correcte 
le temps de réponse 
le modèle utilisé 
le prompt 
la réponse brute


# Métriques :
Les principales métriques sont :

Accuracy
réponses correctes / nombre total de questions × 100
Temps moyen : vitesse moyenne de réponse
Médiane : temps de réponse typique, moins sensible aux valeurs extrêmes
Minimum / Maximum : meilleure et pire latence observée
Écart-type : mesure de la régularité des temps de réponse


# Technologies :
Technologie	Utilisation :

Python : Traitement et automatisation
Pandas : Manipulation des données
PyArrow : Parquet
Ollama : Exécution des LLM
dbt : Transformation des données
DuckDB : Analyse et stockage
Streamlit : Dashboard
Plotly : Visualisations
Git / GitHub : Versionnement


# Structure :
trivial-pursuit-m1-de/
│
├── data/
│   ├── bronze/
│   ├── silver/
│   └── gold/
│
├── dbt/
│   └── models/
│       ├── staging/
│       ├── intermediate/
│       └── marts/
│
├── streamlit/
│   └── app.py
│
├── scripts/
├── requirements.txt
├── dbt_project.yml
├── README.md
└── .gitignore
  Installation

Créer l'environnement virtuel :
python -m venv .venv

Activer :
.\.venv\Scripts\Activate.ps1

Installer les dépendances :
pip install -r requirements.txt

Installer les modèles Ollama :
ollama pull gemma3:4b
ollama pull llama3.2:3b

# ▶Exécution :
Vérifier dbt :
dbt debug --profile trivial_pursuit

Exécuter les modèles :
dbt run --profile trivial_pursuit

Lancer le dashboard :
streamlit run streamlit\app.py

Résultat :
Le projet permet de comparer les modèles selon trois dimensions principales :

              PERFORMANCE
                   │
       ┌───────────┼───────────┐
       ↓           ↓           ↓
    Accuracy    Rapidité   Régularité


L'objectif n'est donc pas uniquement de trouver le modèle ayant la meilleure accuracy, mais d'identifier le meilleur compromis entre précision, rapidité et stabilité.

# Perspectives :
tester davantage de modèles 
tester plusieurs prompts 
analyser la longueur des réponses 
améliorer l'analyse de robustesse 
ajouter davantage de visualisations 
ajouter des tests dbt



Auteurs :
Projet réalisé dans le cadre du M1 Data Engineering & Intelligence Artificielle.



Allan Miguel Domguia & SOUFIANE LEMSOURI

