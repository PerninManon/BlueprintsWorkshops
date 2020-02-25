# Blueprints

Dans ce workshop vous verrez comment utiliser définir et appliquer des blueprints via le portail Azure et via PowerShell. 

- [Prérequis](#prérequis)
- [Portail Azure](#portail-azure)
- [Powershell](#powershell)

## Prérequis

Pour réaliser ce workshop, vous aurez besoin:
- d'une souscription Azure sur laquelle vous êtes Owner

## Portail Azure

Nous allons définir un premier blueprint contenant un artifact de type "Policy Assignment" avec une policy limitant le type de ressources disponibles.

1. Accédez à l'écran de gestion des blueprint
2. Configurez le blueprint avec un artifact de type "Policy Assignment". Sélectionnez la policy permettant de limiter les types de ressources.
3. Appliquer le blueprint à votre subscription.
4. Attendez quelques minutes (5 à peu près)
5. Faites un test en créant des ressource:
    - Créer une ressource autorisée
    - Créer une ressource non autorisée

## PowerShell

Nous allons désormais créer le même blueprint via PowerShell.

1. Commencez par installer le module AZ.Blueprint qui est en Preview: `Install-Module -Name Az.Blueprint -AllowPrerelease -Force`
2. Créez un fichier json pour définir votre blueprint.
3. Créez le blueprint avec la commande suivante: `New-AzBlueprint -Name "blueprint_name" -BlueprintFile "Chemin_vers_le_fichier\blueprint.json" -SubscriptionId "ID_de_votre_subscription"`