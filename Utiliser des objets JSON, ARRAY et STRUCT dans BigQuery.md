# Utiliser des objets JSON, ARRAY et STRUCT dans BigQuery



## Présentation



BigQuery est la base de données d'analyse NoOps, économique et entièrement gérée de Google. Avec BigQuery, vous pouvez interroger plusieurs téraoctets de données sans avoir à gérer d'infrastructure ni faire appel à un administrateur de base de données. Basé sur le langage SQL et le modèle de paiement à l'usage, BigQuery vous permet de vous concentrer sur l'analyse des données pour en dégager des informations pertinentes.



Dans cet atelier, vous allez travailler avec des données semi-structurées (ingestion JSON, types de données tableau) au sein de BigQuery. Dénormaliser votre schéma en une table unique avec des champs imbriqués et répétés permet d'améliorer les performances, mais l'utilisation de la syntaxe SQL avec les données de tableau (array) peut être délicate. Vous allez vous entraîner à charger, interroger, corriger et désimbriquer divers ensembles de données semi-structurées.



Objectifs de l'atelier

Dans cet atelier, vous allez apprendre à effectuer les tâches suivantes :



Charger et interroger des données semi-structurées, y compris les désimbriquer

Corriger des requêtes sur des données semi-structurées

Préparation

Avant de cliquer sur le bouton "Démarrer l'atelier"

Lisez ces instructions. Les ateliers sont minutés, et vous ne pouvez pas les mettre en pause. Le minuteur, qui démarre lorsque vous cliquez sur Démarrer l'atelier, indique combien de temps les ressources Google Cloud resteront accessibles.



Cet atelier pratique vous permet de suivre les activités dans un véritable environnement cloud, et non dans un environnement de simulation ou de démonstration. Des identifiants temporaires vous sont fournis pour vous permettre de vous connecter à Google Cloud le temps de l'atelier.







## Tâche 1 : Créer un ensemble de données pour stocker les tables



Dans BigQuery, cliquez sur les trois points à côté de l'ID de votre projet, puis sélectionnez Créer un ensemble de données :

Option Créer un ensemble de données mise en évidence



Définissez l'ID de l'ensemble de données sur fruit\_store. Conservez les valeurs par défaut des autres options, à savoir Emplacement des données et Expiration du tableau par défaut.



Cliquez sur Créer un ensemble de données.



## Tâche 2 : S'entraîner à utiliser des tableaux en SQL



Normalement, en langage SQL, chaque ligne est associée à une seule valeur, comme dans la liste de fruits ci-dessous :



Ligne



Fruit



1



raspberry



2



blackberry



3



strawberry



4



cherry



Comment procéder si vous voulez une liste de fruits pour chaque personne dans le magasin ? La liste pourrait alors se présenter comme suit :



Ligne



Fruit



Personne



1



raspberry



sally



2



blackberry



sally



3



strawberry



sally



4



cherry



sally



5



orange



frederick



6



apple



frederick



Dans une base de données relationnelle SQL traditionnelle, vous constateriez que les noms sont répétés et penseriez immédiatement à diviser la table ci-dessus en deux tables distinctes : Fruits et Personnes. Ce processus s'appelle la normalisation (passer d'une table à plusieurs). Il s'agit d'une approche courante pour les bases de données transactionnelles telles que mySQL.



Pour l'entreposage de données, les analystes optent souvent pour la méthode inverse (dénormalisation), qui consiste à regrouper plusieurs tables distinctes dans une grande table de rapport.





What are some potential issues if you stored all your data in one giant table?

The table row size could be too large for traditional reporting databases

Any changes to a value (like customer email) could impact many other rows (like all their orders)

Data at differing levels of granularity could lead to reporting issues because less granular fields would be repeated.

All of the above



Vous allez aujourd'hui découvrir une approche différente consistant à stocker les données avec différents niveaux de précision dans une seule et même table, à l'aide de champs répétés :



Ligne



Fruit (objet array)



Personne



1



raspberry



sally



blackberry



strawberry



cherry



2



orange



frederick



apple



Qu'est-ce qui vous paraît bizarre dans la table ci-dessus ?



Elle ne comporte que deux lignes.

Une même ligne contient plusieurs valeurs du champ Fruit.

Les personnes sont associées à toutes les valeurs du champ.

Quel est le principal élément à noter ? Le type de données array !



