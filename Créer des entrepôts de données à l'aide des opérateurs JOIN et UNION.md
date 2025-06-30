# Créer des entrepôts de données à l'aide des opérateurs JOIN et UNION

# experiment



## Présentation



BigQuery est la base de données d'analyse NoOps, économique et entièrement gérée de Google. Avec BigQuery, vous pouvez interroger plusieurs téraoctets de données sans avoir à gérer d'infrastructure ni faire appel à un administrateur de base de données. Basé sur le langage SQL et le modèle de paiement à l'usage, BigQuery vous permet de vous concentrer sur l'analyse des données pour en dégager des informations pertinentes.



Vous allez utiliser un ensemble de données d'e-commerce qui comprend des millions d'enregistrements Google Analytics issus du Google Merchandise Store, et explorer les champs et lignes qu'il contient afin d'obtenir des insights.



Cet atelier va vous apprendre à créer des tables de rapports à l'aide des opérateurs SQL JOIN et UNION.



Scénario : votre équipe marketing vous a fourni, à vous et votre équipe de data scientists, tous les avis sur les produits recueillis sur votre site Web d'e-commerce. Vous collaborez avec elle pour créer dans BigQuery un entrepôt de données qui regroupe des données des trois sources suivantes :



Données d'e-commerce du site Web

Niveau des stocks et délais de livraison des produits

Analyse des sentiments dans les avis sur les produits



## Objectifs de l'atelier



Dans cet atelier, vous allez apprendre à effectuer les tâches suivantes :



Explorer les nouvelles données d'e-commerce sur l'analyse des sentiments

Joindre des ensembles de données et créer des tables

Ajouter des données historiques à l'aide d'unions et de caractères génériques de table

Préparation

Avant de cliquer sur le bouton "Démarrer l'atelier"

Lisez ces instructions. Les ateliers sont minutés, et vous ne pouvez pas les mettre en pause. Le minuteur, qui démarre lorsque vous cliquez sur Démarrer l'atelier, indique combien de temps les ressources Google Cloud resteront accessibles.



Cet atelier pratique vous permet de suivre les activités dans un véritable environnement cloud, et non dans un environnement de simulation ou de démonstration. Des identifiants temporaires vous sont fournis pour vous permettre de vous connecter à Google Cloud le temps de l'atelier.





## Tâche 1 : Créer un ensemble de données pour stocker vos tables



Pour commencer, créez dans BigQuery un ensemble de données intitulé ecommerce pour stocker vos tables.



Dans le volet de gauche, cliquez sur le nom de votre projet BigQuery (qwiklabs-gcp-xxxx).



* Cliquez sur les trois points à côté du nom de votre projet, puis sur Créer un ensemble de données.



* La boîte de dialogue Créer un ensemble de données s'ouvre.



* Définissez le champ ID de l'ensemble de données sur ecommerce et conservez la valeur par défaut de toutes les autres options.



* Cliquez sur Créer un ensemble de données.





## Tâche 2 : Explorer l'ensemble de données de sentiments sur les produits



Votre équipe de data scientists a analysé la totalité des avis sur vos produits à l'aide de l'API, et vous a fourni un score et une magnitude de sentiment moyens pour chacun de vos produits.



Le projet portant sur l'ensemble de données de votre équipe marketing se nomme data-to-insights. Les ensembles de données publics BigQuery ne sont pas affichés par défaut dans BigQuery. Les requêtes de cet atelier vont utiliser l'ensemble de données data-to-insights, même s'il n'est pas visible.



Pour commencer, faites une copie de la table créée par l'équipe de data scientists afin de la lire :

create or replace TABLE ecommerce.products AS

SELECT

\*

FROM

`data-to-insights.ecommerce.products`

Copié



Remarque : Cette table vous est fournie uniquement pour que vous puissiez la lire. Les requêtes de cet atelier utiliseront le projet data-to-insights.

Cliquez sur l'ensemble de données ecommerce pour afficher la table products.

Examiner les données à l'aide des onglets "Aperçu" et "Schéma"

Accédez à l'ensemble de données ecommerce > products et cliquez sur l'onglet Aperçu pour afficher les données.



How many Aluminum Handy Emergency Flashlights have been ordered?

check

66

90

85

close

0



Cliquez sur l'onglet Schéma.



What data type are the sentimentScore and sentimentMagnitude fields?

check

FLOAT

RECORD

INTERGER

STRING



Créer une requête qui affiche les cinq principaux produits recueillant le sentiment le plus positif

Dans l'éditeur de requête, rédigez votre requête SQL.

Solution possible :



SELECT

&nbsp; SKU,

&nbsp; name,

&nbsp; sentimentScore,

&nbsp; sentimentMagnitude

FROM

&nbsp; `data-to-insights.ecommerce.products`

ORDER BY

&nbsp; sentimentScore DESC

LIMIT 5

Copié



What product has the highest sentiment?

check

USB wired soundbar - in store only

G Noise-reducing Bluetooth Headphones

Stylus Pen w/ LED Light

