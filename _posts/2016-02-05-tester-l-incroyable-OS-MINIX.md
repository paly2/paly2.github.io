---
layout: post
title: Tester l'incroyable OS MINIX
---

MINIX est un système d'exploitation à micro-noyau sûr, fiable, gratuit, libre, open-source. Ici, nous le testerons avec VirtualBox sous un système GNU/Linux.

## 1. Installation de VirtualBox

Il vous suffit d'inscrire cette commande dans un terminal :  
`sudo apt-get install virtualbox`

Pour lancer VirtualBox, vous pouvez simplement taper:  
`virtualbox`

## 2. Création de la machine virtuelle

Une fois VirtualBox ouvert, cliquez sur "Nouvelle".

Écrivez par exemple MINIX3 dans la zone "Nom", sélectionnez "Other" pour "Type" et "Other/Unknown" ou "Other/Unknown (64-bits)" pour "Version".

Cliquez sur "Suivant" et choisissez la quantité de RAM. La valeur par défaut est de 64Mio, vous pouvez la laisser si vous n'avez pas beaucoup de RAM, mais je vous conseille plutôt 0.5Gio (soit 500Mio) pour pouvoir faire fonctionner des programmes très gourmands en mémoire (par exemple, les algorithmes mathématiques qui stockent de longues tables dans la mémoire).

Cliquez à nouveau sur "Suivant" et cliquez sur "Créer un disque dur virtuel maintenant", cliquez encore une fois sur "Suivant", cochez "VDI" (qui est normalement sélectionné par défaut), cliquez sur "Suivant".

Vous pouvez maintenant choisir de créer un disque dur fixe ou dynamique. Si vous ne savez pas trop ce que vous faites je vous conseille de laisser dynamique. La valeur par défaut est de 2 Gio, mais je vous recommande vivement de choisir au moins 10 Gio (surtout si vous êtes en stockage dynamique, puisque le disque s'agrandira tout seul jusqu'à atteindre sa taille maximale).

Pour finir, cliquez sur "Créer".

## 3. Mise en place de l'image de MINIX3 sur la machine virtuelle