Il existe une méthode plus simple pour interpréter l'objet array Fruit :



Ligne



Fruit (objet array)



Personne



1



\[raspberry, blackberry, strawberry, cherry]



sally



2



\[orange, apple]



frederick



Ces deux tables sont identiques. Deux points importants sont à retenir ici :



Un objet array est simplement une liste d'éléments entre crochets \[ ].

BigQuery présente ces objets sous une forme aplatie. BigQuery liste simplement la valeur verticalement dans le tableau (notez que toutes les valeurs appartiennent toujours à une ligne unique)

À votre tour !



Saisissez la commande suivante dans l'éditeur de requête BigQuery :

\#standardSQL

SELECT

\['raspberry', 'blackberry', 'strawberry', 'cherry'] AS fruit\_array

Copié

Cliquez sur Exécuter.



Essayez maintenant d'exécuter cette requête :



\#standardSQL

SELECT

\['raspberry', 'blackberry', 'strawberry', 'cherry', 1234567] AS fruit\_array

Copié

Vous allez normalement recevoir un message d'erreur ressemblant à celui-ci :



Error: Array elements of types {INT64, STRING} do not have a common supertype at \[3:1]





Why did you get this error?

Data in an array must only be strings

Data in an array \[ ] must all be the same type

Data in an array cannot exceed 4 elements



Les objets array ne peuvent contenir qu'un seul type de données (seulement des chaînes ou seulement des nombres).



Voici la requête finale, qui indique la table à interroger :

\#standardSQL

SELECT person, fruit\_array, total\_cost FROM `data-to-insights.advanced.fruit\_store`;

Copié

Cliquez sur Exécuter.



Après avoir observé les résultats, cliquez sur l'onglet JSON pour afficher la structure imbriquée des résultats.



résultats sur la page à onglets JSON



Charger des données JSON semi-structurées dans BigQuery

Comment procéderiez-vous si vous deviez ingérer un fichier JSON dans BigQuery ?



Créez une table fruit\_details dans l'ensemble de données.



Cliquez sur l'ensemble de données fruit\_store.

L'option Créer une table s'affiche.



Remarque : Vous devrez peut-être agrandir votre fenêtre de navigateur pour voir l'option "Créer une table".

Ajoutez les informations suivantes pour la table :

Source : choisissez Google Cloud Storage dans la liste déroulante Créer une table à partir de.

Sélectionnez un fichier du bucket Cloud Storage : cloud-training/data-insights-course/labs/optimizing-for-performance/shopping\_cart.json

Format de fichier : JSONL (fichier JSON délimité par un saut de ligne)

Nommez la nouvelle table fruit\_details.



Cochez la case Détection automatique sous "Schéma".



Cliquez sur Créer une table.



Dans le schéma, notez que fruit\_array est marqué comme REPEATED (RÉPÉTÉ), ce qui signifie qu'il s'agit d'un tableau.



Résumé



BigQuery est nativement compatible avec les tableaux.

Les valeurs contenues dans un tableau doivent être du même type.

Les tableaux sont appelés des champs REPEATED (RÉPÉTÉS) dans BigQuery.

Cliquez sur Vérifier ma progression pour valider l'objectif.

Créer un ensemble de données et une table pour stocker les données



## Tâche 3 : Créer vos propres tableaux avec ARRAY\_AGG()



Si vous n'avez pas encore de tableaux dans vos tables, vous pouvez en créer.



Copiez et collez la requête ci-dessous pour explorer cet ensemble de données public :

SELECT

&nbsp; fullVisitorId,

&nbsp; date,

&nbsp; v2ProductName,

&nbsp; pageTitle

&nbsp; FROM `data-to-insights.ecommerce.all\_sessions`

WHERE visitId = 1501570398

ORDER BY date

Copié

Cliquez sur Exécuter, puis observez les résultats.



How many rows are returned?

2

100

70

111



Vous allez maintenant agréger les valeurs de chaîne dans un tableau avec la fonction ARRAY\_AGG().



Copiez et collez la requête ci-dessous pour explorer cet ensemble de données public :

SELECT

&nbsp; fullVisitorId,

&nbsp; date,

&nbsp; ARRAY\_AGG(v2ProductName) AS products\_viewed,

&nbsp; ARRAY\_AGG(pageTitle) AS pages\_viewed

&nbsp; FROM `data-to-insights.ecommerce.all\_sessions`

