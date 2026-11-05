TP2 Cybersécurité

Nom : Aedan Ravet Smith

Matricule : 2475600

Groupe : 1020

Attaque (exploit)

Voici les étapes détaillées réalisées afin de modifier le numéro de téléphone et l'adresse courriel sur la page Contact du site Web hébergé sur le serveur Ubuntu 192.168.0.15.

Étape 1 – Découverte des ports ouverts avec Nmap

Un scan réseau est effectué à l'aide de Nmap depuis la machine Windows 11 afin d'identifier les ports TCP ouverts sur le serveur Ubuntu.

Commande utilisée :

nmap 192.168.0.15

Objectif et résultat :

Le scan permet de détecter le port TCP 80 ouvert sur le serveur Ubuntu. Ce port est utilisé par le service HTTP, qui permet aux utilisateurs d'accéder au site Web hébergé sur le serveur.

Cette découverte confirme que le serveur Web est accessible à distance.

Capture d'écran :

![Résultat du scan Nmap de base](./assets/Capture2.png)



Étape 2 – Accès à la page Contact

Le navigateur Web de la machine Windows 11 est utilisé pour accéder au site hébergé sur le serveur Ubuntu.

Adresse utilisée :

http://192.168.0.15/contact.html

Objectif et résultat :

La page Contact est accessible. Le numéro de téléphone affiché est 555-555-1234.

Le lien de contact par courriel est également présent sur la page et est identifié par le texte « ici ».

Cette étape permet de confirmer que la page à modifier est accessible depuis une autre machine du réseau.

Capture d'écran :

![Page Contact initiale dans le navigateur](./assets/Capture3.png)



Étape 3 – Analyse du service SSH

Une analyse supplémentaire est effectuée afin de vérifier l'état des ports 22 et 80 du serveur Ubuntu.

Commande utilisée :

nmap -p 22,80 192.168.0.15

Objectif et résultat :

Le scan Nmap indique que le port TCP 22 est ouvert et qu'il héberge le service SSH. Le port 80 est également accessible pour le service HTTP.

Le service SSH représente une possibilité d'accès à distance au système Linux.

Une première tentative de connexion est effectuée avec le compte bob.

Commande utilisée depuis Windows :

ssh bob@192.168.0.15

L'authentification est refusée avec le message « Access denied ».

Cette tentative démontre que le service SSH est accessible, mais que les informations d'authentification utilisées lors de cette première tentative ne permettent pas d'ouvrir une session.

Capture d'écran :

![Analyse des ports avec Nmap -p 22,80](./assets/Capture1 (2).png)



Étape 4 – Exploitation du mot de passe compromis

Les informations recueillies sur Bob Smith indiquent que son adresse courriel personnelle a été trouvée dans une fuite de données.

Le mot de passe associé à cette fuite a été retrouvé à partir de son hash MD5 :

Utilisateur : bob

Hash MD5 : 57e788f9ceb6565e39d23061d7b3d8ca

Mot de passe compromis : Kim2012!

Cette information permet de tenter une nouvelle authentification SSH avec les identifiants compromis.

Commande utilisée depuis Windows :

ssh bob@192.168.0.15

Le mot de passe compromis est ensuite saisi lorsque le système le demande.

Objectif :

Vérifier si le mot de passe découvert dans la fuite de données est également utilisé pour le compte Linux de Bob.

Résultat :

Connexion réussie avec le compte bob grâce au mot de passe compromis.

Capture d'écran :

![Connexion SSH réussie avec le mot de passe compromis](./assets/Capture4.png)



Étape 5 – Vérification des permissions du fichier Contact

Une fois la connexion SSH établie, les permissions du fichier de la page Contact sont vérifiées.

Commandes utilisées :

whoami
ls -l /var/www/html/
ls -l /var/www/html/contact.html

Objectif et résultat :

La commande whoami permet de confirmer le compte actuellement utilisé.

La commande ls -l permet d'examiner les permissions du répertoire Web et du fichier contact.html.

Cette vérification permet de déterminer si le compte Bob possède les droits nécessaires pour modifier le contenu de la page Web.

Capture d'écran :

![Vérification des permissions des fichiers HTML](./assets/Capture5.png)



Étape 6 – Modification de la page Contact

Si le compte Bob possède les permissions d'écriture sur le fichier, celui-ci peut être modifié directement depuis la session SSH.

