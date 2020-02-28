![Techno Monkeys](https://user-images.githubusercontent.com/13923756/75512626-5ae2eb80-59f2-11ea-99e3-a160b488be07.png)

# Kubernetes

## Intro

> **Avertissement** : je ne sais pas où tout ça va me mener... On verra bien :-)

Kubernetes est génial, et pas besoin d'être ingénieur chez Google ou chercheur au MIT pour en tirer le meilleur parti.

Mais j'entends aussi dire que Kubernetes est compliqué. Personnellement, c'est monter une étagère IKEA que je trouve compliqué, à côté de ça Kubernetes est limpide. Si vous savez monter une étagère IKEA, alors vous saurez utiliser Kubernetes.

Pourquoi Kubernetes vous semble si compliqué ? Parce qu'il repose sur un socle de concepts qu'il faut connaître et comprendre ; expliquer Kubernetes sans expliquer ces concepts, c'est comme pisser dans un violon (expression favorite de mon prof de math de 4ème).

Je vais prendre touuuut le temps nécessaire pour essayer de rendre ces concepts accessibles, simples et évidents. Et comme j'ai la chance d'écrire sur un outil collaboratif, si quelque chose n'est pas clair, ou si je dis une connerie (personne n'est à l'abri de dire une connerie), alors vous n'aurez qu'à me corriger, le bonheur c'est simple comme une *pull request* :-)

Comme je suis un blogueur et pas un Emile Zola, si j'écris comme je parle, personne ne m'en tiendra rigueur, n'est-ce pas ? Et puisqu'on est en ligne, j'utiliserai toutes les ressources que le web met gratuitement à ma disposition, c'est toute la beauté de la chose !

## Par quoi commencer quand on veut apprendre Kubernetes ?

Je ne sais pas ce qui vous pousse à apprendre Kubernetes, si c'est pour régler une bonne fois pour toutes les limitations que vous rencontrez avec les conteneurs, ou si c'est juste parce que c'est tendance et que vous êtes curieux ; l'un comme l'autre me vont.

Le hic, c'est que dans un cas vous avez déjà (je l'espère) un bagage culturel conséquent, mais dans l'autre cas... pas nécessairement. Il n'y a pas de honte à ça, mais ça rend sans doute la courbe d'apprentissage un peu plus... compliquée. Cependant, je vais essayer de tout expliquer le plus simplement possible, mais tout de même, un petit avertissement :

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

Votre système d'exploitation a des librairies peut-être dans `/lib` et des binaires dans `/bin`. Si lors de la compilation d'un paquet vous modifiez une de ces libraires ou un de ces binaires, alors vous pouvez dire adieu à votre système.

Maintenant, imaginez que vous compiliez votre paquet dans `/var/foo`, et que vous fassiez croire au compilateur que vous êtes à la racine du système de fichiers `/` ou *root*. Les répertoires `/bin` et `/lib` que le compilateur voit sont en réalité `/var/foo/bin` et `/var/foo/lib`. Vous avez changé la racine pour le compilateur, et vous vous êtes ainsi prémuni d'une erreur fatale !

Puis d'autres cas d'usage sont apparus : quand les ordinateurs ont commencé à être connectés, ce mécanisme a été repris pour isoler les intrus dans une prison (*jail*) sur les systèmes BSD (BSD est un Unix ; si vous utilisez un Mac, alors vous utilisez un système BSD :-)).

