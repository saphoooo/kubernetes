![Electro Monk3ys in the Cloud](https://user-images.githubusercontent.com/13923756/75849634-99f3b100-5de5-11ea-97f4-27ee0b3b9457.png)

Si vous êtes ici, c'est sans doute parce que vous avez apprécié les deux premiers épisodes de ce guide. Mais je préfère vous mettre en garde, les choses risquent de changer ! Et il est possible qu'après avoir écouté ce que j'avais à vous dire, vous ne m'aimiez plus du tout. Ceci dit, je ne suis pas payé pour être populaire, et à vrai dire je ne suis même pas payé du tout...

Alors redresser votre siège, remontez vos tablettes, attachez vos ceintures et préparer vous à l'atterissage.

Si vous avez jamais entendu quelqu'un dire que le cloud c'est simplement faire tourner son application sur la machine de quelqu'un d'autre, fuyez cette personne ! Il n'a simplement rien compris !

D'autres peut-être vous dirons que le cloud c'est de la ressource à la demande, élastique (comme le copain de la femme invisible) et self-service.

Certes, c'est un concept qui a de quoi séduire, mais c'est un peu plus compliqué que ça. Devant ces ressources, il y a un modèle de consommation : celui d'Amazon, ou celui de Google, ou celui de Pivotal, etc. Devant ces différents modèles, il convient de se demander : est-ce que sérieusement j'ai envie de consommer de la machine virtuelle ? Je ne sais pas pour vous, mais personnellement, j'ai un début de mal de crâne en m'imaginant devoir choisir un type d'instance (tiens oui au fait, j'ai besoin de combien de mémoire et de combien de cpu pour faire tourner mon application dont j'ignore encore si elle sera populaire ou non), un système d'exploitation (c'est notre interface homme-machine), puis de devoir m'y connecter, faire les mises à jour, y installer mes librairies et mes binaires, puis enfin mon code, et devoir ensuite me préoccuper de gérer tout le cycle de vie et la sécurité de cette pathétique machine virtuelle.

Qui ça intéresse ? La machine virtuel est le symbole de notre résistance au chagement et de notre incapacité d'innover.

Bien sûr, sous un certain angle le cloud n'est jamais que du temps cpu, de la mémoire, du réseau et du stockage, mais est-ce le bon angle d'approche ? Après tout, ne pourrions-nous pas simplement nous imaginer que c'est simplement un endroit où tourne notre code, sans contrainte de ressources ?

C'est avec cette vision que Google App Engine, Heroku et CloudFoundry sont arrivés. Et vous savez quoi ? Pour les développeurs, ça a vraiment tout changé. Pousser son code, le voir s'exécuter et qu'il se mette à l'échelle automatiquement, quelle magie ! Dès lors les machines et leurs systèmes d'exploitation compliqués n'existent plus ; fini de gaspiller son temps en configuration et en patching ; désormais on dispose d'une abstraction qui s'appelle le cloud !

## Perdu dans l'espace

Rappelez vous que dans notre chronologie, nous ne sommes jamais qu'aux alentours de cette bonne vieille année 2010. Or les premiers colons à tenter l'expérience du cloud ont dû pas mal en souffrir. Je me représente la scène comme un départ pour Mars : un grand saut vers l'inconnu. Il y a eu du sang, des larmes et de la sueur, mais ce qui en est ressorti au final est génial : une méthodologie baptisée *The Twelve-Factor App*.

Je sais, une méthodologie quel ennui vous vous dites, pareil pour moi. Cependant, que penseriez-vous si je vous disais qu'elle tient à peine en 12 lignes, et que vous aurez plus vite fait de la lire plutôt que je vous l'explique ? Si vous ne me croyez pas, c'est par ici que ça se passe, [The Twelve-Factor App](https://12factor.net/fr/) et en *french pliz* !

Plus qu'une méthodologie ça pourrait presque ressembler à du bon sens. Dans ce cas, quel est l'intérêt de rappeler des règles qui ne sont jamais que du bon sens ? Parce que nous avons été, vous et moi, dévoyés, corrumpus, floués, préoccupés par des considérations qui n'auraient jamais dû être les notres, et nous avons perdu notre bon sens.

Prenons un bête exemple : les logs. Est-ce que ça concerne vraiment l'application qu'ils soient envoyés vers un fichier ou un serveur ? Non. L'application a juste la responsabilité de produire des logs sous la forme d'un flux d'évènements que l'environnement n'a plus qu'à capturer, et à traiter de la manière qui lui semble la plus appropriée.

Autre exemple, les services dont l'application dépend, comme sa base de donnée : est-ce à l'application de contenir dans son code le chemin pour y accéder, ou ce chemin doit-il être un élément de configation que l'application doit tirer de son environnement ? Bien entendu, l'application doit le tirer de son environnement, car il y a des chances pour que la base de données ne soit pas la même dans votre environnement de développement que dans celui de production, et il serait désastreux de devoir modifier votre code pour changer ça.

Vous trouvez que j'enfonce des portes ouvertes ? Pourtant ça n'est pas évident pour tout le monde, et la véritable question c'est : pourquoi ?

Rien ne vous oblige à adhérer à cette méthodologie, mais si vous rêver d'une application portable, extensible et résiliente, pensez **The Twelve-Factor App**.

## L'abattoire

Je m'excuse par avance s'il y a des défenseurs de la cause animale parmi vous, cependant gardez à l'esprit que :

1. L'analogie cette fois ne vient pas de moi.
1. Ce n'est qu'une analogie, et qu'il n'est pas vraiment question ici de tuer des animaux, ni même de leur faire le moindre mal.

Cette analogie illustre avec brio la propention que nous avons à nous tirer une balle dans le pied sans arrêt. Elle s'appelle *Pet vs Cattle* (ou animal de compagnie contre troupeau). 

Vous vous souvenez au début notre l'histoire, je vous ai parlé de ces grosses machines hors de prix. Telle machine ne peut pas être remplacée aisément, et nous avons pris l'habitude de la traiter avec soin et un max d'attentions. Qu'elle vienne à tomber, et c'est la panique à bord, chacun doit y aller de sa personne pour qu'elle soit de nouveau opérationnelle le plus tôt possible. C'était peut-être ce qu'on pouvait faire de mieux à l'époque, en effet.

Mais c'est bizarrement un modèle que nous avons pris l'habitude de conserver, même quand les machines sont devenues moins coûteuses, même quand les machines sont devenues virtuelles. On a mis en place des machines avec des noms sympas (Starky et Hutch n'est qu'un exemple parmis d'autres), montées et maintenues à la main.

Et avec quelques opérations manuelles, nous étions capable de faire basculer un service de l'une à l'autre...

Mais que l'une de ces machines vienne vraiment à mourir, et c'était la fin ! Comment remplacer une machine qui avait demander tant d'amour ? Elle était unique, et il faudrait peut-être des jours pour en remettre une nouvelle en place ; pendant ce temps, que va devenir Starsky sans Hutch ? Pourvu qu'il ne lui arrive rien !

Ce bon vieux modèle de haute disponibilité a fait son temps : il n'est plus suffisant pour nous garantir contre les conséquences d'une lourde panne, dont tout le monde commence à prendre conscience que, quoi qu'on fasse, elle arrivera.

L'outillage ne nous faisait pourtant pas défaut : CFEngine était là depuis presque 20 ans et Puppet faisait ses débuts. Mais que voulez-vous ?

Ce que nous aurions dû apprendre plus tôt, c'est à traiter nos chères machines comme un troupeau au lieu de les traiter comme un animal de compagnie. Ça commence en leur donnant des noms comme *webserver001*, *webserver006*, et ça suppose de gérer leur configuration et leur cycle de vie de manière totalement automatisée. Quand quelque chose vient à mal se passer, aucune intervention humaine ne doit être nécessaire : la machine est simplement supprimée et remplacée. Parce que la machine n'est pas la seule à savoir faire ce qu'elle sait faire : après tout elle fait partie d'un troupeau de machines qui font exactement la même chose qu'elle, ce qui permet au service de ne jamais être mis en péril par la disparation d'une machine du troupeau.

## De l'automatisation à tous les étages

Ce besoin de résilience nous a entrainé toujours plus loin dans la volonté de tout automatiser. Mais je vous pose la question, qu'est-ce qui aujourd'hui vous retient d'automatiser ?

Mon annecdote favorite à ce sujet reste la boite de vitesse, et je sais par avance que je ne vais pas me faire d'amis en la racontant. Ça fait des années que j'ai une voiture à boite automatique, et j'en suis super content ; tellement content que, bien sûr, j'en parle tout le temps autour de moi. Figurez-vous qu'à ce jour je n'y ai converti absolument personne.

Et les réponses qu'on m'opose ne sont pas : "j'ai peur de consommer plus de carburant", "ça coûte plus cher en entretien non ?", ou le génialissime "j'ai un meilleur algorithme pour passer les vitesses qu'une boite xyz". Non, je me suis toujours retrouvé confronté à la même réaction émotionnel : "j'aime bien passer les vitesses". Sérieusement ? Qu'est-ce que tu aimes bien quand tu es dans un bouchon à jongler toutes les 10 secondes entre la première, la seconde ou la troisième ? Oui, c'est terriblement bien de se concentrer sur ses rapports de vitesse en ville alors que ton attention devrait se porter partout ailleurs. Mais c'est évident que pour optimiser ton passage de 120km/h à 130km/h sur autoroute tu vas d'abord rétrograder pour donner un peu plus de swag à ta conduite. Non, vous voulez que je vous dise, vous manquez d'empathie envers vous-même.

Revenons à nos brebis.

Pour automatiser, il existe des outils qui s'appellent des gestionnaires de configuration. J'ai déjà évoqué CFEngine et Puppet, mais j'aurais tout aussi bien pu vous donner une tout autre liste : Chef, Ansible, Salt, pour ne nommer que les plus connus. Mais dites-vous une chose, aussi cools qu'ils soient pour vous (ou non), ils ne restent que des outils. Et de vous à moi, un simple script maison pourrait tout aussi bien faire l'affaire.

C'est quoi l'idée ? Gérer et maintenir des configurations. Alors penchons-nous un instant sur le cycle de vie d'une machine :

1. Elle est mise dans un centre de données, raccordée à l'électricité et au réseau (opération coûteuse et compliquée à automatiser, donc je ne vous jeterai pas la pierre si vous le faites encore manuellement).
2. Elle a une configuration initiale qui comprend un système d'exploitation et des paramètres spécifiques selon :
  1. Sa localisation (elle est dans le centre de données de Palavas les Flots)
  1. Sa fonction (elle va servir de front-end web)
3. Elle est ensuite préprarée pour l'application qu'elle va héberger : libraires, binaires, etc.
4. Finalement elle reçoit l'application qui lui donne vie.

Et bien sûr toutes ces informations ont besoin d'être consignées dans une base de données, pour qu'à chaque moment on puisse se faire une idée juste de l'état de son parc (un parc ? alors finalement l'analogie était là depuis longtemps, car j'ai plus souvent vu des vaches dans un parc que des chats, pas vous).

Pas si vite coco, ne manquerez-vous pas de me dire, dans ce scénario jamais la machine n'est mise à jour ? C'est un peu facile. Soyez sans crainte, je vais revenir sur ce sujet.

On peut faire tout ça à la main, enfin quand je dis "on", vous peut-être, moi certainement pas. Une fois la première phase accomplie, je peux démarrer ma machine sur PXE pour l'installer et lui fournir une configuration de base, puis lui passer un coup de Puppet pour la mettre à jour et y déployer librairies et binaires avant de la laisser aux bons soins de Dr Jenkins qui gérera élégament le déploiement de mon application. Je peux ainsi configurer 1 ou 1000 machines, le temps que ça me prend reste à peu de choses près le même. Je me ferai peut-être l'effet d'être un dinausore, mais pour une machine physique, c'est tout à fait acceptable.

Mais le cloud nous offre mieux : il nous permet de mettre tout notre stockage et notre puissance de calcul dans un grand pot commun accessible à partir d'une API (RESTful le plus souvent). Dafuq ? A ce stade, je ne vais pas me lancer dans l'explication la plus décalée de ce qu'est une API RESTful ; mais si ça ne vous est pas vraiment familier, je vais tout de même tenter de vous en donner un bref apperçu.

Imaginez que j'ai trois gamins spécialisés dans la construction d'objets en briques plastiques (rassurez-vous, ils ne sont pas exploités pour faire ce genre de constructions, ce n'est que 16 heures de bénévolat par jour, tout au long de l'année). Qui des voitures, qui des châteaux et qui des vaisseaux. Problème, l'un des gamins parle chinois, le second arabe, et le troisième espagnol, et pauvre de moi, aucune de ces trois langues.

Mais je connais quelqu'un, Maya (parce que Maya l'API, mauvais jeu de mot mais qui me sauvera de moi-même ?), qui sait parfaitement comment communiquer avec ces gamins. Maya sait me décrire le type de services qu'elle est en mesure de me fournir, et la façon dont je peux les lui demander :
- crée le château xyz de couleur jaune
- liste les voitures
- change la couleur de la voiture xyz pour qu'elle soit rouge
- detruit le vaisseau xyz

J'utilise des verbes simples pour demander : crée (POST), liste (GET), change (PATCH) et détruit (DELETE) ; j'ai mis leur équivalent en verbes HTTP entre parenthèses pour vous familiariser.

J'ai un contrat avec Maya : j'utilise les verbes que je viens d'énoncer pour faire une demande, je choisis l'objet sur lequel porte mon action (château, voiture ou vaisseau), et je peux y ajouter occasionnellement une série d'arguments.

Si je demande à Maya de créer une voiture jaune, Maya me répondra que la voiture a été créée (201). Si par contre je lui demande de me créer une fusée blanche et rouge, il y a des chances qu'elle me dise qu'elle ne connait pas l'objet dont je lui parle (404). En plus d'une réponse, elle me fourni un code retour, 201 ou 404, qui est un code de retour standardisé (voir les [*status code* HTTP](https://fr.wikipedia.org/wiki/Liste_des_codes_HTTP)).

Maya me libère d'avoir à connaître 3 langues, et je n'ai même plus besoin de savoir quoi que ce soit à propos des gamins qui travaillent pour elle. Si celui qui parle espagnol s'en va et est remplacé par un autre qui parle suédois, ça ne change rien pour moi, tant qu'il sait construire des vaisseaux.

![Maya in the Cloud with Diammonds](https://user-images.githubusercontent.com/13923756/76161515-70fc5480-6134-11ea-9148-08f8a33a8896.png)

## Cachez ce CPU que je ne saurais voir

Ce qu'il y a de chouette dans tout ça, c'est que je n'ai qu'une seule langue à connaître, et que cette langue est on ne peut plus sobre. Du coup, commander 3 voitures de couleurs différentes devient d'une simplicité écoeurante :

```
for i in blue rouge jaune ; do maya crée une voiture ${i} ; done
```

En retour, je recevrai 201, 201 et peut-être 500, malheureusement ça arrive, ce qui pourrait signifier que Maya est débordée par le travail que je lui donne.

Mais quand bien même ! Ce que m'apprennent ces codes de retour :
- Maya n'a pas pu traiter la création de la voiture jaune, et que le problème vient d'elle et pas de moi (erreur 5xx)
- deux de mes voitures sont déjà créées
- pour la troisième, la jaune, il me faudra peut-être retenter ma chance dans une heure

Irrésistiblement simple non ?

Mais revenons à nos joujoux informatiques. Puisque le cloud m'offre une API pour créer des objets tels que des machines virtuelles, des firewalls, des réseaux, voire beaucoup mieux des bases de données ou des agents de messages, qu'est-ce qui m'empêche de créer des scripts pour gérer mon infrastructure. Rien, en effet, et c'est ce qui explique l'émergeance d'outils comme CloudFormation (AWS), Cloud Deployment Manager (GCP) ou Terraform ; une nouvelle façon de porcéder qui s'appelle l'*Infrastructure as Code*.

L'*Infrastructure as Code* (IaC) permet de décrire l'infrastructure qu'on désire, une machine qui expose un port 80 sur internet et qui est reliée par le port 5432 à une base de donnée Postgresql, et Maya se charge du reste.

Rassembler l'*Infrastructure as Code* et un outil de gestion de configuration, et la partie approvisionnement est réglé une bonne fois pour toute, et de manière totalement automatisée ! (J'entends d'ici quelqu'un dire : "j'aime bien mettre en place mes machines virtuelles à la main"...)

![Les individus n'évoluent pas tous au même rythme...](https://user-images.githubusercontent.com/13923756/76169159-d6246a00-6175-11ea-98ea-1b0d06bda049.png)

Approvisionnement, et pas cycle de vie, c'est vrai ; pour gérer le cycle de vie, il faut aussi avoir des réponses automatisées en cas d'erreur... On se rapproche furieusement de Kubernetes ou je me trompe ? Mais n'allons pas trop vite, j'ai encore besoin de vous retourner la cervelle une ou deux fois.

## Vous avez dit immutable, comme c'est immutable

Bravo, vous gérez de l'infrastructure avec du code, donc maintenant vous êtes un développeur ! Avouez, vous l'aviez pas vue venir celle-là. Pourtant, c'est une réalité, c'en est fini de jouer les gros barbus qui montent leur centre de calcul dans leur garage. Et parce que votre infrastructure est un code, il va maintenant vous falloir la gérer... comme du code, et commencer à utiliser un outil de gestion de version (Git), et aussi versionner vos déploiements.

Repartons de notre simplissime exemple : un front-end présentant son port 80 sur internet et reliée à une base de données sur le port 5432. Imaginons que vous souhaitiez maintenant exposer le port 443 au lieu du port 80, pensez vous que vous aller faire une mise à jour de la régle dans le firewall ? Pas exactement : vous aller plus sûrement supprimer la règle et en créer une nouvelle (c'est moins source d'erreur, et plus simple pour tout le monde).

- Première version de votre code : un front-end présentant son port 80 sur internet et reliée à une base de données sur le port 5432.
- Deuxième version de votre code : un front-end présentant son port 443 sur internet et reliée à une base de données sur le port 5432.

Maintenant, plus compliqué : vous voulez mettre à jour votre machine virtuelle. Instinctivement, vous allez me dire que vous vous connecter à la machine et que vous allez lancer la mise à jour. Erreur, ça serait mal !

N'oubliez pas que votre machine fait partie d'un troupeau, et qu'il y en a probablement 500 autres comme elle. Dans ce cas vous allez me dire, j'utilise mon outil de gestion de configuration, et je mets tout le troupeau à jour. L'approche est meilleure, mais malheureusement pas sans effets de bord. Le premier : comment êtes vous assuré qu'à cet instant toutes vos machines sont à la même version ? Le dernier : sur le long terme, comment allez-vous vous assurer qu'il n'y ait jamais la moindre divergeance entre ces machines ? Et plein d'autres niveau de gris au milieu...

Une autre approche bien moins intuitive (à cause de notre éducation) : on crée une nouvelle machine contenant les mises à jour pour remplacer l'ancienne. L'ancienne portait la version 1, la nouvelle la version 2.

D'un seul coup d'oeil, ou d'une seule commande, je peux m'assurer que mes 500 machines sont exactement à la même versions. Que quelque chose n'aille pas dans cette version, et je peux revenir à la version précédente, puisque c'est une configuration qui est connue. Je n'ai jamais de divergeance, parce que je déploie toujours des nouvelles machines pour remplacer les anciennes.

J'ai entendu une fabuleuse analogie à ce sujet (je crois qu'elle est d'Akshay Manchale), vous voulez que je vous la raconte ?

Savez vous ce qu'est un numéro ISBN ? ISBN signifie *International Standard Book Number*, ou Numéro International Normalisé du Livre. Quand un livre est édité, il est affublé de ce drôle de numéro, exemple ISBN-2-7654-1005-4.

Si le livre "Le Guide le plus Décalé pour Comprendre Kubernetes" venait à être édité en 2020, sa première version hériterait d'un numéro similaire, disons ISBN-1-2222-0000-1. Mettons que ce fameux guide soit si populaire qu'une édition de poche soit mise en vente, et elle portera le numéro ISBN-1-2222-0012-3. Une seconde version est maintenant sous presse, et elle portera le numéro ISBN-1-2222-0172-4. Vous avez saisi le principe je pense.

Maintenant, si je demande à quelqu'un d'ouvrir la page 100 du livre portant la référence ISBN-1-2222-0000-1 je sais avec certitude ce qu'il va trouver à cette page, car cette référence identifie de façon unique un livre, et tout autre variante de ce livre porte une autre référence.

C'est le principe fondamental du numéro de version : il est immutable. Chaque fois que vous demander cette version avec son numéro, vous avez exactement la même chose, à la virgule près. Aussi, si vous versionnez la configuration de vos machines (et j'entends par là logiciels et mises à jour), vous vous assurez de savoir précisément ce que voous tourner dans votre parc. Si la version 319 à jamais fonctionné, elle fonctionnera toujours, toujours de la même manière.

Sur ce principe, appliquer un patch à vos front-ends web peut ressembler à ceci :

- Vous avez 3 machines [web-fcf8f694, web-25518417, web-8c342ded]
- Vous ajoutez une machine à votre troupeau [web-fcf8f694, web-25518417, web-8c342ded, web-d943d948]
- Dès qu'elle est opérationnelle, vous supprimer l'une des anciennes [web-25518417, web-8c342ded, web-d943d948]
- Puis vous répétez l'opération jusqu'à avoir remplacé toutes vos machines
- [web-fcf8f694, web-25518417, web-8c342ded] portent la configuration 345
- [web-d943d948, web-740afe52, web-21bd8d3a] portent la configuration 346

Ou à ceci :

- Vous avez 3 machines [web-fcf8f694, web-25518417, web-8c342ded]
- Vous en mettez 3 nouvelles en place [web-d943d948, web-740afe52, web-21bd8d3a]
- Dès qu'elles sont opérationnelles, vous redirigez le trafic entrant vers celles-ci, et vous coupez celui qui vient sur les anciennes machines.

Il y a encore une quatité d'autres variantes sur lesquelles nous aurons l'occasion de revenir.

La configuration de vos machines à cet instant comprend :
- La version de son système d'exploitation
- Sa version de patch
- Sa configuration
- Des binaires et des librairies à une version spécifique pour faire tourner votre application
- Votre application

Si demain vous choisissez de déployer 100 fois votre application par jour, même si ça reste faisable, ça risque de vite devenir fastidieux, parce que préparer 100 templates de machines prend du temps, même quand c'est automatisé, que la création et la destruction de machines prend du temps, même quand c'est automatisé, et parce que le démarrage d'une machine prend du temps.

Encore une fois, nous pouvons nous poser la question : est-ce que la machine virtuelle est la meilleure abstraction que l'on puisse utiliser ? Ou le temps de la revanche des conteneurs est-elle sur le point d'arriver ?

![L'aube des conteneurs](https://user-images.githubusercontent.com/13923756/76171138-594ebb80-6188-11ea-936d-a348701f977b.jpg)