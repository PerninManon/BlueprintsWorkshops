# Bonus

Dans cette partie du Lab, vous serez amené à ajouter un artifact à votre Blueprint via le portail. Cet exercice vous permettra d'ajouter un artifact Blueprint "ARM" qui va créer un ResourceGroup Azure, puis de créer à dedans un ressource Application Insights.

## Prérequis

Pour réaliser cet exercice, vous aurez besoin:
- d'une souscription Azure sur laquelle vous êtes Contributor au minimum, Owner idéalement

## Portail Azure

### Préparation du Blueprint

#### Ajout de l'artifact de déploiement

Pour déployer les ressources de cet exercice, vous devez tout d'abord ajouter un artifact à votre Blueprint si déjà existant (ou en recréer un nouveau, lui ajouter un artifact, le publier puis l'assigner à votre souscription). Dans cet exemple, nous créerons tout le Blueprints from scratch. 

1. Dans la barre de recherche au niveau de votre portail Azure, tapez "Blueprints" et sélectionner le dans les résultats.
2. Séléctionnez "Blueprints definitions"
3. Séléctionnez "Create blueprint"
4. Séléctionnez en suite "Start with blank blueprint" sur la tableau de bord.
5. Un écran "Create blueprint" s'affichera, vous devez maintenant remplir les informations du blueprint dans le formulaire à deux volet (Basics et Artifacts).
6. Dans le volet Basics, donner un nom à votre Blueprint "AzBlueprint-AppIns"
7. Dans la partie "Definition Location", vous devez sélectionner l'emplacement de votre Blueprint.(L'emplacement peut être soit un ManagementGroup ou une souscription), dans cet exemple selectionnez simplement votre souscription en cliquand sur le bouton d'exploration "..." , sélectionnant votre souscription MSDN cellenza dans la liste, et en appuyant sur "Select" en bas de l'écran pour confirmer.
8. Maintenant vous devez créer l'artifact de votre blueprint, pour cela, sélectionnez "Next: Artifacts" en bas du formulaire
9. Vous êtes en ce moment dans le volet Artifact. Cliquez "Add Artifact" pour créer et associer le nouvel Artifact à votre Blueprint.
10. Une fenêtre "Add artifact" s'affiche, séléctionnez le type de l'artifact "Azure Resource Manager template (Subscription)"
11. Donnez un nom à votre artifact "AzBlueprint-AppIns-Artifact"
11. Laissez la description vide.
12. Deux volets s'affiche en bas, "Template" et "Parameters"
13. Importez le fichier "Nom du fichier Deploy.json"
14. Sélectionnez "Add" en bas de la fenêtre.
15. Sélectionnez "Save Draft"
16. Votre blueprint s'affichera dans le tableau de bord des "Blueprints" en version "Draft" visible dans la colonne "Latest Version" et ne peut pour le moment être assigné, vous devez donc le publier pour pouvoir le faire.

#### Publication du blueprint

17. Publiez le blueprint "AzBlueprint-AppIns" en le double-cliquant, et en sélectionnant "Publish-Blueprint".
18. Donnez une version à votre blueprint "1.0" par exemple.
19. Cliquez "Publish" en bas de l'écran

#### Assignement du blueprint

20. Une fois votre blueprint publié, assignez-le à une souscription en sélectionnant "Assign blueprint"
21. Sélectionnez votre souscription si ce n'est pas fait par défaut.
22. Selectionnez La localisation "West Europe" dans "Location"
23. Cliquez "Assign"

#### Vérification 

24. La création de l'Assignement prendra quelques secondes.
25. Revenez à l'ecran principal de votre portail Azure, et vérifier que le resourceGroup "BootCampAppInsight-RG" a bien été créé. 
26. Accéder à votre resource group et vérifier que la ressource "Application insight" portant le nom "BootCampAppIns" a bien été ajoutée.



```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "rgName": "BootCampAppInsight-RG",
	"appInsName": "BootCampAppIns"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "westEurope",
      "name": "[variables('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "AppInsightDeployment",
      "resourceGroup": "[variables('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', variables('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "microsoft.insights/components",
			  "apiVersion": "2014-04-01",
              "name": "BootCampAppInsightName",
              "location": "westEurope",
			  "properties": {
					"ApplicationId": "[variables('appInsName')]"
			  }

            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```


