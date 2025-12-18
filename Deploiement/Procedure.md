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

- Téléchargez le notebook [Deploy_FUAM.ipynb](FUAM_Notebook/Deploy_FUAM.ipynb) depuis le dossier scripts sur votre ordinateur.

## 2. Préparation de votre environnement

### 2.1 Création et autorisation du principal de service

- Créez un principal de service avec un secret client dans Azure Entra ID, sans aucune autorisation d'API.

  > **Important :** L'activation de certaines autorisations de l'API Power BI peut entraîner des erreurs lors de l'exécution ultérieure de pipelines.

- Ajoutez le principal de service à un groupe autorisé pour les deux paramètres d'administration suivants :

  - _Les principaux de service peuvent utiliser les API Fabric_ - [learn.microsoft.com](https://learn.microsoft.com/en-us/fabric/admin/enable-service-principal-admin-apis)

  - _Les principaux de service peuvent accéder aux API d'administration en lecture seule_ - [learn.microsoft.com](https://learn.microsoft.com/en-us/fabric/admin/enable-service-principal-admin-apis)

### 2.2 Créer un espace de travail

- Créez un nouvel espace de travail « FUAM » (le nom peut être modifié), doté d'une capacité P ou F.

- (**Facultatif**) Téléchargez le [logo de l'espace de travail](Deploiement/icon_FUAM_workspace.png) et ajoutez-le à l'espace de travail FUAM.


## 3. Importer et exécuter le notebook

- Importez le notebook **Deploy_FUAM.ipynb** que vous venez de télécharger dans votre espace de travail FUAM.

![](Deploiement/FUAM_basic_deployment_process_3_1.png)

- Cliquez sur « Tout exécuter » dans le notebook.

![](Deploiement/FUAM_basic_deployment_process_3_2.png)

> **Info :** Le notebook créera **automatiquement** deux nouvelles connexions au cloud (sans identifiants). Vous pouvez également choisir vos propres noms si vous avez des conventions de nommage :

| | Connection 1  | Connection 2 |
|-------------| ------------- | ------------- |
|Connection Name| fuam pbi-service-api admin  | fuam fabric-service-api admin  |
|Connection Type| Web v2  | Web v2  |
|Base Url| https://api.powerbi.com/v1.0/myorg/admin  | https://api.fabric.microsoft.com/v1/admin  |
|Token Audience Url| https://analysis.windows.net/powerbi/api| https://api.fabric.microsoft.com|
|Authentification|Service Principal| Service Principal|

> **Gestion des erreurs :** En cas d’erreur, vous pourrez relancer le notebook. Il dispose d’un mécanisme de mise à jour qui agira comme une mise à jour des éléments.
