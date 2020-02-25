# Blueprints

Dans ce workshop vous verrez comment utiliser définir et appliquer des blueprints via le portail Azure et via PowerShell. 

- [Prérequis](#prérequis)
- [Portail Azure - Built-In Policies](#built-in-policies)
- [Portail Azure - Custom Policy](#custom-policy)
- [PowerShell - Créer une custom policy](#créer-une-custom-policy)
- [PowerShell - Assigner une policy](#assigner-une-policy)

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
