---
title: Ansluta till ett Azure Cosmos-konto med Azures privata länk
description: Lär dig hur du säkert får åtkomst till Azure Cosmos-kontot från en virtuell dator genom att skapa en privat slut punkt.
author: asudbring
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 72dbddd449afb262941de93fd812428554496339
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849018"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Ansluta privat till ett Azure Cosmos-konto med Azure Private Link

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. virtuella datorer, att kommunicera privat med privata länk resurser.

I den här artikeln får du lära dig att skapa en virtuell dator i ett virtuellt Azure-nätverk och ett Azure Cosmos-konto med en privat slutpunkt via Azure-portalen. Sedan har du säker åtkomst till Azure Cosmos-kontot från den virtuella datorn.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal.](https://portal.azure.com)

## <a name="create-a-vm"></a>Skapa en virtuell dator

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet ska du skapa ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för åtkomst till din privata länk resurs (ett Azure Cosmos-konto i det här exemplet).

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup|
| **\<virtual-network-name>** | myVirtualNetwork         |
| **\<region-name>**          | USA, västra centrala     |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **beräknings**  >  **virtuell dator**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | **PROJEKT INFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.  |
    | **INSTANS INFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **WestCentralUS**. |
    | Alternativ för tillgänglighet | Lämna standard **ingen redundans för infrastruktur krävs**. |
    | Bild | Välj **Windows Server 2019 Data Center**. |
    | Storlek | Lämna standard **ds1 v2**som standard. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett valfritt användar namn. |
    | lösenordsinställning | Ange ett valfritt lösen ord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösen ordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna standardvärdet **none**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna standardvärdet **Nej**. |
    |||

1. Välj **Nästa: diskar**.

1. I **Skapa en virtuell dator – diskar** lämnar du standardinställningarna och väljer **Nästa: Nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna standard **MyVirtualNetwork**.  |
    | Adressutrymme | Lämna standard **10.1.0.0/24**.|
    | Undernät | Lämna standard **under nätet (10.1.0.0/24)**.|
    | Offentlig IP-adress | Lämna standardvärdet **(New) myVm-IP**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    ||

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration.

1. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

## <a name="create-an-azure-cosmos-account"></a>Skapa ett Azure Cosmos-konto

Skapa ett [Azure Cosmos SQL API-konto](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). För enkelhetens skull kan du skapa ett Azure Cosmos-konto i samma region som de andra resurserna (det vill säga "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Skapa en privat slut punkt för ditt Azure Cosmos-konto

Skapa en privat länk för ditt Azure Cosmos-konto enligt beskrivningen i avsnittet [skapa en privat länk med hjälp av Azure Portal](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) avsnittet i den länkade artikeln.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- *myVm* från Internet på följande sätt:

1. I portalens sökfältet anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol-fil (*. RDP*) och laddar ned den till datorn.

1. Öppna den nedladdade *RDP* -filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Få åtkomst till Azure Cosmos-kontot privat från den virtuella datorn

I det här avsnittet ska du ansluta privat till Azure Cosmos-kontot med hjälp av den privata slut punkten. 

1. Om du vill inkludera IP-adressen och DNS-mappningen loggar du in på den virtuella datorn *myVM*, öppnar `c:\Windows\System32\Drivers\etc\hosts` filen och inkluderar DNS-informationen från föregående steg i följande format:

   [Privat IP-adress] [Konto slut punkt]. Documents. Azure. com

   **Exempel:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. I fjärr skrivbord för *myVM*installerar du [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Välj **Cosmos DB konton (för hands version)** med högerklickning.

1. Välj **Anslut till Cosmos DB**.

1. Välj **API**.

1. Ange anslutnings strängen genom att klistra in informationen som tidigare har kopierats.

1. Välj **Nästa**.

1. Välj **Anslut**.

1. Bläddra i Azure Cosmos-databaser och behållare från *mycosmosaccount*.

1. (Valfritt) Lägg till nya objekt i *mycosmosaccount*.

1. Stäng fjärr skrivbords anslutningen till *myVM*.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den privata slut punkten, Azure Cosmos-kontot och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller: 

1. Skriv *myResourceGroup* i **sökrutan längst** upp i portalen och välj *myResourceGroup* från Sök resultaten.

1. Välj **Ta bort resursgrupp**.

1. Ange *myResourceGroup* för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator i ett virtuellt nätverk, ett Azure Cosmos-konto och en privat slut punkt. Du har anslutit till den virtuella datorn från Internet och på ett säkert sätt kommunicerat med Azure Cosmos-kontot med hjälp av en privat länk.

* Mer information om privata slut punkter finns i [Vad är Azures privata slut punkt?](private-endpoint-overview.md).

* Om du vill veta mer om begränsning av privat slut punkt när du använder med Azure Cosmos DB kan du läsa mer i [Azure privat länk med Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) artikel.
