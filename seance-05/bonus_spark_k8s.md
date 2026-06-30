# Bonus — Spark sur Kubernetes (Kind)

## Démarche

1. Réutilisation du cluster Kind `anfa` créé en séance 3 (`kind get clusters` confirme son existence).
2. Création du namespace `spark` : `kubectl create namespace spark`.
3. Installation de Spark Operator via Helm :
helm repo add spark-operator https://kubeflow.github.io/spark-operator
helm install spark-operator spark-operator/spark-operator --namespace spark --create-namespace

Les pods `spark-operator-controller` et `spark-operator-webhook` démarrent correctement (`Running`).
4. Connexion réseau entre Kind et MinIO : les deux étaient sur des réseaux Docker distincts
   (`kind` en 172.18.x.x, `seance-05_default` en 172.19.x.x). Connexion du conteneur MinIO
   au réseau `kind` avec `docker network connect kind anfa-minio`, le rendant accessible
   en `172.18.0.3:9000` depuis les pods Kubernetes.
5. Création d'une variante du job (`analyse_referentiel_k8s.py`) pointant vers cette IP.
6. Montage du script via un `ConfigMap` Kubernetes (`spark-job-anfa`).
7. Création d'un `ServiceAccount` + `Role` + `RoleBinding` (`spark-rbac.yaml`) pour autoriser
   le driver Spark à créer des pods executors.
8. Rédaction d'un manifeste `SparkApplication` (`spark-application.yaml`) décrivant le job
   en mode `cluster`, avec 1 driver et 2 executors, et les packages `hadoop-aws` / `aws-java-sdk-bundle`
   pour le connecteur S3A.
9. Soumission : `kubectl apply -f spark-application.yaml`.

## Résultat

La `SparkApplication` a été créée côté Kubernetes (`kubectl get sparkapplication` la liste bien),
mais elle n'a jamais été prise en charge par le controller. Le diagnostic via
`kubectl logs deployment/spark-operator-controller` a révélé que l'opérateur démarre avec le
flag `--namespaces=default`, donc il n'observe que le namespace `default`, alors que la
ressource avait été créée dans le namespace `spark`.

Tentative de correction : redéploiement du `ConfigMap`, du RBAC et de la `SparkApplication`
dans le namespace `default`. Malgré cela, le `STATUS` est resté vide et la section `Events`
vide également (`kubectl describe sparkapplication`), sans pod driver créé. Faute de temps
disponible pour pousser plus loin le diagnostic (probable second filtre côté webhook ou
configuration Helm à ajuster avec `--set spark-jobNamespaces={default}` au moment de
l'installation), le bonus a été arrêté à ce stade.

## Conclusion

L'infrastructure Kubernetes (cluster Kind, Spark Operator, RBAC, ConfigMap, manifeste
SparkApplication) a été mise en place avec succès et la cause du blocage a été identifiée
précisément (filtrage de namespace côté opérateur). La correction la plus probable pour une
prochaine tentative serait de réinstaller le chart Helm avec l'option `--set
"spark.jobNamespaces={default}"` (ou `{spark}`) explicitement au moment de l'installation,
plutôt que de tenter un déploiement après coup.

## Réalisé : partiellement (infrastructure en place, exécution du job non aboutie)