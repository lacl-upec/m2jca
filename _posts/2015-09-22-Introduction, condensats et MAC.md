*Programmation sécurisée avec Java*
===================================

Introduction
============

But du cours
------------

Programmer des applications sécurisées en Java

 - Confidentialité des données
 - Confidentialité des transmissions
 - Authentification des parties

Contenu
-------

Outils cryptographiques:

 - Les chiffrement symétriques
 - Les signatures
 - Les codes d'authentifications de message
 - Les condensats

Mais aussi:

 - Les `ServiceProvider`s
 - Le protocole TLS
 - Les certificats X.509

Pré-requis
----------

- Maîtrise du langage Java
- Maîtrise des procédés de cryptographie

Disclaimer
----------

**Ne rien faire en API de cryptographie sans comprendre la totalité des mécanismes mis en jeu !**


Service Provider Interface
==========================

Description
-----------

Utiliser pour les technologies:

- à évolution rapide
- à implémentations multiples

Système développé en Java pour permettre à quiconque de fournir une implémentation aux API standard de Java

 - Bases de données
 - Systèmes de fichier
 - Annuaires
 - Cryptographie
 - Codec sons, images et vidéo
 - Codages de caractères

Fonctionnement
--------------

Il *suffit* de:

- Implémenter un ensemble d'interfaces Java
- Déclarer l'emplacement de la *façade*
- Faire un `jar`

Conséquences
------------

Système de greffons (*plugin*) donc

 - Documentation vague
   - censée être valable pour toute implémentation
 - Pas de constructeurs
   - on utilise des *factory method*

Architecture de cryptographie Java
==================================
La page de description officielle de l'API du JCA se trouve [ici][jca]

- Le *JCA* contient des classes fondamentales pour utiliser de la cryptographie en Java
- Parmi les concepts on trouve :
  - Du chiffrement symétrique (secret partagé)
  - De l'authentification de message
  - Du chiffrement asymétrique
  - Du condensat de message (*digest*)
  - De la signature
- Certains algorithmes sont disponibles dans le Java standard
- Les autres sont à installer comme SPI
  - Les algorithmes disponibles sont disponible [cette adresse][algo]

[jca]: http://docs.oracle.com/javase/8/docs/technotes/guides/security/crypto/CryptoSpec.html
[algo]: http://docs.oracle.com/javase/8/docs/technotes/guides/security/StandardNames.html

Calcul de condensat
-------------------

- Réalisé par la classe `MessageDigest`

Algorithmes standard du JCA:

 - MD5 (cassé)
 - SHA1
 - SHA2 (différentes variantes de taille de condensat)

Utilisation :

 - Créer un `MessageDigest` :

   `MessageDigest digest = MessageDigest.getInstance("`*Algorithme*`");`
    
 - Lui fournir les octets à condenser avec plusieurs appels à :

   `digest.update(`*données sous for de byte[] ou de ByteBuffer*`);`

 - Calculer le condensat avec :

   `byte[] digest = digest.digest();` 

 - L'objet `digest` peut être utilisé de cette façon plusieurs fois de suite.

Calcul de Code d'authentification de message (MAC)
--------------------------------------------------

- Réalisé par le classe `Mac`

Algorithmiques standard du JCS:

 - Hmac avec les différentes variate de SHA

Utilisation :

 - Créer un `Mac` :

   `Mac mac = Mac.getInstance("`*Algorithme*`");`

 - Lui fournir une clef :

   `mac.init(key)`
 
 - Lui fournir les octets à condenser avec plusieurs appels à:

   `mac.update(`*données sous for de byte[] ou de ByteBuffer*`);`

 - Calculer le mac avec:

   `byte[] theMac = mac.doFinal();`

Nous verrons plus généralement les clefs plus tard.

TP
--

- Écrire un programme qui calcule le condensat SHA512 d'un fichier

- Écrire un programme qui calcule le Mac d'un fichier, avec Hmac et SHA512 comme algorithme. La clef sera générée à partir d'un mot de passe avec l'algorithme PBKDF2.
