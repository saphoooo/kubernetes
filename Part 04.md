![Che Guevara](https://user-images.githubusercontent.com/13923756/77730170-8c0a0800-7000-11ea-96de-2b550f9f189b.png)

Une révolution était dans l’air : il y avait déjà toute cette sous-culture qui venait petit à petit bousculer l’ordre établi, mais il n’y avait pas encore de leader.

Ce Che Guevara des temps modernes s’appelle Docker.

Mais si Docker n’est qu’une nième technologie de conteneurisation, vous allez me dire, son unique mérite n’est-il pas d’être arrivé au bon endroit au bon moment ?

## Comment une affreuse baleine bleue a mangé le monde ?

### Règle numéro 1 : ne pas reproduire les erreurs du passé

Certes, il y a eu d'autres technologies de conteneurisation avant Docker, en voici une petite liste non exhaustive :
- VServer (2001)
- OpenVZ (2005)
- LXC (2008)

Lorsque je vous ai parlé de conteneurisation tout au début de cette série d’articles, je vous ai dit que les conteneurs modernes reposent sur deux fonctionnalités du noyau Linux : les namespaces et les cgroups. Pas de modules exotiques, pas de fastidieuse re-compilation du noyau, mais des **fonctionnalités natives**.

Il fut une époque où notre noyau Linux était un carosse.

![Carrosse](https://miro.medium.com/max/1400/1*fool4qt4lcB8LFj99zdGtQ.jpeg)

Pour faire avancer ce carrosse, il fallait un cheval. Ainsi attelé, le carrosse remplissait alors à merveille sa fonction de vous conduire d’un point A à un point B mais… Le cheval ne fait pas partie intégrante du carrosse : il faut s’en occuper, le nourrir, le brosser, le dorloter, l’élever, après quoi seulement on peut l’atteler et l’utiliser afin qu’il tire ce fichu carrosse.

Les *namespaces*, c'est un peu comme si on venait mettre un moteur dans ce carrosse, et les *cgroups* un boitier de vitesses. Le carrosse est maintenant autonome, et plus personne n'irait y atteler un cheval pour le faire avancer !

![Oups!](https://user-images.githubusercontent.com/13923756/76698024-567b2b80-669e-11ea-9e05-196b33efddbb.jpg)

VServer et OpenVZ n'utilisaient pas les fonctionnalités natives du noyau, et au lieu de ça venaient avec un noyau modifié, ce qui posait bien des problèmes opérationnels.

C'est une erreur que n'a pas commise LXC, et c'est la raison pour laquelle les premières versions de Docker étaient basées sur ce dernier. Il est plus facile pour tout un chacun d'adopter un outil quand il demande peu d'efforts pour sa mise en oeuvre et sa maintenance, ça tombe sous le sens.

### Règle numéro 2 : faire un outil pour tous et accessible à tous

Steve Jobs restera peut-être longtemps et à jamais très controversé, mais il y une chose qu'il a toujours comprise : quelque soit la complexité de la technologie, l'expérience utilisateur doit toujours être simple et intuitive. Et qui a jamais eu un de ces vieux iPods entre les mains comprendra très bien ce que je veux dire. Mais faire simple, ce n'est pas simple.

![iPod]()

Or Docker a excellé dans ce domaine. Essaye-le, lancez `docker run hello-world` et voyez le résultat ! Bien sûr, "*hello world*" vous allez dire, quelle technologie n'a pas son "*hello world*" super simple, après quoi la marche suivante est 100km plus haut ?

Très bien, lancez docker run nginx, et sans que vous ayez rien eu à faire de plus, vous avez maintenant un serveur Nginx qui tourne sur votre machine.

Pourquoi est-ce si simple ? La réponse mérite qu'on s'y arrête quelques instants.

- Parce que Docker exécute des images "prêtes à l'emploi" de l'application que vous souhaitez.
- Que ces images peuvent être librement créées et partagées sur le web, dans un dépôt qui s'appelle dockerhub.
- Que lorsque vous exécutez `nginx` docker l'interprète comme la version de Nginx portant le tag `latest` qui se trouve sur le dépôt dockerhub : il est plus tellement plus simple de lancer `docker run nginx` que `docker run docker.io/nginx:latest`.
- Enfin, si l'image Nginx servant à créer votre conteneur n'est pas présente sur votre machine locale, docker se charge lui-même d'aller la chercher dans le dépôt distant, sans que vous ayez d'autres manipulations à faire.

Deux choses qu'il faut encore ajouter à ceci :

- Dockerhub est gratuit pour les projets publics.
- Créer une image est d'une extrême simplicité.

Vous avez besoin d'une base de données pour tester votre application, boum `docker run postgresql` et moins de 10 secondes plus tard, vous avez une base de données Postgresql configurée s'exécutant sur votre machine.

Quoi ? Qui n'a jamais rêvé d'une telle simplicité ?

Docker par ailleurs suit la méthodologie de The Twelve-Factor App (vous auriez dû réviser quand je vous en ai parlé la dernière fois, mais il n'est pas trop tard [https://12factor.net/fr/](https://12factor.net/fr/)).

Par exemple, vos variables sont dans votre environnement, pas dans l'image. Si nous reprenons l'exemple de notre base de données, j'aurai besoin d'une variable **POSTGRES_USER** et une autre **POSTGRES_PASSWORD** pour définir un utilisateur et son mot de passe. Heureusement pour vous que ces valeurs ne se trouvent pas dans l'image ! Mais c'est à vous de les définir quand vous exécutez docker.

Autre exemple, le *port binding* : mon conteneur n'expose aucun port sur ma machine, je dois dire à docker de faire un *binding* du port 5432 du conteneur sur le port 5432 de ma machine pour y accéder (notez que ça marche pour n'importe quel autre port de votre machine, du moment qu'il est disponible ; mais rappelez-vous d'une chose : en gardant les valeurs par défaut, par exemple le port 5432 pour une base de données Postgresql, vous avez une documentation gratuite à votre disposition !).

### Règle numéro 3 : la documentation

Beaucoup la négligent et à tort. Alors je vous le demande : à quoi sert un produit dont personne ne sait comment il fonctionne ? Mis à part ce bon vieil iPod (et encore), je ne connais aucun produit qui soit si intuitif qu'il se passe de mode d'emploi. Ça fait des années que les micro-ondes, les téléviseurs, les plaques à induction ont envahi notre vie, et pourtant sans mode d'emploi ou un diplôme d'ingénieur, n'espérez pas les utiliser du premier coup (voire pour certains modèles pas du tout, je vous donnerai ma liste si ça vous intéresse).

Avoir une bonne documentation, c'est :
- Tout d'abord avoir un *quick start*, qui permet de montrer à l'utilisateur lambda comment exécuter les commandes les plus courantes, ce qui permet de se familiariser rapidement avec le produit.
- C'est aussi avoir une documentation de référence claire, avec des exemples, qui permettent d'accéder sans peines aux fonctionnalités plus avancées.
- Et pour rendre la chose exhaustive, même si ça ne nous concerne pas directement ici, de bien documenter son code pour qu'il soit facile de le maintenir et d'y contribuer. Le père adoptif de Steve Jobs lui répétait que l'arrière d'une armoire était aussi important que son devant, c'est pourquoi les machines Apple ont toujours eu un intérieur soigné à l'extrême ; et selon moi c'est un sage conseil.

La documentation doit être simple à trouver, simple à utiliser (il faudrait d'ailleurs en parler à tous ceux qui font des notices pour nos appareils ménagés), complète, à jour et… agréable à lire.

![Documentation]()

C'est idiot, mais passer du temps à faire une documentation pour ses utilisateurs peut faire la différence entre un succès planétaire et le plus total anonymat…

### Règle numéro 4 : devenir l'unique standard

![Le casse-tête des prises électriques](https://user-images.githubusercontent.com/13923756/77728788-88c14d00-6ffd-11ea-80b1-07601b659ba9.png)


Je ne sais pas si vous avez entendu parler de `runc`, `libcontainer` ou OCI, mais ce sont des termes que vous allez vite connaître par coeur. Pourquoi ? Parce qu'ils sont aujourd'hui des standards (de fait). Je ne vais pas entrer dans les détails à ce stade, mais rapidement :

- **runc/libcontainer** est le binaire qui permet d'exécuter des conteneurs
- **OCI** pour *Open Containers Initiative*, est un projet auquel Docker, mais aussi la Linux Fondation, Google et d'autres, ont activement participé, et qui a publié deux spécifications : une pour le *runtime*, et une autre pour le format d'image des conteneurs. Libre à chacun de l'adopter ou non (ce ne sont que des spécifications), mais avoir un format universel ne peut qu'être un plus dans un écosystème où le partage est au centre de tout. Ceux qui ne l'ont pas suivi, comme Rocket (Rkt), le runtime de CoreOS, sont aujourd'hui totalement abandonné.

Attention, je ne dis pas que Rkt ou LMCTFY (le projet que Google à stoppé pour travailler avec Docker sur libcontainer) sont de mauvais projets, bien loin de là, ils avaient leurs atouts ; tout comme je ne dis pas que libcontainer est le meilleur projet, il a aussi ses faiblesses. Mais avoir un standard était simplement mieux pour tout le monde, et permettait de poser les premières briques d'un édifice qui est bien plus que simplement des conteneurs.

## Grandeur et décadence

Docker a fait tout ça ! Docker a tout de suite compris qu'avec l'avènement des micro-services les conteneurs seraient le truc, un outil qui permettrait aux développeurs de livrer leur code plus rapidement, et de le livrer mieux, en les libérant du fardeaux de la portabilité (vous vous souvenez : "ça marche sur ma machine ?").

Docker à aussi compris que pour apporter de la vélocité aux développeurs, son produit devait être d'une simplicité extrême à l'utilisation, et riche en fonctionnalités.

Docker savait aussi que dans un système basé sur l'échange, avoir une monnaie unique était la clé.

Oui, Docker savait bien des choses, mais Docker ne savait hélas pas tout non plus…

## Pourquoi cette baleine bleue s'est-elle retrouvée échouée sur la plage à mordre la poussière ?

Je ne suis pas un historien de Docker, et je ne connais pas dans le détail le polémiques et les problématiques qu'a connu Docker. Par contre, je peux parler de mon expérience personnelle.

rêver la nuit, mais tout de même. J'ai beaucoup, beaucoup "joué" avec. A partir de 2016, je suis un peu tombé en désamour, pas à cause de Docker, mais à cause de la stratégie commerciale de Docker Inc. Je comprends que toute société qui fait de l'open source ait besoin de trouver son modèle économique, et je ne critique pas ça. Mais la façon dont Docker Inc. a cherché à se rapprocher du monde de l'entreprise ressemblait plus à mes yeux à du racolage qu'à de la collaboration ; et la façon de jeter au visage de la communauté open source lors de la DockerCon EU 17  ses partenariats Avec IBM et Microsoft a sans aucun doute été perçu comme un pied de nez à la communauté qui avait soutenu Docker dès le premier jour, et qui a vu ressurgir ses vieux démons…

Mon petit coeur s'est brisé et je me suis totalement détourné de Docker (enfin quand je dis totalement, c'est un mensonge mais j'y reviendrai quand je vous parlerai plus en détail du runtime). En effet, à ce jour il existe des alternatives à docker-engine, mais vous savez quoi ? Toutes reposent sur *runc*, parce qu'un standard profite à tous, et à l'utilisateur en premier lieu ; de ce point de vue rien n'a changé ! 🙂

Alors Docker a mordu la poussière, et Mirantis a depuis fait l'acquisition de docker-engine, mais il faut rendre à césar ce qui est à césar : **si les conteneurs sont si populaires aujourd'hui, c'est grâce à Docker** qui a, comme personne avant lui, rendu l'expérience de tourner un conteneur sur une machine aussi simple que d'appuyer sur un bouton pour allumer la lumière ; et de vous à moi, je me répète mais tant pis, il n'y a rien de plus compliqué que de rendre les choses aussi simples.

## De la théorie à la pratique

J'ai fait beaucoup de blabla, mais certaines choses méritaient d'être dites. Un regret cependant, j'ai dit et redit : Docker, Docker, Docker dans cet article, alors petit bémol : Docker = les individus qui ont imaginé, créé, maintenu et documenté Docker. Merci les gars, du fond du coeur merci ! 🙏

La prochaine fois, que diriez-vous mettre tous ces concepts en pratique ?