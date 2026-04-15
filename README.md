# Classification de Variants Génétiques par Machine Learning

> Projet M1 Data & IA — Prédiction de la pathogénicité et de la conflictualité inter-laboratoires sur ClinVar.

## Résumé

Ce projet explore **deux questions distinctes** sur le même dataset ClinVar :

1. **Pathogénicité** : un variant génétique est-il pathogène ou bénin ?
2. **Conflictualité** : les laboratoires cliniques seront-ils en désaccord sur sa classification ?

Dans ClinVar, la base de référence en génétique clinique, **environ 25% des variants ont des classifications contradictoires entre laboratoires**. La pathogénicité est largement traitée par des outils existants (REVEL, ClinPred, CADD), la conflictualité l'est beaucoup moins.

## Résultats clés

| Problème | Meilleur modèle | AUC-ROC | F1 | Comparaison |
|----------|-----------------|---------|-----|-------------|
| **Pathogénicité** (n=28 546) | XGBoost | **0.9962** | 0.9499 | Comparable à REVEL (~0.96) |
| **Conflictualité** (n=65 188) | XGBoost | **0.7949** | 0.5669 | Problème intrinsèquement difficile |

### Résultat le plus marquant — SHAP comparatif

Pour la pathogénicité, CADD est la feature #1 (mean |SHAP| = 2.27). Pour la conflictualité, **CADD tombe au 9e rang** (0.08), supplanté par `af_disagreement` — une feature engineerée mesurant l'écart-type entre les 3 bases populationnelles.

- **Pathogénicité** = sévérité biologique (drivers : CADD, fréquences alléliques, IMPACT)
- **Conflictualité** = incertitude épistémique (drivers : désaccord entre bases, CADD ambiguité, nombre de soumetteurs)

Deux problèmes structurellement différents, avec des drivers orthogonaux.

## Méthodologie

- **Dataset** : 65 188 variants, jointure Kaggle (Kevin Arvai) × variant_summary NCBI
- **Features** : 22 au total (CADD, SIFT, PolyPhen, BLOSUM62, LoFtool, fréquences alléliques ESP/ExAC/TGP, encodings ordinaux, features dérivées)
- **Modèles comparés** : LogReg (L1/L2), KNN, SVM RBF, Random Forest, XGBoost, LightGBM, CatBoost
- **Protocole** : 5-Fold CV stratifié + test set holdout (touché une seule fois)
- **Tests statistiques** : Mann-Whitney U avec correction Bonferroni, DeLong pour comparaison AUC
- **Interprétabilité** : SHAP (TreeSHAP exact)

## Structure du repo

```
variants-genetiques-ml/
├── README.md                              ← vous êtes ici
├── RAPPORT_EDA_Variants_Genetiques.pdf    ← rapport complet (21 sections, 1455 lignes)
│
├── notebooks/
│   ├── 01_pathogenicite.ipynb             ← EDA + modélisation + SHAP pathogénicité
│   └── 02_conflictualite.ipynb            ← modélisation conflictualité + analyse comparative
│
├── docs/
│   ├── RAPPORT_EDA_Variants_Genetiques.md ← rapport en markdown (source du PDF)
│   ├── QUESTIONS_REPONSES.md              ← défense du projet (~1300 lignes de clarifications)
│   ├── SCRIPT_ORAL.md                     ← script de la soutenance (5 min)
│   └── PLAN_TECHNIQUE_Variants_Genetiques.md
│
├── presentation/
│   └── presentation.md                    ← slides Marp (16 slides)
│
├── images/                                 ← SHAP plots, ROC curves, matrices de confusion
│
└── data/
    └── README.md                          ← instructions pour télécharger les données ClinVar
```

## Reproduire les résultats

### 1. Prérequis

```bash
pip install pandas numpy scikit-learn xgboost lightgbm catboost shap matplotlib seaborn jupyter
```

### 2. Télécharger les données

Voir `data/README.md` pour les instructions de téléchargement (datasets publics ClinVar, trop lourds pour être versionnés dans le repo).

### 3. Exécuter les notebooks

```bash
jupyter notebook notebooks/01_pathogenicite.ipynb
jupyter notebook notebooks/02_conflictualite.ipynb
```

## Limites du projet

Volontairement documentées dans le rapport (section 21.3) :

- **Biais de sélection** : entraînement sur concordants uniquement, performance potentiellement surévaluée vs conditions réelles — quantifié : AUC chute à 0.978 sur les conflictuels avec consensus majoritaire
- **Pas d'optimisation Optuna** : hyperparamètres par défaut (le plateau d'AUC à 0.99+ rend le gain marginal)
- **Pas de validation externe** : évalué uniquement sur un split du même dataset ClinVar

## Références principales

- **Richards et al.**, *Genetics in Medicine* (2015) — guidelines ACMG/AMP pour la classification des variants
- **Ioannidis et al.**, *American Journal of Human Genetics* (2016) — REVEL
- **Alirezaie et al.**, *American Journal of Human Genetics* (2019) — ClinPred
- **Lundberg et al.**, *Nature Machine Intelligence* (2020) — TreeSHAP
- **Garber et al.**, *Journal of Molecular Diagnostics* (2016) — conflits de classification dans ClinVar

## Contact

- **Auteur** : Mahé Begnis
- **Formation** : M1 Data & IA
- **Année** : 2026

---

*Ce projet est un travail académique. Les modèles développés ne sont pas certifiés pour un usage clinique. Toute utilisation en contexte médical réel nécessiterait une validation prospective externe et une certification réglementaire (ex: CE-IVD en Europe).*
