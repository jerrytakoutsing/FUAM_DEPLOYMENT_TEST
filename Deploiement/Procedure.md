# Déploiement de FUAM

Le déploiement de FUAM est très simple grâce à une automatisation maximale. FUAM sera déployé sur votre tenant, dans un espace de travail.

## Informations importantes

- **Utilisation de la capacité**

  - Veuillez noter que les éléments FUAM consomment votre capacité uniquement en unités de calcul (CU).

  - FUAM est conçu pour minimiser votre consommation de capacité. Toutefois, la consommation de CU de FUAM dépend fortement de la fréquence d'exécution du pipeline et du nombre d'utilisateurs du rapport FUAM.

- **Tests**

  - Veuillez d'abord tester la solution sur une référence Fabric hors production, sans impacter les autres charges de travail de votre environnement.

- **Cycle de vie**

  - Le bloc-notes de déploiement prend en charge les déploiements initiaux et les mises à jour des éléments FUAM. Une fois les mises à jour de FUAM disponibles dans le référentiel, vous pouvez simplement exécuter le même bloc-notes de déploiement dans votre espace de travail. Les éléments seront alors mis à jour (écrasés) en fonction de leur nom.

- **Cycle de vie**

  - Le bloc-notes de déploiement prend en charge les déploiements initiaux et les mises à jour des éléments FUAM. Une fois les mises à jour de FUAM disponibles dans le référentiel, vous pouvez simplement exécuter le même bloc-notes de déploiement dans votre espace de travail. Les éléments seront alors mis à jour (écrasés) en fonction de leur nom.

- **Support**

  - FUAM n'est **pas** un service Microsoft officiel. Il s'agit d'un accélérateur de solutions piloté par la communauté.


## Prérequis

- Une capacité Power BI ou Fabric (les espaces de travail partagés PPU et Pro ne sont pas pris en charge)

- Possibilité de **créer** un **principal de service** sur votre locataire

- Possibilité de **créer** un **espace de travail** sur votre locataire

- Un compte utilisateur disposant des droits EntraID d'**administrateur Fabric** permanents

  - Vous pouvez également utiliser un SPN via Azure Key Vault


- Paramètres d'administration activés pour le compte utilisateur déployant FUAM :

  - _Les utilisateurs peuvent créer des éléments Fabric_ pour les administrateurs d'espace de travail FUAM - [learn.microsoft.com](https://learn.microsoft.com/en-us/fabric/admin/fabric-switch)

  - _Autoriser les points de terminaison XMLA et l'analyse dans Excel avec les modèles sémantiques locaux_ - [learn.microsoft.com](https://learn.microsoft.com/en-us/fabric/admin/service-admin-portal-integration#allow-xmla-endpoints-and-analyze-in-excel-with-on-premises-datasets)

- Application Fabric Capacity Metrics (espace de travail) **avec capacité P ou F associée** avec **point de terminaison XMLA activé** (au moins en lecture)

  - Versions compatibles de l'application Capacity Metrics : v44 ou antérieure.

- **Facultatif :** Possibilité d'accéder à un coffre de clés Azure sur votre locataire.

# Étapes

## 1. Télécharger le notebook

- Téléchargez le notebook [Deploy_FUAM.ipynb](/FUAM_Notebook/Deploy_FUAM.ipynb) depuis le dossier scripts sur votre ordinateur.

## 2. Préparation de votre environnement

### 2.1 Création et autorisation du principal de service

- Créez un principal de service avec un secret client dans Azure Entra ID, sans aucune autorisation d'API.

  > **Important :** L'activation de certaines autorisations de l'API Power BI peut entraîner des erreurs lors de l'exécution ultérieure de pipelines.

