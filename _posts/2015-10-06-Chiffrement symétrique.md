*Programmation sécurisée avec Java*
===================================

Chiffrement symétrique
======================

Principe
--------

- Chiffer et de déchiffer des messages avec la même clef (`SecretKey`).
- Chiffrement de message de longueur fixe
  - Donnée par l'algorithme de chiffrement
  - Pour chiffrer des messages plus long, il faut un « mode de chiffrement des blocs »

Mode de chiffrement des blocs (block cipher mode)
-------------------------------------------------

Passage d'un algorithme de chiffrement de messages de taille fixée à un chiffrement de flux.

- Le message est découpé en bloc
- Le bloc (clair et/ou chiffré) n°*i* est utilisé pour chiffrer le bloc n°*i+1*
  - Nécessite de spécifier un vecteur d'initialisation *initialization vector*, (*IV*) pour le chiffrement du flux
  - Ce vecteur peut être transmis en clair
  - Il doit être tiré au hasard (`SecureRandom`)
     - Entièrement
     - ou en grande partie avec concaténation du numéro de paquet
  - Un même IV ne doit être utilisé qu'une seule fois

Exemples de modes de chiffrement
-------------------------------

- Celui à **NE PAS** utiliser : *ECB* (*electronic codebook*)

  - Chaque bloc est chiffré sans « perturbation ».

- Le mode *PCBC* (*propagating cipher bloc chaining*)

  ![Schema chiffrement PCBC][pcbc-e]

  ![Schema déchiffrement PCBC][pcbc-d]

- Le mode *CTR* (*counter mode*)

  ![Schema chiffrement CTR][ctr-e]

  ![Schema déchiffrement CTR][ctr-d]

[pcbc-e]: /m2jca/img/PCBC_encryption.svg
[pcbc-d]: /m2jca/img/PCBC_decryption.svg
[ctr-e]: /m2jca/img/CTR_encryption_2.svg
[ctr-d]: /m2jca/img/CTR_decryption_2.svg

Images: source wikipedia.

En ce moment, le mode le plus « à la mode » est *GCM*, (*Gallois counter mode*)

-  Il combine le système de *CTR* avec un *tag* d'authentification (sorte de MAC)
-  En Java, avec le *provider* standard, il est ajouté à la fin de message chiffré et vérifié au déchiffrement !

Padding
-------

Certains mode nécessite que la taille du message soit multiple de la taille des blocs :

- Les modes qui appliquent la fonction de chiffrement à des blocs messages (CBC, ECB) en ont besoin
- Les modes qui appliquent un xor de la sortie de la fonction de chiffrement au message n'en ont pas besoin (OFB, CTR, GCM)

Spécification de chiffrement
----------------------------

- Trois ou deux algorithmes:
  - L'algorithme de chiffrement (AES, triple DES...)
  - Le mode de chiffrement des blocs (CTR, GCM)
  - Si nécessaire, l'algorithme de padding (inutile pour les modes modernes)
- La clef
- L'IV avec, pour GCM, en plus la taille du tag
  - La taille minimum du tag dépend de la taille du message
  
Avec le code
------------

- Instantiation du `Cipher`

   `String method = "`*Algo*`/`*BlocMode*`/`*Padding*`";`
  
      - Padding vaut `NoPadding`quand le padding n'est pas nécessaire
      
      - Par exemple : `AES/GCM/NoPadding`
  
   `Cipher cipher = Cipher.getInstance(method);`
  
- Instantiation du conteneur d'IV

   `IvParameterSpec spec = new IvParameterSpec(`*le tableau*`);`
  
      - La taille de l'IV dépend du protocole, pour AES, égal à la taille de la clef
    
      - Pour GCM, il faut aussi la taille de tag d'authentification
  
   `GCMParameterSpec spec = new GCMParameterSpec(`*la taille*`,`*le tableau*`);`

- Initialisation du chiffreur

   `cipher.init(Cipher.ENCRYPT_MODE, `*clef*`, spec, random);`
  
   ou du déchiffreur

   `cipher.init(Cipher.DECRYPT_MODE, `*clef*`, spec, random);`

- Le chiffrement ou déchiffrement s'effectue par une suite d'appel à l'une des méthode `update` terminé par un appel à `doFinal`

- Il est aussi possible d'authentifier des données transmises en clair avec `updateAAD` pour certains algorithmes (c'est le cas de AES/GCM).

TP
==

Écrire une programme qui chiffre et déchiffre un fichiers en AES256/GCM. La clef sera générée à partir d'un mot de passe et du protocole PBKDF#2 utilisant l'une des fonctions SHA.