Vers le début des années 2000, différents types d'isolation ont alors été intégrés dans le noyau Linux : les *namespaces*. D'abord le namespace mount (un chroot++), puis PID (le processus isolé se voit comme le processus 1 sur le système, et n'a pas conscience des autres processus), puis NET pour l'isolation du network, etc. Il y a à ce jour 7 namespaces. Si le sujet vous passionne et que vous voulez en savoir plus, [cet article](https://en.wikipedia.org/wiki/Linux_namespaces) dans Wikipédia est un bon point de départ.

### Des namespaces aux cgroups

Si je devais faire une analogie (et c'est une analogie que je fais souvent), on peut considérer les namespaces comme une limite de ce qu'un processus peut voir. Si je reprends mon exemple, mettre un processus dans le namespace mount va réduire la visibilité qu'il a sur le système de fichiers pour le restreindre à la partie qu'on lui permet (tout en lui faisant croire qu'il a tout le système de fichiers à sa disposition).

Représetez-vous un processus comme un individu. Il est dans une boutique remplie d'autres personnes (qui sont eux-aussi d'autres procesus du même système). Maintenant, placer le processus dans une cabine d'essayage. Dans cet espace confiné, il se voit comme étant la seule personne du magazin ; il n'a plus conscience des autres, pourtant les autres personnes sont toujours là.

Maintenant prenons un autre exemple : le processus est une personne qui travaille dans un open space. Il y a d'autres personnes qui travaillent avec lui dans le même open sapce, et afin qu'ils ne se gênent pas les uns les autres, on met une cloison autour de notre processus (à ce stade je devrais lui donner un nom, mettons qu'il s'appelle Calvin).

Il est à présent seul et peut, par exemple, téléphoner sans perturber les autres. Mais quand Calvin est au téléphone, il aime faire les 100 pas. Son espace confiné étant trop exigu, il commence par pousser les cloisons (pour le bien de mon histoire, ce sont des cloisons fabriquées dans un matériaux révolutionnaire et extensibles à l'infini). Il va finir par pousser les cloisons jusqu'à arriver aux murs physiques de l'open space, et donc prendre tout l'espace disponible, au mépris des autres processus. Cependant ne le jugez pas trop durement, n'oubliez pas qu'il n'a pas conscience de l'existence des autres processus.

Calvin n'a enfrein aucune règle : les namespaces ne font que limiter ce qu'il peut voir. Mais son comportement est des plus fâcheux pour les autres processus. C'est là qu'entre en jeu les *cgroups* (ou *control groups*) : les *cgroups* limitent ce que le processus peut faire.

Calvin est dans l'open space, et nous décidons à nouveau de mettre des cloisons entre lui et les autres processus : c'est notre namespace. Nous décidons également que cet espace fait 4m², ce qui empêche Calvin de pousser les cloisons pour se donner plus d'espace (tout en lui laissant un espace nécessaire pour faire ce qu'il a à faire) : arrivé à la limite que nous lui avons fixé, il ne peut pas aller plus loin, et ne risque plus de déranger les autres processus. Ce sont nos cgroups.

Si nous revenons à notre système d'exploitation, les cgroups vont permettre 
- de limiter les ressources qu'un processus peut consommer (mémoire)
- de les prioritiser (cpu share, I/O)
- et de les controler.

C'est un tableau d'ensemble, mais si vous voulez comprendre les bits et les bytes, il y a des tonnes d'articles sur le sujet tout autour du web, sur [Wiikipédia](https://fr.wikipedia.org/wiki/Cgroups) bien entendu, mais aussi sur [kernel.org](https://www.kernel.org/doc/Documentation/cgroup-v1/cgroups.txt) et ailleurs. Et si vous voulez expérimenter les namespaces et les cgroups par vous même, je vous ai préparé un petit lab sur [Katacoda](https://katacoda.com/saphoooo/scenarios/namespaces-et-cgroups) (profitez-en, c'est totalement gratuit).

### Mini conclusion

Je viens de vous faire entrevoir l'un des aspects fondamentaux des conteneurs : ils permettent d'isoler vos processus, et ce à plusieurs niveau. Du coup, vous pouvez bien faire tourner Python 2 et Python 3 sur la même machine, Java 8 et Java 11, Node 10 ou Node 12, peu importe : il n'y aura aucune collisions entre les librairies, dans la mesure où celles-ci sont idsolées.

Les namespaces ont fait leur entrée dans le noyau Linux en 2003, contre 2006 pour les cgroups. Ces repères histoiriques n'ont pour but que de souligner que **les conteneurs modernes sont intimement liés aux fonctionnalités du noyaux Linux**, alors n'espérez pas les utiliser nativement sur Windows ou Mac.

## J'ai un penguoin dans ma salade

Je suis au courant : peu de personnes utilisent Linux sur leur laptop (par contre, sur votre téléphone il y a des chances pour que ce soit différent si vous utilisez Android). Mais vous avez peut-être entendu parler de Docker Desktop, de WSL ; j'en reparlerai le temps venu, tout comme je vous parlerai de Minikube.

Mais pour l'heure accordons nous sur ce point : quand vous faites tourner un conteneur Linux sous Windows ou sous Mac, vous l'exécutez en réalité dans une machine virtuelle, même si votre expérience utilisateur vous laisse penser le contraire (au passage merci aux ingénieurs de Docker d'avoir rendu cette magie si simple).