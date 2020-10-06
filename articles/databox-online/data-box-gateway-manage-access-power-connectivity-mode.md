---
title: Azure Data Box Gateway enhets åtkomst, energi läge och anslutnings läge
description: Beskriver hur du hanterar åtkomst, energi och anslutnings läge för den Azure Data Box Gateway enheten som hjälper till att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 27b6d8ca61ed10b5c7362e089fe94d8d64164878
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743870"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Hantera åtkomst, energi och anslutnings läge för din Azure Data Box Gateway

Den här artikeln beskriver hur du hanterar åtkomst, ström och anslutnings läge för din Azure Data Box Gateway. De här åtgärderna utförs via det lokala webb gränssnittet eller Azure Portal. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Hantera enhets åtkomst
> * Hantera anslutnings läge
> * Hantera energi

## <a name="manage-device-access"></a>Hantera enhets åtkomst

Åtkomsten till din Data Box Gateway-enhet styrs av användningen av ett enhets lösen ord. Du kan ändra lösen ordet via det lokala webb gränssnittet. Du kan också återställa enhetens lösen ord i Azure Portal.

### <a name="change-device-password"></a>Ändra enhetens lösenord

Följ de här stegen i det lokala användar gränssnittet för att ändra enhetens lösen ord.

1. I det lokala webb gränssnittet går du till **underhåll > ändra lösen ordet**.
2. Ange det aktuella lösen ordet och sedan det nya lösen ordet. Det angivna lösen ordet måste innehålla mellan 8 och 16 tecken. Lösen ordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Bekräfta det nya lösen ordet.

    ![Ändra lösenord](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Klicka på **ändra lösen ord**.
 
### <a name="reset-device-password"></a>Återställ enhets lösen ord

Återställnings arbets flödet kräver inte att användaren återkallar det gamla lösen ordet och är användbart när lösen ordet går förlorat. Det här arbets flödet utförs i Azure Portal.

1. I Azure Portal går du till **översikt > Återställ administratörs lösen ord**.

    ![Återställa lösenord](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Ange det nya lösen ordet och bekräfta det sedan. Det angivna lösen ordet måste innehålla mellan 8 och 16 tecken. Lösen ordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Klicka på **Återställ**.

    ![Återställ lösen ord 2](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Hantera företagsresurser

Om du vill skapa Azure Stack Edge Pro/Data Box Gateway, IoT Hub och Azure Storage resurs måste du ha behörighet som deltagare eller högre på resurs grupps nivå. Du måste också ha motsvarande resurs leverantörer för att kunna registreras. För alla åtgärder som inbegriper aktiverings nyckel och autentiseringsuppgifter krävs även behörighet att Azure Active Directory Graph API. Dessa beskrivs i följande avsnitt.

### <a name="manage-microsoft-graph-api-permissions"></a>Hantera Microsoft Graph API-behörigheter

När du genererar aktiverings nyckeln för Azure Stack Edge Pro-enheten, eller utföra åtgärder som kräver autentiseringsuppgifter, måste du ha behörighet att Microsoft Graph API. De åtgärder som behöver autentiseringsuppgifter kan vara:

-  Skapa en resurs med ett associerat lagrings konto.
-  Skapa en användare som har åtkomst till resurserna på enheten.

Du bör ha `User` åtkomst till Active Directory klient organisation som du behöver kunna `Read all directory objects` . Du kan inte vara gäst användare eftersom de inte har behörighet till `Read all directory objects` . Om du är gäst, kommer de åtgärder som att generera en aktiverings nyckel att skapa en resurs på din Azure Stack Edge Pro-enhet, vilket innebär att en användare inte kan skapa en användare.

Mer information om hur du ger åtkomst till användare till Microsoft Graph API finns i [referens för Microsoft Graph behörigheter](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrera resurs leverantörer

Om du vill etablera en resurs i Azure (i Azure Resource Manager-modellen) behöver du en resurs leverantör som stöder skapandet av resursen. Om du till exempel vill etablera en virtuell dator bör du ha en Resource-Provider för Microsoft. Compute som är tillgänglig i prenumerationen.
 
Resursprovidrar registreras på prenumerationsnivån. Alla nya Azure-prenumerationer är som standard förregistrerade med en lista över vanliga resursprovidrar. Resurs leverantören för Microsoft. DataBoxEdge ingår inte i den här listan.

Du behöver inte bevilja åtkomst behörigheter till prenumerations nivån för att användarna ska kunna skapa resurser som "Microsoft. DataBoxEdge" i deras resurs grupper som de har ägar rättigheter på, så länge resurs leverantörerna för dessa resurser redan har registrerats.

Innan du försöker skapa en resurs måste du kontrol lera att resurs leverantören är registrerad i prenumerationen. Om resurs leverantören inte är registrerad måste du kontrol lera att användaren som skapar den nya resursen har tillräckligt med behörighet för att registrera den nödvändiga resurs leverantören på prenumerations nivån. Om du inte har gjort det kan du se följande fel:

*Prenumerationen \<Subscription name> har inte behörighet att registrera resurs leverantör (er): Microsoft. DataBoxEdge.*


Om du vill hämta en lista över registrerade resurs leverantörer i den aktuella prenumerationen kör du följande kommando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

För Azure Stack Edge Pro-enhet `Microsoft.DataBoxEdge` ska registreras. För att registrera dig `Microsoft.DataBoxEdge` bör prenumerations administratören köra följande kommando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Mer information om hur du registrerar en resurs leverantör finns i [lösa fel för registrering av resurs leverantör](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Hantera anslutnings läge

Förutom det normala standard läget kan enheten också köras i delvis frånkopplad eller frånkopplat läge. Vart och ett av dessa lägen beskrivs nedan:

- **Delvis frånkopplad** – i det här läget kan enheten inte ladda upp några data till resurserna. de kan dock hanteras via Azure Portal.

    Det här läget används vanligt vis i ett avgiftsbelagdt satellit nätverk och målet är att minimera användningen av nätverks bandbredd. Minimal nätverks förbrukning kan fortfarande uppstå för enhets övervaknings åtgärder.

- **Frånkopplad** – i det här läget är enheten helt frånkopplad från molnet och både moln överföringar och hämtningar är inaktiverade. Enheten kan bara hanteras via det lokala webb gränssnittet.

    Det här läget används vanligt vis när du vill ta enheten offline.

Följ dessa steg om du vill ändra enhets läge:

1. I enhetens lokala webb gränssnitt går du till **konfiguration > moln inställningar**.
2. Inaktivera **moln överföring och hämtning**.
3. Aktivera **Azure Portal hantering**för att köra enheten i delvis frånkopplat läge.

    ![Anslutnings läge](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Inaktivera **Azure Portal hantering**för att köra enheten i frånkopplat läge. Nu kan enheten bara hanteras via det lokala webb gränssnittet.

    ![Anslutnings läge 2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Hantera energi

Du kan stänga av eller starta om den virtuella enheten med hjälp av det lokala webb gränssnittet. Innan du startar om rekommenderar vi att du tar ned resurserna offline på värden och sedan enheten. Den här åtgärden minskar risken för skadade data.

1. I det lokala webb gränssnittet går du till **underhåll > energi inställningar**.
2. Klicka på **Stäng** av eller **starta om** beroende på vad du vill göra.

    ![Energiinställningar](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. När du uppmanas att bekräfta klickar du på **Ja** för att fortsätta.

> [!NOTE]
> Om du stänger av den virtuella enheten måste du starta enheten via hypervisor-hanteringen.
