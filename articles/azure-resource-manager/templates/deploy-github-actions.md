---
title: Distribuera Resource Manager-mallar med GitHub-åtgärder
description: Beskriver hur du distribuerar Azure Resource Manager-mallar med hjälp av GitHub-åtgärder.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure,subject-armqs
ms.openlocfilehash: b5852a65b4ed3c7cc73352fed37eeff035f8563c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106798"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Distribuera Azure Resource Manager-mallar med GitHub-åtgärder

[GitHub-åtgärder](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) är en uppsättning funktioner i GitHub för att automatisera dina arbets flöden för program utveckling på samma plats som du lagrar kod och samarbetar om pull-begäranden och-problem.

Använd [åtgärden distribuera Azure Resource Manager mall](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) för att automatisera distributionen av en Resource Manager-mall till Azure. 

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett GitHub-konto. Om du inte har någon kan du registrera dig [kostnads fritt](https://github.com/join).  
    - En GitHub-lagringsplats där du kan lagra dina Resource Manager-mallar och dina arbetsflödes-filer. Information om hur du skapar [en ny databas finns i skapa en ny lagrings plats](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).


## <a name="workflow-file-overview"></a>Översikt över arbets flödes fil

Ett arbets flöde definieras av en YAML-fil (. yml) i `/.github/workflows/` sökvägen i lagrings platsen. Den här definitionen innehåller de olika stegen och parametrarna som utgör arbets flödet.

Filen har två avsnitt:

|Section  |Aktiviteter  |
|---------|---------|
|**Autentisering** | 1. definiera ett huvud namn för tjänsten. <br /> 2. skapa en GitHub-hemlighet. |
|**Distribuera** | 1. distribuera Resource Manager-mallen. |

## <a name="generate-deployment-credentials"></a>Generera autentiseringsuppgifter för distribution


Du kan skapa ett [huvud namn för tjänsten](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) med kommandot [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) i [Azure CLI](/cli/azure/). Kör det här kommandot med [Azure Cloud Shell](https://shell.azure.com/) i Azure Portal eller genom att välja knappen **prova** .

Ersätt plats hållaren `myApp` med namnet på ditt program. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

I exemplet ovan ersätter du plats hållarna med ditt prenumerations-ID och resurs gruppens namn. Utdata är ett JSON-objekt med roll tilldelningens autentiseringsuppgifter som ger åtkomst till din App Service-app på liknande sätt som nedan. Kopiera det här JSON-objektet för senare.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Det är alltid en bra idé att bevilja minimal åtkomst. Omfånget i föregående exempel är begränsat till resurs gruppen.



## <a name="configure-the-github-secrets"></a>Konfigurera GitHub hemligheter

Du måste skapa hemligheter för dina Azure-autentiseringsuppgifter, resurs grupper och prenumerationer. 

1. I [GitHub](https://github.com/)bläddrar du till din lagrings plats.

1. Välj **inställningar > hemligheter > ny hemlighet**.

1. Klistra in hela JSON-utdata från Azure CLI-kommandot i fältet hemligt värde. Ge hemligheten namnet `AZURE_CREDENTIALS` .

1. Skapa en annan hemlighet med namnet `AZURE_RG` . Lägg till namnet på din resurs grupp i fältet hemligt värde. 

1. Skapa ytterligare en hemlighet med namnet `AZURE_SUBSCRIPTION` . Lägg till ditt prenumerations-ID i fältet hemligt värde. 

## <a name="add-resource-manager-template"></a>Lägg till Resource Manager-mall

Lägg till en Resource Manager-mall i GitHub-lagringsplatsen. Med den här mallen skapas ett lagrings konto.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Du kan lagra filen var som helst på lagrings platsen. Arbets flödes exemplet i nästa avsnitt förutsätter att mallfilen heter **azuredeploy.jspå**och lagras i roten på din lagrings plats.

## <a name="create-workflow"></a>Skapa arbets flöde

Arbets flödes filen måste lagras i mappen **. GitHub/arbets flöden** i roten på din lagrings plats. Arbets flödets fil namns tillägg kan vara antingen **. yml** eller **. yaml**.

1. Från din GitHub-lagringsplats väljer du **åtgärder** på den översta menyn.
1. Välj **nytt arbets flöde**.
1. Välj **Konfigurera ett arbets flöde själv**.
1. Byt namn på arbets flödes filen om du vill ha ett annat namn än **main. yml**. Till exempel: **deployStorageAccount. yml**.
1. Ersätt innehållet i YML-filen med följande:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@master

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
     
          # Deploy ARM template
        - uses: azure/arm-deploy@v1
        - name: Run ARM deploy
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS
        
          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```

    Det första avsnittet i arbets flödes filen innehåller:

    - **namn**: namnet på arbets flödet.
    - **på**: namnet på de GitHub-händelser som utlöser arbets flödet. Arbets flödet utlöses när det finns en push-händelse på huvud grenen, vilket ändrar minst en av de angivna två filerna. De två filerna är arbets flödes filen och mallfilen.

1. Välj **Starta genomförande**.
1. Välj **genomför direkt på huvud grenen**.
1. Välj **genomför ny fil** (eller **genomför ändringar**).

Eftersom arbets flödet har kon figurer ATS för att utlösas av antingen arbets flödes filen eller mallfilen som uppdateras, startar arbets flödet direkt efter att du har bekräftat ändringarna.

## <a name="check-workflow-status"></a>Kontrol lera arbets flödes status

1. Välj fliken **åtgärder** . Ett arbets flöde för att **skapa deployStorageAccount. yml** visas. Det tar 1-2 minuter att köra arbets flödet.
1. Välj arbets flödet för att öppna det.
1. Verifiera distributionen genom att välja **Kör arm Deploy** på menyn.

## <a name="clean-up-resources"></a>Rensa resurser

När din resurs grupp och lagrings plats inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resurs gruppen och GitHub-lagringsplatsen. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din första ARM-mall](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
