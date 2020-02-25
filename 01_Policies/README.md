# Policies

Dans ce workshop vous verrez comment utiliser des policies via le portail Azure et via PowerShell. Vous utiliserez des policies "Built-In" ainsi que des policies "Custom" que vous aurez créées.

> Une "built-In" policy est une policy pré-définie mise à disposition par Azure, vous pouvez les appliquer sur un périmètre choisi (Resource Group, Subscription, Management Group).

> Une "Initiative" est un regroupement de policies que l'on va pouvoir appliquer en une fois.

- [Prérequis](#prérequis)
- [Portail Azure - Built-In Policies](#built-in-policies)
- [Portail Azure - Custom Policy](#custom-policy)
- [PowerShell - Créer une custom policy](#créer-une-custom-policy)
- [PowerShell - Assigner une policy](#assigner-une-policy)

## Prérequis



## Portail Azure

### Built-In Policies

#### Deny Policy
Une policy avec un effet "Deny" va bloquer le déploiement d'une ressource non conforme.

1. Créer un Resource Group
2. Accédez à l'écran de gestion des policies
3. Consultez les définitions de policies et d'initiatives
4. Recherchez la policy permettant de limiter les régions (location) autorisées
5. Appliquez (assign) cette policy sur un Resource Group
6. Attendez quelques minutes (5 à peu près)
7. Faites un test en créant une ressource (Application Insights par exemple, la création est simple et rapide):
    - Créer une ressource dans une région autorisée
    - Créer une ressource dans une région non autorisée


#### Audit Policy
Une policy avec un effet "Audit" va autoriser le déploiement d'une ressource non conforme mais le statut de non conformité sera remonté dans les logs et dans le dashboard de compliance.

1. Créez un Resource Group dédié
2. Recherchez la policy qui compare la région d'une ressource avec celle de son Resource Group
3. Appliquez (assign) cette policy sur un Resource Group
5. Attendez quelques minutes (5 à peu près)
6. Faites un test en créant une ressource (Application Insights par exemple, la création est simple et rapide):
    - Créer une ressource dans la même région que celle de son Resource Group
    - Créer une ressource dans une région différente de celle de son Resource Group
7. Vous reviendrez plus tard voire le résultat de l'audit, cela peut prendre un peu de temps.

### Custom Policy

## PowerShell

### Créer une custom policy

### Assigner une policy