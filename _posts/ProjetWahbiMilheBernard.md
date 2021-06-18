---
title: "GitHub Actions: Providing Data Scientists With New Superpowers"
description: A Tutorial on GitHub <a href="https://github.com/features/actions">Actions</a> For Data Scientists
toc: true
comments: true
layout: post
categories: [actions, markdown]
image: images/fastpages_posts/actions/actions_logo.png
author: Hamel Husain & Jeremy Howard
---

## Comment expliquer le fait qu\'un jeu soit un véritable succès ??

**L\'Objectif de notre Projet**

Du point de vue du projet, notre objectif est de réaliser un dashboard
complet et fonctionnel qui permet aux utilisateurs de découvrir des
statistiques sur notre dataset. En remplissant un formulaire
l\'utilisateur doit pouvoir découvrir des statistiques enrichissantes
sur les éléments qu\'il a saisi.

D\'un point de vue personnel, le but de l\'ensemble des collaborateurs
était de :

-   Progresser dans les différents outils utilisés(JS,Python,Flask)
-   Travailler efficacement en équipe (Git, etc \...)
-   Réaliser un projet de bonne qualité, que l\'on pourrait mettre en
    avant par la suite.

**Un début chaotique : le choix du dataSet**

Une des principales difficultées que nous avons rencontré durant ce
projet a été le choix du dataset, ce choix nous a été compliqué pour
différentes raisons:

-   La première raison de cette difficulté a été la volonté de
    travailler sur des données qui nous tiennent à cœur . Des données
    de santé ou encore météorologiques étaient assez facilement
    accessibles, mais ne nous intéressaient pas.
-   La deuxième difficulté rencontrée a été le manque d\'informations et
    de variables dans le jeu de données. Dans le but de faire un
    dashboard, avoir des données diverses et exploitables est quelque
    chose de nécessaire.
-   D\'autres difficultés, comme par exemple le format des données nous
    a fait perdre du temps également.

Etant tous les 3 fans de jeux vidéos, nous avons donc décider de nous
orienter vers une dataset qui recense les ventes de jeux vidéos assez
complÃ¨te pour Ãªtre exploitable.

**Elaboration de notre problématique :**

La problématique qui nous intéresse dans notre cas est de savoir:
\"Comment expliquer les ventes des jeux vidéos selon des critères
diverses ?

Schématisation de notre site

Nous avons décider de créer un formulaire qui permet de trier sur:

- les différents genres de jeux vidéos (via un select)

- les différentes plateformes de jeux vidéos (via un select)

- les différents éditeurs de jeux vidéos (via un select)

- l\'année de sortie de ces jeux vidéos (via un slider double)

Afin de pouvoir répondre à notre problématique, les graphes qui nous
semblent judicieux sont , selon les filtres ci-dessus:

 - Un diagramme circulaire expliquant la répartition des ventes selon les régions. (Popularité des plateformes/genres selon les régions)
   
  
 - Un diagramme en barre représentant les 5 editeurs qui ont le plus vendus. (Popularité des Editeurs)
   
  
 - Un diagramme en barre représentant les plateformes qui ont le plus de critiques sur internet(Popularité globale des plateformes )
   
   
 - Un diagramme temporel qui indique les ventes selon les années de sortie (Popularité des plateformes et Genre dans le temps)

![](Pictures/10000000000002B3000002FF45CB6E53043C5A92.png)

***Mock réalisé avant la conception du site***

**

***Nettoyage des données:***

Nous avons, pour le nettoyage des données, décider de:

-   Supprimer les lignes qui n\'avaient pas de Dates valides (Nan)
-   Supprimer les lignes qui ont ont des valeurs nulles pour les notes
    des critiques /utilisateurs uniquement au moment de la récupération
    des données pour notre graphe répertoriant le nombre de critiques.

**Architecture du logiciel:**

![](Pictures/1000000000000108000001F015901B4928E76C62.png)

Répartition des classes:

-   Dans static/js:

    -   dashboard.js (classe qui recoit la réponse des fonctions de
        api.js et qui grÃ¢ce Ã  ces informations appelle les classes de
        création de graphes dans graph.js)

    -   form.js (classe qui appelle les fonctions d\'update des graphs
        aprÃ¨s la validation du formulaire)

    -   utils:

        -   api.js (classe js qui contient les requÃªtes post vers le
            serveur python)
        -   graphs.js (classe js qui contient les fonctions responsables
            des créations de graphes)

    -   element:

        -   slider.js (classe JavaScript qui decrit le fonctionnement du
            slider)

            element:


-   Dans templates:

    -   base.html et index.html qui sont les fichiers html de base

    -   partials:

        -   dashboard.html (fichier html qui contient le code html des
            graphes)

        -   footer.html (fichier html qui contient le code html du
            footer)

        -   head.html (fichier html qui contient le code html du header
            et des scripts)

        -   form:

            -   form_jv.html (fichier html qui contient le code html du
                formulaire)

**Exemple d\'update du graphe circulaire:**