WHERE visitId = 1501570398

GROUP BY fullVisitorId, date

ORDER BY date

Copié

Cliquez sur Exécuter, puis observez les résultats.



How many rows are returned?

2 - one for each day

63 - one for each day

100 - one for each day

70 - one for each day



Ensuite, utilisez la fonction ARRAY\_LENGTH() pour compter le nombre de pages et de produits qui ont été visualisés :

SELECT

&nbsp; fullVisitorId,

&nbsp; date,

&nbsp; ARRAY\_AGG(v2ProductName) AS products\_viewed,

&nbsp; ARRAY\_LENGTH(ARRAY\_AGG(v2ProductName)) AS num\_products\_viewed,

&nbsp; ARRAY\_AGG(pageTitle) AS pages\_viewed,

&nbsp; ARRAY\_LENGTH(ARRAY\_AGG(pageTitle)) AS num\_pages\_viewed

&nbsp; FROM `data-to-insights.ecommerce.all\_sessions`

WHERE visitId = 1501570398

GROUP BY fullVisitorId, date

ORDER BY date

Copié



How many pages were visited by this user on 20170801?

101

8

70

109



Pour poursuivre, dédupliquez les pages et les produits afin de déterminer combien de produits uniques ont été visualisés, en ajoutant le paramètre DISTINCT à la fonction ARRAY\_AGG() :

SELECT

&nbsp; fullVisitorId,

&nbsp; date,

&nbsp; ARRAY\_AGG(DISTINCT v2ProductName) AS products\_viewed,

&nbsp; ARRAY\_LENGTH(ARRAY\_AGG(DISTINCT v2ProductName)) AS distinct\_products\_viewed,

&nbsp; ARRAY\_AGG(DISTINCT pageTitle) AS pages\_viewed,

&nbsp; ARRAY\_LENGTH(ARRAY\_AGG(DISTINCT pageTitle)) AS distinct\_pages\_viewed

&nbsp; FROM `data-to-insights.ecommerce.all\_sessions`

WHERE visitId = 1501570398

GROUP BY fullVisitorId, date

ORDER BY date

Copié



How many DISTINCT pages were visited by this user on 20170801?

109

101

8

70



Cliquez sur Vérifier ma progression pour valider l'objectif.

Exécuter la requête pour savoir combien de produits uniques ont été visualisés



Résumé



Les tableaux vous permettent de faire des choses très utiles comme :



trouver le nombre d'éléments dans le tableau avec ARRAY\_LENGTH(<array>) ;

dédupliquer ces éléments avec ARRAY\_AGG(DISTINCT <field>) ;

classer ces éléments avec ARRAY\_AGG(<field> ORDER BY <field>) ;

définir une limite avec ARRAY\_AGG(<field> LIMIT 5).

## Tâche 4 : Interroger des tables contenant des tableaux



L'ensemble de données public de BigQuery pour Google Analytics (bigquery-public-data.google\_analytics\_sample) comprend beaucoup plus de champs et de lignes que celui de notre atelier (data-to-insights.ecommerce.all\_sessions). Mais surtout, il stocke déjà nativement des valeurs de champs (produits, pages et transactions, par exemple) sous la forme d'objets ARRAY.



Copiez et collez la requête ci-dessous pour explorer les données disponibles et voir si vous pouvez trouver des champs contenant des valeurs répétées (tableaux) :

SELECT

&nbsp; \*

FROM `bigquery-public-data.google\_analytics\_sample.ga\_sessions\_20170801`

WHERE visitId = 1501570398

Copié

Exécutez la requête.



Parcourez les résultats vers la droite jusqu'à ce que vous trouviez le champ hits.product.v2ProductName (nous aborderons très bientôt les alias de champs multiples).





You'll note a lot of seemingly 'blank' values in the results as you scroll. Why do you think that is?

BigQuery is still loading the data for those fields

The fields that appear to be missing data are actually at a higher level of granularity than other fields

The dataset is missing data values for those fields



Le nombre de champs disponibles dans le schéma Google Analytics peut être trop important pour une analyse.



Essayez de formuler une requête uniquement sur les champs des visites et des noms de pages, comme précédemment :

SELECT

&nbsp; visitId,

&nbsp; hits.page.pageTitle

FROM `bigquery-public-data.google\_analytics\_sample.ga\_sessions\_20170801`

