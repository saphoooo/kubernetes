![Electro Monk3ys in the Cloud](https://user-images.githubusercontent.com/13923756/75849634-99f3b100-5de5-11ea-97f4-27ee0b3b9457.png)

Si vous êtes ici, c'est sans doute parce que voous avez apprécié les deux premières parties de ce guide. Mais je préfère vous mettre en garde, les choses risquent de changer ! Et il est possible qu'après avoir écouté ce que j'avais à vous dire, vous ne m'aimiez plus du tout. Mais après je ne suis pas payé pour qu'on m'aime, d'ailleurs je ne suis même pas payé du tout...

Alors redresser votre siège, remontez vos tablettes, attachez vos ceintures et préparer vous à l'atterissage.

Si vous avez jamais entendu quelqu'un dire que le cloud c'est simplement faire tourner son application sur la machine de quelqu'un d'autre, alors fuyez ! Il n'a simplement rien compris !

D'autres vous dirons que le cloud ce n'est jamais que des ressources à la demande, élastiques (comme le copain de la femme invisible) et en self-service. Traduction : grosso-modo à tout moment vous pouvez demander des ressources à votre fournisseur cloud, et ces ressources sont extensibles (à l'infini ?)...

Certes, c'est un concept qui a de quoi séduire, mais c'est un peu plus compliqué que ça. Devant ces ressources, il y a un modèle de consommation : celui d'Amazon, celui de Google, celui de Pivotal, etc. Est-ce que sérieusement on a envie de consommer de la machine virtuelle ? Je ne sais pas pour vous, mais personnellement, j'ai un début de mal de crâne en m'imaginant devoir choisir un type d'instance (tiens oui au fait, j'ai besoin de combien de mémoire et de combien de cpu pour faire tourner mon application dont j'ignore encore si elle sera populaire ou non), un système d'exploitation (c'est mon interface homme-machine), puis de devoir m'y connecter, faire les mises à jour, y installer mes librairies et mes binaires, puis enfin mon code, et devoir ensuite me préoccuper de gérer tout le cycle de vie et la sécurité de cette p*** de machine virtuelle (pardon, le p*** c'est certainement pour pathétique).

Qui ça intéresse ? La machine virtuel est le symbole de notre résistance au chagement et de notre incapacité d'innover.

Bien sûr, sous un certain angle le cloud n'est jamais que du temps cpu, de la mémoire, du réseau et du stockage, mais est-ce le bon angle d'approche ? Après tout, ne pourrions-nous pas simplement nous imaginer que c'est simplement un endroit où tourne notre code, sans contrainte de ressources ?

C'est avec cette vision que App Engine (Google), Heroku et CloudFoundry sont arrivés. Et vous savez quoi ? Pour les développeurs, ça a vraiment tout changé. Pousser son code, le voir s'exécuter et qu'il se mette à l'échelle automatiquement, quelle magie ! Dès lors les machines et leurs systèmes d'exploitation compliqués n'existent plus ; fini de gaspiller son temps en configuration et en patching ; désormais on a une abstraction qui s'appelle le cloud !

## Perdu dans l'espace

Rappelez vous que dans notre chronologie, nous ne sommes jamais qu'aux alentours de cette bonne vieille année 2010. Or les premiers colons à tenter l'expérience du cloud ont dû pas mal en souffrir. Je me représente la scène comme un départ pour Mars : un grand saut vers l'inconnu. Il y a eu du sang, des larmes et de la sueur, mais ce qui est est ressorti au final est génial : une méthodologie baptisée The Twelve-Factor App.

Je sais, une méthodologie quel ennui vous vous dites, pareil pour moi. Cependant, que diriez-vous si je vous disais qu'elle tient à peine en 12 lignes, et vous vous aurez plus vite fait de la lire plutôt que je vous l'explique ? Si vous ne me croyez pas, c'est par ici que ça se passe, [The Twelve-Factor App](https://12factor.net/fr/) et en *french pliz* !

Plus qu'une méthodologie ça pourrait presque ressembler à du bon sens. Dans ce cas, quel est l'intérêt de rappeler des règles qui ne sont jamais que du bon sens ? Parce que nous avons été, vous et moi, dévoyés, corrumpus, floués, préoccupés par des considérations qui n'auraient jamais dû être les notres.

Prenons un bête exemple : les logs. Est-ce que ça concerne vraiment l'application qu'ils soient envoyés vers un fichier ou un serveur ? Non. L'application a juste la responsabilité de produire des logs sous la forme d'un flux d'évènements que l'environnement n'a plus qu'à capturer, et à traiter de la manière qui lui semble la plus appropriée.

