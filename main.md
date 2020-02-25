# Intro

**Avertissement** : je ne sais pas où tout ça va me mener... On verra bien :-)

Kubernetes est génial, pas besoin d'être ingénieur chez Google ou chercheur au MIT pour s'en rendre compte.

J'entends aussi dire que Kubernetes est compliqué. Personnellement, c'est monter une étagère IKEA que je trouve compliqué, à côté de ça Kubernetes est limpide. Vous savez monter une étagère IKEA ? Alors vous saurez utiliser Kubernetes.

Pourquoi Kubernetes vous semble compliqué ? Parce qu'il repose sur une tonne de concepts dont vous n'avez peut-être jamais entendu parlé. Expliquer Kubernetes sans expliquer les concepts sur lesquels il repose, c'est comme pissé dans un violon (expression favorite de mon prof de math de 4ème).

Je vais prendre touuuut le temps nécessaire pour essayer de rendre ces concepts simples, évidents. Et comme j'ai la chance d'écrire sur un outil collaboratif, si quelque chose n'est pas clair, ou si je dis une connerie, alors vous n'aurez qu'à me corriger, le bonheur c'est simple comme une pull request :-)

Comme je suis un blogueur et pas un Emile Zola, si j'écris comme je parle, personne ne m'en tiendra rigueur. Et puisqu'on est en ligne, j'utiliserai toutes les ressources que le web met gratuitement à ma disposition, c'est toute la beauté de la chose !

# Par quoi commencer quand on veut apprendre Kubernetes ?

Je ne sais pas ce qui vous pousse à apprendre Kubernetes, si c'est pour régler une bonne fois pour toutes les limitations que vous rencontrer avec les conteneurs, ou si c'est juste parce que c'est tendance et que vous êtes curieux.

Le hic, c'est que dans un cas vous avez déjà (je l'espère) un bagage culturel conséquent, mais pas forcément dans l'autre cas. Et il n'y a pas de honte à ça. Je vais essayer de tout expliquer le plus simplement possible, mais tout de même, un petit avertissement :

> Vous savez ce que signifie RTFM ? Littéralement c'est *Read The Fucking Manual*. On voyait souvent ça sur les forum avant l'arrivée de Stack Overflow ; ça manquait peut-être un poil d'empathie et de bienveillance. Cependant, car il y a un cependant, je trouve qu'il y a aussi une limite à l'empathie et à la bienveillance. Prenons un exemple qui sort du domaine informatique : imaginons que quelqu'un me demande à quoi sert une télécommande, je veux bien lui expliquer en long, en large et en travers à quoi sert ce magnifique objet qui me permet de rester à pourrir sur mon canapé pour regarder la télé (oui, j'ai connu l'époque où les télécommandes n'existaient pas) ; par contre, si cette même personne revient vers moi pour me demander à quoi sert la touche 6 sur la télécommande, alors là je ne pourrai plus rien pour elle. Quand on veut apprendre, se faire aider c'est bien, mais il faut aussi y mettre un peu du sien. That's life.

Donc, j'en étais à : par où commencer ? Si je vous dis que Kubernetes est un orchestrateur de conteneur et que vous ne comprennez ni "orchestrateur", ni "conteneur", alors ne paniquez pas, je vais faire les rappels qui s'imposent. Si vous connaissez déjà tout ça, rendez-vous quelque part plus loin.

# Vous avez dit conteneur ?

Si vous pensez que conteneur rime avec Docker, alors vous n'avez pas tout à fait raison, sans avoir non plus complètement tord.

Figurez-vous que l'histoire des coneteneurs à plus de 40 ans (oui, vous n'étiez sûrement pas né), alors autant vous dire que ça n'a rien d'une technologie dans le vent tout dernier cri.

Alors qu'est-ce qu'un conteneur ? Pour faire simple, on peut se représenter ça comme un lieu isolé. Imaginez-vous abatre une cloison dans votre appartement (ou votre maison). Il y a des chances pour que vous vouliez mettre quelques bâches dans les pièces où se trouve cette cloison pour protéger les autres pièces de la maison de la poussière que ce genre de travaux va nécessairement générer.

Dans les années 70-80 (à l'époque où Starky et Hutch et Shaft faisaient un tabac), quand les ordinateurs étaient encore rares et hors de prix, compiler et tester un binaire ou une librairie n'était pas quelque chose d'annodin et risquait d'endommager définitivement votre système (cher et rare). En isolant ce processus au niveau du système de fichiers, le système était protéger : chroot (pour *change root*) était né !

## chroot vite fait

Votre système d'exploitation a des librairies peut-être dans `/lib` et des binaires dans `/bin`. Si lors de la compilation d'un paquet vous modifier une libraire ou un binaire, il y a des chances pour que vous puissiez dire adieu à votre système.

Maintenant, imaginez que vous compiliez votre paquet dans `/var/foo`, et que vous fassiez croire au compilateur que vous êtes à la racine du système de fichiers `/` ou root. `/bin` et `/lib` que le compilateur voit sont en réalité `/var/foo/bin` et `/var/foo/lib`. Vous avez changé la racine pour le compilateur, et vous vous êtes ainsi prémuni d'une erreur fatale !

Puis d'autres cas d'usage sont apparus : quand les ordinateurs ont commencé à être connecté les uns avec les autres, ce mécanisme a été repris pour isoler les intrus dans une prison (*jail*) sur les systèmes BSD (c'est un Unix, si vous utilise Mac, alors vous utiliser un système BSD :-)).

Vers le début des années 2000, différents types d'isolation ont alors été implémentés dans le noyau Linux : les namespaces. D'abord le namespace mount (un chroot++), puis PID (le processus isolé se voit comme le processus 1 sur le système, et n'a pas conscience des autres processus), puis NET pour l'isolation du network, etc. Il y a à ce jour 7 namespaces, si le sujet vous intéresse, vous pouvez en lire plus dans [cet article](https://en.wikipedia.org/wiki/Linux_namespaces).