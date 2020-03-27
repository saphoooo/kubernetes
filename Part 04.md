![Che Guevara](https://user-images.githubusercontent.com/13923756/77730170-8c0a0800-7000-11ea-96de-2b550f9f189b.png)

Une révolution était dans l'air : il y avait déjà toute cette sous-culture qui venait petit à petit bousculer l'ordre établi, mais il n'y avait pas encore de leader.

Ce Che Guevara des temps modernes s'appelle Docker.

Mais si Docker n'est qu'une nième technologie de conteneurisation, vous allez me dire, son unique mérite n'est-il pas d'être arrivé au bon endroit au bon moment ?

## Comment une affreuse baleine bleue a mangé le monde ?

### Règle numéro 1 : ne pas reproduire les erreurs du passé

Certes, il y a eu d'autres technologies de conteneurisation avant Docker, en voici une petite liste non exhaustive :
- VServer (2001)
- OpenVZ (2005)
- LXC (2008)

Lorsque je vous ai parlé de conteneurisation tout au début de cette série d'articles, je vous ai dit que les conteneurs modernes reposent sur deux fonctionnalités du noyau Linux : les *namespaces* et les *cgroups*. Pas de modules exotiques, pas de fastidieuse recompilation du noyau, mais des **fonctionnalités natives**.

Il fut une époque où notre noyau Linux était un carosse. Pour faire avancer ce carosse, il fallait un cheval. Ainsi attelé, le carosse remplissait alors à merveille sa fonction de vous conduire d'un point A à un point B mais... Le cheval ne fait pas partie intégrante du carosse : il faut s'en occuper, le nourrir, le brosser, le dorlotter, l'élever, après quoi seulement on peu l'atteler et l'utiliser afin qu'il tirer notre carosse.

Les *namespaces*, c'est un peu comme si on venait mettre un moteur dans le carosse, et les *cgroups* une boîte de vitesse. Le carosse est maintenant autonome, et plus personne n'irait atteler un cheval pour le faire avancer !

![Oups!](https://user-images.githubusercontent.com/13923756/76698024-567b2b80-669e-11ea-9e05-196b33efddbb.jpg)

VServer et OpenVZ n'utilisaient pas les fonctionnalités natives de noyau, et au lieu de ça venaient avec un noyau modifié, ce qui n'était pas sans poser bien des problèmes opérationnels. C'est une erreur que n'a pas commise LXC, et c'est la raison pour laquel les preières versions de Docker étaient basées sur ce dernier. Il est plus facile pour tout un chacun d'adopter un outil quand il demande peu d'efforts pour sa mise en oeuvre et sa maintenance, c'est on ne peut plus logique.

### Règle numéro 2 : faire un outil pour tous et accessible à tous

Steve Jobs restera peut-être à jamais très controversé, mais il y une chose qu'il a toujoours comprise : quelque soit la complexité de la technologie, l'expérience utilisateur doit toujours être simple et intuitive. Et qui a jamais eu un ces vieux iPods dans les mains comprendra très bien ce que je veux dire. Mais faire simple, ce n'est pas simple.

Or Docker excelle dans ce domaine. Essaye-le, lancez `docker run hello-world` et voyez le résultat ! Bien sûr, "hello world" vous allez dire, quelle technologie n'a pas son "hello world" super simple, et la marche suivante est 100km plus haut ? Très bien, lancez `docker run nginx`, et sans que vous ayez rien eu à faire de plus, vous avez maintenant un serveur Nginx qui tourne sur votre machine.

Pourquoi est-ce si simple ? La réponse mérite qu'on s'y arrête quelques instants.

- Parce que Docker exécute des images "prêtes à l'emploi" de l'application que vous souhaitez.
- Que ces images peuvent être librement créées et partagées sur le web, dans un repo qui s'appelle dockerhub.
- Que lorsque vous exécutez `nginx` docker l'interprète comme la version de Nginx portant le tag `latest` qui se trouve sur le repo dockerhub. Et il est plus tellement plus simple de lancer `docker run nginx` que `docker run docker.io/nginx:latest`.
- Enfin, si l'image Nginx qui va servir à créer votre conteneur n'est pas présente sur votre machine local, docker se charge lui-même d'aller la chercher dans le repo distant, sans que vous ayez d'autres manipulations à faire.

Deux choses qu'il faut encore ajouter à ceci :

- Dockerhub est gratuit pour les projets publics.
- Créer une image est d'une extrême simplicité.

Vous avez besoin d'une base de données pour tester votre application, boum `docker run postgresql` et moins de 10 secondes plus tard, vous avez une base de donnée Postgresql configurée s'exécutant sur votre machine. Quoi ? Qui n'a jamais rêvé d'une telle simplicité ?

Docker par ailleurs suit la méthodologie de The Twelve-Factor App (vous auriez dû réviser quand je vous en ai parlé la dernière fois, mais il n'est pas trop tard [https://12factor.net/fr/](https://12factor.net/fr/)).

Par exemple, vos variables sont dans votre environnement, pas dans l'image. Si nous reprenons notre base de données, j'aurai besoin d'une variable POSTGRES_USER et une autre POSTGRES_PASSWORD pour définir un utilisateur et son mot de passe.

Autre exemple, le *port binding* : mon conteneur n'expose aucun port sur ma machine, je dois dire à docker de faire un *binding* du port 5432 du conteneur sur le port 5432 de ma machine pour y accéder depuis le port standard (notez que ça marche pour n'importe quel autre port de ma machine, du moment qu'il est disponible ; mais rappelez-vous une chose : essayez au maximum de garder les choses standardisées, c'est une documentation gratuite dont vous disposez là !).

### Règle numéro 3 : la documentation

Beaucoup la négligent et à tort. Alors je vous le demande : à quoi sert un produit dont personne ne sait comment il fonctionne ? Mis à part ce bon vieil iPod, je ne connais aucun produit qui soit si intuitif qu'il se passer de son mode d'emploi. Ça fait des années que les micro-ondes, les téléviseurs, les plaques à inductions ont envahi notre vie, et pourtant sans mode d'emploi ou un diplôme d'ingénieur, n'espérez pas les utiliser du premier coup.

Avoir une bonne documentation, c'est tout d'abord avoir un *quick start*, qui permet de montrer comment exécuter la plupart des commandes les plus courantes, pour se familiariser rapidement sur le produit. C'est aussi avoir une documentation de référence claire, avec des exemples, qui permettent d'accéder sans peines aux fonctionnalités plus avancées. La documentation doit être simple à trouver, simple à utiliser (il faudrait d'ailleurs en parler à tous ceux qui font des notices pour nos appareils ménagés), complète, à jour et... agréable à lire.

C'est idiot, mais passer du temps à faire une documentation pour ses utilisateurs peut faire la différence entre un succès planétaire et le plus total anonymat...

### Règle numéro 4 : devenir l'unique standard

![Le casse-tête des prises éléectriques](https://user-images.githubusercontent.com/13923756/77728788-88c14d00-6ffd-11ea-80b1-07601b659ba9.png)


Je ne sais pas si vous avez entendu parler de `runc`, `libcontainer` ou OCI, mais ce sont des termes que vous allez vite connaître par coeur. Pourquoi ? Parce qu'ils sont aujourd'hui des standards. Je ne vais pas entrer dans les détails à ce stade, mais rapidement :

- **runc/libcontainer** est le binaire qui permet d'exécuter des conteneurs
- **OCI** pour *Open Containers Initiative*, est un projet auquel Docker, mais aussi et surtout la Linux Foundation, ont activement participé, et qui a publié deux spécifications : une pour le *runtime*, et une autre pour le format d'image des conteneurs. Libre à chacun de l'adopter ou non, mais avoir un format universel ne peut qu'être un plus dans un écosystème où le partage est au centre de tout. Ceux qui ne l'ont pas suivi, comme Rocket (Rkt), un runtime de CoreOS, sont aujourd'hui totalement abandonné.

Attention, je ne dis pas que Rkt ou LMCTFY (le projet que Google à abandonné pour travailler avec Docker sur libcontainer) sont de mauvais projets, bien loin de là, ils avaient leurs atouts ; tout comme je ne dis pas que libcontainer est le meilleur projet, il a aussi ses faiblesses. Mais avoir un standard était simplement mieux pour tout le monde, et permettait de poser les première briques de l'édifice.

## Pourquoi cette baleine bleue s'est-elle retrouvée échouée sur la plage à mordre la poussière ?

Je ne suis pas un historien de Docker, et je ne connais pas dans le détail le polémiques et les problématiques qu'a connu Docker. Par contre, je peux parler de mon expérience personnelle.

J'ai adoré Docker dès le premier jour où je l'ai découvert, pas au point d'en rêver la nuit, mais tout de même. J'ai beaucoup, beaucoup "joué" avec. A partir de 2016, je suis un peu tombé en désamour, pas à cause de Docker, mais à cause de la stratégie commerciale de Docker Inc. Je comprends que toute société qui fait de l'open source ai besoin de trouver son modèle économique, et je ne critique pas. Mais les partenariat avec IBM et Microsoft en 2017 ont dû fortement contrarié une communauté open source qui a vu renaître ses vieux démons. J'étais à la DockerCon 2017, et mon petit coeur s'est brisé.

Je me suis totalement détourné de Docker (enfin quand je dis totalement, c'est un mensonge mais j'y reviendrai quand je vous parlerai plus en détail du runtime). En effet, à ce jour il existe des alternatives à docker-engine, et vous savez quoi ? Toutes repose sur runc, parce qu'un standard profite à tous, et à l'utilisateur en premier lieu.

Alors Docker a mordu la poussière, et Mirantis a fait l'aquisition de docker-engine, mais il faut rendre à césar ce qui est à césar : si les conteneurs sont si populaire aujourd'hui, c'est grâce à Docker qui a, comme personne avant lui, rendu l'expérience de tourner un conteneur sur sa machine aussi simple que d'appuyer sur un bouton pour allumer les lumière ; et de vous à moi, je me répète mais tant pis, il n'y a rien de plus compliqué que de rendre les choses aussi simples.

## De la théorie à la pratique

J'ai fait beaucoup de blabla, mais certaines choses méritent d'être dites. La prochaine fois, que diriez-vous mettre tous ces concepts en pratique ?