- Ajoutez le principal de service à un groupe autorisé pour les deux paramètres d'administration suivants :

  - _Les principaux de service peuvent utiliser les API Fabric_ - [learn.microsoft.com](https://learn.microsoft.com/en-us/fabric/admin/enable-service-principal-admin-apis)

  - _Les principaux de service peuvent accéder aux API d'administration en lecture seule_ - [learn.microsoft.com](https://learn.microsoft.com/en-us/fabric/admin/enable-service-principal-admin-apis)

### 2.2 Créer un espace de travail

- Créez un nouvel espace de travail « FUAM » (le nom peut être modifié), doté d'une capacité P ou F.

- (**Facultatif**) Téléchargez le [logo de l'espace de travail](/Deploiement/icon_FUAM_workspace.png) et ajoutez-le à l'espace de travail FUAM.


## 3. Importer et exécuter le notebook

- Importez le notebook **Deploy_FUAM.ipynb** (FUAM_Notebook/Deploy_FUAM.ipynb) que vous venez de télécharger dans votre espace de travail FUAM.

![](/Deploiement/FUAM_basic_deployment_process_3_1.png)

- Cliquez sur « Tout exécuter » dans le notebook.

![](/Deploiement/FUAM_basic_deployment_process_3_2.png)

> **Info :** Le notebook créera **automatiquement** deux nouvelles connexions au cloud (sans identifiants). Vous pouvez également choisir vos propres noms si vous avez des conventions de nommage :

| | Connection 1  | Connection 2 |
|-------------| ------------- | ------------- |
|Connection Name| fuam pbi-service-api admin  | fuam fabric-service-api admin  |
|Connection Type| Web v2  | Web v2  |
|Base Url| https://api.powerbi.com/v1.0/myorg/admin  | https://api.fabric.microsoft.com/v1/admin  |
|Token Audience Url| https://analysis.windows.net/powerbi/api| https://api.fabric.microsoft.com|
|Authentification|Service Principal| Service Principal|

> **Gestion des erreurs :** En cas d’erreur, vous pourrez relancer le notebook. Il dispose d’un mécanisme de mise à jour qui agira comme une mise à jour des éléments.

## 4. Ajouter des identifiants aux connexions

- Dans Fabric, accédez à « Gérer les connexions et les passerelles » dans les Paramètres.

- Configurez les identifiants des connexions avec les informations du principal de service récemment créé :

![](/Deploiement/FUAM_basic_deployment_process_4_1.png)

> **Information :** Ces connexions sont utilisées dans les pipelines FUAM pour récupérer des données depuis les API REST. Si les identifiants sont incorrects ou si le secret a expiré, le pipeline échouera.

## 5. Configurer l'application Métriques de capacité

Nous vous recommandons de créer une nouvelle application Métriques de capacité (avec un espace de travail déployé automatiquement) dans votre locataire.

- Créez une nouvelle application Métriques de capacité.

- Configurez l'application comme d'habitude.

- Accédez à l'espace de travail de l'application Métriques de capacité.

- Associez cet espace de travail à une capacité P ou F.

- Renommez-le « Métriques de capacité FUAM ».

- Copiez le nom de l'espace de travail (par exemple, « Métriques de capacité FUAM ») et le nom du modèle sémantique (par exemple, « Métriques de capacité Fabric »).

> **Information :** Le nom de l'espace de travail de la métrique de capacité sera utilisé ultérieurement comme valeur du paramètre **metric_workspace** dans le pipeline « Load_FUAM_Data_E2E », et le nom du modèle sémantique de la métrique de capacité sera utilisé ultérieurement comme valeur du paramètre **metric_dataset** dans le pipeline « Load_FUAM_Data_E2E ».

> **Important :** Par défaut, l’espace de travail de l’application Métriques est créé avec une licence Pro. Si vous ne le modifiez pas pour une licence F/P, vous obtiendrez une erreur.

## 6. Exécuter le pipeline d'orchestration

> **Info :** Le pipeline **Load_FUAM_Data_E2E** est le pipeline d'orchestration principal de bout en bout de FUAM. Il contient et déclenche tous les autres sous-pipelines (modules FUAM) implémentés dans la solution.

> Les sous-pipelines téléchargent toutes les données nécessaires via les API, et les notebooks référencés transforment et écrivent ces données dans les tables delta finales de FUAM_Lakehouse.

- Accédez à votre espace de travail FUAM.

- Recherchez l'élément « Load_FUAM_Data_E2E ».

- Ouvrez le pipeline **Load_FUAM_Data_E2E**.

Ce pipeline possède différents paramètres qui contrôlent le flux de chargement des données.


|       Parameter Name         | Description                        |Allowed values                        |
|----------------|-------------------------------|-----------------------------|
|has_tenant_domains|If **true**, the tenant inventory is enriched with domain information. Use it only, if domains are in use at your tenant. **Default is false**        | true or false            |
|extract_powerbi_artifacts_only|If **true**, the tenant inventory contains **only** semantic models, dataflows, datamarts, reports, dashboard and apps. If **false** the pipeline extracts Power BI **and** Fabric items. Currently, first-party workloads are supported only. **Default is false** | true or false |
|metric_days_in_scope|Defines how many days should be extracted from the capacity metrics app. A maximum of 14 days can be extracted. For an initial load you can set it to the maximum and in subsequent runs reduce it to 2 days|range between **1** and **14**|
|metric_workspace|This is the name _or_ id of the workspace where the capacity metrics app was deployed|string|
|metric_dataset|This is the name _or_ id of the semantic model of the capacity metrics app |string|
|activity_days_in_scope|It defines how many days in the past the activity must be retrieved from the API. Recommended to **use 28 for the initial load** and change the value to **2 for daily load**.| range between **2** and **28** |
|display_data|If **true**, the notebooks will display more information about each relevant step at runtime. This is useful for debugging. **Default is false**| true or false |
|optional_keyvault_name|**Optional**: If you have configured a key vault, enter the name of the key vault. Otherwise, simply leave this field blank. In this case, the Load_Inventory module will use the Notebook owner's identity.| empty or string|
|optional_keyvault_sp_ tenantId_secret_name|**Optional**: If you have configured a key vault and its secrets, enter the name of the tenantId secret name. Otherwise, simply leave this field blank. In this case, the Load_Inventory module will use the Notebook owner's identity.|empty or string|
|optional_keyvault_sp_ clientId_secret_name|**Optional**: If you have configured a key vault and its secrets, enter the name of the clientId secret name. Otherwise, simply leave this field blank. In this case, the Load_Inventory module will use the Notebook owner's identity.|empty or string|
|optional_keyvault_sp_ secret_secret_name|**Optional**: If you have configured a key vault and its secrets, enter the name of the service principal's secret secret name. Otherwise, simply leave this field blank. In this case, the Load_Inventory module will use the Notebook owner's identity.|empty or string|

- Exécutez le pipeline une fois. Cela chargera initialement les données dans FUAM_Lakehouse.
    ![](/Deploiement/FUAM_basic_deployment_process_5_1.png)

> **Gestion des erreurs :**

> Assurez-vous d’être connecté avec un utilisateur disposant des droits d’administrateur Fabric sur le locataire. Vous pouvez également ajouter les noms des clés secrètes de votre coffre de clés Azure aux valeurs du paramètre optional_keyvault_*.

> Assurez-vous que l’utilisateur et le fournisseur de services (SP) sont membres du groupe d’activation des paramètres d’administration, comme indiqué précédemment.

> Vérifiez les informations d’identification des connexions.

> ou consultez la section « Remarques ».

### 7.1 Actualiser le modèle sémantique Core_SM

- Accédez à votre espace de travail FUAM

- Recherchez l'élément « **FUAM_Core_SM** »

- Cliquez sur « Actualiser » le modèle sémantique

### 7.1 Actualiser le modèle sémantique Item_SM

- Accédez à votre espace de travail FUAM

- Recherchez l'élément « **FUAM_Item_SM** »

- Cliquez sur « Actualiser » le modèle sémantique

### 7.3 Ouvrir le rapport Power BI

- Accédez à votre espace de travail FUAM

- Recherchez l'élément « FUAM_Core_Report »

- Ouvrez le rapport Power BI **FUAM_Core_Report**

- Explorez les pages du rapport

![](/Deploiement/FUAM_basic_deployment_process_7_3.png)

> **Gestion des erreurs :** En cas d’erreurs telles que « Impossible de rendre l’élément visuel », veuillez consulter la section « Remarques ».

## 8. Planification du pipeline pour le chargement quotidien

- Accédez à votre espace de travail FUAM.

- Recherchez l'élément « Load_FUAM_Data_E2E ».

- Ouvrez le pipeline **Load_FUAM_Data_E2E**.

- (Recommandé) Modifiez la valeur du paramètre **metric_days_in_scope** à **2**.

- (Recommandé) Modifiez la valeur du paramètre **activity_days_in_scope** à **2**.

- Cliquez sur **Exécuter** -> **Planifier**.

- Configurez la planification.

![](/Deploiement/FUAM_basic_deployment_process_8_1.png)

Vous avez ainsi configuré le pipeline d'orchestration principal, qui sera exécuté quotidiennement. Les nouvelles valeurs des paramètres simulent un chargement incrémentiel. Les métriques de capacité et les journaux d'activité seront récupérés uniquement pour les deux derniers jours et intégrés à la table Lakehouse.

---------------

## Félicitations !

Vous avez déployé et configuré FUAM.

-------------


# Remarques

#### Limitations
- L'API Scanner (learn.microsoft.com) est limitée à 500 requêtes pour 100 espaces de travail. Si votre organisation compte plus de 50 000 espaces de travail associés à des capacités, le pipeline devra attendre une heure avant le prochain appel à l'API Scanner. Cela peut allonger la durée d'exécution du pipeline.

- Des erreurs peuvent survenir si certains types d'éléments n'ont pas encore été créés sur le locataire. Nous avons tenté de réduire ces erreurs en les interceptant, mais sur les locataires relativement peu sollicités, elles peuvent néanmoins impacter l'exécution.

- Le pipeline « Load_Inventory_E2E » utilise l’identité du propriétaire du notebook pour interroger l’API Scanner. Si l’utilisateur ne dispose pas des autorisations d’administrateur Fabric, le notebook échouera. Utilisez Azure Key Vault pour exécuter les appels à l’API Scanner dans le contexte du principal de service.

Dans certains cas, les rapports génèrent une erreur en raison de champs manquants (dans le modèle sémantique), non fournis par l’API. Dans ce cas, veuillez suivre les étapes suivantes :

  - Actualisez le modèle sémantique sous-jacent.

  - Ouvrez le modèle sémantique.

  - Cliquez sur « Modifier les tables ».

  - Cliquez sur « Confirmer » pour actualiser les métadonnées du modèle sémantique.

  - Testez l’actualisation du modèle sémantique et le rapport.
    
- Dans le pipeline 'FUAM_Load_Data_E2E', le paramètre activity_days_in_scope prend actuellement en charge la valeur minimale 2.

#### Erreurs connues

- Certains problèmes connus affectent les locataires « vides » ou de démonstration, où certains objets sont absents, ce qui provoque des erreurs :

  - Si aucune description d'espace de travail n'est définie pour l'ensemble du locataire, ajoutez-en une. Cela corrigera l'erreur.

  - En l'absence d'actualisations planifiées régulières sur le locataire, l'exécution des actualisations de capacité peut échouer. Pour résoudre ce problème, créez une actualisation planifiée et exécutez-la plusieurs fois.

  - Si aucun paramètre de locataire délégué n'est défini dans l'une des capacités, l'étape d'extraction échouera. Vous pouvez supprimer cette étape si elle n'est pas nécessaire pour votre locataire.

  - Essayez d'exécuter le notebook « Init_FUAM_Lakehouse_Tables » pour créer automatiquement les colonnes manquantes.

- Déploiement en cas de lien privé (possible avec des étapes manuelles supplémentaires)

  - Dans certains cas, le notebook de déploiement ne parvient pas à télécharger le contenu requis depuis le dépôt.

  - Dans ce cas, suivez les étapes suivantes pour déployer FUAM :

    1) Exécutez le notebook (la cellule commençant par : `_src_file_path = "./builtin/data/table_definitions.snappy.parquet"_` échouera).

    2) Clonez l'intégralité du dépôt avec Git afin de télécharger tous les fichiers sur votre ordinateur.

    3) Créez manuellement une archive ZIP (`src.zip`) pour le dossier `src`. Le fichier zip doit contenir uniquement le             dossier src, comme ceci : ![image](/Deploiement/FUAM_deployment_process_with_private_link_limitation.png)

    4) Ensuite, importez les fichiers suivants dans la structure indiquée dans la première capture d'écran :

        - monitoring/fabric-unified-admin-monitoring/config/deployment_order.json

        - monitoring/fabric-unified-admin-monitoring/config/item_config.yaml

        - monitoring/fabric-unified-admin-monitoring/data/table_definitions.snappy.parquet
          src.zip
    5) Exécutez les cellules du notebook individuellement, en ignorant le téléchargement des fichiers de la cellule 7.

    6) Tout devrait alors être présent.
