---
title: Skapa en moln tjänst behållare med PowerShell | Microsoft Docs
description: Den här artikeln beskriver hur du skapar en moln tjänst behållare med PowerShell. Behållaren är värd för webb-och arbets roller.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: d40a5b64cc8018f45bf08158ce808b2baae27962
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87049095"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Använd ett Azure PowerShell-kommando för att skapa en tom moln tjänst behållare

Den här artikeln förklarar hur du snabbt skapar en Cloud Services behållare med hjälp av Azure PowerShell-cmdletar. Följ stegen nedan:

1. Installera Microsoft Azure PowerShell-cmdleten från sidan [Azure PowerShell nedladdningar](https://aka.ms/webpi-azps) .
2. Öppna PowerShell-Kommandotolken.
3. Använd [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-4.0.0) för att logga in.

   > [!NOTE]
   > Mer information om hur du installerar Azure PowerShell-cmdlet och ansluter till din Azure-prenumeration finns i så [här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).
   >
   >
4. Använd cmdleten **New-AzureService** för att skapa en tom moln tjänst behållare i Azure.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Följ det här exemplet för att anropa cmdleten:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Mer information om hur du skapar Azure Cloud Service får du genom att köra:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Nästa steg

* Om du vill hantera moln tjänst distributionen läser du kommandona [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService?view=azuresmps-4.0.0)och [set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice?view=azuresmps-4.0.0) . Du kan också se [hur du konfigurerar Cloud Services](cloud-services-how-to-configure-portal.md) för ytterligare information.
* Information om hur du publicerar ditt moln tjänst projekt till Azure finns i  **PublishCloudService.ps1** kod exempel från [Arkiverad moln tjänst lagrings plats](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).