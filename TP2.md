# TP2 Cybersécurité
- Nom: (Aedan Ravet Smith)
- Matricule: (2475600)
- Groupe: (1020)

## Attaque (exploit)

Voici les étapes détaillées pour pouvoir modifier le courriel / téléphone sur la page demandée:

### Étape 1
Description de l'étape + capture d'écran

Un scan réseau est effectué à l'aide de Nmap depuis la machine Windows 11 afin d'identifier les ports TCP ouverts sur le serveur Ubuntu. Cette analyse permet de découvrir les services accessibles à distance et de déterminer les possibilités d'investigation. Commande utilisée: nmap 192.168.0.15

Résultat et info: Le scan permet de détecter le port TCP 80 ouvert sur le serveur Ubuntu. Ce port est utilisé par le service HTTP, qui permet aux utilisateurs d'accéder au site Web hébergé sur le serveur.

### Étape 2
Description de l'étape + capture d'écran

Le navigateur Web de la machine Windows 11 est utilisé pour accéder au site hébergé sur le serveur Ubuntu. Cette étape permet de vérifier que le serveur Web est accessible à distance et que la page Contact peut être consultée. Adresse utilisée: http://192.168.0.15/contact.html

Résultat et info: La page Contact est accessible. Le numéro de téléphone affiché est 555-555-1234. Le lien de contact par courriel est également présent sur la page et est identifié par le texte « ici »

### Étape 3
Description de l'étape + capture d'écran

Une analyse supplémentaire est effectuée afin de vérifier l'état des ports 22 et 80 du serveur Ubuntu. Cette étape permet de déterminer si le service SSH est accessible à distance et de confirmer la disponibilité du service HTTP.

### ....

## Correctif 1

Commandes à effectuer ou étapes à mettre en place. 

Capture d'écran de l'exploit qui ne fonctionne plus.

## Correctif 2

Commandes à effectuer ou étapes à mettre en place.

Capture d'écran de l'exploit qui ne fonctionne plus. 

## Correctif 3

Commandes à effectuer ou étapes à mettre en place. 

Capture d'écran de l'exploit qui ne fonctionne plus.
