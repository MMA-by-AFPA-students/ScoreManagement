# SCORE MANAGMENT

## description :

Projet de fin de formation, **"Score Managment"** est une application visant à permettre le partage de partitions de morceaux dans un ensemble musicale (chorale, orchestre, groupe de musiciens, etc...).
Un ensemble sera composé d'un administrateur (chef de choeur, chef de groupe), créateur de celui-ci, ainsi que des membres qui auront la possibilité d'interagir avec les morceaux proposés : offre de documentation, d'audios, de vidéos.

## Structure du projet :

- Diagramme : conceptualisation du projet au travers de diagrammes (cas d'usage, d'activité, de séquence) fonctionnels et techniques
- ... (back)
- ... (front)


# Déploiement

## Mise en place des deploy key

Plusieurs "deploy keys" ont du être utilisées pour pouvoir récupérer les dépôts Git en SSH sur le serveur.

Voici, par exemple, l'ensemble des "deploy keys" créées pour un projet avec 3 submodules :
```sh
ubuntu@vps-d1e1d10a:~/.ssh$ ls -al
total 52
drwx------ 2 ubuntu ubuntu 4096 Oct 31 12:24 .
drwxr-x--- 7 ubuntu ubuntu 4096 Oct 31 12:27 ..
-rw------- 1 ubuntu ubuntu    0 Oct 27 06:26 authorized_keys
-rw-rw-r-- 1 ubuntu ubuntu  602 Oct 31 12:24 config
-rw------- 1 ubuntu ubuntu  399 Oct 31 11:45 deploy-back
-rw-rw-r-- 1 ubuntu ubuntu   93 Oct 31 11:45 deploy-back.pub
-rw------- 1 ubuntu ubuntu  399 Oct 31 11:48 deploy-db
-rw-rw-r-- 1 ubuntu ubuntu   91 Oct 31 11:48 deploy-db.pub
-rw------- 1 ubuntu ubuntu  399 Oct 31 11:46 deploy-front
-rw-rw-r-- 1 ubuntu ubuntu   94 Oct 31 11:46 deploy-front.pub
-rw------- 1 ubuntu ubuntu  399 Oct 31 11:47 deploy-global
-rw-rw-r-- 1 ubuntu ubuntu   95 Oct 31 11:47 deploy-global.pub
-rw------- 1 ubuntu ubuntu  978 Oct 31 12:23 known_hosts
-rw-r--r-- 1 ubuntu ubuntu  142 Oct 31 12:21 known_hosts.old
```

Voici un exemple de commande pour créer une clef spécifique nommée "deploy-back" :
```sh
ssh-keygen -t ed25519 -C "deploy-back" -f ~/.ssh/deploy-back
```

L'idée est de pouvoir utiliser chacune des ces clefs pour chaque submodule (nécessaire car il est impossible d'utiliser plusiers fois la même clef SSH).

Pour que cela fonctionne il faut modifier deux choses :
- la configuration SSH du serveur ;
- la configuration du fichier `.gitsubmodules` (attention de ne le faire que sur la branche prévue pour le déploiement).

## Configuration SSH serveur

**Sur le serveur**, une configuration SSH doit être ajoutée, la voici (ce fichier est contenu dans le /home/nom-utilisateur/.ssh) :
```conf
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

Ainsi, il faut changer sur la branche **"deploy"** l'URL des submodules dans le fichier `.gitmodules`, voici le nouveau fichier (attention de ne l'avoir que sur la branche "deploy" au risque de casser le "clone" habituel) :

```conf
[submodule "Backend_ScoreManagment"]
	path = Backend_ScoreManagment
	url = git@github.com-back:MMA-by-AFPA-students/Backend_ScoreManagment.git
[submodule "Front_ScoreManagment"]
	path = Front_ScoreManagment
	url = git@github.com-front:MMA-by-AFPA-students/Front_ScoreManagment.git
[submodule "db_scoremanagment"]
	path = db_scoremanagment
	url = git@github.com-db:MMA-by-AFPA-students/db_scoremanagment.git
```

Il faut **bien veiller** à ce que toutes les clefs SSH existent dans le dossier `~/.ssh` (cf. partie précédente).

## Procédure de clone

Une fois la configuration SSH en place suivre la procédure suivante pour clone sur le serveur :

1. Clone du dépôt global en SSH :
```sh
git clone git@github.com:MMA-by-AFPA-students/ScoreManagement.git
```

2. Positionnement dans le dossier cloné :
```sh
cd ScoreManagement
```

3. Changement de branche dans le dépôt global pour obtenir le fichier `.gitsubmodules` modifié avec la nouvelle configuration SSH :
```sh
git switch deploy
```

4. Synchronisation des submodules :
```sh
git submodule sync
```

5. Mise à jour des submodules, si le SSH est correctement configuré les projets seront récupérés :
```sh
git submodule update --init --recursive
```

6. Se positionner dans chacun des submodules et **bien changer de branche** (par défaut Git est en "tête détachée", c'est à dire que nous ne sommes pas sur une branche) :
```sh
cd <dossier-submodule>
git switch <nom-branche-souhaitée>
```