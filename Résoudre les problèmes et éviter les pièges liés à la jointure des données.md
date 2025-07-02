# Résoudre les problèmes et éviter les pièges liés à la jointure des données



## Présentation



BigQuery est la base de données d'analyse NoOps, économique et entièrement gérée de Google. Avec BigQuery, vous pouvez interroger plusieurs téraoctets de données sans avoir à gérer d'infrastructure ni faire appel à un administrateur de base de données. Basé sur le langage SQL et le modèle de paiement à l'usage, BigQuery vous permet de vous concentrer sur l'analyse des données pour en dégager des informations pertinentes.



La jointure des tables de données peut vous fournir des renseignements très utiles sur votre ensemble de données. Toutefois, lorsque vous joignez des données, il se peut que vous vous heurtiez à des pièges courants qui peuvent altérer vos résultats. Cet atelier a pour objectif de vous apprendre à éviter ces pièges. Voici tout d'abord les types de jointures :



Jointure croisée : elle combine chaque ligne du premier ensemble de données avec chaque ligne du deuxième ensemble de données, et toutes les combinaisons sont représentées dans les résultats.

Jointure interne : elle nécessite la présence de valeurs de clé dans les deux tables pour que les enregistrements apparaissent dans la table de résultats. Les enregistrements n'apparaissent dans la fusion que s'il existe des correspondances dans les deux tables pour les valeurs de clé.

Jointure gauche : chaque ligne de la table de gauche apparaît dans les résultats, que la table de droite contienne ou non des correspondances.

Jointure droite : l'inverse d'une jointure gauche. Chaque ligne de la table de droite apparaît dans les résultats, que la table de gauche contienne ou non des correspondances.

Pour en savoir plus, reportez-vous à la page sur les jointures.



Vous allez utiliser un ensemble de données d'e-commerce comprenant des millions d'enregistrements Google Analytics pour le Google Merchandise Store, chargé dans BigQuery. Vous disposez d'une copie de cet ensemble de données pour cet atelier, et vous allez explorer les champs et lignes qu'il contient afin d'obtenir des informations.



Pour en savoir plus sur la syntaxe permettant de suivre et de mettre à jour vos requêtes, consultez la section Syntaxe des requêtes en SQL standard.



Objectifs de l'atelier

Dans cet atelier, vous allez apprendre à effectuer les tâches suivantes :



Utiliser BigQuery pour rechercher et résoudre les problèmes de lignes en double dans un ensemble de données

Créer des jointures entre les tables de données

Choisir entre différents types de jointures

Préparation

Avant de cliquer sur le bouton "Démarrer l'atelier"

Lisez ces instructions. Les ateliers sont minutés, et vous ne pouvez pas les mettre en pause. Le minuteur, qui démarre lorsque vous cliquez sur Démarrer l'atelier, indique combien de temps les ressources Google Cloud resteront accessibles.



Cet atelier pratique vous permet de suivre les activités dans un véritable environnement cloud, et non dans un environnement de simulation ou de démonstration. Des identifiants temporaires vous sont fournis pour vous permettre de vous connecter à Google Cloud le temps de l'atelier.





## Tâche 1 : Créer un ensemble de données pour stocker vos tables



Dans votre projet BigQuery, créez un ensemble de données intitulé ecommerce.



Cliquez sur les trois points à côté de l'ID de votre projet et sélectionnez Créer un ensemble de données.

Option Créer un ensemble de données mise en évidence



La boîte de dialogue Créer un ensemble de données s'ouvre.



Définissez l'ID de l'ensemble de données sur ecommerce.



Laissez la valeur par défaut des autres options et cliquez sur Créer un ensemble de données.



Dans le volet de gauche, vous voyez une table ecommerce sous votre projet.



Cliquez sur Vérifier ma progression pour valider l'objectif.



Success: Create a new dataset

Créer un ensemble de données

Success: Create a new dataset

## Tâche 2 : Épingler le projet de l'atelier dans BigQuery