Commande utilisée :

nano /var/www/html/contact.html

Le contenu de la page est modifié afin de remplacer les informations originales.

Les nouvelles informations demandées sont :

Ancien numéro : 555-555-1234

Nouveau numéro : 555-555-9876

Nouvelle adresse courriel : ouch.hacked@cem.ca

Le lien de contact doit également être modifié pour pointer vers la nouvelle adresse.

Exemple de modification HTML :

<a href="mailto:ouch.hacked@cem.ca">ici</a>

Objectif et résultat :

Cette étape permet de modifier les coordonnées affichées sur la page Contact du site Web.

Capture d'écran :

![Modification du fichier HTML source](./assets/Capture6.png)



Étape 7 – Vérification de la modification

Le navigateur Web de Windows 11 est utilisé une nouvelle fois pour consulter la page Contact.

Adresse utilisée :

http://192.168.0.15/contact.html

Objectif et résultat :

La page est actualisée afin de vérifier que les modifications ont été appliquées.

Le nouveau numéro de téléphone doit être 555-555-9876 et le lien de contact doit pointer vers ouch.hacked@cem.ca.

Cette étape confirme la réussite de l'attaque si les deux modifications sont visibles.

Capture d'écran :

![Page Contact modifiée avec succès dans le navigateur](./assets/CaptureDSA.jfif)



Correctif 1 – Bloquer l'accès SSH avec le pare-feu

Objectif :

Le premier correctif consiste à bloquer les connexions SSH provenant du réseau afin d'empêcher les utilisateurs distants d'accéder au serveur Linux par le port 22.

Le service HTTP doit cependant rester accessible afin que les visiteurs puissent continuer à consulter le site Web.

Commandes à effectuer sur Ubuntu avec le compte DaBoss :

Autoriser le trafic HTTP :

sudo ufw allow 80/tcp

Bloquer le trafic SSH :

sudo ufw deny 22/tcp

Activer le pare-feu si nécessaire :

sudo ufw enable

Vérifier les règles :

sudo ufw status verbose

Explication :

La règle autorisant le port 80 permet de conserver l'accès au site Web.

La règle bloquant le port 22 empêche les connexions SSH provenant du réseau, ce qui bloque la méthode utilisée pour accéder au système et modifier les fichiers du site.

Étape de l'exploit bloquée :

Ce correctif empêche les étapes 3 et 4 de fonctionner, puisque le port SSH n'est plus accessible depuis la machine Windows.

Vérification de l'exploit :

Depuis Windows 11, exécuter :

nmap -p 22,80 192.168.0.15

Le résultat attendu est que le port 22 soit filtré ou fermé et que le port 80 demeure accessible.

Tenter ensuite :

ssh bob@192.168.0.15

La connexion SSH ne doit plus pouvoir être établie.

Captures d'écran à insérer :

![Vérification du blocage du port SSH par pare-feu](./assets/Capture7.png)



Impact sur les fonctionnalités :

L'accès SSH à distance est bloqué. Cela empêche l'administration du serveur par SSH depuis le réseau, mais le site Web demeure accessible par HTTP.

Correctif 2 – Modifier les permissions des fichiers du site Web

Objectif :

Le deuxième correctif consiste à empêcher les comptes utilisateurs ordinaires, comme Bob, de modifier les fichiers du site Web.

Les fichiers doivent appartenir à l'administrateur du système et ne doivent pas être modifiables par les utilisateurs non autorisés.

Commandes à effectuer sur Ubuntu avec le compte DaBoss :

Attribuer la propriété des fichiers à root :

sudo chown -R root:root /var/www/html

Définir les permissions des répertoires :

sudo find /var/www/html -type d -exec chmod 755 {} \;

Définir les permissions des fichiers :

sudo find /var/www/html -type f -exec chmod 644 {} \;

Vérifier les permissions :

ls -l /var/www/html/

Explication :

La commande chown attribue la propriété des fichiers à l'administrateur root.

La permission 755 permet aux utilisateurs de consulter les répertoires et de les parcourir, mais seuls les propriétaires peuvent les modifier.

La permission 644 permet aux utilisateurs de lire les fichiers HTML, mais seul le propriétaire peut les modifier.

Ainsi, Bob peut toujours consulter le site Web, mais il ne peut plus modifier les fichiers qui le composent.