WHERE visitId = 1501570398

Copié

Vous recevez le message d'erreur suivant : Error:Cannot access field page on a value with type ARRAY<STRUCT<hitNumber INT64, time INT64, hour INT64, ...>> at \[3:8]



Pour pouvoir interroger des champs REPEATED (tableaux) normalement, vous devez tout d'abord fractionner ces tableaux en lignes.



Par exemple, le tableau associé à hits.page.pageTitle est actuellement stocké sous la forme d'une ligne unique semblable à ceci :



\['homepage','product page','checkout']

Copié

et il doit se présenter ainsi :



\['homepage',

'product page',

'checkout']

Copié

Comment faire cela avec SQL ?



Réponse : En appliquant la fonction UNNEST() à votre champ de tableau :



SELECT DISTINCT

&nbsp; visitId,

&nbsp; h.page.pageTitle

FROM `bigquery-public-data.google\_analytics\_sample.ga\_sessions\_20170801`,

UNNEST(hits) AS h

WHERE visitId = 1501570398

LIMIT 10

Copié

Nous parlerons de UNNEST() plus tard. Pour le moment, notez simplement les points suivants :



Vous devez appliquer la fonction UNNEST() aux tableaux pour replacer chacun de leurs éléments sur une ligne distincte.

UNNEST() suit toujours le nom de table figurant dans votre clause FROM (conceptuellement, vous pouvez considérer cela comme une table préjointe).

Cliquez sur Vérifier ma progression pour valider l'objectif.

Exécuter la requête pour utiliser la fonction UNNEST() sur le champ de tableau



## Tâche 5 : Présentation des objets STRUCT



Vous vous êtes peut-être demandé pourquoi l'alias de champ hit.page.pageTitle ressemble à trois champs séparés par un point. Tout comme les valeurs ARRAY vous donnent la possibilité d'accroître la précision de vos champs, un autre type de données vous permet d'élargir la portée des requêtes dans votre schéma en regroupant des champs associés. Il s'agit du type de données SQL STRUCT.



Pour comprendre facilement ce qu'est un objet STRUCT, il suffit de se le représenter comme une table distincte qui est préjointe à votre table principale.



Un objet STRUCT peut inclure :



un ou plusieurs champs ;

des types de données identiques ou différents pour chaque champ ;

son propre alias.

Cela ressemble à une table, n'est-ce pas ?



Explorer un ensemble de données avec des objets STRUCT

Pour ouvrir l'ensemble de données bigquery-public-data, cliquez sur + Ajouter des données, sélectionnez Ajouter un projet aux favoris en saisissant son nom, puis saisissez le nom bigquery-public-data.



Cliquez sur Ajouter aux favoris.



Le projet bigquery-public-data apparaît désormais dans la section "Explorateur".



Ouvrez bigquery-public-data.



Recherchez et ouvrez l'ensemble de données google\_analytics\_sample.



Cliquez sur la table ga\_sessions\_ (366).



Commencez à faire défiler le schéma, puis répondez à la question suivante en utilisant la fonction de recherche de votre navigateur.





In a BigQuery schema, a STRUCT field is noted as a RECORD Type. Search for RECORD in the Google Analytics schema. How many STRUCTs are present in this dataset?

1

5

11

32





What are the names of some of the STRUCT (RECORD Type) fields?

Totals

TrafficSource

trafficSource.adwordsClickInfo

device

All of the above





How can both TrafficSource and trafficSource.adwordsClickInfo both be STRUCTs?

A STRUCT can have another STRUCT as one of its fields (you can nest STRUCTs)

They are not STRUCTs

Because they are all ARRAYs

This is an invalid data type





In a BigQuery schema, an ARRAY field is noted as a REPEATED Mode. Search for REPEATED in the Google Analytics schema. How many ARRAYs are present in this dataset?

1

5

11

32



Comme vous pouvez l'imaginer, un site Web d'e-commerce moderne peut stocker un volume impressionnant de données de sessions.



Le principal intérêt d'une table comportant 32 objets STRUCT est que cela permet d'exécuter des requêtes comme celle-ci sans jointures :



SELECT

&nbsp; visitId,

&nbsp; totals.\*,

&nbsp; device.\*

FROM `bigquery-public-data.google\_analytics\_sample.ga\_sessions\_20170801`

WHERE visitId = 1501570398

LIMIT 10

Copié

