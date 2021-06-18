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

## Comment expliquer le fait qu\'un jeu soit un v�ritable succ�s ??

**L\'Objectif de notre Projet**

Du point de vue du projet, notre objectif est de r�aliser un dashboard
complet et fonctionnel qui permet aux utilisateurs de d�couvrir des
statistiques sur notre dataset. En remplissant un formulaire
l\'utilisateur doit pouvoir d�couvrir des statistiques enrichissantes
sur les �l�ments qu\'il a saisi.

D\'un point de vue personnel, le but de l\'ensemble des collaborateurs
�tait de :

-   Progresser dans les diff�rents outils utilis�s(JS,Python,Flask)
-   Travailler efficacement en �quipe (Git, etc \...)
-   R�aliser un projet de bonne qualit�, que l\'on pourrait mettre en
    avant par la suite.

**Un d�but chaotique : le choix du dataSet**

Une des principales difficult�es que nous avons rencontr� durant ce
projet a �t� le choix du dataset, ce choix nous a �t� compliqu� pour
diff�rentes raisons:

-   La premi�re raison de cette difficult� a �t� la volont� de
    travailler sur des donn�es qui nous tiennent � c�ur . Des donn�es
    de sant� ou encore m�t�orologiques �taient assez facilement
    accessibles, mais ne nous int�ressaient pas.
-   La deuxi�me difficult� rencontr�e a �t� le manque d\'informations et
    de variables dans le jeu de donn�es. Dans le but de faire un
    dashboard, avoir des donn�es diverses et exploitables est quelque
    chose de n�cessaire.
-   D\'autres difficult�s, comme par exemple le format des donn�es nous
    a fait perdre du temps �galement.

Etant tous les 3 fans de jeux vid�os, nous avons donc d�cider de nous
orienter vers une dataset qui recense les ventes de jeux vid�os assez
complète pour être exploitable.

**Elaboration de notre probl�matique :**

La probl�matique qui nous int�resse dans notre cas est de savoir:
\"Comment expliquer les ventes des jeux vid�os selon des crit�res
diverses ?

Sch�matisation de notre site

Nous avons d�cider de cr�er un formulaire qui permet de trier sur:

- les diff�rents genres de jeux vid�os (via un select)

- les diff�rentes plateformes de jeux vid�os (via un select)

- les diff�rents �diteurs de jeux vid�os (via un select)

- l\'ann�e de sortie de ces jeux vid�os (via un slider double)

Afin de pouvoir r�pondre � notre probl�matique, les graphes qui nous
semblent judicieux sont , selon les filtres ci-dessus:

 - Un diagramme circulaire expliquant la r�partition des ventes selon les r�gions. (Popularit� des plateformes/genres selon les r�gions)
   
  
 - Un diagramme en barre repr�sentant les 5 editeurs qui ont le plus vendus. (Popularit� des Editeurs)
   
  
 - Un diagramme en barre repr�sentant les plateformes qui ont le plus de critiques sur internet(Popularit� globale des plateformes )
   
   
 - Un diagramme temporel qui indique les ventes selon les ann�es de sortie (Popularit� des plateformes et Genre dans le temps)

![](Pictures/10000000000002B3000002FF45CB6E53043C5A92.png)

***Mock r�alis� avant la conception du site***

**

***Nettoyage des donn�es:***

Nous avons, pour le nettoyage des donn�es, d�cider de:

-   Supprimer les lignes qui n\'avaient pas de Dates valides (Nan)
-   Supprimer les lignes qui ont ont des valeurs nulles pour les notes
    des critiques /utilisateurs uniquement au moment de la r�cup�ration
    des donn�es pour notre graphe r�pertoriant le nombre de critiques.

**Architecture du logiciel:**

![](Pictures/1000000000000108000001F015901B4928E76C62.png)

R�partition des classes:

-   Dans static/js:

    -   dashboard.js (classe qui recoit la r�ponse des fonctions de
        api.js et qui grâce �  ces informations appelle les classes de
        cr�ation de graphes dans graph.js)

    -   form.js (classe qui appelle les fonctions d\'update des graphs
        après la validation du formulaire)

    -   utils:

        -   api.js (classe js qui contient les requêtes post vers le
            serveur python)
        -   graphs.js (classe js qui contient les fonctions responsables
            des cr�ations de graphes)

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
R�cup�ration dans form.js
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
R�cup�ration des datas en getSales 
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