1)Validation du formulaire

2\)
Récupération dans form.js
```js 
{form.js}
var  message = new  FormData()
message.append("genre", document.getElementById("genre").value);
message.append('platform', document.getElementById("platform").value);
message.append('minYear', document.getElementById("minYear").value);
message.append('maxYear', document.getElementById("maxYear").value);
updatePublisherGraph(publisherGraph,message)
updateSalesGraph(salesGraph,message)
e.preventDefault()
```



3\)
Modification du graphe dans dashboard.js
```js 
{dashboard.js}
export  async  function  updateSalesGraph(chart,message) {
	let  sales = await  getSales(message);
	if (sales !== null) {
		let  labels = []
		let  sales_sum = []
		for (const [key, value] of  Object.entries(sales)) {
			labels.push(key)
			sales_sum.push(value)
		}
		chart.data.datasets[0].data = sales_sum;
		chart.data.labels = labels;
		chart.update();

}

}
```
4\)
Récupération des datas en getSales 
```js 
{api.js}
export  async  function  getSales(message) {
let  res
	try {
		await  axios.post('/api/get/sales', message)
		.then(function (response) {
			res=response.data
		})
		.catch(function (error) {
			console.log(error);
		});
	} catch (error) {
	console.error(error);
	return  null
	}
return  res

}
```

**Réponse à notre problématique**

Il est difficile de valider des réponses avec ce jeu de données et le
peu de temps que l\'on a eu, mais il est possible d\'y extraire quelques
informations:

-   On remarque notamment que certaines plateformes ont un succès bien
    plus élevé dans certaines régions. Nintendo vend, en proportion,
    bien plus au Japon que la plupart des autres éditeurs. Il serait
    donc conseillé pour Nintendo de ne pas négliger le marché Japonais
    et de le prendre en compte sérieusement.On peut le voir avec les
    ventes de la gameboy(a gauche) par rapport aux ventes de
    l\'ensembles des plateformes (à  droite)

![](Pictures/10000000000002BC00000272D046961F5356D644.png)![](Pictures/10000000000002A80000025F916EBDA1C83CFBCD.png)

-   On peut également apercevoir que les ventes des jeux ont augmenté Ã 
    certains moments (pics dans le graphe). Ces moments (2006 /2008)
    semblent correspondre aux sorties des grosses consoles .Logiquement
    les ventes des jeux sont bien plus élevés lors des sorties des
    grosses consoles. On peut voir également une baisse dans les
    dernières années. Cela s\'explique par le fait qu\'il y ait eu moins
    de temps pour acheter les jeux, il est donc nécessaire de laisser du temps aux
    jeux , car leurs ventes peuvent Ãªtre dans la durée

-   On peut également apercevoir que les consoles qui ont le plus de
    succès (PC,PS3,PS4) sont celles qui ont le plus de critiques sur le
    web. Les plateformes qui réussisent Ã  avoir une communauté active
    sont celles qui vendent le plus.Pour les nouveaux éditeurs et
    concepteurs de plateformes , il est donc important de faire des
    jeux de qualité et d\'avoir une communauté active.

Difficultés rencontrées

Nous avons été confronté Ã  plusieurs problèmes:

-   Le premier problème a été de gérer les méthodes asynchrones afin de
    pouvoir visualiser toutes les données directement.
-   Le jeu de données a également posé problÃ¨me, car nous avons
    découvert au fur et à  mesure des données aberrantes (Jeu Ds en 1985
    ?), il y avait également beaucoup de données manquantes. Le nombre
    de lignes (16000), était très important également.
-   Nous avons tenté d\'automatiser la création de l\'ensemble des
    graphes au sein d\'une méthode. Néamoins les petites différences
    entre l\'ensemble des graphes ont donné un rendu moins propre que
    celui souhaité.

**Ressenti sur le projet**

De notre cÃ´té , nous avons beaucoup apprécié travailler sur ce projet,
et de découvrir des nouvelles technologies. Le travail d\'équipe s\'est
également trÃ¨s bien déroulé.

Cependant, nous avons trouvé que le temps prévu pour ce projet était un
peu insuffisant afin de raliser un projet de meilleure qualité.

### Getting In Touch

Please feel free to get in touch with us on Twitter:

- Hamel Husain [@HamelHusain](https://twitter.com/HamelHusain)
- Jeremy Howard [@jeremyphoward](https://twitter.com/jeremyphoward)

___
##### Footnotes

[^1]: You can see some of Hugging Face's Actions workflows for machine learning [on GitHub](https://github.com/huggingface/transformers/tree/master/.github/workflows)
[^2]: These additional dependencies are [defined here](https://github.com/fastai/fastpages/blob/master/_action_files/fastpages-jekyll.Dockerfile), which uses the "jekyll build" command to add ruby dedpendencies from the Gemfile located at the root of the repo.  Additionally, this docker image is built by another Action workflow [defined here](https://github.com/fastai/fastpages/blob/master/.github/workflows/docker.yaml).