Maintenant, rendez-vous sur la [page de téléchargement](http://wiki.minix3.org/doku.php?id=www:download:start) de MINIX3, et prenez l'image correspondant à votre architecture (la plupart des ordinateurs "habituels" ont un CPU x86, [ce lien](http://download.minix3.org/iso/minix_R3.3.0-588a35b.iso.bz2) devrait donc être tout à fait utilisable dans la plupart des cas). Vous devez ensuite la décompresser (vous pouvez pour cela utiliser le bouton "Extraire" présent sur la plupart des logiciels capables de lire des archivez .bz2, tels que XArchiver).

Dans la liste de VirtualBox, sélectionnez MINIX3 et cliquez sur le bouton "Démarrer". On vous demande maintenant de choisir un disque de démarrage. Cliquez sur le bouton "Parcourir" à droite de la zone de texte, et ouvrez votre image .iso extraite. Cliquez sur "Ouvrir".

## 4. Installation de MINIX3

MINIX3 devrait maintenant être en train de se lancer. Attendez jusqu'à ce qu'il vous demande "login:" (cela peut prendre un peu de temps). Répondez en écrivant "root", puis pressez "Entrez".

Entrez maintenant "setup" (et, bien sûr, pressez "Entrée"). Après avoir lu les notes (si vous parlez anglais), pressez à nouveau "Entrée".

### 4.1. Le clavier

Si vous lisez ce tutoriel en français, il est probable que vous soyez en France. Si c'est le cas, inscrivez "french" et pressez "Entrée". Cependant, si vous êtes par exemple en Suisse ou au Canada et que vous avez un clavier QWERTY, pressez "Entrée" sans rien écrire (votre clavier sera alors laissé en "us-std").

### 4.2. Créer une partition pour MINIX3

Pressez "Entrée" lorsqu'on vous demande si vous un expert ou non, afin d'être en mode automatique (de toute façon, même si vous êtes un expert, sur un disque dur virtuel vierge, le mode expert ne vous apportera strictement rien de plus que le mode automatique, il sera simplement plus complexe).

On vous demande ensuite de sélectionner le disque voulu, normalement il n'y en a qu'un, pressez donc "Entrée" sans rien écrire pour continuer. Faites de même lorsque qu'on vous demande la partition.

Vous devriez normalement lire "This is the point of no return". En vérité, sur une machine virtuelle, ce n'est pas totalement vrai, vous pourrez la supprimer et la réinstaller (ou pas). Écrivez "yes" et pressez "Entrée".

On vous demande ensuite la taille du "/home", ce qui correspond au dossier où seront stockées tous les fichiers de utilisateurs. Vous pouvez laisser la valeur par défaut en pressant "Entrée" (cette valeur laisse beaucoup de place au "/usr", ce qui correspond à l'endroit où sont stockés tous les programmes). Confirmez ensuite en pressant "Entrée".

Vous devez maintenant choisir la "block size", laissez la valeur par défaut qui est très bien et soyez gentils de ne pas me demander à quoi ça correspond :>

### 4.3. Copie des fichiers

Maintenant, MINIX3 copie tous les fichiers depuis l'image jusqu'au disque. Cette étape de l'installation de MINIX3 est beaucoup moins longue que celle des gros systèmes plus connus comme GNU/Linux, BSD ou encore le champion toutes catégories des poids lourds Windows.

### 4.4. Configuration du réseau

MINIX3 vous demande maintenant le type de carte réseau qu'il doit utiliser. Je vous conseille de choisir la carte "AMD LANCE", puisque c'est celle qui est émulée par VirtualBox. Il s'agit normalement du choix par défaut, pressez donc "Entrée" sans rien écrire.

On vous demande maintenant si vous voulez que l'adresse IP de votre ordinateur (selon MINIX3 bien sûr, puisque vous êtes sur une machine virtuelle) soit statique ou choisie par DHCP. Si vous ne savez pas ce que c'est laissez 1, mais vous pouvez tout à fait choisir 2 (ce qui vous demandera de remplir quelques champs supplémentaires, mais en général les valeurs par défaut vous conviendront (pour la "gateway" il n'y a pas de valeur par défaut, mais dans la plupart des cas c'est 192.168.1.1)).

### 4.5. Redémarrage

C'est enfin la fin de l'exécution de la commande "setup", et vous redonne un prompt ("#"). Inscrivez "poweroff" et pressez "Entrée" afin d'éteindre la machine virtuelle.

## 5. Supprimer l'image de MINIX3

Il ne faut plus que la machine démarre sur l'image .iso que vous lui avez donné pour l'installer, mais sur le disque dur lui-même, à présent ! Nous allons donc la supprimer (exactement comme si nous enlevions un CD-ROM d'installation, et en réalité, pour MINIX3, c'est exactement ce qui se passe).

Dans la fenêtre de VirtualBox, sélectionnez la machine "MINIX3", et cliquez sur "Configuration".

Dans la liste de gauche de la fenêtre qui vient d'apparaître, cliquez sur "Stockage", puis sélectionnez votre image de MINIX3 (dont le nom doit ressembler à "minix_R3...iso"). Cliquez maintenant sur le petit bouton "supprimer" en bas de la liste.

Cliquez ensuite sur "Ok".

## 6. Installation terminée !

Vous pouvez maintenant relancer la machine en cliquant sur "Démarrer". Éteignez-la en entrant toujours la commande "poweroff".

Vous pouvez aller voir la [documentation de MINIX3](http://wiki.minix3.org/doku.php?id=www:documentation:start) (en anglais...) !

Vous verrez que cet incroyable système a plus d'une qualité, la première étant d'être incroyablement petit, ce qui vous permet de l'étudier à volonté.
