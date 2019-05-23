# Heroku 4 AncGIS
Vm pour le déploiement de AncGIS sur la plateforme Heroku

## Mode opératoire

#### 1- Créer/lancer la machine virtuelle
```
vagrant up
```

#### 2- Synchroniser les fichiers
Dans une autre console (à laisser en arrière-plan) lancer la commande suivante:
```
vagrant rsync-auto
```

#### 3- Création de l'application via heroku
Cela ne doit être fait qu'une seule fois dans la vie de l'application.
```
vagrant provision --provision-with create
```

#### 4- Mise à jour des sources
```
vagrant provision --provision-with update-src
```

#### 5- Configurer les variables d'environnement (fichier .env)
- Créer des fichiers .env à partir des fichiers .env.dist dans les répertoires suivants (ou vérifier qu'ils soient à jour):
  - ~/ancgis/application/
  - ~/ancgis/shell/
- Editer le fichier "~/ancgis/.gitignore" et supprimer la ligne suivante:
  - /application/.env
- Commiter le fichier sur la branche master afin de le pousser dans le repository de heroku:
```
cd ~/ancgis
git add application/.env
git status
git commit -am "fichier .env"
```

#### 6- Tester l'application localement
```
vagrant provision --provision-with test
```

#### 7- Déploiement de l'application sur Heroku
```
vagrant provision --provision-with deploy
```

#### 8- Provisionnement de la base de données
```
vagrant provision --provision-with populate-db
```

Note:
- La base de donnée a été créée par la commande ```heroku addons:create mongolab:sandbox -a ancgis```,
- La base de donnée peut être consultée en ligne via un dba sur le site de mlab.com (lien disponible sur le site d'heroku),
- Une version de mongo supérieure à la version 3 est requise.

## Déboguer

#### Se connecter à la machine virtuelle

```
vagrant ssh
```
```
ssh -x vagrant@192.168.50.25
```

#### Consulter les logs
```
heroku logs --tail -a ancgis
```

#### Effacer le dépôt git de heroku
```
heroku repo:reset
```