G Noise-reducing Bluetooth Headphones



Modifiez votre requête pour afficher les cinq principaux produits recueillant le sentiment le plus négatif et filtrer les valeurs NULL.

Solution possible :



SELECT

&nbsp; SKU,

&nbsp; name,

&nbsp; sentimentScore,

&nbsp; sentimentMagnitude

FROM

&nbsp; `data-to-insights.ecommerce.products`

WHERE sentimentScore IS NOT NULL

ORDER BY

&nbsp; sentimentScore

LIMIT 5

Copié

Quel produit recueille le sentiment le plus négatif ?





What is the product with the lowest sentiment?

4 Womens Vintage Hero Tee Platinum

check

7 inch Dog Frisbee

Mens Vintage Henley

Womens Convertible Vest-Jacket Sea Foam Green



Cliquez sur Vérifier ma progression pour valider l'objectif.

Assessment Completed!

Explorer l'ensemble de données de sentiments sur les produits

Assessment Completed!



## Tâche 3 : Joindre des ensembles de données pour obtenir des insights



Scénario : C'est le premier jour du mois, et l'équipe chargée de l'inventaire vous a informé que le champ orderedQuantity de l'ensemble de données de l'inventaire des produits était obsolète. Elle a besoin de votre aide pour obtenir le montant total des ventes par produit pour le 01/08/2017 et comparer ce chiffre au niveau actuel des stocks dans l'inventaire, afin de déterminer quels produits réapprovisionner en priorité.



Calculer le volume de ventes journalier par code produit (SKU)

Créez une table dans votre ensemble de données ecommerce en respectant les spécifications suivantes :

Intitulez la table sales\_by\_sku\_20170801

Extrayez les données de data-to-insights.ecommerce.all\_sessions\_raw

Incluez uniquement les résultats distincts

Renvoyez productSKU

Renvoyez la quantité totale commandée (productQuantity). Conseil : utilisez un opérateur SUM() avec une condition IFNULL

Filtrez uniquement les ventes effectuées à la date 20170801

Utilisez ORDER BY pour trier les SKU dans l'ordre décroissant du nombre de commandes

Solution possible :



\# pull what sold on 08/01/2017

CREATE OR REPLACE TABLE ecommerce.sales\_by\_sku\_20170801 AS

SELECT

&nbsp; productSKU,

&nbsp; SUM(IFNULL(productQuantity,0)) AS total\_ordered

FROM

&nbsp; `data-to-insights.ecommerce.all\_sessions\_raw`

WHERE date = '20170801'

GROUP BY productSKU

ORDER BY total\_ordered DESC #462 skus sold

Copié

Cliquez sur la table sales\_by\_sku, puis sur l'onglet Aperçu.

Combien de codes produit distincts ont été vendus ?



Réponse : 462





True or false: GGOEGOAQ012899 is the top selling product SKU.

check

Vrai

Faux



Enrichissez ensuite vos données de ventes à l'aide des informations de l'inventaire des produits en joignant les deux ensembles de données.



Joindre des données de ventes et des données d'inventaire

À l'aide d'une jointure, enrichissez les données d'e-commerce du site Web avec les champs suivants de l'ensemble de données de l'inventaire des produits :

name

stockLevel

restockingLeadTime

sentimentScore

sentimentMagnitude

Complétez la requête partiellement rédigée :

\# join against product inventory to get name

SELECT DISTINCT

&nbsp; website.productSKU,

&nbsp; website.total\_ordered,

&nbsp; inventory.name,

&nbsp; inventory.stockLevel,

&nbsp; inventory.restockingLeadTime,

&nbsp; inventory.sentimentScore,

&nbsp; inventory.sentimentMagnitude

FROM

&nbsp; ecommerce.sales\_by\_sku\_20170801 AS website

&nbsp; LEFT JOIN `data-to-insights.ecommerce.products` AS inventory



ORDER BY total\_ordered DESC

Copié

Solution possible :



\# join against product inventory to get name

SELECT DISTINCT

&nbsp; website.productSKU,

&nbsp; website.total\_ordered,

&nbsp; inventory.name,

&nbsp; inventory.stockLevel,

&nbsp; inventory.restockingLeadTime,

&nbsp; inventory.sentimentScore,

&nbsp; inventory.sentimentMagnitude

FROM

&nbsp; ecommerce.sales\_by\_sku\_20170801 AS website

&nbsp; LEFT JOIN `data-to-insights.ecommerce.products` AS inventory

&nbsp; ON website.productSKU = inventory.SKU

ORDER BY total\_ordered DESC

Copié

Modifiez la requête que vous avez rédigée pour inclure les éléments suivants :

Un champ calculé (total\_ordered / stockLevel) auquel vous associez l'alias "ratio". Conseil : utilisez SAFE\_DIVIDE(field1,field2) pour éviter les erreurs dues à la division par 0 lorsque le niveau de stock est de 0.

Filtrez les résultats pour inclure uniquement les produits qui ont déjà écoulé au moins 50 % de leur stock au début du mois.

