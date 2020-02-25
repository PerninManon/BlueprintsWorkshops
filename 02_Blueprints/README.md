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

### Création d'une blueprint definition
Nous allons désormais créer le même blueprint via PowerShell.

1. Commencez par installer le module AZ.Blueprint qui est en Preview: `Install-Module -Name Az.Blueprint -AllowPrerelease -Force`
2. Créez un fichier json pour définir votre blueprint (example [BlueprintFiles/0_blueprint.json](BlueprintFiles/0_blueprint.json))
3. Créez le blueprint avec la commande suivante: `$MyBlueprint=New-AzBlueprint -Name "blueprint_name" -BlueprintFile "Chemin_vers_le_fichier\blueprint.json" -SubscriptionId "ID_de_votre_subscription"`
4. Récupérez l'ID de la policy à l'aide de la commande: `Get-AzPolicyDefinition | where {$_.Properties -match "Allowed resource types"}`
5. Récupérez également les paramètres attendus par cette policy
6. Ajoutez un artifact de type "Policy Assignment" à l'aide d'une commande PowerShell:
```powershell
New-AzBlueprintArtifact -Type PolicyAssignmentArtifact -Name "artifact_name" -Blueprint $MyBlueprint -PolicyDefinitionId "/providers/Microsoft.Authorization/policyDefinitions/xxxxxxxxx" -PolicyDefinitionParameter @{listOfResourceTypesAllowed="[parameters('allowedresourcetypes')]"}
```
7. Avant de pouvoir appliquer le blueprint, il faut le publier en spécifiant une version: `Publish-AzBlueprint -Blueprint $MyBlueprint -Version "bootcamp-0"`

### Appliquer le blueprint
Une fois le blueprint publié, nous allons pouvoir l'appliquer à une subscription:
```powershell
New-AzBlueprintAssignment -Name "MyBlueprintAssignment" -Blueprint $MyBlueprint -SubscriptionId "ID_de_votre_subscriptio" -Parameter @{allowedresourcetypes=@("microsoft.insights/components")} -Location "West Europe"
```

C'est à cette étape que l'on vient spécifier la valeur des paramètres.
Ici "microsoft.insights/components" correspond au provider pour Application Insights.

Vous pouvez faire un test en créant:
- une ressource Application Insights
- un autre type de ressource

> Pour la manipulation des blueprints, les commandes PowerShell suivantes sont également intéressantes:
> - `Export-AzBlueprintWithArtifact -Blueprint $MyBlueprint -OutputPath "chemin_destination" -Version "version_blueprint"` pour récupérer un dossier décrivant le blueprint et ses artifacts.
> - `Import-AzBlueprintWithArtifact -Name "blueprint_name" -SubscriptionId "ID_de_votre_subscription" -InputPath "chemin_vers_dossier_blueprint"` pour créer un blueprint à partir d'un dossier de définition du blueprint. Ce dossier doit respecter l'arborescence suivante: 
> ```
> | blueprint_definition.json 
> | Artifacts
>     | artifact_definition.json
> ``` 