Scénario : votre équipe vous fournit un nouvel ensemble de données recensant le stock de chacun de vos produits en vente sur votre site d'e-commerce. Vous souhaitez vous familiariser avec les produits du site Web et avec les champs que vous pouvez utiliser pour effectuer une jointure avec d'autres ensembles de données.



Le projet avec le nouvel ensemble de données s'intitule data-to-insights.



Dans la console Google Cloud, accédez au menu de navigation (Icône du menu de navigation), puis cliquez sur BigQuery.

Le message "Bienvenue sur BigQuery dans la console Cloud" s'affiche.



Remarque : Il contient un lien vers le guide de démarrage rapide et les nouveautés de l'interface utilisateur.

Cliquez sur OK.



Les ensembles de données publics BigQuery ne sont pas affichés par défaut. Pour ouvrir le projet d'ensembles de données publics, copiez data-to-insights (vous le collerez dans une boîte de dialogue à l'étape suivante).



Cliquez sur Ajouter > Ajouter un projet aux favoris en saisissant son nom, puis collez le nom "data-to-insights".



Cliquez sur Ajouter aux favoris.



Le projet data-to-insights apparaît désormais dans la section Explorateur.



## Tâche 3 : Examiner les champs



Ensuite, familiarisez-vous avec les produits et les champs du site Web que vous pouvez utiliser pour créer des requêtes d'analyse de l'ensemble de données.



Dans le volet de gauche de la section "Ressources", accédez à data-to-insights > ecommerce > all\_sessions\_raw.



À droite, sous l'éditeur de requête, cliquez sur l'onglet Schéma pour afficher les champs et des informations sur chacun d'eux.



## Tâche 4 : Identifier un champ de clé dans votre ensemble de données d'e-commerce



Examinez de plus près les produits et les champs. Vous pourrez ainsi vous familiariser avec les produits du site Web et avec les champs que vous pouvez utiliser pour effectuer une jointure avec d'autres ensembles de données.



Examiner les enregistrements

Dans cette section, vous verrez combien de noms de produits et de SKU figurent sur votre site Web, et si certains de ces champs sont uniques.



Recherchez combien de noms de produits et de SKU sont présents sur le site Web. Copiez et collez la requête ci-dessous dans l'éditeur BigQuery :

\#standardSQL

\# how many products are on the website?

SELECT DISTINCT

productSKU,

v2ProductName

FROM `data-to-insights.ecommerce.all\_sessions\_raw`

Copié

Cliquez sur Exécuter.

Consultez les résultats de la pagination dans la console pour connaître le nombre total d'enregistrements renvoyés.



Résultats de la requête avec la pagination mise en évidence





How many rows of product data are returned?

2,205 products and SKUs

check

2,273 products and SKUs

1,925 products and SKUs



Les résultats signifient-ils pour autant qu'il existe un aussi grand nombre de SKU uniques ? L'une des premières requêtes que vous effectuerez en tant qu'analyste de données consistera à vérifier le caractère unique des valeurs de vos données.



Effacez la requête précédente et exécutez la requête ci-dessous pour lister le nombre de SKU différents à l'aide de la clause DISTINCT :

\#standardSQL

\# find the count of unique SKUs

SELECT

DISTINCT

productSKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw`

Copié



How many DISTINCT SKUs are returned?

2,273 distinct SKUs

check

1,909 distinct SKUs

119 distinct SKUs





There are fewer DISTINCT SKUs than the SKU \& Product Name query had before. Why do you think that is?

close

The first query showed that only one Product Name can belong to a SKU.

The first query was excluding some Product Names.

check

The first query also returned Product Name. It appears multiple Product Names can have the same SKU.



Examiner la relation entre le SKU et le nom

À présent, déterminez quels produits sont associés à plusieurs SKU et quels SKU s'appliquent à plus d'un nom de produit.



Effacez la requête précédente et exécutez la requête ci-dessous pour déterminer si certains noms de produits sont associés à plus d'un SKU. La fonction STRING\_AGG() permet de regrouper tous les SKU associés à un nom de produit en une liste de valeurs séparées par des virgules.

SELECT

&nbsp; v2ProductName,

&nbsp; COUNT(DISTINCT productSKU) AS SKU\_count,

&nbsp; STRING\_AGG(DISTINCT productSKU LIMIT 5) AS SKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw`

