# TP — Auditer un jeu de données open data

| | |
|---|---|
| **Semaine** | P1 (S38) · Socle · jeudi 17/09/2026, 15h00–16h30 |
| **Compétence visée** | **C1.1** — Identifier les usages possibles des données et en évaluer la qualité |
| **Niveau visé** | **1 · Imiter** (reproduire la démarche montrée en démo) |
| **Évaluation** | Formative |
| **Outils** | Tableur (Excel ou LibreOffice), navigateur, Git/GitHub |
| **Prérequis** | Tri, filtre, mise en forme conditionnelle (mardi) · dépôt, commit (lundi) |
| **Alimente** | Livrable du vendredi 18/09 : *Repo GitHub + première analyse commentée* |

## Contexte

Vous venez d'être recruté·e comme Data Analyst dans une agence de développement économique du Pas-de-Calais. Avant de lancer toute analyse, votre responsable veut savoir si les données publiques disponibles sont fiables et à quoi elles pourraient servir.

## Choisir son parcours

**Parcours socle** — pour celles et ceux qui découvrent le tableur ou l'analyse de données.
Jeu imposé sur data.gouv.fr : *Prix des carburants en France — flux instantané* (export CSV). Filtrez sur le département 62.

**Parcours +** — pour celles et ceux qui ont déjà manipulé des données.
Jeu libre sur data.gouv.fr ou insee.fr, en lien avec le territoire (Hauts-de-France). Faites en plus l'étape 5.

## Étapes

### 1. Lire la fiche du jeu (15 min)
Complétez le tableau d'identité ci-dessous **avant** d'ouvrir le fichier.

| Élément | Votre réponse |
|---|---|
| **Titre du jeu** | Prix des carburants en France - Flux instantané - v2 |
| **Producteur** | Ministères économiques et financiers (DGCCRF) |
| **URL** | https://www.data.gouv.fr/datasets/prix-des-carburants-en-france-flux-instantane-v2-amelioree |
| **Licence** | Licence Ouverte / Open Licence version 2.0 |
| **Date de dernière mise à jour** | 18 septembre 2026 |
| **Fréquence de mise à jour** | En continu |
| **Couverture géographique et temporelle** | France entière |
| **Format téléchargé** | CSV |
| **Dictionnaire des variables disponible ? (oui/non)** | Oui |

### 2. Ouvrir et décrire (15 min)
- Nombre de lignes et de colonnes. (Pas-de-Calais) 231 stations 47 colonnes
- Pour 5 colonnes au choix : nom, type de valeur (texte, nombre, date), exemple de valeur.
1. `id` | Nombre | Exemple : `62000001`
2. `Adresse` | Texte | Exemple : `15 rue de la Gare`
3. `Prix Gazole` | Nombre | Exemple : `2.25`
4. `Prix Gazole mis à jour le` | Date/Heure | Exemple : `2026-09-18T09:07:28+00:00`
5. `Services proposés` | Texte | Exemple : `Toilettes publiques, Station de gonflage`
- Signalez tout problème d'ouverture (séparateur, accents mal affichés, dates en texte).
- Présence de colonnes complexes au format JSON brut (`horaires`, `services`, `prix`) nécessitant un nettoyage.
- Formatage des codes postaux : la suppression automatique des zéro initiaux par le tableur (ex: `06200` devenant `6200`) crée de la confusion lors des filtres texte.

### 3. Remplir la grille qualité (35 min)
Pour chaque dimension, donnez **au moins un constat chiffré ou un exemple précis** (ligne, colonne, valeur).

| Dimension | Méthode utilisée dans le tableur | Constat (chiffré ou exemple) | Gravité (faible / moyenne / forte) |
|---|---|---|---|
| **Complétude** | Formule `=NB.VIDE` / Filtres | 16 valeurs manquantes identifiées dans la colonne `Prix Gazole` (et 16 stations sans services renseignés). | Moyenne |
| **Exactitude** | Tri croissant / décroissant | Prix du Gazole compris entre 2,25 € et 2,80 € / L. Aucune valeur aberrante (type 0 € ou 99 €). | Faible |
| **Cohérence** | Filtre croisé Carburants disponibles et Prix Gazole | Pas d'incohérence majeure : les stations indiquant le Gazole disponible affichent bien un prix valide. | Faible |
| **Validité** | Inspection visuelle du format | Les colonnes `horaires` et `services` contiennent du texte brut en JSON (`{"@automate-24-24": ""}`), inexploitable sans traitement. | Moyenne |
| **Unicité** | Mise en forme conditionnelle (doublons) | 0 doublon détecté sur les 231 identifiants `id`. | Faible |
| **Fraîcheur** | Tri sur la date de mise à jour | Date la plus récente : 18/09/2026. Date la plus ancienne : 24/07/2026 (certains prix n'ont pas été réactualisés depuis 2 mois). | Forte |

### 4. Proposer des usages (15 min)
Proposez **deux usages** de ce jeu pour l'agence. Pour chacun :
- la question métier à laquelle il répond ;
- la ou les colonnes utilisées ;
- le défaut qualité qui pourrait fausser la réponse.

### Usage 1 : Cartographie comparative des prix du gazole pour les transporteurs
* **Question métier :** Quelles sont les communes du Pas-de-Calais où les tarifs du gazole sont les plus élevés pour les flottes de véhicules professionnels ?
* **Colonnes mobilisées :** `Prix Gazole`, `Ville`, `Code postal`, `latitude`, `longitude`.
* **Défaut de qualité  :** **La fraîcheur**. Des écarts de mise à jour allant jusqu'à deux mois (de juillet à septembre 2026) risquent d'invalider la pertinence des comparaisons tarifaires.

### Usage 2 : Diagnostic de l'attractivité et des équipements des stations du territoire
* **Question métier :** Les zones d'activités du Pas-de-Calais disposent-elles d'une couverture suffisante en automates 24/24 et en services annexes (station de gonflage, lavage, restauration) ?
* **Colonnes mobilisées :** `Code postal`, `Adresse`, `Services proposés`, `Automate 24-24 (oui/non)`.
* **Défaut de qualité  :** **La validité et la complétude**. Les 16 stations sans aucun service renseigné et le format JSON brut inexploitable directement empêchent un inventaire automatique fiable des équipements.

### 5. Parcours + uniquement : croiser deux sources (en autonomie)
Trouvez une seconde source pour un même indicateur (ex. population communale INSEE vs portail régional). Comparez les valeurs sur 5 lignes et expliquez les écarts éventuels.

### 6. Versionner (10 min)
Dans votre dépôt GitHub :
- ajoutez la grille et vos notes dans un fichier `audit_qualite.md` ;
- faites au moins un commit avec un message explicite (ex. `Ajout grille qualité carburants 62`).

## Critères de réussite (C1.1 · niveau 1)

- [ ] La fiche d'identité est complète : source, licence et date de mise à jour sont renseignées.
- [ ] Les 6 dimensions de la grille sont renseignées, chacune avec un constat précis.
- [ ] Au moins deux usages sont proposés et reliés à des colonnes réelles du jeu.
- [ ] Le travail est commité sur GitHub.

## Ressources
- data.gouv.fr — portail national des données publiques
- insee.fr — statistiques officielles
- Slides de la séance : *Panorama de la donnée & open data*
