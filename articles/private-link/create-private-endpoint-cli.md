---
title: Snabb start – skapa en privat Azure-slutpunkt med Azure CLI
description: Läs om den privata Azure-slutpunkten i den här snabb starten
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: e7c098ba06086781306960f76978aac9e4fa06bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "87502672"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Snabb start: skapa en privat slut punkt med Azure CLI

Privat slut punkt är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, t. ex. virtuella datorer, att kommunicera privat med privata länk resurser. I den här snabb starten får du lära dig hur du skapar en virtuell dator i ett virtuellt nätverk, en server i SQL Database med en privat slut punkt med hjälp av Azure CLI. Sedan kan du komma åt den virtuella datorn till och säkert komma åt den privata länk resursen (en privat server i SQL Database i det här exemplet).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i stället, måste du köra Azure CLI version 2.0.28 eller senare i den här snabbstarten. Kör `az --version` för att hitta den installerade versionen. Se [Installera Azure CLI](/cli/azure/install-azure-cli) för installations- eller uppgraderingsinformation.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa en resurs måste du skapa en resurs grupp som är värd för Virtual Network. Skapa en resursgrupp med [az group create](/cli/azure/group). I det här exemplet skapas en resurs grupp med namnet *myResourceGroup* på *westcentralus* -platsen:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa en Virtual Network med [AZ Network VNet Create](/cli/azure/network/vnet). I det här exemplet skapas en standard Virtual Network med namnet *myVirtualNetwork* med ett undernät med namnet *undernät*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Inaktivera privata slut punkts principer för undernät

Azure distribuerar resurser till ett undernät i ett virtuellt nätverk, så du måste skapa eller uppdatera under nätet för att inaktivera nätverks principer för privata slut punkter. Uppdatera en under näts konfiguration med namnet *mitt undernät* med [AZ Network VNet Subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator med AZ VM Create. När du uppmanas anger du ett lösen ord som ska användas som inloggnings uppgifter för den virtuella datorn. I det här exemplet skapas en virtuell dator med namnet *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```

Anteckna den offentliga IP-adressen för den virtuella datorn. Du kommer att använda den här adressen när du ansluter till den virtuella datorn från Internet i nästa steg.

## <a name="create-a-server-in-sql-database"></a>Skapa en server i SQL Database

Skapa en server i SQL Database med kommandot AZ SQL Server Create. Kom ihåg att namnet på servern måste vara unikt i Azure, så Ersätt plats hållarens värde inom hakparenteser med ditt eget unika värde:

```azurecli-interactive
# Create a server in the resource group
az sql server create \
    --name "myserver"\
    --resource-group myResourceGroup \
    --location WestUS \
    --admin-user "sqladmin" \
    --admin-password "CHANGE_PASSWORD_1"

# Create a database in the server with zone redundancy as false
az sql db create \
    --resource-group myResourceGroup  \
    --server myserver \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen4 \
    --capacity 1
```

Server-ID: t liknar att  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/myserver.``` du använder Server-ID i nästa steg.

## <a name="create-the-private-endpoint"></a>Skapa den privata slut punkten

Skapa en privat slut punkt för den logiska SQL-servern i Virtual Network:

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Konfigurera Privat DNS zon

Skapa en Privat DNS zon för SQL Database domän, skapa en kopplings länk med Virtual Network och skapa en DNS-zon för att koppla den privata slut punkten till Privat DNS zon. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \
   --name  "privatelink.database.windows.net"
az network private-dns link vnet create --resource-group myResourceGroup \
   --zone-name  "privatelink.database.windows.net"\
   --name MyDNSLink \
   --virtual-network myVirtualNetwork \
   --registration-enabled false
az network private-endpoint dns-zone-group create \
   --resource-group myResourceGroup \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- *myVm* från Internet på följande sätt:

1. I portalens sökfältet anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Hämta RDP-fil**. Azure skapar en Remote Desktop Protocol-fil (*. RDP*) och laddar ned den till datorn.

1. Öppna den nedladdade RDP *-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  

## <a name="access-sql-database-privately-from-the-vm"></a>Åtkomst SQL Database privat från den virtuella datorn

I det här avsnittet ska du ansluta till SQL Database från den virtuella datorn med hjälp av den privata slut punkten.

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.
2. Ange nslookup-myserver.database.windows.net

   Du får ett meddelande som liknar detta:

    ```
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  myserver.database.windows.net
    ```

3. Installera SQL Server Management Studio
4. I Anslut till server anger eller väljer du den här informationen:

   - Server Typ: Välj databas motor.
   - Server Namn: Välj myserver.database.windows.net
   - Användar namn: Ange ett användar namn som angavs vid skapandet.
   - Lösen ord: Ange ett lösen ord som anges när du skapar.
   - Kom ihåg lösen ord: Välj Ja.

5. Välj **Anslut**.
6. Bläddra bland **databaser** från menyn till vänster.
7. Du kan också Skapa eller fråga efter information från en *databas*
8. Stäng fjärr skrivbords anslutningen till *myVm*.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda AZ Group Delete för att ta bort resurs gruppen och alla resurser den har:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azures privata länk](private-link-overview.md)
