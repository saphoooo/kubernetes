![Techno Monkeys](https://user-images.githubusercontent.com/13923756/75614249-2462b900-5b37-11ea-8926-485a78fdbdc9.png)

# Kubernetes

## Intro

> **Avertissement** : Il ne s'agit pas d'un livre, plus d'une sorte de blog que je pourrais qualifié du guide le plus décalé pour comprendre Kubernetes

Kubernetes est génial, et pas besoin d'être ingénieur chez Google ou chercheur au MIT pour en tirer le meilleur parti.

Mais j'entends aussi dire que Kubernetes est compliqué. Personnellement, c'est monter une étagère IKEA que je trouve compliqué, à côté de ça Kubernetes est limpide. Si vous savez monter une étagère IKEA, alors vous saurez utiliser Kubernetes.

Pourquoi Kubernetes vous semble si compliqué ? Parce qu'il repose sur un socle de concepts qu'il faut connaître et comprendre ; expliquer Kubernetes sans expliquer ces concepts, c'est comme pisser dans un violon (expression favorite de mon prof de math de 4ème).

Je vais prendre touuuut le temps nécessaire pour essayer de rendre ces concepts accessibles, simples et évidents. Et comme j'ai la chance d'écrire sur un outil collaboratif, si quelque chose n'est pas clair, ou si je dis une connerie (personne n'est à l'abri de dire une connerie), alors vous n'aurez qu'à me corriger, le bonheur c'est simple comme une *pull request* :-)

Comme je suis un blogueur et pas un Emile Zola, si j'écris comme je parle, personne ne m'en tiendra rigueur, n'est-ce pas ? Et puisqu'on est en ligne, j'utiliserai toutes les ressources que le web met gratuitement à ma disposition, c'est toute la beauté de la chose !

## Par quoi commencer quand on veut apprendre Kubernetes ?

Je ne sais pas ce qui vous pousse à apprendre Kubernetes, si c'est pour régler une bonne fois pour toutes les limitations que vous rencontrez avec les conteneurs, ou si c'est juste parce que c'est tendance et que vous êtes curieux ; l'un comme l'autre me vont.

Le hic, c’est que dans un cas vous avez déjà (je l’espère) un bagage culturel/technologique conséquent, mais dans l’autre cas… pas nécessairement. Il n’y a pas de honte à ça, mais ça rend sans doute la courbe d’apprentissage un peu plus… compliquée. Cependant, je vais essayer de tout expliquer le plus simplement possible, mais tout de même, un petit avertissement :

## RTFM

![RTFM](https://live.staticflickr.com/167/419976117_79fe0b10af_z.jpg)

> Savez-vous ce que signifie **RTFM** ? Littéralement c'est *Read The Fucking Manual*. On voyait souvent ça sur les forums avant l'arrivée de Stack Overflow ; ça manquait peut-être un poil d'empathie et de bienveillance, cependant, car il y a un cependant, je trouve qu'il y a aussi une limite à l'empathie et à la bienveillance.
> Prenons un exemple qui sort du domaine informatique : imaginons que quelqu'un me demande à quoi sert une télécommande ; je veux bien lui expliquer en long, en large et en travers à quoi sert ce magnifique objet qui me permet de rester à me pourrir sur le canapé pour regarder la télé (oui, j'ai connu l'époque où les télécommandes n'existaient pas). Par contre, si cette même personne revient vers moi pour me demander à quoi sert la touche 6 sur la télécommande, alors là je ne pourrai plus rien pour elle. Quand on veut apprendre, se faire aider c'est bien, mais il faut aussi y mettre un peu du sien. That's life.

Donc, j'en étais à : par où commencer ? Si je vous dis que Kubernetes est un orchestrateur de conteneurs et que vous ne comprennez ni "orchestrateur", ni "conteneur", alors ne paniquez pas, je vais faire les rappels qui s'imposent. Si vous connaissez déjà tout ça, rendez-vous quelque part plus loin.

## Vous avez dit conteneur ?

Si vous pensez que conteneur rime avec Docker, alors vous n'avez pas tout à fait raison, sans avoir non plus complètement tord.

Figurez-vous que l'histoire des coneteneurs à plus de 40 ans (oui, vous n'étiez sûrement pas né), alors autant vous dire que ça n'a rien d'une technologie dernier cri.

