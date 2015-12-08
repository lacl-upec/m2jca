*Programmation sécurisée*
=========================

Architecture de sécurité en Java
================================

Principe
--------

Pour chaque action « critique », la machine virtuelle Java vérifie le le programme est autorisé à l'effectuer

Actions critiques :

 - Accéder au disque
 - Accéder au réseau
 - Accéder aux propriété système (`System.getProperty`) comme le répertoire principal de l'utilisateur
 - Représentées par les sous-classes de `java.security.Permission`

Par défaut, ces vérifications sont supprimées pour plus d'efficacité

Intérêt
-------

On met en place le système de vérification pour se protéger de programmes externes :

- soit directement et lancé en ligne de commande
- soit téléchargé par un programme et exécuté à la volée (`URLClassLoader` par exemple)

Ce système ne sert qu'à protéger l'utilisateur mais pas à contraindre l'utilisation d'un programme par l'utilisateur (il faudrait plutôt un système de DRM pour cela)

Mise en place du système de vérification
----------------------------------------

Soit en ligne de commande :

>   `-Djava.security.manager -Djava.security.policy=`*policy file*

Soit de manière programmatique :

>   `System.setSecurityManager(new SecurityManager())`

Paramétrer les restrictions, solution 1
---------------------------------------

Écrire un sous-classe de `SecurityManager`

Il suffit de redéfinir la méthode `checkPermission` pour décider si la demande est autorisée.

 - Peut se baser sur le Thread appelant (`Thread.currentThread`)
 - Peut se baser sur l'utilisateur en train d'être servi par le serveur (il faudra une méthode statique capable de le retrouver)
 - ...

C'est la méthode la plus simple mais demande d'écrire un code très critique

Paramétrer les restrictions, solution 2
---------------------------------------

En utilisant le `SecurityManager` par défaut, on utilise la système de politique de sécurité de Java

- Basé sur les permissions accordées au code
- Décrite par un fichier de politique de sécurity, représenté par la classe `Policy`

Les permissions sont accordées selon :

- L'emplacement de téléchargement du code `codeBase`
- La personne qui a signé le jar contenant le code `signedBy` ou `principal`
   - Les certificats doivent se trouver dans un keystore

Vérification du droit d'accès
-----------------------------

Quand la machine virtuelle vérifie un droit d'accès, elle vérifie que tout le code de la pile d'appel possède ce droit d'accès, depuis `main` jusqu'au code qui demande la vérification

On peut arrêter la remontée dans le pile d'appel avec `AccessControler.doPrivileged`

Problèmes
---------

Le problème de ce système est que les choses compliquées ne marche pas comme indiquées

On peut demander le debugging avec `-Djava.security.debug`

Pour ne pas avoir de difficultés, se contenté de :

- Faire dépendre les droits du `codeBase` : mettre les classes privilégiées dans un jar et le reste dans un autre
- Ne pas créer de nouvelles classes de permission

TP
==

Réaliser le tutoriel [sur les politiques de securité][pol]

[pol]: https://docs.oracle.com/javase/tutorial/security/userperm/index.html

