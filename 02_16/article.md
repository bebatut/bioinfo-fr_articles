Comment fixer les problèmes de déploiement et de durabilité des outils en bioinformatique?
======

Actuellement, des données biologiques sont générées à des vitesses folles pour répondre aux questions posées par les chercheurs. La diversité des questions entraine une diversité des données : génomiques, images, _etc_. Pour manipuler les données et extraire les informations utiles, des solutions et outils bioinformatiques sont nécessaires. De nombreux outils existent déjà pour répondre à de nombreuses questions. Mais parfois, des nouveaux outils sont nécessaires pour répondre à une question spécifique. Intervient alors le développement d'un nouvel outil bioinformatique.


# Développer et distribuer un outil bioinformatique : le chemin idéal

Lorsque vous développez un outil bioinformatique, vous le faites dans le but premier de répondre à une question. Une fois celle-ci correctement formulée, vous choisissez votre méthode de travail et les outils ([1](http://bioinfo-fr.net/the-bio-code-guide-du-bon-bioinformaticien) [2](http://bioinfo-fr.net/introduction-sur-les-bonnes-pratiques-de-developpement)) qui vous aiderons à bien gérer votre projet. Par exemple, si vous avez choisi Java pour développer votre projet, il se peut que vous utilisiez [Git](http://bioinfo-fr.net/git-premiers-pas) comme gestionnaire de versions et [Maven](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) comme gestionnaire de _build_.

Vous avez donc écrit du code source et vous pouvez être amener à le publier pour expliquer votre méthode (sinon, ce n'est pas de la science reproductible, donc pas de la science, et tac!). Il vous est alors possible de distribuer votre programme de bien des façons :

- [en imprimant le code (PDF)](http://api.rue89.nouvelobs.com/sites/news/files/assets/document/2016/10/algorithme.pdf) (Ceci est un troll)
- sous la forme d'un [espace web dédiée](https://blast.ncbi.nlm.nih.gov)
- via une plateforme de partage de code source, avec les instructions qui vont bien

Le partage des sources est primordial pour assurer la transparence, mais il peut être particulièrement difficile d'installer correctement un logiciel (multitude de dépendances, incompatibilité entre des versions, _etc_). Le constat est simple : si votre algorithme est révolutionnaire mais que personne ne peut l'utiliser, "je ne lui prédis pas un grand avenir" (#OSS117).

> FIGURE 1
> "question -> dev -> ( support, formation, doc, distribution ) -> déploiement -> utilisation"
> qui situe le développeur et l'utilisateur (l'utilisateur est en charge du déploiement, et donc de gérer les dépendances)

Cette voie royale fonctionne bien lorsque l'outil est simple et ne dépend pas de trop nombreux autres outils. Cependant, la phase de déploiement reste à la charge de l'utilisateur (ou de l'admin sys du labo). Et le déploiement d'un logiciel est la proie de deux grands fléaux :

- les dépendances manquantes (OHMYGOD!)
- les versions des dépendances (I'MGONNADIE!!)

Il s'en suit alors un casse-tête dantesque où l'utilisateur doit installer impeccablement _TOUTES_ les bonnes versions de _TOUTES_ les dépendances (si on peut encore les trouver et si elles sont compatibles avec son système, évidemment). __A LA MAIN !__ C'est évidemment une source colossale de fausse manip' et de découragement pour l'utilisateur, qui préfèrera alors se tourner vers une solution alternative.

Nous sommes donc face à un double problème de durabilité des outils et de leur déploiement. Ceux-ci ont des impacts importants sur la productivité et la reproductibilité en sciences. Il devient donc urgent de résoudre ces deux questions et rendre la bioinformatique meilleure !

# Faciliter le déploiement d'un logiciel

Les problèmes précédemment cités sont une chose que les utilisateurs de systèmes Linux/OSX ne connaissent qu'à moitié, puisque rares sont ceux qui installent tout à la main. Le commun des mortels utilise, quand il le peut, un gestionnaire de paquets. Il en existent plusieurs étant pour la plupart spécifique :

- à un langage ([pip]() pour Python, [CPAN]() pour Perl, [CRAN]() pour R, _etc_)
- à un système d'exploitation ([yum]() pour Fedora, [APT]() pour Debian, [howebrew]() pour OSX, _etc_)

> FIGURE 2
> "question -> dev -> ( support, formation, doc, packaging ) -> déploiement -> utilisation"
> qui situe le développeur, le gestionnaire de paquets et l'utilisateur (le dev est en charge du packaging, le gestionnaire de paquet pour le déploiement, et l'utilisateur pour l'utilisation)

Le packaging demande un petit effort de la part du développeur, mais le déploiement de l'outil derrière le code est grandement facilité. L'utilisateur n'a a se préoccuper que de la partie "utilisation" (ce qui est somme toute plutôt logique). Pour que tout soit parfait, il est également nécessaire de documenter le logiciel, de proposer des formations, du support et d'en faire la publicité.


# La solution à tous nos maux : Conda

Pour qu'un outil soit utilisé, il doit être facilement déployable n'importe où. Pour cela, il faut le packagé avec un gestionnaire de paquets qui soit :

- Indépendant d'un langage de programmation : Des outils bioinformatiques sont disponibles dans pratiquement tous les langages disponibles
- Indépendent du système d'exploitation : Les outils sont utilisés sur les principaux systèmes d'exploitation
- Indépendant de privilèges super utilisateurs : Certains utilisateurs n'ont pas les droits d'administration de leur ordinateur
- Capable de gérer plusieurs versions d'outils : Des versions différentes d'un outil peuvent être requises par différents outils
- Compatible avec une utilisation sur le Cloud ou en environnement HPC

[conda](https://conda.io/docs/intro.html) est un gestionnaire de paquets open-source qui répond très bien à ces problématiques. Bien que développé par la communauté PyData, conda est conçu pour gérer des paquets et dépendances de n'importe quel programme dans n'importe quel langage. conda est donc moins `pip` qu'une version multi système d'exploitation de `apt` et `yum`.

Un paquet conda correspond à un fichier tarball contenant des librairies au niveau système, Python ou d'autres modules, des programmes exécutables ou d'autres composants. En distribuant des outils précompilés, l'installation de paquet conda est rapide, robuste et facile : 

```
$ conda install deeptools
```

conda garde une trace des dépendances entre les paquets et les plateformes. conda vient aussi avec une application de gestion d'environnement, sur le même principe que les environnements virtuels de Python. Un [environnement conda](https://conda.io/docs/using/envs.html) est un dossier contenant une collection spécifique de paquets conda installés. Ce principle permet de gérer l'installation et la gestion de plusieurs versions d'outils, comme Python 2.7 et Python 3.5 par exemple.

Encore des résistances vis à vis de Conda? Je vous conseille de lire ce [blog post](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/) sur les mythes et fausses idées liées à Conda. 

Envie d'écrire un paquet conda pour un outil ? On pourrait penser que cela est difficile étant donnés les avantages apportés par conda. Mais au contraire, l'écriture de paquets conda a été pensée pour être facile et permettre ainsi à tous d'intégrer les outils dans conda avec une [documentation extensive](https://conda.io/docs/building/build.html). Ainsi, un paquet conda consiste en deux fichiers : 

- Un fichier YAML contenant les méta-données du paquet : nom, où trouver les sources de l'outil, dépendances de l'outil, ... 
- Un fichier BASH pour expliquer comment conda doit créer le paquet

![](images/conda_package.png)

Les *channels* sont les chemins que prend conda pour chercher des paquets. Les outils assez généralistes peuvent être trouvé dans le channel [*conda-forge*](https://conda-forge.github.io/). Spécialisés dans les outils bioinformatiques, le *channel* [Bioconda](https://bioconda.github.io) est consiste en :

- Un [dépôt GitHub](https://github.com/bioconda/bioconda-recipes) de recettes
- Un système de construction qui transforme ces recettes en paquet conda
- Un répertoire de plus de 2 000 paquets bioinformatiques prêt à être utilisés

Avec resque 200 contributeurs, cette communauté accueillante et formée il y a un peu plus de 1 an grossit rapidement. Elle permet d'ajouter, modifier, mettre à jour et maintenir les nombreuses recettes des paquets conda d'outils bioinformatiques. 

Pour faciliter le déploiement tout en suivant les besoins évoqués précédemment, un autre moyen de packager un outil est de le containeriser. La containerisation la plus connues est [Docker](https://www.docker.com/), mais il existe d'autres solutions comme [rkt](https://github.com/coreos/rkt) ou [Singularity](http://singularity.lbl.gov/). Ces containeurs permettent d'obtenir un plus haut niveau d'abstraction du système de base pour un outil. 

La création de containeurs pour un outil est plus complexes que pour créer un paquet conda. Par exemple, pour créer un containeur Docker, il faut créer un fichier *Dockerfile* décrivant l'image de base utilisées, les commandes pour créer l'outil, ...

[Mulled](https://github.com/mulled/mulled) est un projet permettant de générer un containeur minimal pour Docker avec [BioContainer](http://biocontainers.pro/) ou rkt à partir d'un paquet conda, alpine or linuxbrew. Il faut seulement ajouter un ligne dans un fichier TSV pour indiquer à Mulled de créer le containeur. 

![](images/mulled_scheme.png)

Pour des paquets Bioconda, c'est encore plus facile : il n'y a rien à faire. Mulled parcourre tous les paquets Bioconda tous les jours et génère des BioContainer automatiquement pour tous les paquets BioConda.

![](images/tool_deployment.png)

En packageant les outils avec conda au sein de Bioconda, on résoud le problème de déploiement des outils. Les outils deviennent facilement déployable via les paquets conda ou via des BioContainer construits automatiquement.

# La durabilité et disponibilité

Un outil peut dépendre de nombreux autres outils, qui peuvent ne plus être maintenus ou même disponibles. Ce problème de disponibilité des outils posent de nombreux problèmes dont celui de reproductibilité et durabilité. 

Pour résoudre ces problèmes, l'idéal serait d'avoir un stockage permanent de toutes les versions des paquets et outils utilisés pour qu'ils soient toujours accessibles. 

La reproducibilité et l'accessibilité sont des causes qui tiennent à coeur à la communauté autour du projet [Galaxy](https://galaxyproject.org/). Ainsi, pour répondre au problème de disponibilité et de durabilité des outils et paquets, la communauté a mis en place [Cargo Port](https://depot.galaxyproject.org/software/), un répertoire public pour archiver de nombreux paquets de façon stable et permanente. 

Ajouter un paquet dans ce dépôt est facile. Il suffit d'ajouter une ligne dans un fichier TSV avec les informations (nom et URL) sur le paquet à stocker. Pour les paquets créés avec Bioconda, c'est même encore plus facile : il n'y a rien à faire. Cargo Port fait des archives journalières des recettes et paquets Bioconda.

![](images/tool_sustainability.png)

Avec la sauvegarde automatique des paquets Bioconda au sein de Cargo Port, le problème de durabilité et disponibilité des outils est fixé.

# Déploiement et durabilité des outils en bioinformatique: Fixés !

Le développement des paquets Bioconda est très facile et facilite le packaging et le déploiement de tout outil bioinformatique. Avec le projet Muller, des containeurs Linux efficaces sont automatiquement construits pour tous paquets conda pour permettre un plus haut niveau d'abstraction et d'isolation par rapport au système de base. C'est un super effort de différentes communautés pour créer un système flexible et extensible pour fixer le problème de déploiement une fois pour toute.

L'interface avec paquets Bioconda avec Cargo Port améliore la disponibilité et la durabilité en conservant toutes les sources.

![](images/tool_development.png)

J'espère vous avoir convaincu que grâce à ces projets collaboratifs, leur communauté et leurs collaborations, les outils bioinformatiques peuvent être facilement packagé et être toujours disponibles pour leurs utilisateurs. La seule chose à faire est de créer une recette Bioconda et rendre ainsi vos utilisateurs heureux et leurs (et vos) analyses efficaces et reproductibles !
