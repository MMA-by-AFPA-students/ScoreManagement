# SCORE MANAGMENT

## description :

Projet de fin de formation, **"Score Managment"** est une application visant à permettre le partage de partitions de morceaux dans un ensemble musicale (chorale, orchestre, groupe de musiciens, etc...).
Un ensemble sera composé d'un administrateur (chef de choeur, chef de groupe), créateur de celui-ci, ainsi que des membres qui auront la possibilité d'interagir avec les morceaux proposés : offre de documentation, d'audios, de vidéos.

## Structure du projet :

- Diagramme : conceptualisation du projet au travers de diagrammes (cas d'usage, d'activité, de séquence) fonctionnels et techniques
- ... (back)
- ... (front)


# Déploiement

Plusieurs "deploy keys" ont du être utilisées pour pouvoir récupérer les dépôts Git en SSH sur le serveur.

Attention, sur la branche "deploy" l'URL des submodules a été changée pour avec des alias SSH.

**Sur le serveur**, une configuration SSH a été ajoutée, la voici (ce fichier est contenu dans le /home/<utilisateur>/.ssh) :
```
# Clé pour le dépôt principal
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/deploy-global
    IdentitiesOnly yes

# Clé pour le dépôt de la BDD
Host github.com-db
    HostName github.com
    User git
    IdentityFile ~/.ssh/deploy-db
    IdentitiesOnly yes

# Clé pour le dépôt du backend
Host github.com-back
    HostName github.com
    User git
    IdentityFile ~/.ssh/deploy-back
    IdentitiesOnly yes

# Clé pour le dépôt du frontend
Host github.com-front
    HostName github.com
    User git
    IdentityFile ~/.ssh/deplpy-front
    IdentitiesOnly yes
```

L'objectif de ce fichier est d'associer une clef SSH spécifique à chacune des URL choisie.

Par exemple, la commande `git clone git@github.com-back:MMA-by-AFPA-students/Backend_ScoreManagment.git` est un équivalent de `git@github.com:MMA-by-AFPA-students/Backend_ScoreManagment.git` mais en utilisant la clef SSH nommée `deploy-back`.

Il faut bien veiller à ce que toutes les clefs SSH existent dans le dossier `~/.ssh`.

Voici un exemple de commande pour créer une clef spécifique nommée "deploy-back" :
```sh
ssh-keygen -t ed25519 -C "deploy-back" -f ~/.ssh/deploy-back
```