Alors qu'est-ce qu'un conteneur ? Pour faire simple, on peut se représenter ça comme un lieu isolé. Vous voulez abattre une cloison dans votre appartement. Il y a des chances pour que vous mettiez quelques bâches dans les pièces où se trouve cette cloison pour protéger la maison de la poussière que ce genre de travaux va nécessairement provoquer. La conteneurisation n'est pas autre chose.

Dans les années 70-80 (à l'époque où Starky et Hutch faisaient un tabac), quand les ordinateurs étaient encore rares et hors de prix, compiler et tester un binaire ou une librairie n'était pas quelque chose d'annodin et risquait d'endommager définitivement votre système (cher et rare). En isolant ce processus au niveau du système de fichiers, le système était protégé : *chroot* (pour *change root*) était né !

### chroot vite fait

Votre système d’exploitation a des librairies dans `/lib` par exemple, et des binaires dans `/bin`. Si lors de la compilation d'un paquet vous modifiez une de ces libraires ou un de ces binaires, alors vous pouvez dire adieu à votre système.

Maintenant, imaginez que vous compiliez votre paquet dans `/var/foo`, et que vous fassiez croire au compilateur que vous êtes à la racine du système de fichiers `/` ou *root*. Les répertoires `/bin` et `/lib` que le compilateur voit sont en réalité `/var/foo/bin` et `/var/foo/lib`. Vous avez changé la racine pour le compilateur, et vous vous êtes ainsi prémuni d'une erreur fatale !

Puis d’autres cas d’usage sont apparus : quand les ordinateurs ont commencé à être connectés, ce mécanisme a été repris pour isoler les intrus dans une prison (*jail*) sur les systèmes BSD (notamment FreeBSD). Les *jails* poussent l’isolation bien plus loin que ne le fait *chroot*, et ont l’avantage d’être relativement simples à mettre en oeuvre.

Vers le début des années 2000, différents types d'isolation ont alors été intégrés dans le noyau Linux : les *namespaces*. D'abord le namespace mount (un chroot++), puis PID (le processus isolé se voit comme le processus 1 sur le système, et n'a pas conscience des autres processus), puis NET pour l'isolation du network, IPC, etc. Il y a à ce jour 7 namespaces. Si le sujet vous passionne et que vous voulez en savoir plus, [cet article](https://en.wikipedia.org/wiki/Linux_namespaces) dans Wikipédia est un bon point de départ.

### Des namespaces aux cgroups

Si je devais faire une analogie (et c'est une analogie que je fais souvent), on peut considérer les namespaces comme une limite de ce qu'un processus peut voir. Si je reprends mon exemple, mettre un processus dans le namespace mount va réduire la visibilité qu'il a sur le système de fichiers pour le restreindre à la partie qu'on lui permet (tout en lui faisant croire qu'il a tout le système de fichiers à sa disposition).

Essayez de vous représenter un processus comme un individu. Il est dans une boutique remplie d’autres personnes (qui sont eux-aussi d’autres processus du même système). Maintenant, placez le processus dans une cabine d’essayage. Dans cet espace confiné, il est totalement seul, et il n’a plus conscience des autres se promenant dans le magasin ; pour autant, les autres personnes sont toujours là.

Maintenant prenons un autre exemple : imaginez notre processus comme une personne qui travaille dans un open space. D’autres personnes travaillent avec lui dans ce même open space, et afin qu’ils ne se gênent pas les uns les autres, on met une cloison autour de notre processus (à ce stade je devrais lui donner un nom, alors disons qu’il s’appelle Calvin).

Il est à présent seul et peut, par exemple, téléphoner sans perturber les autres. Mais quand Calvin est au téléphone, il aime faire les 100 pas. Son espace confiné étant trop exigu, il commence à pousser les cloisons (pour le bien de mon histoire, ce sont des cloisons fabriquées dans un matériaux révolutionnaire et extensibles à l’infini). Il va finir par pousser les cloisons jusqu’à arriver aux murs physiques de l’open space, et donc prendre tout l’espace disponible… au mépris des autres processus. Cependant ne le jugez pas trop durement, n’oubliez pas qu’il n’a pas conscience de l’existence des autres processus.

Calvin n'a enfrein aucune règle : les namespaces ne font que limiter ce qu'il peut voir. Mais son comportement est des plus fâcheux pour les autres processus. C'est là qu'entre en jeu les *cgroups* (ou *control groups*) : les *cgroups* limitent ce que le processus peut faire.

Calvin est dans l'open space, et nous décidons à nouveau de mettre des cloisons entre lui et les autres processus : c'est notre *namespace*. Nous décidons également que cet espace fait 4m², ce qui empêche Calvin de pousser les cloisons pour se donner plus d'espace (tout en lui laissant un espace nécessaire pour faire ce qu'il a à faire) : arrivé à la limite que nous lui avons fixé, il ne peut pas aller plus loin, et ne risque plus de déranger les autres processus. Ce sont nos *cgroups*.

Si nous revenons à notre système d'exploitation, les *cgroups* vont permettre 
- de limiter les ressources qu'un processus peut consommer (mémoire)
- de les prioritiser (cpu share, I/O)
- et de les controler.

C'est un tableau d'ensemble, mais si vous voulez comprendre les *bits* et les *bytes*, il y a des tonnes d'articles sur le sujet tout autour du web, sur [Wiikipédia](https://fr.wikipedia.org/wiki/Cgroups) bien entendu, mais aussi sur [kernel.org](https://www.kernel.org/doc/Documentation/cgroup-v1/cgroups.txt) et ailleurs. Et si vous voulez expérimenter les *namespaces* et les *cgroups* par vous même, je vous ai préparé un petit lab sur [Katacoda](https://katacoda.com/saphoooo/scenarios/namespaces-et-cgroups) (profitez-en, c'est totalement gratuit).

### Mini conclusion

Je viens de vous faire entrevoir l'un des aspects fondamentaux des conteneurs : ils permettent d'isoler vos processus, et ce à plusieurs niveau (ressources, système de fichier, réseau...). Du coup, vous pouvez bien faire tourner Python 2 (mais plus personne ne fait ça) et Python 3 sur la même machine, Java 8 et Java 11, Node 10 ou Node 12, peu importe : il n'y aura aucune collisions entre les librairies, puisque celles-ci sont idsolées.

Les namespaces ont fait leur entrée dans le noyau Linux en 2003, contre 2006 pour les cgroups. Ces repères histoiriques n'ont pour but que de souligner que **les conteneurs modernes sont intimement liés aux fonctionnalités du noyaux Linux**, alors n'espérez pas les utiliser nativement sur Windows ou Mac.

---

## J'ai un pengouin dans ma salade

Je ne sais pas quelle peut être votre réaction face à ce qui vient d'être dit, mais je peux assez bien m'imaginer que ça peut aller du : "C'est un truc de malade de s'imaginer que les conteneurs ont plus de 40 ans" au simple "Et alors ?" en passant par une grande variété de "Si c'est vrai, alors pourquoi on n'en parle que maintenant ?"

Je pense vraiment qu'un historien des technologies de l'information devrait se pencher sur le sujet, car c'est tout à la fois passionnant et extrêmement complexe. Mais comme toujours je vais essayer d'y apporter mon éclairage personnel, ma vision ; mais on sait tous qu'une histoire n'est jamais totalement neutre, et qu'elle n'est jamais qu'une interprétation des faits. Cependant ne perdez pas de vue que tout ceci explique "pourquoi Kubernetes".

Tout d'abord, replaçons-nous dans un contexte historique, celui du début des années 2000 (vous vous sovenez, Matrix, Kaamelott et tout ça). A cette époque, l'open source n'est encore qu'un mouvement clandestin, un vent de mutinerie face à l'impérialisme de la propriété intellectuelle (du moins d'après certains) ; Google n'est qu'un moteur de recherche, Amazon un marchant de livres, et le marché est dominé par Microsoft (Windows), Sun Microsystems (Solaris), IBM (Aix) et HP (UX). [Linux est au mieux considéré comme un cancer](https://www.zdnet.fr/actualites/microsoft-a-un-jour-qualifie-linux-de-cancer-et-c-etait-une-grossiere-erreur-39887203.htm), quand les gens savent de quoi on leur parle lorsqu'on évoque Linux (hé oui).

Le problème majeur du début des années 2000, c'est que les machines qui se trouvent dans les centres de données sont ce qu'on pourrait qualifier de *gros systèmes* (sans vouloir leur manquer de respect), avec de nombreux processeurs et des giga octets de mémoire (à l'époque, cette unité de mesure était impresionnate) ; faire tourner une seule application sur un tel système n'a pas vraiment de sens, car peu d'applications sont en mesure de consommer toutes les ressources qui sont à leur disposition, et on se retouve vite à avoir des machines hors de prix qui passent la majeure partie de leur temps à ne rien faire. L'idée de "consolider son infrastructure" en exécutant plusieurs appliactions sur la même machine commence à faire son chemin.

Mais on le sait déjà : ce n'est pas si simple, plusieurs versions d'un même runtime applicatif (Java par exemple) peuvent difficilement tourner sur la même machine sans entrer à un moment ou à un autre en conflit.

Ce phénomène entraîne l'essor d'une autre technologie que vous connaissez bien, encore plus vieille que les conteneurs : les machines virtuelles. Désolé de vous l'apprendre si brutalement, mais ce n'est pas VMware qui est à l'origine de la virtualisation, mais bien IBM et le MIT dans les années 1960. Ceci dit, si VMware est à la place qu'il occupe aujourd'hui, c'est parce qu'il a misé sur l'architecture x86, qui est l'architecture la plus populaire de nos jours.

Virtualisation et conteneurisation sont deux réponses à ce même problème : "comment faire tourner plusieurs applications sur un même système ?". Toutes deux ont leurs avantages et leurs inconvénients, mais la conteneurisation a un modèle plus disruptif que la machine virtuelle. Il est en effet facile de se représenter une machine virtuelle : elle a un disque, un cpu, de la mémoire et une carte réseau, tout comme une machine physique. On y installe un système d'exploitation, et roule ma poule, on peut y mettre son application. A l'opposé, isoler un processus et se représenter les intéractions qu'il a avec la machine (disques, cpu, mémoire et réseau) n'a rien d'une évidence.

Comment illustrer cette différence ? Imaginez que notre machine virtuelle est une maison : elle est reliée au réseau électrique, à l'eau, aux égoûts, elle a sa porte d'entrée et sa boîte aux lettres. Elle fonctionne en toute autonomie, dans la mesure où on lui assure les services dont elle dépend : eau, électricité, courrier... Votre conteneur quant à lui est un appartement : lui aussi dispose de l'électricité, de l'eau et du courrier, mais ces différents services sont mutualisés au niveau de l'immeuble dans lequel il se trouve. Cette analogie est terriblement pertinente pour comprendre que la gestion d'une machine virtuelle et celle d'un conteneur sont profondément différentes : dans le premier cas nous disposons d'une grande liberté pour un coût plus élevé (la maison), dans le deuxième cas il faut se plier aux règles de la copropriété, mais pour un coût largement plus faible (l'appartemment).

Mais reprenons le fil de notre histoire. A cette époque, comme je l'ai mentionné, le marché est ségmenté entre différents Unix propriétaires d'un côté et Windows de l'autre, ce dernier n'implémentant ni la conteneursisation ni la virtualisation. Dans ces circonstances, comment consolider son parc de serveurs Windows ?

Savez-vous comment fonctionne une machine virtuelle ? Ce n'est un secret pour personne : à l'aide d'un hyperviseurr. Du coup, si nous considérons toujours qu'une machine virtuelle est une maison, l'hyperviseur peut être comparé à une commune : elle gère les parcelles de terrain, et les rend constructibles en les réliants à différents services (eau, électricité, etc). Le conteneur lui n'a pas d'hyperviseur : il ne dépend que du système d'exploitation (l'immeuble).

Un hyperviseur sert donc à virtualiser une architecture spécifique, comme x86, et permet de faire tourner théoriquement n'importe quel système d'exploitation propre à cette architecture (il ne faut donc pas le confondre avec un émulateur).

De son côté, le conteneur est propre aux fonctionnalités du noyau d'un système d'exploitation (un conteneur Linux ne tournera pas sur BSD et vice versa). Or Windows (dans les années 2000, entendons-nous bien) n'implémente pas de technologie de conteneurisation ; impossible donc d'utiliser la conteneurisation pour isoler deux applications sur un système Windows. Par contre, il est possible de virtualiser Windows, et d'avoir une instance de Windows pour chaque application qu'on veut isoler. Il en découle qu'en l'absence d'autre choix, les machines virtuelles deviennent la norme pour virtualiser une application exécutée sous Windows.

A cette même époque pourtant Sun Microsystems mise la conteneurisation avec les Zones Solaris, choix que fait également Google, mais avec les conteneurs Linux ([chez Google, tout tourne dans des conteneurs](https://www.infoq.com/fr/news/2014/06/everything-google-containers/), c'est bien connu). Pour l'annecdote, le système de conteneurisation de Google s'appelle Borg (retenez ce nom, nous en reparlerons).

![cyborg-pinguin](https://user-images.githubusercontent.com/13923756/75623629-21ee7680-5bac-11ea-9f37-65bf62a3e42d.png)

### En bref

- Le modèle des mahines virtuelles s'est largement imposé vers le milieu ds années 2000 sous l'influence de 2 acteurs majeurs : VMware et Microsoft.
- Sous Unix, il y a eu des initiatives aussi bien vers la machine virtuelle que vers la conteneursitation.
- Linux n'est pas non plus en reste, puisqu'il dispose lui aussi de plusieurs technologies de virtualisation et de conteneurisation. Cependant il ne bénéficie pas alors de la popularité dont il jouit aujourd'hui et n'est donc pas l'acteur de cette standardisation en faveur des vm.

## Je vous ressers un peu de VM ?

Tout cela est bien joli et permet de comprendre pourquoi la machine virtuelle est si populaire encore aujourd'hui, mais pas "pourquoi on s'intéresse aux conteneurs" ?

Or cette fois, ce n'est plus vers l'infrastructure qu'il faut se tourner, mais vers le génie logiciel, car quoi que vous puissiez en penser, les machines ne sont jamais là que pour faire tourner des serveurs, et plus elles sont abstraites, et mieux on se porte.

> Par soucis de clarté, je distingue toujours **machine** : une machine physique ou virtuelle ; d'un **serveur** : programme qui s'exécute sur une machine.

### Les microservices en abrégé

Les microservices étaient déjà dans l'air du temps en ce milieu des années 2000, même si le terme n'est vraiment apparu qu'en 2011.

Mais non, je ne vais pas vous expliquer en détail ce que sont les microservices, pour autant ce sont des personnages de mon histoire, et vous allez apprendre à les connaître.

> Je fais souvent des analogies pour décrire les choses ; illustrer une idée me permet de la rendre plus concrète, alors qu'en réalité nous parlons de concepts assez peu évidents à se représenter. L'analogie est cependant toujours approximative, donc prenez toujours mes analogies pour ce qu'elles sont : des illustrations. Une fois l'analogie comprise, il faut bien entendu creuser un peu le sujet ; mais une fois l'idée générale assimilée, c'est déjà bien plus simple. Tout ça pour vous dire que l'analogie qui suit n'est pas la meilleure qui soit, mais elle sert bien mon propos :-) J'en ferai une meilleure pour me rattraper, c'est promis :

Notre application est une bonne vieille télé (je sais, vous vous dites il nous refait le coup de la télé, eh bien oui), et peut-être n'avez-vous pas connu ces modèles de télé, mais il fut un temps où les boutons pour changer les chaînes étaient *sur la télé*, je vous assure.

![Vieille télé](https://user-images.githubusercontent.com/13923756/75653263-8a496080-5c5d-11ea-80d0-fd38fe3af6ea.jpg)

Cette télévision est ce que l'on pourrait qualifier de monolite : toutes les fonctions qui vous permettent de gérer la télé sont dans la télé, tout simplement. Puis un beau jour, quelqu'un s'est dit : "regarder la télé et changer les chaînes de la télévision sont deux choses bien distinctes, il est donc possible de sortir la fonction *changer les chaines de la télé* de l'objet télé". Et la télécommande fût inventée. Aujourd'hui, vous pouvez regarder la télé tout en changeant les piles de la télécommande sans que cela n'impacte votre expérience de spectateur. Vous avez un service dédié au changement des chaînes, et un service dédié au visionnage d'un programme télé.

Bien sûr, les microservices sont un peu plus complexes que ça. Mais cette image va nous servir à aller plus loin. La télé est un objet lourd et encombrant, on le sait tous. Je n'ai jamais vu chez personne une télé posée à même le sol. Généralement, on place la télé sur un meuble conçu pour cet usage, et disons que notre meuble représente une machine virtuelle (elle aussi est "lourde" et prend de la place : elle a un système d'exploitation et des *drivers* pour tous ses composants matériels, même s'ils sont virtuels). Mais ce n'est pas un problème que le meuble soit lourd et prenne de la place, parce qu'il n'occupe pas plus de place au sol que la télévision à elle seule. Mais la télécommande ? La télécommande a-t-elle aussi besoin d'un meuble à télécommande, ou cela paraîtrait totalement inaproprié ? Généralement, un microservice est bien plus léger qu'un monolite, et de ce fait, on est moins à même de vouloir quelque chose d'aussi encombrant qu'une machine virtuelle pour le supporter.

Pour synthétiser, aux vues de la taille d'une application monolitique, la charge générée par la machine virtuelle n'est généralement pas une préoccupation. Mais quand les applications commencent à être plus légères, et que le conteneur (dans ce cas la machine virtuelle) génère plus de charge que le contenant (l'application), il est temps de se poser la question : la machine virtuelle est-elle encore l'abstraction dont nous avons besoin ?

Mais je vous avais promis une meilleure métaphore sur les microservices, la voici :

#### Le monolite

- Vous vous rendez à votre banque pour une raison xyz. Vous attendez patiemment votre tour, puis vous voyez votre conseiller qui vous dit qu'il lui manque un justificatif des impôts pour traîter votre demande.
- Vous vous rendez donc au centre des impôts, vous poireautez à nouveau jusqu'à ce que toutes les personnes devant vous soient passées, puis vous rencontrez un agent qui doit rechercher les informations vous concernant avant de vous donner votre justificatif.
- Justificatif en main, vous retrounez à la banque, attendez encore, puis vous remettez le précieux document à votre banquier qui a alors toutes les informations pour traîter votre demande.

**Avantages** : c'est relativement rapide, car l'ensemble du processus n'aura pris qu'une seule journée. Si quelque chose se passe mal à un moment où à un autre, par exemple votre voiture tombe en panne alors que vous vous rendez aux impôts, il est facile d'identifier à quel moment et pourquoi quelque chose s'est mal passé.

**Inconvénients** : vous passez beaucoup de temps à attendre, temps que vous auriez pu utiliser pour d'autres activités. Par ailleurs, vous risquez sous certaines conditions (il y a trop de monde aux impôts ou bien lorsque vous revenez à la banque elle est déjà fermée) de devoir renoncer à faire les actions que vous devez faire.

#### Le microservice

- Vous écrivez un courrier à la banque pour une raison xyz et vous la remettez à la poste.
- Vous recevez par retour du courrier une lettre vous expliquant qu'il manque un justificatif des impôts à votre dossier pour pouvoir traîter votre demande.
- Vous écrivez aux centre des impôts pour obtenir ce justificatif, et remettez votre lettre à la poste.
- Vous recevez le document demandé des impôts (c'est là où mon exemple n'est plus crédible), toujours par voie postale.
- Vous envoyez ensuite le document à la banque.
- Et finalement vous recevez une réponse de votre conseiller pour vous dire que votre demande a été traîtée.

**Avantages** : tout ceci vous a pris relativement peu de temps, et vous n'avez pas été bloqué une journée entière dans ces démarches.

**Inconvénients** : l'ensemble du processus a pris plus de temps, et si quelque chose se passe mal à un moment (vous ne recevez pas de réponse du centre des impôts), il est difficile d'identifier où et pourquoi ça s'est mal passé : est-ce le centre des impôts qui n'a pas encore eu le temps de traîter votre demande, ou est-ce la poste qui a perdu votre courrier ?

Il y a encore bien des choses à expliquer sur les microservices, aloes gardez cette image bien au chaud pour plus tard. Je vous l'ai dit, les microservices jouent un grand rôle dans cette histoire, il y a des chances pour que nous les recroisions tôt ou tard.

### Le mmonde est sur le point de basculer...

Nous voici arrivés aux environs des années 2010, à l'époque où l'opinion semble pencher favorablement vers la machine virtuelle (mais lui a-t-on vraiment laissé le choix ?)

La conteneurisation est là, guettant son heure. Mais nous sommes à un tournant historique : Linux et le Cloud son sur le point de bouleverser ce que tout le monde croyait acquis !

![Visage effrayé](https://user-images.githubusercontent.com/13923756/75716439-78a0a100-5ccf-11ea-8f8a-9031473a099c.png)

---