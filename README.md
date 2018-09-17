# Heroku 4 AncGIS
Vm pour le déploiement de AncGIS sur la plateforme Heroku

## Exemple de création de l'application

#### Récupération des sources
```
cd ~
git clone https://github.com/sgalopin/ancgis.git
```

#### Création de l'application
```
cd ancgis/application
heroku login # Vous devez créer un compte sur heroku avant de jouer cette commande
heroku create ancgis
heroku addons:create mongolab:sandbox -a ancgis
```

#### Déploiement de l'application sur Heroku
```
git remote add heroku https://git.heroku.com/ancgis.git # Si ce n'est pas fait par la commande create
git subtree push --prefix application heroku master
heroku ps:scale web=1 # Active un seul dyno gratuit
heroku open -a ancgis # Ouvre le navigateur à l'adresse https://ancgis.herokuapp.com
```

## Exemple de re-déploiement de l'application

Prérequis:
- L'application a déjà été créée et déployée via la VM.

#### Récupération des sources
```
cd ~/ancgis
git fetch origin
git pull
```

#### Déploiement de l'application sur Heroku
```
git subtree push --prefix application heroku master
heroku open -a ancgis # Ouvre le navigateur à l'adresse https://ancgis.herokuapp.com
```

## Exemple de provisionnement de la base de donnée
```
cd ancgis/application
source .env # Set the env variables
cd ancgis/database
/bin/bash ../shell/populate-db.sh
```

Note:
- La base de donnée a été créée par la commande ```heroku addons:create mongolab:sandbox -a ancgis```,
- La base de donnée peut être consultée en ligne via un dba sur le site de mlab.com (lien disponible sur le site d'heroku),
- Une version de mongo supérieure à la version 3 est requise.

## Exemple pour tester l'application localement
```
cd ancgis/application
npm install
source .env # Set the env variables
heroku local web
```

## Exemple de fichier .env
```
export MONGODB_URI="..."
```
