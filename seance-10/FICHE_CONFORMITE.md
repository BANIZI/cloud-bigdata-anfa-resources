# Fiche de conformité — Application mobile passagers Anfa

> Gabarit fourni. Complétez chaque section **en 2-4 lignes**, en vous appuyant sur le CM.
> Il n'y a pas de "bonne réponse" unique sur certains points — l'important est le raisonnement.


## 1. Finalité du traitement
La collecte sert un seul objectif : proposer le trajet le plus proche (GPS) et gérer l'abonnement du passager (historique mobile money), le numéro de téléphone servant d'identifiant de compte. Conformément au principe de finalité du RGPD, ces données ne doivent pas être réutilisées pour un autre usage (revente publicitaire, profilage commercial) sans nouvelle base légale.

## 2. Données collectées et leur sensibilité
Trois données : position GPS, historique de paiements mobile money, numéro de téléphone. La plus sensible est l'historique de paiements mobile money : c'est à la fois une donnée personnelle identifiante et une donnée financière, dont la fuite expose le passager à un risque de fraude bien plus lourd qu'une simple localisation.

## 3. Base légale applicable
Le numéro de téléphone et la position GPS relèvent de la Loi n° 2019-014 togolaise relative à la protection des données à caractère personnel (l'équivalent togolais du RGPD). L'historique de paiements mobile money relève en plus de la Loi n° 2017-007 (modifiée par la Loi n° 2023-012) sur les transactions électroniques. Ces deux textes s'appliquent simultanément à cette donnée : elle identifie une personne (loi 2019-014) et constitue un paiement électronique (loi 2017-007/2023-012).

## 4. Durée de conservation
Par principe de minimisation, la position GPS ne devrait être conservée que le temps du trajet en cours, puis supprimée ou anonymisée. L'historique de paiements devrait être gardé uniquement pour la durée de l'abonnement actif, plus une période limitée imposée par les obligations comptables/financières — jamais indéfiniment.

## 5. Hébergement et souveraineté
Ces données doivent être hébergées localement (comme le principe 100% open source et auto-hébergé suivi depuis la séance 1 : MinIO, Postgres, etc.), afin de rester sous juridiction togolaise et de garantir la souveraineté des données. Un hébergement chez un cloud américain exposerait ces données au Patriot Act, qui permet aux autorités américaines d'y accéder même si les serveurs sont physiquement hors des États-Unis — un cas d'extraterritorialité qui complexifierait la conformité avec la loi 2019-014.

## 6. Droit des personnes concernées
Oui, un passager doit pouvoir exercer son droit à l'oubli et demander la suppression de ses données. Techniquement, avec l'architecture actuelle d'Anfa (données dispersées entre plusieurs systèmes : stockage objet, bases de métadonnées, artefacts MLflow, logs de monitoring), ce serait difficile à réaliser rapidement : il n'existe pas aujourd'hui de mécanisme centralisé de suppression en cascade, ce qui va à l'encontre du principe de privacy by design — cette capacité aurait dû être pensée dès la conception du pipeline, pas ajoutée après coup.
