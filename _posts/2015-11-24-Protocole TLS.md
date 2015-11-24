*Programmation sécurisée*
=========================

Protocole TLS
=============

Utilisation du protocole
------------------------

Le protocol TLS, anciennement appelé TLS, permet de :

- Assurer la confidentialité d'une connexion réseau 
- Assurer l'authenticité du serveur et optionnellement du client 
- Permettre les re-connexions rapides avec un mécanisme de « *session id* » ou de « *session ticket* »

N'importe quel service TCP peut être « sécurisé » en ajoutant la couche TLS

Cryptographie
-------------

Pour cela, le protocoles cryptographiques mis en jeu sont :

- Signature avec certificats et échange de clef
   - Peut utiliser le même protocole (RSA) ou deux différents (ECDHE-ECDSA)
   - Certaines paires assure la *forward secrecy* (i.e. la compromission d'une clef n'impacte pas les connexions futures ou enregistrées)
- Un algorithme de chiffrement symétrique et un *block cipher mode* (en général le premier est AES)
- Un fonction pseudo-aléatoire

L'ensemble des algorithmes cryptographiques utilisés ainsi que la taille des clef AES se nomme *cipher suite* (Ex: `TLS_ECDH_ECDSA_WITH_AES_256_GCM_SHA384`)

Handshake
---------

- Les parties commencent par décider la version de SSL/TLS et la *cipher suite* à utiliser (c'est le serveur qui choisit parmi ce que propose le client)
- Ensuite, elles s'authentifient avec des certificats et génèrent un secret partagé, à moins d'utiliser un *session id*/*ticket*
- Enfin, elles échangent des messages chiffrés en commençant par vérifiet que tout est en ordre à l'aide de MAC
- Le protocole prévoit la possibilité de refaire un handshake, notamment pour changer la clef ou augmenter la complexité cryptographique

SNI
---

- Avec les `VirtualHost` d'Apache, il est nécessaire de choisir le certificat en fonction du nom de serveur
- SNI est une extension de TLS qui permet au client d'indiquer avant l'échange de certificat le nom (FQDN) du serveur auquel il s'attend à parler

Variantes d'utilisation
-----------------------

- TLS peut être utilisé directement (https ou smtps sur le port 465 par exemple) en démarrant la négociation TLS dès la connexion
- Ou alors la connexion peut commencer non chiffrée puis, à l'issue d'une commande spéciale (en général `StartTLS` pour les protocoles textuels) le handshake commence (smtps sur le port 587 par exemple)

En Java
-------

En Java, il y a deux possibilités pour TLS :

- Celle qui correspond à `java.io` : `TLSSocketFactory` pour la connexion d'un clinet et `TLSServerSocketFactory` pour la réalisation d'un serveur
   - créées soit à partir de leurs méthode `getDefault` soit à partir d'un `SSLContext`
   - cette méthode ne permet pas de mode non bloquant
- Celle qui correspond à `java.nio` : `SSLEngine` qui demande de s'occuper de l'envoi et la réception des données
   - créé à partir d'un `SSLContext`
   - permet le mode non bloquant
- Pour configurer les clefs et les certificats, il faut passer par la méthode `init` de `SSLContext`
   - les clefs proviennent d'un `keystore` lu par un `KeyManagerFactory`
   - les CA proviennent aussi d'un `keystore` lu par un `TrustManagerFactory`  

Configuration
-------------

La configuration des algorithmes utilisés sont paramétrés par une instance de `SSLParameters` qui indique :

- Les algorithmes supportés
- Les *cipher suites* supportées
- S'il faut authentifier le client
- Les noms disponibles pour SNI
- La puissance des clefs (`AlgorithmConstraints`)