&nbsp; WHERE productSKU IS NOT NULL

&nbsp; GROUP BY v2ProductName

&nbsp; HAVING SKU\_count > 1

&nbsp; ORDER BY SKU\_count DESC

Copié

Cliquez sur Exécuter.

Voici ce que vous obtiendrez :



Résultats de la requête





Do some product names have more than one SKU? Look at the query results to confirm

No

check

Yes





Which product has the most SKUs associated?

Android Womens Short Sleeve Badge Tee Dark Heather

Google Sunglasses

check

Waze Womens Typography Short Sleeve Tee



Le catalogue du site Web d'e-commerce indique que chaque nom de produit peut avoir plusieurs options (taille, couleur, etc.), qui sont vendues avec un SKU distinct.



Vous avez constaté qu'un produit était associé à 12 SKU. Qu'en est-il d'un seul SKU ? Peut-il être associé à plus d'un produit ?



Pour le savoir, effacez la requête précédente et exécutez la requête ci-dessous :

SELECT

&nbsp; productSKU,

&nbsp; COUNT(DISTINCT v2ProductName) AS product\_count,

&nbsp; STRING\_AGG(DISTINCT v2ProductName LIMIT 5) AS product\_name

FROM `data-to-insights.ecommerce.all\_sessions\_raw`

&nbsp; WHERE v2ProductName IS NOT NULL

&nbsp; GROUP BY productSKU

&nbsp; HAVING product\_count > 1

&nbsp; ORDER BY product\_count DESC

Copié

Résultats de la requête



Remarque : Essayez de remplacer STRING\_AGG() par ARRAY\_AGG(). Impressionnant non ? De façon native, BigQuery accepte les valeurs de tableaux imbriqués. Pour en savoir plus, consultez le guide d'utilisation des tableaux.



When you look at the query results, are there single SKU values with more than one product name associated? What do you notice about those product names?

No, the Product SKUs match the Product Names one-for-one.

check

Yes, most of the product names are similar but not exactly the same.



Dans la section suivante, vous comprendrez pourquoi cette relation de données plusieurs à plusieurs est problématique.



Cliquez sur Vérifier ma progression pour valider l'objectif.



Success: Identify a key field in your ecommerce dataset

Identifier un champ de clé dans votre ensemble de données d'e-commerce

Success: Identify a key field in your ecommerce dataset

## Tâche 5 : Piège : clé non unique



Dans le suivi de l'inventaire, un SKU est conçu pour identifier un produit de façon unique. Ici, il constitue la base de votre condition de jointure lorsque vous joignez d'autres tables. Comme nous allons le voir, une clé non unique peut provoquer de graves problèmes pour les données.



Écrivez une requête pour identifier tous les noms de produits associés au SKU 'GGOEGPJC019099'.

Solution possible :



SELECT DISTINCT

&nbsp; v2ProductName,

