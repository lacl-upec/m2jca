*Programmation sécurisée*
=========================

Stockage de clefs
=================

Les clefs ne peuvent rester seulement en mémoire :

- problème du transport entre deux applications

    - du secret partagé

    - d'une clef publique

- problème de conserver la même clef privée/partagée entre deux redémarrage d'un serveur

Problématiques
--------------

Il se pose deux problèmes pour effectuer cette tâche :

- définir un format précis afin qu'il soit portable d'un système à l'autre
- protéger les clefs stockées contre le vol

Formats de clef
---------------

- Les clefs privées sont en général stockées sous le format PKCS#8

- Les clefs publiques sont en général stockées au sein de *certificats* sous différents formats:

  - PKCS#7

  - DER ou PEM de la norme X.509

- Plusieurs objets cryptographiques peuvent être regroupés au sein de fichier

  - Au format JKS (format Java) ou PKCS#12

  - Peuvent aussi contenir des clefs symmétriques

  - Il est possible de chiffrer le fichier complet et pour jks chaque élément

La classe `KeyStore`
--------------------

Cette classe permet de charger, modifier et enregistrer des fichier PKCS#12 ou JKS

Créer avec `KeyStore.getInstance`, argument `jks` ou `pkcs12`.

> pkcs#12 est plus portable

Le keystore contient une liste d'éléments cryptographiques référencés par des *alias*
> Un alias est une chaîne de caractères

À un alias peut correspondre

- Un certificat
- Une clef privée avec un éventuel certificat
- Une clef symétrique

Les utilitaires `keytool` et `openssl`
--------------------------------------

Bien qu'il soit possible de générer des paires de clefs en Java, il n'est pas possible (sans bibliothèque externe) de générer un certificat associé ) la clef publique obligatoire pour l'ajout à un keystore !

Il faut donc créer la paire de clef avec un programme externe.

- Pour JKS

  - Il faut passer par `keytool`avec l'option `-genkeypair`

  - Penser à bien préciser l'alias avec `-alias` (par défaut `mykey`)
  
  - Le certificat associé à une clef a le même alias, il est généré auto-signé
  
  - Il est possible de le remplacer par un vrai certificat

     - Il faut d'abord extraire un CSR associé à la clef publique
     
     - Une fois signé, ce certificat peut être importé à la place de l'ancien

- Pour PKCS#12

  - On peut aussi utiliser `keytool` avec `-storetype pkcs12`

  - Ou alors des outils standard comme `openssl`

     - lors de la génération du PKCS#12, préciser `-noiter -nomaciter`
    
     - l'alias est donné avec `-name`

TP
--

 - Créer une paire de clefs avec `keytool` au format JKS et les charger dans un programme Java
 - Même question avec le format pkcs#12
 - Lire les informations du fichier obtenu avec la commande `openssl`
 - Créer cette fois-ci une paire de clefs avec `openssl` et les importer dans un programme Java
 - Obtenir un CSR à partir des informations contenus dans les trois keystore créés
 - Créer un CA et s'en servir pour signer les CSR
 - Importer les certificats obtenus avec les deux outils à votre disposition (`keytool` et `openssl`)