Étape de l'exploit bloquée :

Ce correctif bloque les étapes 5 et 6, car le compte Bob ne possède plus les permissions d'écriture nécessaires pour modifier contact.html.

Vérification de l'exploit :

Vérifier les permissions du fichier :

ls -l /var/www/html/contact.html

Tester ensuite les droits d'écriture avec le compte Bob :

sudo -u bob test -w /var/www/html/contact.html
echo $?

Le résultat attendu est 1, ce qui signifie que Bob ne possède pas les droits d'écriture sur le fichier.

Le site Web doit toutefois demeurer accessible depuis Windows 11 à l'adresse :

http://192.168.0.15/contact.html

Captures d'écran à insérer :

Oublier de prendre une capture



Impact sur les fonctionnalités :

Les utilisateurs peuvent toujours consulter le site Web normalement. Cependant, les utilisateurs ordinaires ne peuvent plus modifier directement les fichiers HTML. Les modifications doivent être effectuées par un administrateur autorisé.

Correctif 3 – Sécuriser les mots de passe et l'authentification SSH

Objectif :

Le troisième correctif consiste à empêcher l'utilisation d'un mot de passe compromis pour accéder au serveur.

Un mot de passe qui a été exposé dans une fuite de données ne doit plus être accepté. De plus, l'authentification SSH par mot de passe peut être désactivée afin de privilégier l'utilisation de clés cryptographiques.

Commandes à effectuer sur Ubuntu avec le compte DaBoss :

Changer le mot de passe du compte Bob :

sudo passwd bob

Choisir un nouveau mot de passe fort, unique et différent de celui qui a été compromis.

Créer un fichier de configuration SSH :

sudo nano /etc/ssh/sshd_config.d/99-hardening.conf

Ajouter les paramètres suivants :

PasswordAuthentication no
KbdInteractiveAuthentication no

Enregistrer le fichier et vérifier la configuration SSH :

sudo sshd -t

Si aucune erreur n'est affichée, recharger le service SSH :

sudo systemctl reload ssh

Vérifier les paramètres appliqués :

sudo sshd -T | grep -E 'passwordauthentication|kbdinteractiveauthentication'

Explication :

La commande passwd permet de remplacer le mot de passe compromis du compte Bob.

La configuration SSH désactive l'authentification par mot de passe et par clavier interactif.

Les connexions SSH doivent donc utiliser une méthode d'authentification plus sécurisée, comme une clé SSH autorisée.

Étape de l'exploit bloquée :

Ce correctif bloque l'étape 4, car le mot de passe compromis ne plus être utilisé pour ouvrir une session SSH par authentification avec mot de passe.

Vérification de l'exploit :

Depuis Windows 11, tenter une nouvelle connexion :

ssh bob@192.168.0.15

Le résultat attendu est que l'authentification par mot de passe soit refusée.

Il est également possible de vérifier les paramètres SSH sur Ubuntu avec :

sudo sshd -T | grep passwordauthentication

Le résultat attendu est :

passwordauthentication no

Vérifier ensuite que le site Web est toujours accessible :

http://192.168.0.15/contact.html

Captures d'écran à insérer :

Oublier de prendre une capture 

Impact sur les fonctionnalités :

L'authentification SSH par mot de passe n'est plus disponible. Les administrateurs doivent utiliser des clés SSH pour se connecter à distance. Le service HTTP et l'accès au site Web restent fonctionnels.

Conclusion

Ce travail m'a permis de comprendre comment un serveur Web peut être vulnérable lorsque des services réseau sont accessibles, que des identifiants sont compromis et que les permissions des fichiers ne sont pas correctement configurées.

L'utilisation de Nmap a permis de découvrir les services accessibles sur le serveur Ubuntu. L'analyse des informations disponibles sur Bob Smith a ensuite permis d'identifier un risque lié à la réutilisation d'un mot de passe compromis.

Les trois correctifs proposés permettent de renforcer la sécurité du serveur de différentes façons :

Le pare-feu limite les accès réseau non autorisés.

Les permissions des fichiers empêchent les utilisateurs ordinaires de modifier le site Web.

La sécurisation des mots de passe et de SSH empêche l'utilisation d'identifiants compromis.

Ces mesures permettent de réduire les risques d'intrusion tout en conservant le fonctionnement du site Web.
