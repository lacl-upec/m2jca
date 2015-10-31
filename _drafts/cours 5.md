*Programmation sécurisée*
=========================

Signature
=========

Mécanisme de signature
----------------------

- Il permet de prouver que l'on est l'auteur d'un message
- L'auteur utilise une clef privée
- Les destinataires vérifient avec la clef publique associée
- Pour associer une identité à cette clef publique, elle est souvent incluse dans un certificat
- Le mécanisme est asymétrique, par opposition à l'authentification par *MAC*

En Java
-------

- L'objet associé est de type `Signature`

- Il s'utilise comme l'objet `Cipher` :

  - construction de l'objet

     `Signature sig = Signature.getInstance("`*algorithme*`")`
  
  - initialisation : génération de signature

     `sig.initSign(`*clef privée*`,`*secureRandom*`)` 
  
     ou vérification de signature
     
     `sig.initVerify(`*clef privée ou certificat*`)`
     
  - analyse du message par appels successifs à 

     `sig.update(`*données*`)`
     
  - obtention de la signature
  
     `byte[] signature = sig.sign()`
     
     ou vérification
     
     `sig.verify(`*la signature*`)`