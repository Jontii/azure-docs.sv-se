---
title: Skapa Azure integration runtime i Azure Data Factory
description: Lär dig hur du skapar Azure integration runtime i Azure Data Factory, som används för att kopiera data och skicka Transform-aktiviteter.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 97f3a8f79e503b8870baf67ee982b0091d926b1e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895349"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Så här skapar och konfigurerar du Azure Integration Runtime
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Integration Runtime (IR) är den beräknings infrastruktur som används av Azure Data Factory för att tillhandahålla funktioner för data integrering i olika nätverks miljöer. Mer information om IR finns i [integration runtime](concepts-integration-runtime.md).

Azure IR ger en fullständigt hanterad beräkning för att internt utföra data förflyttning och omvandlings aktiviteter för att skicka data till beräknings tjänster som HDInsight. Den finns i Azure-miljön och har stöd för att ansluta till resurser i en offentlig nätverks miljö med offentliga tillgängliga slut punkter.

Det här dokumentet beskriver hur du kan skapa och konfigurera Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standard Azure IR
Som standard har varje data fabrik en Azure IR i Server delen som stöder åtgärder i moln data lager och beräknings tjänster i offentliga nätverk. Platsen för Azure IR är autolösa. Om egenskapen **connectVia** inte anges i den länkade tjänst definitionen används standard Azure IR. Du behöver bara skapa en Azure IR när du uttryckligen vill definiera platsen för IR, eller om du vill gruppera aktivitets körningarna i olika IRs i hanterings syfte. 

## <a name="create-azure-ir"></a>Skapa Azure IR

För att skapa och konfigurera en Azure IR kan du använda följande procedurer.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Skapa en Azure IR via Azure PowerShell
Integration Runtime kan skapas med PowerShell-cmdleten **set-AzDataFactoryV2IntegrationRuntime** . Om du vill skapa en Azure IR anger du namnet, platsen och typen för kommandot. Här är ett exempel kommando för att skapa en Azure IR med plats inställt på "Västeuropa":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
För Azure IR måste typen anges som **hanterad** . Du behöver inte ange beräknings information eftersom den hanteras helt elastiskt i molnet. Ange beräknings information som Node-storlek och antal noder när du vill skapa Azure-SSIS IR. Mer information finns i [skapa och konfigurera Azure-SSIS IR](create-azure-ssis-integration-runtime.md).

Du kan konfigurera en befintlig Azure IR att ändra dess plats med hjälp av Set-AzDataFactoryV2IntegrationRuntime PowerShell-cmdleten. Mer information om platsen för en Azure IR finns i [Introduktion till integration runtime](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Skapa en Azure IR via Azure Data Factory användar gränssnitt
Använd följande steg för att skapa en Azure IR med hjälp av Azure Data Factory UI.

1. På sidan för att **komma igång** i Azure Data Factory UI väljer du [fliken Hantera](./author-management-hub.md) i rutan längst till vänster.

   ![Knappen Hantera start sida](media/doc-common-process/get-started-page-manage-button.png)

1. Välj **integrerings körningar** i den vänstra rutan och välj sedan **+ ny** .

   ![Skärm bild som visar integrerings körningar i det vänstra fönstret och knappen + Ny.](media/doc-common-process/manage-new-integration-runtime.png)

1. På sidan **installation av integration runtime** väljer du **Azure, egen värd** och väljer sedan **Fortsätt** . 

1. På följande sida väljer du **Azure** för att skapa en Azure IR och väljer sedan **Fortsätt** .
   ![Skapa Integration Runtime](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Ange ett namn för din Azure IR och välj **skapa** .
   ![Skapa en Azure IR](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Ett popup-meddelande visas när skapandet har slutförts. På sidan **integrerings körningar** ser du till att du ser den nyligen skapade IR-filen i listan.

## <a name="use-azure-ir"></a>Använd Azure IR

När en Azure IR har skapats kan du referera till den i den länkade tjänst definitionen. Nedan visas ett exempel på hur du kan referera till Azure Integration Runtime som skapats ovan från en Azure Storage länkad tjänst:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar om hur du skapar andra typer av integrerings körningar:

- [Skapa Integration Runtime med egen värd](create-self-hosted-integration-runtime.md)
- [Skapa Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)
