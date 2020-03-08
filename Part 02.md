![Deuxième partie](https://user-images.githubusercontent.com/13923756/75755820-addcdb80-5d2f-11ea-905c-8c3c715beb04.png)

## J'ai un pingouin dans ma salade

Je ne sais pas quelle peut être votre réaction face à ce qui vient d'être dit, mais je peux assez bien m'imaginer que ça peut aller du : "C'est un truc de malade de s'imaginer que les conteneurs ont plus de 40 ans" au simple "Et alors ?" en passant par une grande variété de "Si c'est vrai, alors pourquoi on n'en parle que maintenant ?"

Je pense vraiment qu'un historien des technologies de l'information devrait se pencher sur le sujet, car c'est tout à la fois passionnant et extrêmement complexe. Mais comme toujours je vais essayer d'y apporter mon éclairage personnel, ma propre vision ; mais on sait tous qu'une histoire n'est jamais totalement neutre, et qu'elle n'est jamais qu'une interprétation des faits. Cependant ne perdez pas de vue que tout ceci explique "pourquoi Kubernetes".

Tout d'abord, replaçons-nous dans un contexte historique, celui du début des années 2000 (vous vous souvenez, Matrix, Kaamelott et tout ça). A cette époque, l'open source n'est encore qu'un mouvement clandestin, un vent de mutinerie face à l'impérialisme de la propriété intellectuelle (du moins d'après certains) ; Google n'est qu'un moteur de recherche, Amazon un marchant de livres, et le marché est dominé par Microsoft (Windows), Sun Microsystems (Solaris), IBM (Aix) et HP (UX). [Linux est au mieux considéré comme un cancer](https://www.zdnet.fr/actualites/microsoft-a-un-jour-qualifie-linux-de-cancer-et-c-etait-une-grossiere-erreur-39887203.htm), quand les gens savent de quoi on leur parle lorsqu'on évoque Linux (hé oui).

Le problème majeur du début des années 2000, c'est que les machines qui se trouvent dans les centres de données sont ce qu'on pourrait qualifier de *gros systèmes* (sans vouloir leur manquer de respect), avec de nombreux processeurs et des giga octets de mémoire (à l'époque, cette unité de mesure était impressionnante) ; faire tourner une seule application sur un tel système n'a vraiment pas de sens, car peu d'applications sont en mesure de consommer toutes les ressources qui sont à leur disposition, et on se retrouve vite à avoir des machines hors de prix qui passent la majeure partie de leur temps à ne rien faire. L'idée de "consolider son infrastructure" en exécutant plusieurs applications sur la même machine commence à faire son chemin.

Mais on le sait déjà : ce n'est pas si simple, plusieurs versions d'un même runtime applicatif (Java par exemple) peuvent difficilement tourner sur la même machine sans entrer à un moment ou à un autre en conflit.

Ce phénomène entraine l'essor d'une autre technologie que vous connaissez bien, encore plus vieille que les conteneurs : les machines virtuelles. Désolé de vous l'apprendre si brutalement, mais ce n'est pas VMware qui est à l'origine de la virtualisation, mais bien IBM et le MIT dans les années 1960. Ceci dit, si VMware est à la place qu'il occupe aujourd'hui, c'est parce qu'il a misé sur l'architecture x86, qui est l'architecture la plus populaire de nos jours.

Virtualisation et conteneurisation sont deux réponses à ce même problème : "comment faire tourner plusieurs applications sur un même système ?". Toutes deux ont leurs avantages et leurs inconvénients, mais la conteneurisation a un modèle plus disruptif que la machine virtuelle. Il est en effet facile de se représenter une machine virtuelle : elle a un disque, un cpu, de la mémoire et une carte réseau, tout comme une machine physique. On y installe un système d'exploitation, et roule ma poule, on peut y mettre son application. A l'opposé, isoler un processus et se représenter les interactions qu'il a avec la machine (disques, cpu, mémoire et réseau) n'a rien d'une évidence.

Comment illustrer cette différence ? Imaginez que notre machine virtuelle est une maison : elle est reliée au réseau électrique, à l'eau, aux égouts, elle a sa porte d'entrée et sa boîte aux lettres. Elle fonctionne en toute autonomie, dans la mesure où on lui assure les services dont elle dépend : eau, électricité, courrier... Votre conteneur quant à lui est un appartement : lui aussi dispose de l'électricité, de l'eau et du courrier, mais ces différents services sont mutualisés au niveau de l'immeuble dans lequel il se trouve. Cette analogie est terriblement pertinente pour comprendre que la gestion d'une machine virtuelle et celle d'un conteneur est profondément différente : dans le premier cas nous disposons d'une grande liberté pour un coût plus élevé (la machine virtuelle), dans le deuxième cas il faut se plier aux règles de la copropriété, mais pour un coût largement plus faible (le conteneur).

Mais reprenons le fil de notre histoire. A cette époque, comme je l'ai mentionné, le marché est segmenté entre différents Unix propriétaires d'un côté et Windows de l'autre, et ce dernier n'implémente ni la conteneurisation ni la virtualisation. Dans ces circonstances, comment consolider son parc de serveurs Windows ?

D'ailleurs savez-vous comment fonctionne une machine virtuelle ? Ce n'est un secret pour personne : à l'aide d'un hyperviseur.

Une minute, c'est quoi un hyperviseur ?

Un hyperviseur est un programme qui sert à virtualiser une architecture spécifique, comme x86, et permet de faire tourner théoriquement n'importe quel système d'exploitation propre à cette architecture (il ne faut donc pas le confondre avec un émulateur), comme par exemple Windows sur x86. Il se charge de gérer le matériel, et en donne une vue virtualisée à ses hôtes (les machines virtuelles).

Du coup, si nous considérons toujours qu'une machine virtuelle est une maison, l'hyperviseur peut être comparé à une commune : elle gère les parcelles de terrain, et les rend constructibles en les reliants à différents services (eau, électricité, etc).

Le conteneur lui n'a pas d'hyperviseur : il ne dépend que du système d'exploitation qui l'héberge (l'immeuble).

Le conteneur est donc spécifique aux fonctionnalités du noyau d'un système d'exploitation (un conteneur Linux ne tournera pas sur BSD et vice versa). Or Windows (dans les années 2000, entendons-nous bien) n'implémente pas de technologie de conteneurisation ; impossible donc d'utiliser la conteneurisation pour isoler deux applications sur un système Windows. Par contre, il est possible de virtualiser Windows, et d'avoir une instance de Windows pour chaque application qu'on veut isoler. Il en découle qu'en l'absence d'autre choix, les machines virtuelles deviennent la norme pour virtualiser une application exécutée sous Windows.

A cette même époque pourtant Sun Microsystems mise la conteneurisation avec les Zones Solaris (même si il ne met pas tous ses oeufs dans le même panier en misant dans le même temps sur l'hyperviseur et les LDOM), choix que fait également Google, mais avec les conteneurs Linux ([chez Google, tout tourne dans des conteneurs](https://www.infoq.com/fr/news/2014/06/everything-google-containers/), c'est bien connu). Google ne se laisse pas d'autre porte de sortie, c'est un choix très arrêté et plutôt audacieux pour l'époque (si on considère que l'écosystème est loin d'être mature) ; et pour l'anecdote, le système de conteneurisation de Google s'appelle Borg (retenez ce nom, nous en reparlerons).

![cyborg-pinguin](https://user-images.githubusercontent.com/13923756/75623629-21ee7680-5bac-11ea-9f37-65bf62a3e42d.png)

### En bref

- Le modèle des machines virtuelles s'est largement imposé vers le milieu des années 2000 sous l'influence de 2 acteurs majeurs : VMware et Microsoft (même si ce dernier a plus joué un rôle de figurant).
- Sous Unix, il y a des initiatives aussi bien vers la machine virtuelle que vers la conteneurisation.
- Linux n'est pas non plus en reste, puisqu'il dispose lui aussi de plusieurs technologies de virtualisation et de conteneurisation. Cependant il ne bénéficie pas alors de la popularité dont il jouit aujourd'hui et n'est donc pas l'acteur de cette standardisation en faveur des vm.

## Je vous ressers un peu de VM ?

Tout cela est bien joli et permet de comprendre pourquoi la machine virtuelle est si populaire encore aujourd'hui, mais pas "pourquoi on s'intéresse aux conteneurs" ?

Or cette fois, ce n'est plus vers l'infrastructure qu'il faut se tourner, mais vers le génie logiciel, car quoi que vous puissiez en penser, les machines ne sont jamais là que pour faire tourner des serveurs, et plus elles sont abstraites, et mieux on se porte.

> Par soucis de clarté, je distingue toujours **machine** : une machine physique ou virtuelle ; d'un **serveur** : programme qui s'exécute sur une machine.

### Les microservices en abrégé

Les microservices étaient déjà dans l'air du temps en ce milieu des années 2000, même si le terme n'est vraiment apparu qu'en 2011, et qu'on parlait plus volontiers de SOA/AOS (*Service Oriented Architecture* ou Architecture Orientée Services) à l'époque.

Mais non, je ne vais pas vous expliquer en détail ce que sont les microservices, pour autant il font partie des personnages de mon histoire, et vous allez apprendre à les connaître.

> Je fais souvent des analogies pour décrire les choses ; illustrer une idée me permet de la rendre plus concrète, alors qu'en réalité nous parlons de concepts assez peu évidents à se représenter. L'analogie est cependant toujours approximative, donc prenez toujours mes analogies pour ce qu'elles sont : des illustrations. Une fois l'analogie comprise, il faut bien entendu creuser un peu le sujet ; mais une fois l'idée générale assimilée, c'est déjà bien plus simple. Tout ça pour vous dire que l'analogie qui suit n'est pas la meilleure qui soit, mais elle sert bien mon propos :-) J'en ferai une meilleure pour me rattraper, c'est promis :

Notre application est une bonne vieille télé (je sais, vous vous dites il nous refait le coup de la télé, eh bien oui), et peut-être n'avez-vous pas connu ces modèles de télé, mais il fut un temps où les boutons pour changer les chaînes étaient *sur la télé*.

![Vieille télé](https://user-images.githubusercontent.com/13923756/75653263-8a496080-5c5d-11ea-80d0-fd38fe3af6ea.jpg)

Cette télévision est ce qu'on pourrait qualifier de monolithe : toutes les fonctions qui vous permettent de gérer la télé sont dans la télé, tout simplement. Puis un beau jour, quelqu'un s'est dit : "regarder la télé et changer les chaînes de la télévision sont deux choses distinctes, il est donc possible de sortir la fonction *changer les chaines de la télé* de la télé elle-même". Et la télécommande fût inventée. Aujourd'hui, vous pouvez regarder la télé tout en changeant les piles de la télécommande sans que cela n'impacte votre expérience de spectateur. Vous avez un service dédié au changement des chaînes, et un service dédié au visionnage d'un programme télé.

Bien sûr, les microservices sont un peu plus complexes que ça. Mais cette image va nous servir à aller plus loin. La télé est un objet lourd et encombrant, on le sait tous. Je n'ai jamais vu chez personne une télé posée à même le sol. Généralement, on place la télé sur un meuble conçu pour cet usage, et disons que notre meuble représente une machine virtuelle (elle aussi est "lourde" et prend de la place : elle a un système d'exploitation et des *drivers* pour tous ses composants matériels, même s'ils sont virtuels). Mais ce n'est pas un problème que le meuble soit lourd et prenne de la place, parce qu'il n'occupe pas plus de place au sol que la télévision à elle seule. Mais la télécommande ? La télécommande a-t-elle aussi besoin d'un meuble à télécommande, ou cela paraîtrait totalement incongru ? Généralement, un microservice est bien plus léger qu'un monolithe, et de ce fait, on est moins à même de vouloir quelque chose d'aussi imposant qu'une machine virtuelle pour le supporter.

Pour synthétiser, aux vues de la taille d'une application monolithique, la charge générée par la machine virtuelle n'est généralement pas une préoccupation. Mais quand les applications commencent à être plus légères, et que le conteneur (dans ce cas la machine virtuelle) génère plus de charge que le contenant (l'application), il est temps de se poser la question : la machine virtuelle est-elle encore la meilleure abstraction pour notre usage ?

Mais je vous avais promis une meilleure métaphore sur les microservices, la voici :

#### Le monolithe

- Vous vous rendez à votre banque pour une raison xyz. Vous attendez patiemment votre tour, puis vous voyez votre conseiller qui vous dit qu'il lui manque un justificatif des impôts pour traiter votre demande.
- Vous vous rendez donc au centre des impôts, vous poireautez à nouveau jusqu'à ce que toutes les personnes devant vous soient passées, puis vous rencontrez un agent qui doit rechercher les informations vous concernant avant de vous donner votre justificatif.
- Justificatif en main, vous retournez à la banque, attendez encore, puis vous remettez le précieux document à votre banquier qui a alors toutes les informations pour traiter votre demande.

**Avantages** : c'est relativement rapide, car l'ensemble du processus n'aura pris qu'une seule journée. Si quelque chose se passe mal à un moment où à un autre, par exemple votre voiture tombe en panne alors que vous vous rendez aux impôts, il est facile d'identifier à quel moment et pourquoi quelque chose s'est mal passé.

**Inconvénients** : vous passez beaucoup de temps à attendre, temps que vous auriez pu utiliser pour d'autres activités. Par ailleurs, vous risquez sous certaines conditions (il y a trop de monde aux impôts ou bien lorsque vous revenez à la banque elle est déjà fermée) de devoir renoncer à faire les actions que vous deviez faire.

#### Le microservice

- Vous écrivez un courrier à la banque pour une raison xyz et vous la remettez à la poste.
- Vous recevez par retour du courrier une lettre vous expliquant qu'il manque un justificatif des impôts à votre dossier pour pouvoir traiter votre demande.
- Vous écrivez aux centre des impôts pour obtenir ce justificatif, et remettez votre lettre à la poste.
- Vous recevez le document demandé des impôts (c'est là où mon exemple manque de crédibilité), toujours par voie postale.
- Vous envoyez ensuite le document à la banque.
- Et finalement vous recevez une réponse de votre conseiller pour vous dire que votre demande a été traitée.

**Avantages** : tout ceci vous a pris relativement peu de temps, et vous n'avez pas été bloqué une journée entière dans ces démarches.

**Inconvénients** : l'ensemble du processus a pris plus de temps, et si quelque chose se passe mal à un moment, "vous ne recevez pas de réponse du centre des impôts", il est plus difficile d'identifier où et pourquoi ça s'est mal passé : est-ce le centre des impôts qui n'a pas encore eu le temps de traiter votre demande, ou est-ce la poste qui a perdu votre courrier ?

Il y a encore bien des choses à expliquer sur les microservices, alors gardez cette image bien au chaud pour plus tard. Je vous l'ai dit, les microservices jouent un grand rôle dans cette histoire, il y a des chances pour que nous les recroisions tôt ou tard.

### Le monde est sur le point de basculer...

Nous voici arrivés aux environs des années 2010, à l'époque où l'opinion semble pencher favorablement vers la machine virtuelle (mais lui a-t-on vraiment laissé le choix ?)

La conteneurisation est là, guettant son heure...

Mais nous sommes à un tournant de l'histoire, car le Cloud est déjà en train de bouleverser ce que tout le monde prenait déjà pour acquis !

![Visage effrayé](https://user-images.githubusercontent.com/13923756/75716885-6a06b980-5cd0-11ea-9510-29039a1bbf82.png)