&nbsp; productSKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw`

WHERE productSKU = 'GGOEGPJC019099'

Copié

Cliquez sur Exécuter.

v2ProductName



productSKU



7\&quot; Dog Frisbee



GGOEGPJC019099



7" Dog Frisbee



GGOEGPJC019099



Google 7-inch Dog Flying Disc Blue



GGOEGPJC019099





What do you notice about the product names?

They are exactly the same.

check

They are mostly the same except for a few characters.



D'après les résultats de la requête, il existe apparemment trois noms différents pour le même produit. Dans l'exemple, nous notons un caractère spécial dans l'un des noms et une formulation légèrement différente pour un autre :



Joindre des données du site Web à la liste d'inventaire de vos produits

Découvrez maintenant l'effet d'une jointure sur un ensemble de données contenant plusieurs produits associés à un même SKU. Explorez d’abord l'ensemble de données de l’inventaire de produits (la table products) pour voir si ce SKU est unique.



Effacez la requête précédente et exécutez la requête suivante :

SELECT

&nbsp; SKU,

&nbsp; name,

&nbsp; stockLevel

FROM `data-to-insights.ecommerce.products`

WHERE SKU = 'GGOEGPJC019099'

Copié



Is the SKU unique in the product inventory dataset?

check

Yes, just one record is returned.

No, there are duplicate SKUs in the inventory dataset.





How many dog frisbees do you have in inventory?

check

154

0

10,540



Piège lié aux jointures : création involontaire d'une relation de SKU de type plusieurs à un

Vous disposez maintenant de deux ensembles de données : un pour le niveau d'inventaire et un autre pour les analyses de site Web. Joignez l’ensemble de données de l’inventaire aux noms de produits et SKU de votre site Web pour associer le niveau d'inventaire à chaque produit en vente sur le site Web.



Effacez la requête précédente et exécutez la requête suivante :

SELECT DISTINCT

&nbsp; website.v2ProductName,

&nbsp; website.productSKU,

&nbsp; inventory.stockLevel

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

JOIN `data-to-insights.ecommerce.products` AS inventory

&nbsp; ON website.productSKU = inventory.SKU

&nbsp; WHERE productSKU = 'GGOEGPJC019099'

Copié



What happens when you join the website table and the product inventory table on SKU? Do you now have inventory stock levels for the product?

No, there is no inventory data, the join did not work.

Yes, there is inventory data and everything looks fine.

check

Yes, there are inventory levels but the stockLevel is showing three times (one for each record).



Ensuite, développez la requête précédente pour calculer le stock disponible pour chaque produit à l'aide de la fonction SUM.



Effacez la requête précédente et exécutez la requête suivante :

WITH inventory\_per\_sku AS (

&nbsp; SELECT DISTINCT

&nbsp;   website.v2ProductName,

&nbsp;   website.productSKU,

&nbsp;   inventory.stockLevel

&nbsp; FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

&nbsp; JOIN `data-to-insights.ecommerce.products` AS inventory

&nbsp;   ON website.productSKU = inventory.SKU

&nbsp;   WHERE productSKU = 'GGOEGPJC019099'

)



SELECT

&nbsp; productSKU,

&nbsp; SUM(stockLevel) AS total\_inventory

FROM inventory\_per\_sku

GROUP BY productSKU

Copié



Is the dog Frisbee properly showing a stock level of 154?

check

No, it is now at 462 showing three times (one for each record!)

Yes, it is at 154



Malheureusement, le résultat correspond à trois fois 154, c'est-à-dire 462 au total. Il s'agit d'un triple comptage. C'est ce qu'on appelle une jointure croisée involontaire (nous reviendrons plus tard sur ce sujet).



Cliquez sur Vérifier ma progression pour valider l'objectif.



Success: Pitfall: non-unique key

Piège : clé non unique

Success: Pitfall: non-unique key

## Tâche 6 : Solution pour éviter le piège lié aux jointures : utiliser des SKU distincts avant de procéder à la jointure



Quelles sont les options qui permettraient de résoudre votre problème de triple comptage ? Vous devez tout d'abord sélectionner uniquement des SKU distincts sur le site Web avant de joindre d'autres ensembles de données.



Vous savez que plusieurs noms de produit (ex. : 7" Dog Frisbee) peuvent partager un seul SKU.



Regroupez tous les noms possibles dans un tableau :

SELECT

&nbsp; productSKU,

&nbsp; ARRAY\_AGG(DISTINCT v2ProductName) AS push\_all\_names\_into\_array

FROM `data-to-insights.ecommerce.all\_sessions\_raw`

WHERE productSKU = 'GGOEGAAX0098'

GROUP BY productSKU

Copié

Maintenant, au lieu d'avoir une ligne pour chaque nom de produit, vous avez uniquement une ligne pour chaque SKU unique.



Si vous vouliez dédupliquer les noms de produit, vous pourriez même limiter le tableau comme suit :

SELECT

&nbsp; productSKU,

&nbsp; ARRAY\_AGG(DISTINCT v2ProductName LIMIT 1) AS push\_all\_names\_into\_array

FROM `data-to-insights.ecommerce.all\_sessions\_raw`

WHERE productSKU = 'GGOEGAAX0098'

GROUP BY productSKU

Copié

Piège lié aux jointures : perte d'enregistrements de données après une jointure

Vous êtes maintenant prêt à refaire la jointure de l'ensemble de données de votre inventaire.



Effacez la requête précédente et exécutez la requête suivante :

\#standardSQL

SELECT DISTINCT

website.productSKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

JOIN `data-to-insights.ecommerce.products` AS inventory

ON website.productSKU = inventory.SKU

Copié



How many records were returned? All 1,909 distinct SKUs?

check

No, just 1,090 records

Yes, all 1,909 records



Il semblerait que 819 SKU aient été perdus après la jointure des ensembles de données. Enquêtez sur cette disparition en affinant vos champs (une colonne SKU de chaque ensemble de données) :



Effacez la requête précédente et exécutez la requête suivante :

\#standardSQL

\# pull ID fields from both tables

SELECT DISTINCT

website.productSKU AS website\_SKU,

inventory.SKU AS inventory\_SKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

JOIN `data-to-insights.ecommerce.products` AS inventory

ON website.productSKU = inventory.SKU

\# IDs are present in both tables, how can you dig deeper?

Copié

Il semblerait que les SKU apparaissent dans les deux ensembles de données après la jointure pour ces 1 090 enregistrements. Comment trouver les enregistrements manquants ?



Solution pour éviter le piège lié aux jointures : sélectionner le bon type de jointure et filtrer les valeurs NULL

Le type de jointure par défaut est INNER JOIN (JOINTURE INTERNE), qui renvoie des enregistrements uniquement s'il existe une correspondance de SKU dans les tables de gauche et de droite qui ont été jointes.



Reprenez la requête précédente pour employer un autre type de jointure. Vous pourrez ainsi inclure tous les enregistrements de la table du site Web, qu'il y ait ou non une correspondance avec un enregistrement de SKU de l'inventaire. Types de jointures possibles : INNER JOIN (JOINTURE INTERNE), LEFT JOIN (JOINTURE GAUCHE), RIGHT JOIN (JOINTURE DROITE), FULL JOIN (JOINTURE COMPLÈTE), CROSS JOIN (JOINTURE CROISÉE).

Solution possible :



\#standardSQL

\# the secret is in the JOIN type

\# pull ID fields from both tables

SELECT DISTINCT

website.productSKU AS website\_SKU,

inventory.SKU AS inventory\_SKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

LEFT JOIN `data-to-insights.ecommerce.products` AS inventory

ON website.productSKU = inventory.SKU

Copié

Cliquez sur Exécuter.

À l'aide du type LEFT JOIN (JOINTURE GAUCHE), vous obtenez l'intégralité des 1 909 SKU d'origine du site Web dans vos résultats.





True or False: Many inventory SKU values are NULL.

check

True

False



Combien de SKU manque-t-il dans l'inventaire de vos produits ?



Saisissez une requête pour filtrer les valeurs NULL de la table de l'inventaire.

Solution possible :



\#standardSQL

\# find product SKUs in website table but not in product inventory table

SELECT DISTINCT

website.productSKU AS website\_SKU,

inventory.SKU AS inventory\_SKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

LEFT JOIN `data-to-insights.ecommerce.products` AS inventory

ON website.productSKU = inventory.SKU

WHERE inventory.SKU IS NULL

Copié

Cliquez sur Exécuter.

Question : combien de produits manque-t-il ?



Réponse : il manque 819 produits (SKU IS NULL \[SKU est NULL]) dans l'ensemble de données de l'inventaire.



Effacez la requête précédente et exécutez la requête ci-dessous pour confirmer l'utilisation de l'un des SKU spécifiques de l'ensemble de données du site Web :

\#standardSQL

\# you can even pick one and confirm

SELECT \* FROM `data-to-insights.ecommerce.products`

WHERE SKU = 'GGOEGATJ060517'

\# query returns zero results

Copié



Why might the product inventory dataset be missing SKUs?

check

Some SKUs could be digital products that you do not store in warehouse inventory

close

Old products you sold in past website orders are no longer offered in current inventory

close

There is legitimate missing data from inventory and should be tracked

close

All of the above



Maintenant, que se passe-t-il si l'on inverse la situation ? Y a-t-il des produits qui se trouvent dans l'ensemble de données de l'inventaire de produits, mais qui sont absents du site Web ?



Saisissez une requête en utilisant un autre type de jointure pour en savoir plus.

Solution possible :



\#standardSQL

\# reverse the join

\# find records in website but not in inventory

SELECT DISTINCT

website.productSKU AS website\_SKU,

inventory.SKU AS inventory\_SKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

RIGHT JOIN `data-to-insights.ecommerce.products` AS inventory

ON website.productSKU = inventory.SKU

WHERE website.productSKU IS NULL

Copié

Cliquez sur Exécuter.

Réponse : Oui. Il manque deux SKU dans l'ensemble de données du site Web.



Ensuite, ajoutez d'autres champs de l'ensemble de données de l'inventaire de produits pour afficher plus de détails.



Effacez la requête précédente et exécutez la requête suivante :

\#standardSQL

\# what are these products?

\# add more fields in the SELECT STATEMENT

SELECT DISTINCT

website.productSKU AS website\_SKU,

inventory.\*

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

RIGHT JOIN `data-to-insights.ecommerce.products` AS inventory

ON website.productSKU = inventory.SKU

WHERE website.productSKU IS NULL

Copié

Pourquoi les produits ci-dessous seraient-ils absents de l'ensemble de données du site Web d'e-commerce ?



website\_SKU



SKU



name



orderedQuantity



stockLevel



restockingLeadTime



sentimentScore



sentimentMagnitude



null



GGOBJGOWUSG69402



USB wired soundbar - in store only



10



15



2



1



1



null



GGADFBSBKS42347



PC gaming speakers



0



100



1



null



null



Réponses possibles :



Un nouveau produit (aucune commande, rien sous sentimentScore) et un produit "in store only" (disponible en magasin uniquement).

Un nouveau produit avec 0 commande

Pourquoi le nouveau produit n'apparaîtrait-il pas dans l'ensemble de données de votre site Web ?



L'ensemble de données du site Web répertorie les commandes passées des clients. Les nouveaux produits qui n'ont jamais été vendus n'apparaîtront pas dans les analyses Web tant qu'ils ne seront pas vus ou achetés.

Remarque : En règle générale, on ne fait pas de jointures RIGHT JOIN (JOINTURE DROITE) dans les requêtes de production. On fait simplement une jointure LEFT JOIN (JOINTURE GAUCHE) et on change l'ordre des tables.

Comment obtenir une requête répertoriant tous les produits manquants sur le site Web ou dans l'inventaire ?



Saisissez une requête en utilisant un autre type de jointure.

Solution possible :



\#standardSQL

SELECT DISTINCT

website.productSKU AS website\_SKU,

inventory.SKU AS inventory\_SKU

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

FULL JOIN `data-to-insights.ecommerce.products` AS inventory

ON website.productSKU = inventory.SKU

WHERE website.productSKU IS NULL OR inventory.SKU IS NULL

Copié

Cliquez sur Exécuter.

Vous obtenez 819 + 2 = 821 SKU.



LEFT JOIN + RIGHT JOIN = FULL JOIN. Cette opération renvoie tous les enregistrements des deux tables, qu'il y ait ou non correspondance entre les clés de jointure. Ensuite, vous filtrez les résultats pour identifier les enregistrements pour lesquels il n'y a pas de correspondance d'un côté ou de l'autre.



Piège lié aux jointures : jointure croisée involontaire

Ne pas savoir quelle est la relation entre les clés de table de données (1:1, 1:N, N:N) peut générer des résultats inattendus et réduire considérablement les performances des requêtes.



Le dernier type de jointure est CROSS JOIN (JOINTURE CROISÉE).



Créez une table avec un pourcentage de remise valable sur l'ensemble du site que vous souhaitez appliquer à tous les produits de la catégorie "Clearance" (Liquidation).



Effacez la requête précédente et exécutez la requête suivante :

\#standardSQL

CREATE OR REPLACE TABLE ecommerce.site\_wide\_promotion AS

SELECT .05 AS discount;

Copié

Dans le volet de gauche, site\_wide\_promotion est désormais répertorié dans la section "Ressources" sous votre projet et ensemble de données.



Effacez la requête précédente et exécutez la requête suivante pour savoir combien de produits sont en liquidation :

SELECT DISTINCT

productSKU,

v2ProductCategory,

discount

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

CROSS JOIN ecommerce.site\_wide\_promotion

WHERE v2ProductCategory LIKE '%Clearance%'

Copié



How many products are on clearance?

52

0

check

82

91



Remarque : Pour une jointure croisée, vous remarquerez qu'il n'y a pas de condition de jointure (ex. : ON ou USING). Le champ est simplement multiplié en fonction du premier ensemble de données, ce qui correspond ici à une réduction de 0,05 pour tous les articles.

Voyons quelles sont les conséquences de l'ajout involontaire de plusieurs enregistrements dans la table de la remise.



Effacez la requête précédente et exécutez la requête suivante pour insérer deux autres enregistrements dans la table de la promotion :

INSERT INTO ecommerce.site\_wide\_promotion (discount)

VALUES (.04),

&nbsp;      (.03);

Copié

Examinons ensuite les valeurs de données de la table de la promotion.



Effacez la requête précédente et exécutez la requête suivante :

SELECT discount FROM ecommerce.site\_wide\_promotion

Copié

Combien d'enregistrements ont été renvoyés ?



Réponse : 3



Que se passe-t-il lorsque vous appliquez à nouveau la remise sur les 82 produits en liquidation ?



Effacez la requête précédente et exécutez la requête suivante :

SELECT DISTINCT

productSKU,

v2ProductCategory,

discount

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

CROSS JOIN ecommerce.site\_wide\_promotion

WHERE v2ProductCategory LIKE '%Clearance%'

Copié

Combien de produits sont renvoyés ?



Réponse : au lieu de 82, vous en obtenez maintenant 246, ce qui représente plus d'enregistrements que n'en contient votre table d'origine.



Penchons-nous sur la cause sous-jacente en examinant un SKU.



Effacez la requête précédente et exécutez la requête suivante :

\#standardSQL

SELECT DISTINCT

productSKU,

v2ProductCategory,

discount

FROM `data-to-insights.ecommerce.all\_sessions\_raw` AS website

CROSS JOIN ecommerce.site\_wide\_promotion

WHERE v2ProductCategory LIKE '%Clearance%'

AND productSKU = 'GGOEGOLC013299'

Copié

Quel a été l'effet de la jointure croisée ?



Réponse : Dans la mesure où il existe trois codes de réduction à associer, vous multipliez l'ensemble de données d'origine par 3.



Remarque : Ce comportement ne se limite pas aux jointures croisées. Avec une jointure normale, il est possible de croiser involontairement des jointures lorsque les relations de données sont de type plusieurs à plusieurs, ce qui peut facilement renvoyer des millions voire des milliards d'enregistrements.

Vous devez donc connaître les relations entre vos données avant de procéder à une jointure et ne pas partir du principe que les clés sont uniques.



Cliquez sur Vérifier ma progression pour valider l'objectif.



Success: Join pitfall solution

Solution pour éviter le piège lié aux jointures

Success: Join pitfall solution

