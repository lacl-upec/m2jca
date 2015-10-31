*Programmation sécurisée*
=========================

Signature
=========

Mécanisme de signature
----------------------

- il permet de prouver que l'on est l'auteur d'un message
- l'auteur utilise une clef privée
- les destinataires vérifient avec la clef publique associée
- pour associer une identité à cette clef publique, elle est souvent incluse dans un certificat
- le mécanisme est asymétrique, par opposition à l'authentification par *MAC*

En Java
-------

L'objet associé est des type `Signature`