**R�ponse � notre probl�matique**

Il est difficile de valider des r�ponses avec ce jeu de donn�es et le
peu de temps que l\'on a eu, mais il est possible d\'y extraire quelques
informations:

-   On remarque notamment que certaines plateformes ont un succ�s bien
    plus �lev� dans certaines r�gions. Nintendo vend, en proportion,
    bien plus au Japon que la plupart des autres �diteurs. Il serait
    donc conseill� pour Nintendo de ne pas n�gliger le march� Japonais
    et de le prendre en compte s�rieusement.On peut le voir avec les
    ventes de la gameboy(a gauche) par rapport aux ventes de
    l\'ensembles des plateformes (�  droite)

![](Pictures/10000000000002BC00000272D046961F5356D644.png)![](Pictures/10000000000002A80000025F916EBDA1C83CFBCD.png)

-   On peut �galement apercevoir que les ventes des jeux ont augment� � 
    certains moments (pics dans le graphe). Ces moments (2006 /2008)
    semblent correspondre aux sorties des grosses consoles .Logiquement
    les ventes des jeux sont bien plus �lev�s lors des sorties des
    grosses consoles. On peut voir �galement une baisse dans les
    derni�res ann�es. Cela s\'explique par le fait qu\'il y ait eu moins
    de temps pour acheter les jeux, il est donc n�cessaire de laisser du temps aux
    jeux , car leurs ventes peuvent être dans la dur�e

-   On peut �galement apercevoir que les consoles qui ont le plus de
    succ�s (PC,PS3,PS4) sont celles qui ont le plus de critiques sur le
    web. Les plateformes qui r�ussisent �  avoir une communaut� active
    sont celles qui vendent le plus.Pour les nouveaux �diteurs et
    concepteurs de plateformes , il est donc important de faire des
    jeux de qualit� et d\'avoir une communaut� active.

Difficult�s rencontr�es

Nous avons �t� confront� �  plusieurs probl�mes:

-   Le premier probl�me a �t� de g�rer les m�thodes asynchrones afin de
    pouvoir visualiser toutes les donn�es directement.
-   Le jeu de donn�es a �galement pos� problème, car nous avons
    d�couvert au fur et �  mesure des donn�es aberrantes (Jeu Ds en 1985
    ?), il y avait �galement beaucoup de donn�es manquantes. Le nombre
    de lignes (16000), �tait tr�s important �galement.
-   Nous avons tent� d\'automatiser la cr�ation de l\'ensemble des
    graphes au sein d\'une m�thode. N�amoins les petites diff�rences
    entre l\'ensemble des graphes ont donn� un rendu moins propre que
    celui souhait�.

**Ressenti sur le projet**

De notre côt� , nous avons beaucoup appr�ci� travailler sur ce projet,
et de d�couvrir des nouvelles technologies. Le travail d\'�quipe s\'est
�galement très bien d�roul�.

Cependant, nous avons trouv� que le temps pr�vu pour ce projet �tait un
peu insuffisant afin de raliser un projet de meilleure qualit�.

### Getting In Touch

Please feel free to get in touch with us on Twitter:

- Hamel Husain [@HamelHusain](https://twitter.com/HamelHusain)
- Jeremy Howard [@jeremyphoward](https://twitter.com/jeremyphoward)

___
##### Footnotes

[^1]: You can see some of Hugging Face's Actions workflows for machine learning [on GitHub](https://github.com/huggingface/transformers/tree/master/.github/workflows)
[^2]: These additional dependencies are [defined here](https://github.com/fastai/fastpages/blob/master/_action_files/fastpages-jekyll.Dockerfile), which uses the "jekyll build" command to add ruby dedpendencies from the Gemfile located at the root of the repo.  Additionally, this docker image is built by another Action workflow [defined here](https://github.com/fastai/fastpages/blob/master/.github/workflows/docker.yaml).
