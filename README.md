# Earthquake-Notification-System
## Objective
Given a 100 GB database of cellular data (phone number, date, latitude, longitude), the location and date of an earthquake, find the fastest way to warn people on Japan's coast (1 person warn = 1 INSERT in a result table).

## Technologies used
MongoDB + ElasticSearch or Cassandra + Spark

## Visualization
Using gmap.js
![Tsunami Notification System](http://i61.tinypic.com/2gte0xz.png)

------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------

# PROBLEME
- créer un cluster avec au moins 5 noeuds dans les 5 villes les plus peuplées du Japon
- charger les données dans le Cluster
- les coordonnées d'un tremblement de terre sont fournies
- couper les noeuds présents dans la zone à risque (ie à 500 km de l'épicentre)
- faire un INSERT avec au minimum 'date/heure de réception', 'numéro de téléphone', 'position lors du temblement de terre'
- donner le temps pour avoir prévenu 80% de la population

## Bonus
- suivi en temps réel de la notification
- avertir en premier les personnes les plus proches
- évaluer les performances en faisant varier le nombre de noeuds ou de réplicas
- ajouter une interface graphique
- imaginer des répliques au tremblement de terre

# TO DO LIST:
- faire la même chose avec Cassandra/Spark en local
- comparer, pour un gros fichier, quel est la meilleure solution
- voir comment implanter ça dans AWS

# IMPLEMENTATION

## Possibilité 1: Cassandra
En deux parties, en utilisant Python et Cassandra

### Partie Python
- créer le tunnel entre Python et Cassandra
- insérer manuellement les coordonnées du tremblement de terre
- calculer la zone à risque et en envoyer les coordonnées à Cassandra

### Partie Cassandra
- charger la BDD .csv dans une table 'base'
- la trier par numero de téléphone + date
- créer une deuxième table avec pour clé 'numéro de téléphone' et pour champ 'position' seulement (comme cela la position se met à jour puisque la dernière insérée dans la base écrase les précédente) ou éventuellement un simple 'distance par rapport à la longtiude de la côte la plus proche'...etc 
- éventuellement créer un attribut 'distance par rapport à la côte' et trier sur cet attribut
- faire un insert en filtrant les numéros de téléphone qui sont dans la zone à risque + l'heure d'insert

## Possibilité 2: Cassandra/Spark

## A résoudre:
- dans la base initiale: combien de positions par personne? Combien de personnes?
- combien y a t il de personnes? Pas 120 millions puisque les numéros de téléphone n'ont que 6 chiffres
- réplication entre les différents noeuds: quels facteurs choisir?
- pour l'insert, il faut passer par un csv (externe). 
- Utilisation d'une base graph plutôt?
- utilisation d'un geoHash?

## Résolu:
- est-ce que l'INSERT se fait par rapport à l'ordre de la table insérée? Oui

## Améliorations possibles:
- filtrer directement la latitude et la longitude avec:
WHERE Lmin < latitude AND Lmax < latitude AND lmin < longitude AND lmax < longitude 
- voir si des clauses de ce WHERE sont inutiles (dépend de la géographie du pays)

## Liens utiles (calcul de distances sur une sphère et prise en charge en SQL)
http://janmatuschek.de/LatitudeLongitudeBoundingCoordinates
http://www.movable-type.co.uk/scripts/latlong-db.html
