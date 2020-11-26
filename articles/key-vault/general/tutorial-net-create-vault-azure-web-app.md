---
title: Självstudie – använda Azure Key Vault med en Azure-webbapp i .NET
description: I den här självstudien konfigurerar du en Azure-webbapp i ett ASP.NET Core program för att läsa en hemlighet från ditt nyckel valv.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f0121a264c3fcf4cba1de72a1b9b81c1af66e82a
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96297108"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Självstudie: Använd en hanterad identitet för att ansluta Key Vault till en Azure-webbapp i .NET

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) är ett sätt att lagra autentiseringsuppgifter och andra hemligheter med ökad säkerhet. Men din kod måste autentiseras för att Key Vault ska kunna hämta dem. [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md) hjälper till att lösa det här problemet genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva Visa autentiseringsuppgifter i din kod.

I den här självstudien använder du en hanterad identitet för att autentisera en Azure-webbapp med ett Azure Key Vault. Du använder [klient biblioteket för Azure Key Vault version 4 för .net](/dotnet/api/overview/azure/key-vault) och [Azure CLI](/cli/azure/get-started-with-azure-cli). Samma grundläggande principer gäller när du använder det utvecklings språk du väljer, Azure PowerShell och/eller Azure Portal.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

* En Azure-prenumeration. [Skapa ett kostnads fritt.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.Net Core 3,1 SDK (eller senare)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* En [git](https://www.git-scm.com/downloads) -installation.
* [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/general/overview) Du kan skapa ett nyckel valv med hjälp av [Azure Portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md)eller [Azure PowerShell](quick-create-powershell.md).
* En Key Vault [hemlighet](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets). Du kan skapa en hemlighet med hjälp av [Azure Portal](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal), [POWERSHELL](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell)eller [Azure CLI](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli).

## <a name="create-a-net-core-app"></a>Skapa en .NET Core-app
I det här steget ska du konfigurera det lokala .NET Core-projektet.

I ett terminalfönster på datorn skapar du en katalog med namnet `akvwebapp` och gör den till den aktuella katalogen:

```bash
mkdir akvwebapp
cd akvwebapp
```

Skapa en .NET Core-app med hjälp av det [nya dotNet-webb](/dotnet/core/tools/dotnet-new) kommandot:

```bash
dotnet new web
```

Kör programmet lokalt så att du vet hur det ska se ut när du distribuerar det till Azure:

```bash
dotnet run
```

I en webbläsare går du till appen på `http://localhost:5000` .

Du ser då meddelandet ”Hello World!” meddelande från den exempel app som visas på sidan.

## <a name="deploy-the-app-to-azure"></a>distribuera appen till Azure

I det här steget ska du distribuera ditt .NET Core-program till Azure App Service med hjälp av lokal git. Mer information om hur du skapar och distribuerar program finns i [skapa ett ASP.net Core-webbprogram i Azure](https://docs.microsoft.com/azure/app-service/quickstart-dotnetcore).

### <a name="configure-the-local-git-deployment"></a>Konfigurera lokal Git-distribution

I terminalfönstret väljer du **CTRL + C** för att stänga webb servern.  Initiera en git-lagringsplats för .NET Core-projektet:

```bash
git init
git add .
git commit -m "first commit"
```

Du kan använda FTP och lokal git för att distribuera en Azure-webbapp med hjälp av en *distributions användare*. När du har konfigurerat din distributions användare kan du använda den för alla dina Azure-distributioner. Ditt användar namn och lösen ord för distribution på konto nivå skiljer sig från dina autentiseringsuppgifter för Azure-prenumerationen. 

Konfigurera distributions användaren genom att köra kommandot [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) . Välj ett användar namn och lösen ord som följer dessa rikt linjer: 

- Användarnamnet måste vara unikt inom Azure. För lokala git-push-meddelanden får den inte innehålla snabel-a-symbolen (@). 
- Lösen ordet måste innehålla minst åtta tecken och innehålla två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON-utdata visar lösen ordet som `null` . Ändra användar namnet om du får ett `'Conflict'. Details: 409` fel meddelande. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

Registrera ditt användar namn och lösen ord så att du kan använda det för att distribuera dina webb program.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resurs grupp är en logisk behållare dit du distribuerar Azure-resurser och hanterar dem. Skapa en resurs grupp som innehåller både ditt nyckel valv och din webbapp med hjälp av kommandot [AZ Group Create](/cli/azure/group?#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

Skapa en [App Service plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) med hjälp av kommandot Azure CLI [AZ AppService plan Create](/cli/azure/appservice/plan) . I följande exempel skapas ett App Service plan med namnet `myAppServicePlan` på `FREE` pris nivån:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

När App Service plan skapas visar Azure CLI information som liknar vad du ser här:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Mer information finns i [Hanera en App Service-plan i Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage).

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en [Azure-webbapp](../../app-service/overview.md) i `myAppServicePlan` App Service plan. 

> [!Important]
> Precis som ett nyckel valv måste en Azure-webbapp ha ett unikt namn. Ersätt `<your-webapp-name>` med namnet på din webbapp i följande exempel.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

När webbappen skapas visar Azure CLI utdata som liknar det du ser här:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


URL: en för git-fjärrdatabasen visas i `deploymentLocalGitUrl` -egenskapen i formatet `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` . Spara den här URL: en. Du behöver det senare.

Gå till den nya appen med hjälp av följande kommando. Ersätt `<your-webapp-name>` med namnet på appen.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Du ser standard webb sidan för en ny Azure-webbapp.

### <a name="deploy-your-local-app"></a>Distribuera din lokala app

I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. I följande kommando ersätter `<deploymentLocalGitUrl-from-create-step>` du med URL: en för den git-fjärrdatabas som du sparade i avsnittet [skapa en webbapp](#create-a-web-app) .

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Använd följande kommando för att skicka till Azure-fjärrservern för att distribuera din app. När git Credential Manager uppmanas att ange autentiseringsuppgifter använder du de autentiseringsuppgifter som du skapade i avsnittet [Konfigurera lokal Git-distribution](#configure-the-local-git-deployment) .

```bash
git push azure master
```

Det kan ta några minuter att köra det här kommandot. När den körs visas information som liknar det du ser här:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Gå till (eller uppdatera) det distribuerade programmet med hjälp av webbläsaren:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Du ser då meddelandet ”Hello World!” meddelande som du såg tidigare när du besökte `http://localhost:5000` .
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Konfigurera webb programmet för att ansluta till Key Vault

I det här avsnittet ska du konfigurera webb åtkomst till Key Vault och uppdatera din program kod för att hämta en hemlighet från Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Skapa och tilldela en hanterad identitet

I den här självstudien använder vi [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för att autentisera till Key Vault. Hanterad identitet hanterar automatiskt programautentiseringsuppgifter.

Skapa identiteten för programmet i Azure CLI genom att köra kommandot [AZ webapp-Identity Assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) :

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Kommandot returnerar följande JSON-kodfragment:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Om du vill ge ditt webb program behörighet att utföra **Get** -och **list** -åtgärder i ditt nyckel valv skickar `principalId` du till Azure CLI [-AZ för att ange princip](/cli/azure/keyvault?#az-keyvault-set-policy) kommando:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Du kan också tilldela åtkomst principer med hjälp av [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) eller [PowerShell](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-powershell).

### <a name="modify-the-app-to-access-your-key-vault"></a>Ändra appen för åtkomst till ditt nyckel valv

#### <a name="install-the-packages"></a>Installera paketen

I terminalfönstret installerar du Azure Key Vault klient biblioteket för .NET-paket:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Uppdatera koden

Hitta och öppna filen Startup.cs i ditt akvwebapp-projekt. 

Lägg till följande rader i rubriken:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Lägg till följande rader före `app.UseEndpoints` anropet och uppdatera URI: n för att avspegla `vaultUri` ditt nyckel valv. I den här koden används  [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential) för att autentisera till Key Vault, som använder en token från hanterad identitet för att autentisera. Mer information om att autentisera till Key Vault finns i [Developer ' s guide](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code). Koden använder också exponentiell backoff för återförsök om Key Vault begränsas. Mer information om Key Vault transaktions gränser finns i avsnittet [Azure Key Vault begränsnings vägledning](https://docs.microsoft.com/azure/key-vault/general/overview-throttling).

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Uppdatera raden `await context.Response.WriteAsync("Hello World!");` så att den ser ut som den här raden:

```csharp
await context.Response.WriteAsync(secretValue);
```

Se till att spara ändringarna innan du fortsätter till nästa steg.

#### <a name="redeploy-your-web-app"></a>Distribuera din webbapp igen

Nu när du har uppdaterat din kod kan du distribuera om den till Azure med hjälp av dessa git-kommandon:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="go-to-your-completed-web-app"></a>Gå till din slutförda webbapp

```bash
http://<your-webapp-name>.azurewebsites.net
```

Där innan du såg "Hello World!" bör du nu se värdet för din hemlighet som visas: "lyckades!"

## <a name="next-steps"></a>Nästa steg

- [Använda Azure Key Vault med program som distribueras till en virtuell dator i .NET](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- Lär dig mer om [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)
- Läs mer om [hanterade identiteter för App Service](../../app-service/overview-managed-identity.md?tabs=dotnet)
- Visa [Guide för utvecklare](https://docs.microsoft.com/azure/key-vault/general/developers-guide)
- [Säker åtkomst till ett nyckel valv](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault)