Solution possible :



\# calculate ratio and filter

SELECT DISTINCT

&nbsp; website.productSKU,

&nbsp; website.total\_ordered,

&nbsp; inventory.name,

&nbsp; inventory.stockLevel,

&nbsp; inventory.restockingLeadTime,

&nbsp; inventory.sentimentScore,

&nbsp; inventory.sentimentMagnitude,



&nbsp; SAFE\_DIVIDE(website.total\_ordered, inventory.stockLevel) AS ratio

FROM

&nbsp; ecommerce.sales\_by\_sku\_20170801 AS website

&nbsp; LEFT JOIN `data-to-insights.ecommerce.products` AS inventory

&nbsp; ON website.productSKU = inventory.SKU



\# gone through more than 50% of inventory for the month

WHERE SAFE\_DIVIDE(website.total\_ordered,inventory.stockLevel) >= .50



ORDER BY total\_ordered DESC

Copié



What is the name of the top selling product and what percent of its inventory has been sold already?

Youth Short Sleeve Tee Red with a restocking leadtime of 9

Android Infant Short Sleeve Tee Pewter with 7 product orders out of 2 in stock

check

Leather Journal-Black with 250 product orders out of 354 in stock



Cliquez sur Vérifier ma progression pour valider l'objectif.

Assessment Completed!

Joindre des ensembles de données pour obtenir des insights

Assessment Completed!



## Tâche 4 : Ajouter des enregistrements



Votre équipe internationale a déjà réalisé à la date du 02/08/2017 des ventes en magasin que vous voulez enregistrer dans vos tables de ventes journalières.



Créer une table vide pour stocker les ventes par code produit pour le 02/08/2017

Spécifiez les champs suivants pour le schéma :

nom de la table : ecommerce.sales\_by\_sku\_20170802

productSKU STRING

total\_ordered en tant que champ INT64

Solution possible :



CREATE OR REPLACE TABLE ecommerce.sales\_by\_sku\_20170802

(

productSKU STRING,

total\_ordered INT64

);

Copié

Vérifiez que vous avez maintenant des tables de ventes partagées pour deux dates. Pour cela, cliquez sur le menu déroulant situé à côté du nom de la table Sales\_by\_sku dans les résultats de la table, ou actualisez votre navigateur pour les voir s'afficher dans le menu de gauche :

Deux tables sales\_by\_sku mises en évidence dans l\&#39;ensemble de données \&quot;ecommerce\&quot;



Insérez l'enregistrement de ventes qui vous a été fourni par votre équipe commerciale :

INSERT INTO ecommerce.sales\_by\_sku\_20170802

(productSKU, total\_ordered)

VALUES('GGOEGHPA002910', 101)

Copié

Affichez l'aperçu de la table pour vérifier que l'enregistrement apparaît : cliquez sur le nom de la table pour afficher les résultats.

Combiner des données historiques

Il existe plusieurs manières de combiner des données possédant le même schéma. Deux moyens courants sont les opérateurs UNION et les caractères génériques de table.



Union est un opérateur SQL qui permet de combiner des lignes issues d'ensembles de résultats différents.

Les caractères génériques de table permettent d'interroger plusieurs tables à l'aide d'instructions SQL concises. Les tables génériques sont disponibles uniquement en langage SQL standard.

Rédigez une requête UNION qui renverra tous les enregistrements des deux tables ci-dessous :

ecommerce.sales\_by\_sku\_20170801

ecommerce.sales\_by\_sku\_20170802

SELECT \* FROM ecommerce.sales\_by\_sku\_20170801

UNION ALL

SELECT \* FROM ecommerce.sales\_by\_sku\_20170802

Copié

Remarque : La différence entre UNION et UNION ALL est qu'une instruction UNION n'inclut pas les enregistrements en double.

Quel est l'inconvénient d'utiliser plusieurs tables de ventes journalières ? Vous devrez coder plusieurs instructions UNION enchaînées ensemble.



Il est préférable d'utiliser le filtre générique de table et le filtre \_TABLE\_SUFFIX.



Rédigez une requête qui utilise le caractère générique de table (\*) pour sélectionner tous les enregistrements de ecommerce.sales\_by\_sku\_ pour l'année 2017.

Solution possible :



SELECT \* FROM `ecommerce.sales\_by\_sku\_2017\*`

Copié

Modifiez la requête précédente en ajoutant un filtre afin de limiter les résultats à la date du 02/08/2017 uniquement.

Solution possible :



SELECT \* FROM `ecommerce.sales\_by\_sku\_2017\*`

WHERE \_TABLE\_SUFFIX = '0802'

Copié

Remarque : Vous pouvez également créer une table partitionnée qui peut ingérer automatiquement les données de ventes quotidiennes dans la partition appropriée.



A UNION ALL join does not include duplicate records.

Vrai

check

Faux



Cliquez sur Vérifier ma progression pour valider l'objectif.

Assessment Completed!

Ajouter des enregistrements

Assessment Completed!

