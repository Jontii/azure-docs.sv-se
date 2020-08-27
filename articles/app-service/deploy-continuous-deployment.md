---
title: Konfigurera kontinuerlig distribution
description: Lär dig hur du aktiverar CI/CD för att Azure App Service från GitHub, BitBucket, Azure databaser eller andra databaser. Välj den build-pipeline som passar dina behov.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa30c6c2c3ecd9c9c119fee80b7ef90999e42d30
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962629"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Kontinuerlig distribution till Azure App Service

[Azure App Service](overview.md) aktiverar kontinuerlig distribution från GitHub, BitBucket och [Azure databaser](https://azure.microsoft.com/services/devops/repos/) -databaser genom att hämta de senaste uppdateringarna. Den här artikeln visar hur du använder Azure Portal för att kontinuerligt distribuera din app via kudu Build Service eller [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Mer information om käll kontroll tjänsterna finns i [skapa en lagrings platsen (GitHub)], [skapa en lagrings platsen (BitBucket)]eller [skapa en ny git-lagrings platsen (Azure databaser)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Auktorisera Azure App Service 

Om du vill använda Azure databaser kontrollerar du att din Azure DevOps-organisation är länkad till din Azure-prenumeration. Mer information finns i [Konfigurera ett Azure DevOps Services-konto så att det kan distribueras till en webbapp](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

För BitBucket eller GitHub kan du auktorisera Azure App Service att ansluta till din lagrings plats. Du behöver bara auktorisera med en käll kontroll tjänst en gång. 

1. Sök efter **app Services** i [Azure Portal](https://portal.azure.com)och välj.

   ![Sök efter app Services.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Välj App Service som du vill distribuera.

   ![Välj din app.](media/app-service-continuous-deployment/select-your-app.png)
   
1. På sidan app väljer du **Deployment Center** på den vänstra menyn.
   
1. På sidan **distributions Center** väljer du **GitHub** eller **BitBucket**och väljer sedan **auktorisera**. 
   
   ![Välj käll kontroll tjänst och välj sedan auktorisera.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Logga in på tjänsten om det behövs och följ anvisningarna för auktorisering. 

## <a name="enable-continuous-deployment"></a>Aktivera kontinuerlig distribution 

När du har auktoriserat en käll kontroll tjänst konfigurerar du din app för kontinuerlig distribution genom den inbyggda [Kudu App Service](#option-1-kudu-app-service) build-servern eller via [Azure-pipeliner](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Alternativ 1: kudu App Service

Du kan använda den inbyggda kudu App Service build-servern för att kontinuerligt distribuera från GitHub, BitBucket eller Azure databaser. 

1. Sök efter **app Services**i [Azure Portal](https://portal.azure.com)och välj sedan den app service som du vill distribuera. 
   
1. På sidan app väljer du **Deployment Center** på den vänstra menyn.
   
1. Välj din Provider för verifierad käll kontroll på sidan **distributions Center** och välj **Fortsätt**. För GitHub eller BitBucket kan du även välja **Ändra konto** för att ändra det auktoriserade kontot. 
   
   > [!NOTE]
   > Om du vill använda Azure databaser kontrollerar du att Azure DevOps Services-organisationen är länkad till din Azure-prenumeration. Mer information finns i [Konfigurera ett Azure DevOps Services-konto så att det kan distribueras till en webbapp](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. För GitHub eller Azure databaser väljer du **App Service Build Service**på sidan **build Provider** och väljer sedan **Fortsätt**. BitBucket använder alltid App Service build-tjänsten.
   
   ![Välj App Service build-tjänst och välj sedan Fortsätt.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. På sidan **Konfigurera** :
   
   - För GitHub, list rutan och välj den **organisation**, **lagrings plats**och **gren** som du vill distribuera kontinuerligt.
     
     > [!NOTE]
     > Om du inte ser några databaser kan du behöva auktorisera Azure App Service i GitHub. Bläddra till GitHub-lagringsplatsen och gå till **Inställningar**  >  **program**  >  **auktoriserade OAuth-appar**. Välj **Azure App Service**och välj sedan **bevilja**. Du måste vara ägare till organisationen för att kunna bevilja behörighet för organisations databaser.
     
   - För BitBucket väljer du det BitBucket- **team**, den **lagrings plats**och den **gren** som du vill distribuera kontinuerligt.
     
   - För Azure databaser väljer du den **Azure DevOps-organisation**, det **projekt**, den **lagrings plats**och den **gren** som du vill distribuera kontinuerligt.
     
     > [!NOTE]
     > Om din Azure DevOps-organisation inte finns med i listan kontrollerar du att den är länkad till din Azure-prenumeration. Mer information finns i [Konfigurera ett Azure DevOps Services-konto så att det kan distribueras till en webbapp](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
     
1. Välj **Fortsätt**.
   
   ![Fyll i informationen om lagring och välj sedan Fortsätt.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. När du har konfigurerat build-providern granskar du inställningarna på sidan **Sammanfattning** och väljer sedan **Slutför**.
   
1. Nya incheckningar i den valda databasen och grenen distribueras nu kontinuerligt till din App Service-app. Du kan spåra incheckningar och distributioner på sidan **distributions Center** .
   
   ![Spåra incheckningar och distributioner i distributions Center](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Alternativ 2: Azure-pipeline 

Om ditt konto har de behörigheter som krävs kan du konfigurera Azure-pipeliner för att kontinuerligt distribuera från GitHub eller Azure databaser. Mer information om hur du distribuerar via Azure-pipeliner finns i [distribuera en webbapp till Azure App tjänster](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Förutsättningar

För att Azure App Service ska kunna skapa kontinuerlig leverans med Azure-pipeliner bör din Azure DevOps-organisation ha följande behörigheter: 

- Ditt Azure-konto måste ha behörighet att skriva till Azure Active Directory och skapa en tjänst. 
  
- Ditt Azure-konto måste ha **ägar** rollen i din Azure-prenumeration.

- Du måste vara administratör i det Azure DevOps-projekt som du vill använda.

#### <a name="github--azure-pipelines"></a>GitHub + Azure-pipeline

1. Sök efter **app Services**i [Azure Portal](https://portal.azure.com)och välj sedan den app service som du vill distribuera. 
   
1. På sidan app väljer du **Deployment Center** på den vänstra menyn.

1. Välj **GitHub** som käll kontroll leverantör på sidan **distributions Center** och välj **Fortsätt**. För **GitHub**kan du välja **Ändra konto** för att ändra det auktoriserade kontot.

    ![käll kontroll](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. På sidan **build Provider** väljer du **Azure-pipelines (för hands version)** och väljer sedan **Fortsätt**.

    ![Build-Provider](media/app-service-continuous-deployment/select-build-provider.png)
   
1. På sidan **Konfigurera** i avsnittet **kod** väljer du den **organisation**, den **lagrings plats**och **gren** som du vill distribuera kontinuerligt och väljer **Fortsätt**.
     
     > [!NOTE]
     > Om du inte ser några databaser kan du behöva auktorisera Azure App Service i GitHub. Bläddra till GitHub-lagringsplatsen och gå till **Inställningar**  >  **program**  >  **auktoriserade OAuth-appar**. Välj **Azure App Service**och välj sedan **bevilja**. Du måste vara ägare till organisationen för att kunna bevilja behörighet för organisations databaser.
       
    I avsnittet **skapa** anger du den Azure DevOps-organisation, projekt, språk ramverk som Azure-pipelinen ska använda för att köra Bygg uppgifter och väljer sedan **Fortsätt**.

   ![Build-Provider](media/app-service-continuous-deployment/build-configure.png)

1. När du har konfigurerat build-providern granskar du inställningarna på sidan **Sammanfattning** och väljer sedan **Slutför**.

   ![Build-Provider](media/app-service-continuous-deployment/summary.png)
   
1. Nya incheckningar i den valda databasen och grenen distribueras nu kontinuerligt till din App Service. Du kan spåra incheckningar och distributioner på sidan **distributions Center** .
   
   ![Spåra incheckningar och distributioner i distributions Center](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure databaser + Azure-pipeline

1. Sök efter **app Services**i [Azure Portal](https://portal.azure.com)och välj sedan den app service som du vill distribuera. 
   
1. På sidan app väljer du **Deployment Center** på den vänstra menyn.

1. Välj **Azure-databaser** som käll kontroll leverantör på sidan **distributions Center** och välj **Fortsätt**.

    ![käll kontroll](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. På sidan **build Provider** väljer du **Azure-pipelines (för hands version)** och väljer sedan **Fortsätt**.

    ![käll kontroll](media/app-service-continuous-deployment/azure-pipelines.png)

1. På sidan **Konfigurera** i avsnittet **kod** väljer du den **organisation**, den **lagrings plats**och **gren** som du vill distribuera kontinuerligt och väljer **Fortsätt**.

   > [!NOTE]
   > Om din befintliga Azure DevOps-organisation inte finns med i listan kan du behöva länka den till din Azure-prenumeration. Mer information finns i [definiera din pipeline för CD-version](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   I avsnittet **skapa** anger du den Azure DevOps-organisation, projekt, språk ramverk som Azure-pipelinen ska använda för att köra Bygg uppgifter och väljer sedan **Fortsätt**.

   ![Build-Provider](media/app-service-continuous-deployment/build-configure.png)

1. När du har konfigurerat build-providern granskar du inställningarna på sidan **Sammanfattning** och väljer sedan **Slutför**.  
     
   ![Build-Provider](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. Nya incheckningar i den valda databasen och grenen distribueras nu kontinuerligt till din App Service. Du kan spåra incheckningar och distributioner på sidan **distributions Center** .

## <a name="disable-continuous-deployment"></a>Inaktivera kontinuerlig distribution

Om du vill inaktivera kontinuerlig distribution väljer du **Koppla från** längst upp på sidan **distributions Center** för appen.

![Inaktivera kontinuerlig distribution](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Använd databaser som inte stöds

För Windows-appar kan du manuellt konfigurera kontinuerlig distribution från en git-eller Mercurial-lagringsplats i molnet som portalen inte stöder direkt, till exempel [GitLab](https://gitlab.com/). Du gör det genom att välja den externa rutan på sidan **distributions Center** . Mer information finns i [Konfigurera kontinuerlig distribution med hjälp av manuella steg](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Ytterligare resurser

* [Undersök vanliga problem med kontinuerlig distribution](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Använda Azure PowerShell](/powershell/azure/)
* [Git-dokumentation](https://git-scm.com/documentation)
* [Kudu-projektet](https://github.com/projectkudu/kudu/wiki)

[Skapa en repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Skapa en repo (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Skapa en ny git-lagrings platsen (Azure-databaser)]: /azure/devops/repos/git/creatingrepo