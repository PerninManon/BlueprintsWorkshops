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

Pour réaliser ce workshop, vous aurez besoin:
- d'une souscription Azure sur laquelle vous êtes Contributor au minimum, Owner idéalement

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

Nous souhaitons bloquer le déploiement d'un storage account de type Datalake Gen2 sur certaines souscriptions.

Nous allons pour cela créer notre propre policy (custom policy).

Créez un fichier json "PolicyDenyDatalakeKind.json" contenant la règle à appliquer : 

```json
{
        "if": {
          "allOf": [
            {
                "field":"type",
                "equals": "Microsoft.Storage/storageaccounts"
            },
            {
                "field":"Kind",
                "equals":"StorageV2"
            },
            {
                "field":"Microsoft.Storage/storageAccounts/isHnsEnabled",
                "equals":"true"
            }
        ] 
        },
        "then": {
          "effect": "deny"
        }
      }
```


Une règle est toujours constituée de la même manière avec 
1. "if" décrivant les conditions dans lesquelles nous appliquerons notre règle : Dans cet exemple, nous allons rechercher ce qu'on appelle les "Aliases" qui vont nous permettre de détecter la situation dans laquelle nous nous trouvons. Dans la règle "PolicyDenyDatalakeKind.json", nous allons appliquer notre règle dans le cas où toutes les conditions suivantes seront réunies ("allOf"):
- Lorsqu'il s'agira de la création d'un storage account ("field":"type",
                "equals": "Microsoft.Storage/storageaccounts")
- Lorsque le type de storage account à créer sera de type "StorageV2",
- Lorsque le namespace pour le Data Lake Storage Gen2 sera "Enabled"

Pour répertorier les Aliases par exemple pour 'Microsoft.Storage' : 
```powershell
PS C:\> (Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').
```

2. "then" avec l'effet à appliquer ("deny", "audit" notamment cf. explication ci-dessus)


Pour créer la custom policy en Powershell :
```powershell
PS C:\> New-AzPolicyDefinition -Name 'DenyDatalakeKind' -Policy C:\Users\BlueprintsWorkshops\01_Policies\PolicyDenyDatalakeKind.json
```

```
Name               : DenyDatalakeKind
ResourceId         : /subscriptions/************/providers/Microsoft.Authorization/policyDefinitions/DenyDatalakeKind
ResourceName       : DenyDatalakeKind
ResourceType       : Microsoft.Authorization/policyDefinitions
SubscriptionId     : ************
Properties         : @{policyType=Custom; mode=All; metadata=; policyRule=}
PolicyDefinitionId : /subscriptions/************/providers/Microsoft.Authorization/policyDefinitions/DenyDatalakeKind
```

Vous pouvez vérifier que la policy a bien été créée via le portail : https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions

### Assigner une custom policy

Pour assigner cette policy à une souscription :
```powershell
PS C:\> $SubscriptionName = "Nom de la souscription sur laquelle vous voulez assignez votre Custom policy"

PS C:\> $Subscription = Get-AzSubscription -SubscriptionName $SubscriptionName
PS C:\> $Policy = Get-AzPolicyDefinition -Name 'DenyDatalakeKind'
PS C:\> New-AzPolicyAssignment -Name 'DenyDatalakeKindPolicyAssignment' -PolicyDefinition $Policy -Scope "/subscriptions/$($Subscription.Id)"
```

```
Name               : DenyDatalakeKindPolicyAssignment
ResourceId         : /subscriptions/************/providers/Microsoft.Authorization/policyAssignments/DenyDatalakeKindPolicyAssignment
ResourceName       : DenyDatalakeKindPolicyAssignment
ResourceType       : Microsoft.Authorization/policyAssignments
SubscriptionId     : ************
Properties         : @{policyDefinitionId=/subscriptions/************/providers/Microsoft.Authorization/policyDefinitions/DenyDatalakeKind;
                     scope=/subscriptions/************; metadata=}
Sku                : @{name=A0; tier=Free}
PolicyAssignmentId : /subscriptions/************/providers/Microsoft.Authorization/policyAssignments/DenyDatalakeKindPolicyAssignment
```

Vous pouvez vérifier que la policy a bien été assignée à votre souscription via le portail : https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Assignments

> Note: La création de la policy peut prendre un peu de temps. Il faut parfois attendre quelques secondes avant de pouvoir assigner une custom policy nouvellement créée. 


Maintenant, vous pouvez tester le fonctionnement de cette policy en essayant de créer un storage account sur votre souscription, en sélectionnant :
1. dans l'onglet Basics dans "Account kind" : "StorageV2 (general purpose v2)" 
2. et dans l'onglet Advanced en cochant "Enabled" pour "Data Lake Storage Gen2 - Hierarchical namespace
"
Cliquez sur l'onglet Review + Create. Vous devriez voir apparaitre après quelques secondes un panneau "Validation failed" qui vous indique que notre policy bloque bien le déploiement du storage account :
"Resource 'nameofyourstorageaccount' was disallowed by policy. "policyDefinitionName":"DenyDatalakeKind""