Remarque : La syntaxe .\* indique à BigQuery de retourner tous les champs pour cet objet STRUCT (comme il le ferait si totals.\* était une table distincte avec laquelle une jointure existait).

Lorsque vous stockez vos grandes tables de rapport sous la forme d'objets STRUCT ("tables" préjointes) et ARRAY (précision accrue), vous pouvez :



bénéficier de bien meilleures performances en évitant d'avoir à effectuer 32 jointures de tables avec JOIN ;

obtenir des données précises des objets ARRAY, si nécessaire, mais sans être pénalisé si vous n'en avez pas besoin (BigQuery stocke chaque colonne individuellement sur le disque) ;

disposer de tout le contexte commercial dans une seule et même table, sans avoir à vous soucier des clés JOIN ou de savoir quelles tables contiennent les données voulues.

## Tâche 6 : S'entraîner à utiliser des objets STRUCT et ARRAY



L'ensemble de données suivant contient les temps mis par des coureurs pour réaliser un tour de piste. Chaque tour sera nommé "split" (tour de piste).



Coureurs sur une piste



Avec cette requête, essayez la syntaxe STRUCT et notez les différents types de champ présents dans le conteneur struct :

\#standardSQL

SELECT STRUCT("Rudisha" as name, 23.4 as split) as runner

Copié

Ligne



runner.name



runner.split



1



Rudisha



23.4



Que remarquez-vous concernant les alias de champ ? En raison de la présence de champs imbriqués dans l'objet struct ("name" et "split" sont un sous-ensemble de "runner"), vous obtenez une notation par points.



Comment associer le coureur à plusieurs temps pour chaque tour d'une même course ?





How could you have multiple split times within a single record? Hint: the splits all have the same numeric datatype.

Store each split time in a separate STRING field with STRING\_AGG()

Store each split time as an element in an ARRAY of splits

Store each split time in a separate table called race\_splits

Use a SQL UNION to join the race and split details



Avec un tableau bien sûr !



Exécutez la requête ci-dessous pour vérifier :

\#standardSQL

SELECT STRUCT("Rudisha" as name, \[23.4, 26.3, 26.4, 26.1] as splits) AS runner

Copié

Ligne



runner.name



runner.splits



1



Rudisha



23.4



26.3



26.4



26.1



En résumé :



Les objets struct sont des conteneurs qui peuvent présenter plusieurs noms de champ et types de données imbriqués.

Un objet struct peut contenir un champ de type array (comme on peut le voir ci-dessus avec le champ "splits").

S'entraîner à ingérer des données JSON

Créez un ensemble de données nommé racing.



Cliquez sur l'ensemble de données racing, puis sur Créer une table.



Remarque : Vous devrez peut-être agrandir votre fenêtre de navigateur pour voir l'option "Créer une table".

Source : sélectionnez Google Cloud Storage dans la liste déroulante Créer une table à partir de.

Sélectionnez un fichier du bucket Cloud Storage : cloud-training/data-insights-course/labs/optimizing-for-performance/race\_results.json

Format de fichier : JSONL (fichier JSON délimité par un saut de ligne)

Dans la section Schéma, cliquez sur Modifier sous forme de texte et ajoutez les lignes suivantes :

\[

&nbsp;   {

&nbsp;       "name": "race",

&nbsp;       "type": "STRING",

&nbsp;       "mode": "NULLABLE"

&nbsp;   },

&nbsp;   {

&nbsp;       "name": "participants",

&nbsp;       "type": "RECORD",

&nbsp;       "mode": "REPEATED",

&nbsp;       "fields": \[

&nbsp;           {

&nbsp;               "name": "name",

&nbsp;               "type": "STRING",

&nbsp;               "mode": "NULLABLE"

&nbsp;           },

&nbsp;           {

&nbsp;               "name": "splits",

&nbsp;               "type": "FLOAT",

&nbsp;               "mode": "REPEATED"

&nbsp;           }

&nbsp;       ]

&nbsp;   }

]

Copié

Nommez la nouvelle table race\_results.



Cliquez sur Créer une table.



Une fois le chargement effectué, affichez l'aperçu du schéma associé à la table que vous venez de créer :



Page à onglets du schéma race\_results



Quel champ correspond à l'objet STRUCT ? Comment le savez-vous ?



Le champ participants correspond à l'objet STRUCT, car il est du type RECORD.



