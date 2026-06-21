# Rendu - Séance 2

**Nom et prénom :** BANIZI Gnimdou David
**Identifiant GitHub :** BANIZI
**Date de soumission :** 21/06/2026

## Résumé de la séance
Durant cette séance, j'ai écrit mon premier Dockerfile pour conteneuriser
un script PySpark analysant le référentiel Anfa. J'ai construit l'image
Docker, observé le mécanisme de cache, puis orchestré un stack à 3 services
(MinIO + Jupyter + image custom) avec Docker Compose. Enfin, j'ai exploré
les données du bucket anfa-raw depuis un notebook Jupyter via boto3 et pandas.

## Étapes principales
1. Écriture du Dockerfile et construction de l'image `anfa-analyse:v1` (taille observée : 1.17 Go).
2. Mise en place du `.dockerignore` et observation du cache de Docker.
3. Écriture du `docker-compose.yml` orchestrant MinIO, Jupyter, et l'image custom.
4. Création du notebook `exploration_minio.ipynb` qui lit les données depuis MinIO via boto3 et pandas.

## Captures d'écran

### docker compose ps
![docker compose ps](captures/docker-ps.png)

### Notebook Jupyter
![Notebook Jupyter - Cellules 1 à 3](captures/jupyter-pandas-1.png)
![Notebook Jupyter - Cellules 4 et 5](captures/jupyter-pandas-2.png)
![Notebook Jupyter - DataFrame](captures/jupyter-pandas-3.png)
![Notebook Jupyter - Top 3](captures/jupyter-pandas-4.png)

## Bonus multi-stage (optionnel)
Réalisé. Construction de l'image avec `Dockerfile.multistage` (build en 2 étapes).
- Image v1 (standard) : 1.17 GB
- Image v2-multistage : 1.17 GB
- Gain : 0 MB — résultat attendu pour PySpark qui dépend de Java.
  Le gain multi-stage est spectaculaire pour des apps compilées (Go, Rust)
  mais limité ici car Java reste nécessaire dans les deux étapes.

## Réponses aux exercices d'application
À compléter dès réception de l'énoncé.

## Difficultés rencontrées
- Conflit de nom de conteneur `anfa-minio` entre la séance 1 et la séance 2 : résolu avec `docker rm -f anfa-minio`.
- Le bucket MinIO et la clé applicative ont dû être recréés via `mc` dans le terminal car le volume était neuf.
- Le dossier `work/` de Jupyter n'était pas accessible via l'URL : le notebook a été créé directement dans `/` et s'est bien synchronisé avec `seance-02/notebooks/`.