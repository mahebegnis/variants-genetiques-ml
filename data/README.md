# Données — Instructions de téléchargement

Les fichiers de données ne sont pas versionnés dans ce repo (trop volumineux pour GitHub). Vous devez les télécharger manuellement avant d'exécuter les notebooks.

## Fichier 1 — ClinVar Conflicting Classifications (Kaggle)

**Source** : Kevin Arvai, Kaggle
**URL** : https://www.kaggle.com/datasets/kevinarvai/clinvar-conflicting
**Taille** : 3.6 MB compressé, 30 MB décompressé

### Téléchargement

1. Connectez-vous à Kaggle
2. Téléchargez `clinvar_conflicting.csv`
3. Placez le fichier dans `data/clinvar_conflicting.csv`

### Structure

- 65 188 variants
- 46 colonnes (CADD, SIFT, PolyPhen, BLOSUM62, LoFtool, fréquences alléliques ESP/ExAC/TGP, annotations VEP, etc.)
- Variable cible `CLASS` : 0 = concordant, 1 = conflictuel

## Fichier 2 — ClinVar variant_summary (NCBI FTP)

**Source** : National Center for Biotechnology Information
**URL** : https://ftp.ncbi.nlm.nih.gov/pub/clinvar/tab_delimited/variant_summary.txt.gz
**Taille** : 435 MB compressé

### Téléchargement

```bash
cd data/
curl -L -o variant_summary.txt.gz \
  https://ftp.ncbi.nlm.nih.gov/pub/clinvar/tab_delimited/variant_summary.txt.gz
```

### Usage

Le notebook de pathogénicité joint ce fichier avec le dataset Kaggle pour récupérer la colonne `ClinicalSignificance` (Pathogenic/Likely_pathogenic/Benign/Likely_benign/VUS/Conflicting).

**Note importante** : ClinVar est mis à jour mensuellement. Les chiffres exacts du rapport correspondent à une extraction de mars 2026. Une version plus récente produira des résultats légèrement différents (ajout de nouvelles soumissions, reclassifications).

## Licences

- **ClinVar Kaggle** (Arvai) : CC0 (domaine public)
- **ClinVar NCBI** : données publiques, usage libre pour recherche

## Vérification

Après téléchargement, votre dossier `data/` doit contenir :

```
data/
├── README.md                       (ce fichier)
├── clinvar_conflicting.csv         (~30 MB)
└── variant_summary.txt.gz          (~435 MB)
```