Autre exemple, les services dont l'application dépend, comme sa base de donnée : est-ce à l'application de contenir dans son code le chemin pour y accéder, ou ce chemin doit-il être un élément de configation que l'application doit tirer de son environnement ? Bien entendu, l'application doit tirer cette information de son environnement, car il y a des chances pour que la base de données ne soit pas la même dans votre environnement de développement que dans celui de production.

Vous trouvez que j'enfonce des portes ouvertes ? Pourtant ça n'est pas évident pour tout le monde, et la véritable question c'est : pourquoi ?

Rien ne vous oblige à adhérer à cette méthodologie, mais si vous rêver d'une application portable, extensible et résiliente, pensez **The Twelve-Factor App**.

## L'abattoire

Je m'excuse par avance s'il y a des défenseurs de la cause animale parmi vous, cependant gardez à l'esprit que :

1. L'analogie cette fois ne vient pas de moi.
1. Ce n'est qu'une analogie, et qu'il n'est pas vraiment question ici de tuer des animaux, ni même de leur faire le moindre mal.

Cette analogie, je l'adore, car elle illustre avec force la propention que nous avons à nous tirer une balle dans le pied sans arrêt. Elle s'appelle *Pet vs Cattle* (ou animal de compagnie contre troupeau). 

Vous vous souvenez au début notre l'histoire, je vous ai parlé de ces grosses machines hors de prix. Telle machine ne peut pas être remplacée aisément, et nous avons pris l'habitude de la traiter avec soin et un max d'attentions. Qu'elle vienne à tomber, et c'est la panique à bord, chacun doit y aller de sa personne pour qu'elle soit de nouveau opérationnelle le plus tôt possible. C'était peut-être ce qu'on pouvait faire de mieux à l'époque.

Mais c'est bizarrement un modèle que nous avons pris l'habitude de suivre, même quand les machines sont devenues moins coûteuses, même quand les machines sont devenues virtuelles. On a mis en place des machines avec des noms sympas (Starky et Hutch n'est qu'un exemple parmis d'autres), montées et maintenues à la main, et en quelques opérations, nous étions capable de faire basculer un service de l'une à l'autre...
Mais que l'une des machine viennent vraiment à mourir, et c'était la fin ! Comment remplacer une machine qui avait demander tant d'amour ? Elle était unique, et il faudra peut-être des jours pour en remettre une nouvelle en place ; pendant ce temps, que va devenir Starsky sans Hutch ? Pourvu qu'il ne lui arrive rien ! Ce bon vieux modèle de haute disponibilité a fait son temps, mais n'est plus suffisant pour nous garantir contre les conséquences d'une lourde panne, dont tout le monde commence à prendre conscience que, quoi qu'on fasse, elle arrivera.

L'outillage ne nous faisait pourtant pas défaut : CFEngine était là depuis presque 20 ans et Puppet faisait ses débuts. Mais que voulez-vous ?

Ce que nous aurions dû apprendre plus tôt, c'est à traiter nos chères machines comme un troupeau au lieu de les traiter comme un animal de compagnie. Ça commence en leur donnant des noms comme *webserver001*, *webserver006*, et ça suppose de gérer leur configuration et leur cycle de vie de manière totalement automatisée. Quand quelque chose vient à mal se passer, aucune intervention humaine ne doit être nécessaire : la machine est simplement supprimée et remplacée. Parce que la machine n'est pas la seule à savoir faire ce qu'elle fait : elle fait en effet partie d'un troupeau de machines qui font exactement la même chose qu'elle, ce qui permet au service de ne jamais être mis en péril par la disparation d'une machine du troupeau.

## De l'automatisation à tous les étages

Ce besoin de résilience nous a entrainé toujours plus loin dans la volonté de tout automatiser. Mais je vous pose la question, qu'est-ce qui aujourd'hui vous retient d'automatiser ?

Mon annecdote favorite à ce sujet reste la boite de vitesse, et je sais que je ne vais pas me faire des amis en la racontant. Ça fait des années que j'ai une voiture à boite automatique, et j'en suis super content ; tellement content que, bien sûr, j'en parle tout le temps autour de moi. Figurez-vous qu'à ce jour je n'y ai converti absolument personne. Et les réponses qu'on m'opose ne sont pas : "j'ai peur de consommer plus de carburant", "ça coûte plus cher en entretien non ?", ou le génialissime "j'ai un meilleur algorithme pour passer les vitesses qu'une boite xyz". Non, je me suis toujours retrouvé confronté à la même réaction émotionnel : "j'aime bien passer les vitesses". Sérieusement ? Qu'est-ce que tu aimes bien quand tu es dans un bouchon à jongler toutes les 10 secondes entre la première, la seconde ou la troisième ? Oui, c'est terriblement bien de se concentrer sur ses rapports de vitesse en ville alors que ton attention devrait se porter partout ailleurs. Mais c'est évident que pour optimiser ton passage de 120km/h à 130km/h sur autoroute tu vas d'abord rétrograder pour donner un peu plus de swag à ta conduite. Non, vous voulez que je vous dise, vous manquez d'empathie envers vous-même.

Revenons à nos brebis.

Pour automatiser, il existe des outils qui s'appellent des gestionnaires de configuration. J'ai déjà évoqué CFEngine et Puppet, mais j'aurais pu vous donner une liste bien plus longue : Chef, Ansible, Salt et bien d'autres encore en sont d'autres exemples. Mais dites-vous une chose, aussi cools qu'ils soient pour vous (ou non), ils ne restent que des outils. Et de vous à moi, un simple script maison pourrait tout aussi bien faire affaire.

C'est quoi l'idée ? Gérer et maintenir des configurations. Alors penchons-nous un instant sur le cycle de vie d'une machine :

1. Elle est mise dans un centre de données, raccordée à l'électricité et au réseau (opération coûteuse et compliquée à automatiser, donc je ne vous jeterai pas la pierre si vous le faites encore manuellement).
2. Elle a une configuration initiale qui comprend un système d'exploitation et des paramètres spécifiques selon :
  1. Sa localisation (elle est dans le centre de données xyz)
  1. sa fonction (elle doit être placée dans le VLAN xyz)
3. Elle est ensuite préprarée pour l'application qu'elle va héberger : libraires, binaires, etc.
4. Finalement elle reçois l'application lui donne vie.

Et toutes ces informations ont besoin d'être consignées dans une base de données, pour qu'à chaque moment on puisse se faire une idée juste de l'état de son parc (un parc ? alors finalement l'analogie est là depuis longtemps, car je vois plus souvent des vaches dans un parc que des chats, pas vous).