Quel champ correspond à l'objet ARRAY ?



Le champ participants.splits est un tableau de valeurs flottantes à l'intérieur de l'objet struct participants parent. Il possède un mode REPEATED qui indique un tableau. On dit que les valeurs de ce tableau sont imbriquées, car plusieurs valeurs sont présentes dans un champ unique.



Cliquez sur Vérifier ma progression pour valider l'objectif.

Créer un ensemble de données et un tableau pour ingérer les données JSON



S'entraîner à interroger des champs imbriqués et répétés

Examinons l'ensemble des coureurs pour le 800 mètres :

\#standardSQL

SELECT \* FROM racing.race\_results

Copié

Combien de lignes ont été renvoyées ?



Réponse : 1



Résultats de la requête dans la page à onglets Résultats, avec le numéro de la ligne mis en évidence (1).



Comment procéder si vous voulez lister le nom de chaque coureur et le type de course ?



Exécutez la requête ci-dessous et observez le résultat :

\#standardSQL

SELECT race, participants.name

FROM racing.race\_results

Copié

Error: Cannot access field name on a value with type ARRAY<STRUCT<name STRING, splits ARRAY<FLOAT64>>>> at \[2:27]



Le résultat est très semblable à celui qui vous serait renvoyé si vous omettiez l'instruction GROUP BY avec les fonctions d'agrégation. Il existe ici deux niveaux de précision différents : une ligne pour la course et trois lignes pour les noms des participants. Comment transformer ceci...



Ligne



race



participants.name



1



800M



Rudisha



2



???



Makhloufi



3



???



Murphy



...en ceci :



Ligne



race



participants.name



1



800M



Rudisha



2



800M



Makhloufi



3



800M



Murphy



Avec une base de données relationnelle SQL traditionnelle comportant une table des courses et une table des participants, comment procéderiez-vous pour obtenir des informations à partir de ces deux tables ? Vous utiliseriez la commande JOIN pour les joindre. Ici, l'objet STRUCT des participants (qui est très semblable du point de vue conceptuel à une table) fait déjà partie de votre table des courses, mais il n'est pas encore correctement corrélé à votre champ non-STRUCT "race".



Quelle commande SQL en deux mots pourriez-vous utiliser pour corréler la course de 800 mètres avec chacun des coureurs de la première table ?



Réponse : CROSS JOIN



Parfait.



Essayez maintenant d'exécuter cette requête :

\#standardSQL

SELECT race, participants.name

FROM racing.race\_results

CROSS JOIN

participants  # this is the STRUCT (it is like a table within a table)

Copié

Ensemble de données manquant pour la table "participants", alors qu'aucun ensemble de données par défaut n'est défini dans la requête.



Bien que l'objet STRUCT des participants soit semblable à une table, techniquement il s'agit toujours d'un champ de la table racing.race\_results.



Ajoutez le nom de l'ensemble de données à la requête :

\#standardSQL

SELECT race, participants.name

FROM racing.race\_results

CROSS JOIN

race\_results.participants # full STRUCT name

Copié

Ensuite, cliquez sur Exécuter.

Félicitations ! Vous avez réussi à afficher la liste de tous les coureurs de chaque course.



Ligne



race



name



1



800M



Rudisha



2



800M



Makhloufi



3



800M



Murphy



4



800M



Bosse



5



800M



Rotich



6



800M



Lewandowski



7



800M



Kipketer



8



800M



Berian



Pour simplifier la dernière requête, vous pouvez :

ajouter un alias pour la table initiale ;

remplacer les mots "CROSS JOIN" par une virgule (la virgule représente implicitement une jointure croisée).

Le résultat de la requête sera identique :



\#standardSQL

SELECT race, participants.name

FROM racing.race\_results AS r, r.participants

Copié

S'il y a plusieurs types de courses (800M, 100M, 200M), une commande CROSS JOIN n'aurait-elle pas seulement pour effet d'associer chaque nom de coureur à chaque course possible de la même manière qu'un produit cartésien ?



Réponse : Non. Il s'agit ici d'une jointure croisée corrélée qui ne désimbrique que les éléments associés à une ligne unique. Pour plus de détails, consultez la page consacrée à l'utilisation des objets ARRAY et STRUCT.



Résumé concernant les objets STRUCT :



