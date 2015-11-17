*Programmation sécurisée*
=========================

Certificats X.509
=================

Rappels
-------

- Il permet de prouver son identité et de signer
- Il contient :
  - une clef publique (dont on a la clef privée)
  - des informations sur notre identité
  - il est signé par une autorité qui atteste que le possesseur de la clef privée est l'identité présente dans le certificat
- L'autorité est elle-même identifiée par un certificat
- En général, on a une chaîne de trois certificats
  - l'autorité de certification (CA, `TrustAnchor`), auto-signée, connue de la terre entière
  - un certificat intermédiaire, signé par la CA
  - le certificat signé par le certificat intermédiaire

Chaînes de certificats
----------------------

- Représentés par l'objet `CertPath`

- Créés comme les certificats par une `CertificateFactory`

- Il faut vérifier la validité :

  - Que chaque élément signe le suivant

  - Que le CA est valide

- Pour cela, on utilise un `CertPathValidator`

         CertPathValidator cpv = CertPathValidator.getInstance("PKIX");

  il faut construire un objet `PKIXParameters` avec la liste des CA :
  
         PKIXParameters params = new PKIXParameters(ca);
  
  si on souhaire vérifier la revocation (CRL ou OSCP)

         PKIXRevocationChecker rc = (PKIXRevocationChecker)cpv.getRevocationChecker();
         params.addCertPathChecker(rc);
         
  on demande la vérification
         
         CertPathValidatorResult cpvr = cpv.validate(path, params);
         
Certificats racine
==================

Chaque JVM vient avec sa liste de certificats racine, présent dans un *keystore* de mot de passe `changeit` dans

> `/`*path to JRE*`/lib/security/cacerts`

Ça n'est donc pas le même que la liste du système

Sur MacOS, c'est le fichier `/System/Library/Keychains/SystemRootCertificates.keychain` (à manipuler soit avec le *Trousseau* soit avec la commande `security`)

Sous Linux, ils sont *souvent* dans `/etc/ssl/certs`

sous Windows, on peut y accéder avec la commande `certmgr.msc`

TP
==

Ajouter la vérification des certificats dans l'exercice précédent