Pas si vite coco, ne manquerez-vous pas de me dire, dans ce scénario jamais la machine n'est mise à jour ? C'est un peu facile. Soyez sans crainte, je vais revenir sur ce sujet.

On peut faire tout ça à la main, enfin quand je dis "on", vous peut-être, moi certainement pas. Une fois la première phase accomplie, je peux démarrer ma machine sur PXE pour l'installer et lui fournir une configuration de base, puis lui passer un coup de Puppet pour la mettre à jour et y déployer librairies et binaires avant de la laisser aux bons soins de Dr Jenkins qui gérera élégament le déploiement de mon application. Je peux ainsi configurer 1 ou 1000 machines, le temps que ça me prend reste à peu prêt le même. Je me ferai peut-être l'effet d'être un dinausore, mais pour une machine physique, c'est tout à fait acceptable.

Mais le cloud nous offre mieux : il nous permet de mettre tout notre stockage et notre puissance de calcul dans un grand pot commun accessible à partir d'une API (RESTful le plus souvent). Dafuq ? A ce stade, je ne vais pas me lancer dans l'explication la plus décalée de ce qu'est une API RESTful ; mais si ça ne vous est pas vraiment familier, je vais tout de même tenter de vous en donner un bref apperçu.

Imaginez que j'ai trois gamins spécialisés dans la construction d'objets en briques plastiques (rassurez-vous, ils ne sont pas exploités pour faire ce genre de constructions, ce n'est que 16 heures par jour de bénévolat toute l'année). Qui des voitures, qui des châteaux et qui des vaisseaux. Problème, l'un des gamins parle chinois, le second arabe, et le troisième espagnol, et pauvre de moi, aucune de ces trois langues.

Mais je connais quelqu'un, Maya (parce que Maya l'API, mauvais jeu de mot mais qui me sauvera de moi-même ?), qui sait parfaitement comment communiquer avec ces gamins. Maya sait me décrire le type de services qu'elle est en mesure de me fournir, et la façon dont je peux les lui demander :
- crée le château xyz de couleur jaune
- liste les voitures
- change la couleur de la voiture xyz pour qu'elle soit rouge
- detruit le vaisseau xyz

J'utilise des verbes simples pour demander : crée (POST), liste (GET), change (PATCH) et détruit (DELETE) ; j'ai mis leur équivalent en verbes HTTP entre parenthèses pour vous familiariser.

J'ai un contrat avec Maya : j'utilise les verbes que je viens d'énoncer pour demande une action, je choisis l'objet sur lequel elle porte (château, voiture ou vaisseau), et je peux y ajouter occasionnellement une série d'arguments.

