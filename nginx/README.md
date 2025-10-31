# Nginx reverse proxuy

Ce dossier contient les fichiers de configuration d'un "reverse proxy" Nginx.

C'est le "reverse proxy" qui va être responsable du chiffrement des requêtes entre le client et le serveur.

Il y a donc, dans ce dossier, des fichiers conrrespondant à un certificat TSL.

Il vous sera nécessaire de recréer un certificat (attention, celui-ci sera auto-signé) via la commande suivante :
```sh
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout nginx-selfsigned.key -out nginx-selfsigned.crt
```

Pour une mise en production réelle il nous faudrait faire appel à une authorité de certification légitime telle que Let's Encrypt pour obtenir un certificat.