Un objet SQL STRUCT est simplement un conteneur d'autres champs de données qui peuvent être de types différents. Le mot "struct" signifie "structure de données". Souvenez-vous de l'exemple précédent : STRUCT(``"Rudisha" as name, \[23.4, 26.3, 26.4, 26.1] as splits``)`` AS runner

Les objets STRUCT reçoivent un alias (comme "runner" dans l'exemple ci-dessus) et peuvent être conceptuellement assimilés à une table contenue dans votre table principale.

Vous devez désimbriquer les objets STRUCT (et ARRAY) afin de pouvoir exécuter des opérations sur leurs éléments. Appliquez la fonction UNNEST() autour du nom de l'objet STRUCT lui-même ou du champ STRUCT se présentant sous forme de tableau, pour le désimbriquer et l'aplatir.

## Tâche 7 : Questions de l'atelier : STRUCT()



Répondez aux questions ci-dessous en vous servant de la table racing.race\_results que vous avez créée précédemment.



Tâche : écrire une requête pour compter (COUNT) le nombre total de coureurs.



Pour commencer, utilisez la requête partiellement rédigée ci-dessous :

\#standardSQL

SELECT COUNT(participants.name) AS racer\_count

FROM racing.race\_results

Copié

Remarque : N'oubliez pas que vous devrez utiliser une jointure croisée dans votre nom d'objet struct en tant que source de données supplémentaire après l'instruction FROM.

Solution possible :



\#standardSQL

SELECT COUNT(p.name) AS racer\_count

FROM racing.race\_results AS r, UNNEST(r.participants) AS p

Copié

Ligne



racer\_count



1



8



Réponse : 8 coureurs ont participé à cette course.



Cliquez sur Vérifier ma progression pour valider l'objectif.

Exécuter la requête pour COMPTER le nombre de coureurs au total



## Tâche 8 : Question de l'atelier : Désimbriquer les éléments d'un objet ARRAY avec UNNEST( )



Rédigez une requête qui listera le temps de course total pour les coureurs dont le nom commence par R. Classez les résultats par ordre décroissant du meilleur temps total. Utilisez l'opérateur UNNEST() avec la requête partiellement rédigée ci-dessous.



Complétez la requête :

\#standardSQL

SELECT

&nbsp; p.name,

&nbsp; SUM(split\_times) as total\_race\_time

FROM racing.race\_results AS r

, r.participants AS p

, p.splits AS split\_times

WHERE

GROUP BY

ORDER BY

;

Copié

Remarque :

Vous devrez désimbriquer à la fois l'objet STRUCT et l'objet ARRAY contenu dans l'objet STRUCT sous la forme de sources de données après votre clause FROM.

Veillez à utiliser des alias si nécessaire.

Solution possible :



\#standardSQL

SELECT

&nbsp; p.name,

&nbsp; SUM(split\_times) as total\_race\_time

FROM racing.race\_results AS r

, UNNEST(r.participants) AS p

, UNNEST(p.splits) AS split\_times

WHERE p.name LIKE 'R%'

GROUP BY p.name

ORDER BY total\_race\_time ASC;

Copié

Ligne



name



total\_race\_time



1



Rudisha



102.19999999999999



2



Rotich



103.6



Cliquez sur Vérifier ma progression pour valider l'objectif.

Exécuter la requête qui listera la durée de course totale pour les coureurs dont les noms commencent par R



## Tâche 9 : Filtrer les valeurs d'un tableau



Vous avez pu constater que le tour de piste le plus rapide enregistré pour la course de 800 mètres a été effectué en 23,2 secondes, mais vous n'avez pas identifié le coureur qui a réalisé ce temps. Créez une requête qui renvoie ce résultat.



Complétez la requête partiellement rédigée :

\#standardSQL

SELECT

&nbsp; p.name,

&nbsp; split\_time

FROM racing.race\_results AS r

, r.participants AS p

, p.splits AS split\_time

WHERE split\_time = ;

Copié

Solution possible :



\#standardSQL

SELECT

&nbsp; p.name,

&nbsp; split\_time

FROM racing.race\_results AS r

, UNNEST(r.participants) AS p

, UNNEST(p.splits) AS split\_time

WHERE split\_time = 23.2;

Copié

Ligne



name



split\_time



1



Kipketer



23.2



Cliquez sur Vérifier ma progression pour valider l'objectif.

Exécuter la requête pour afficher le coureur ayant le meilleur temps par tour