Si je demande à Maya de créer une voiture jaune, Maya me répondra que la voiture a été créée (201). Si par contre je lui demande de me créer une fusée blanche et rouge, il y a des chances qu'elle me dise qu'elle ne connait pas l'objet dont je lui parle (404). En plus d'une réponse, elle me fourni un code retour, 201 ou 404, qui est un status standardisé (voir les [*status code* HTTP](https://fr.wikipedia.org/wiki/Liste_des_codes_HTTP)).

Maya me libère d'avoir à connaître 3 langues, et je n'ai même plus besoin de savoir quoi que ce soit à propos des gamins qui travaillent pour elle. Si celui qui parlait espagnol s'en va et est remplacé par un autre qui parle suédois, ça ne change rien pour moi, tant qu'il sait construire des vaisseaux.

![Maya in the Cloud with Diammonds](https://user-images.githubusercontent.com/13923756/76161515-70fc5480-6134-11ea-9148-08f8a33a8896.png)

## Cachez ce CPU que je ne saurais voir

Ce qu'il y a de chouette dans tout ça, c'est que je n'ai qu'une seule langue à connaître, et que cette langue est on ne peut plus simpliste. Du coup, commander 3 voitures de couleur différente devient d'une simplicté écoeurante :

```
for i in blue rouge jaune ; do maya crée une voiture ${i} ; done
```

En retour, je recevrai 201, 201 et peut-être 500, mais je n'espère pas, car ça pourrait signifier que Maya est débordée par le travail que je lui donne.

Et quand bien même ! Ce que m'apprennent ces code de retour :
- Maya n'a pas pu traiter la création de la voiture jaune, et que le problème vient d'elle et pas de moi (erreur 5xx)
- deux de mes voitures sont déjà créées
- pour la troisième, la jaune, il me faudra retenter peut-être dans une heure

Irrésistiblement simple non ?

Mais revenons à nos joujoux informatiques. Puisque le cloud m'offre une API pour créer des objets tels que des machines virtuelles, des firewalls, des réseaux, voire beaucoup mieux des bases de données ou des agents de messages, qu'est-ce qui m'empêche de créer des scripts pour gérer mon infrastructure. Rien, en effet, et c'est ce qui explique l'émergeance d'outils comme CloudFormation (AWS), Cloud Deployment Manager (GCP) ou Terraform ; ce qu'on a appelé l'Infrastructure as Code.

L'Infrastructure as Code (IaC) permet de décrire l'infrastructure qu'on désire, une machine qui expose un port 80 sur internet et qui est reliée par le port 5432 à une base de donnée Postgresql, et Maya se charge du reste.

Rassembler l'Infrastructure as Code et un outil de gestion de configuration, et la partie approvisionnement est réglé une bonne fois pour toute, et de manière totalement automatisée ! (J'entends d'ici quelqu'un dire, j'aime bien mettre en place mes machines virtuelles à la main...)

Approvisionnement, et pas cycle de vie, c'est vrai ; pour gérer le cycle de vie, il faut aussi avoir des réponses automatisées en cas d'erreur... On se rapproche furieusement de Kubernetes ou je me trompe ? Mais n'allons pas trop vite, j'ai encore besoin de vous retourner la cervelle une ou deux fois.

## Vous avez dit immutable, comme c'est immutable

Bravo, vous gérez de l'infrastructure avec du code, vous êtes maintenant un développeur ! Avouez, vous l'aviez pas vue venir celle-là. Pourtant, c'est une réalité, c'en est fini de jouer les gros barbus qui montent leur centre de données dans un garage. Et parce que votre infrastructure est un code, il va maintenant vous falloir la gérer... comme un code. Utiliser un outil de gestion de version (Git), et donc versionner vos déploiements.

Repartons de notre simplissime exemple : une ouverture de port 80 sur une machine reliée à une base de données sur le port 5432. Imaginons que vous souhaitiez maintenant exposer le port 443 au lieu du port 80, pensez vous que vous aller faire une mise à jour de la régle dans le firewall ? Pas exactement : vous aller plus sûrement supprimer la première règle et en créer une nouvelle (c'est moins source d'erreur, et plus simple pour tout le monde).

Maintenant, plus compliqué : vous voulez mettre à jour votre machine virtuelle. Instinctivement, vous allez me dire que vous vous connecter à la machine et que vous allez lancer la mise à jour. Très mal !

N'oubliez pas que votre machine fait partie d'un troupeau, et qu'il y en a probablement 500 comme elle. Dans ce cas vous allez me dire, j'utilise mon outil de gestion de configuration, et je mets tout le troupeau à jour. L'approche est meilleure, mais malheureusement pas sans effets de bord. Le premier : comment êtes vous assuré qu'à cet instant toutes vos machines sont à la même version ? Le dernier : sur le long terme, comment allez-vous vous assurer qu'il n'y ait jamais la moindre divergeance entre ces machines ?


12 Factor
Pets vs Cattle
API
Infrastructure as Code
Immutabilité