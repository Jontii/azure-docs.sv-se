---
title: Så här kör du ett program med Fortanix enklaven Manager
description: Lär dig hur du konverterar dina behållar avbildningar med hjälp av Fortanix enklaven Manager
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: d46354b18d5c3317cc1ed67d839feb26b7b6b5d0
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815852"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>Så här gör du: kör ett program med Fortanix enklaven Manager 

Börja köra ditt program i Azures konfidentiella data behandling med [Fortanix enklaven Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) och [Fortanix Node agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) från [Fortanix](https://www.fortanix.com/).


Fortanix är en tredjeparts program varu leverantör med produkter och tjänster som bygger på Azure-infrastrukturen. Det finns andra leverantörer av tredje part som erbjuder liknande tjänster för konfidentiell behandling på Azure, till exempel [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) och [Scone](https://sconedocs.github.io).  

> [!Note] 
 > DE PRODUKTER SOM REFERERAS I DETTA DOKUMENT ÄR INTE UNDERSTYRDA AV MICROSOFT. MICROSOFT TILLHANDAHÅLLER DEN HÄR INFORMATIONEN ENDAST SOM EN BEKVÄMLIGHET, OCH REFERENSEN TILL DESSA PRODUKTER FRÅN ANDRA TILLVERKARE ÄN MICROSOFT KRÄVER INTE MICROSOFT.



Den här självstudien visar hur du konverterar program avbildningen till en konfidentiellt Compute-skyddad avbildning. I den här miljön används [Fortanix](https://www.fortanix.com/) -programvara som drivs av Azures DCsv2-serien Intel SGX-aktiverade virtuella datorer. Den här lösningen dirigerar kritiska säkerhets principer, till exempel identitets verifiering och data åtkomst kontroll.

 För Fortanix support ansluter du till Fortanix- [slack-communityn](https://fortanix.com/community/) och använder kanal #enclavemanager.


## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har ett Fortanix enklaven Manager-konto måste du [Registrera](https://em.fortanix.com/auth/sign-up) dig innan du börjar.
1. Ett privat [Docker](https://docs.docker.com/) -register för att skicka konverterade program avbildningar.
1. Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

> [!NOTE]
> De kostnads fria utvärderings kontona har inte åtkomst till de virtuella datorerna som används i den här självstudien. Uppgradera till en prenumeration där du betalar per användning.

## <a name="add-an-application-to-fortanix-enclave-manager"></a>Lägga till ett program i Fortanix enklaven Manager
1. Logga in på [FORTANIX EM](https://fortanix.com)
1. Gå till sidan **konton** och välj **Lägg till konto** för att skapa ett nytt konto. 
    
![Skapa ett konto](media/how-to-fortanix-enclave-manager/create-account.png)

1. När ditt konto har skapats trycker du på **Välj** för att välja det nyligen skapade kontot. Nu kan vi börja registrera Compute-noderna och skapa program. 
1. Klicka på knappen **+ program** för att lägga till ett program. I det här exemplet ska vi lägga till ett enklaven OS-program för en kolv. 

1. Välj knappen **Lägg till** för enklaven OS-programmet. 

    ![Lägga till ett program](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > I den här självstudien beskrivs bara att lägga till enklaven OS-program. [Läs mer](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager) om hur du ansluter EdP Rust-program till Fortanix enklaven Manager. 

6. I den här självstudien använder vi Fortanix Docker-registret för exempel programmet. Fyll i informationen från följande information. Använd ditt privata Docker-register för att behålla avbildningen av utdata. 
 
    - **Program namn**: python-program Server
    - **Beskrivning**: python-mätkolv-Server
    - **Namn på inspelnings avbildning**: fortanix/python-flaska
    - **Namn på utdata-avbildning**: fortanx-Private/python-mätkolv-SGX
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Minnes storlek**: 1 GB
    - **Antal trådar**: 128

    *Valfritt*: kör programmet.
    - **Docker-hubb**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **App**: fortanix/python-flaska

        Kör följande kommando:
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. Lägg till ett certifikat. Fyll i informationen med hjälp av informationen nedan och välj sedan **Nästa**:
    - **Domän**: MyApp. domain. dom
    - **Typ**: certifikat utfärdat av enklaven Manager 
    - **Nyckel Sök väg**:/appkey.pem
    - **Nyckel typ**: RSA
    - **Certifikat Sök väg**:/appcert.pem
    - **RSA-nyckel storlek**: 2048 bitar
    

## <a name="create-an-image"></a>Skapa en avbildning
En Fortanix EM-bild är en program varu version eller version av ett program. Varje avbildning är associerad med en enklaven-hash (MRENCLAVE). 
1. På sidan **Lägg till avbildning** anger du **autentiseringsuppgifterna** för **avbildnings namnet på utdata**. Dessa autentiseringsuppgifter används för att få åtkomst till det privata Docker-registret där avbildningen ska flyttas. 

    ![Skapa avbildning](media/how-to-fortanix-enclave-manager/create-image.png)
1. Ange avbildnings tag gen och välj **skapa**.

    ![Lägg till tagg](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>Lista över tillåtna domäner och avbildningar 
Ett program vars domän läggs till i listan över tillåtna kommer att få ett TLS-certifikat från Fortanix enklaven Manager. När ett program körs från den konverterade avbildningen kommer det att försöka kontakta Fortanix enklaven Manager. Programmet kommer sedan att fråga efter ett TLS-certifikat. 

Växla till fliken **aktiviteter** till vänster och godkänn väntande begär Anden för att tillåta domänen och avbildningen. 

## <a name="enroll-compute-node-agent-in-azure"></a>Registrera Compute Node-agenten i Azure

### <a name="generate-and-copy-join-token"></a>Generera och kopiera kopplings-token
I Fortanix enklaven Manager skapar du en token. Med den här token kan en Compute-nod i Azure autentisera sig själv. Du måste ge den här token till din virtuella Azure-dator.
1. I hanterings konsolen väljer du knappen **+ Registrera nod** . 
1. Välj **generera token** för att generera kopplings-token. Kopiera token.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Registrera noder i Fortanix Node agent på Azure Marketplace

Om du skapar en Fortanix-Node-agent distribueras en virtuell dator, ett nätverks gränssnitt, ett virtuellt nätverk, en nätverks säkerhets grupp och en offentlig IP-adress till din Azure-resurs grupp. Din Azure-prenumeration kommer att faktureras per timme för den virtuella datorn. Innan du skapar en Fortanix Node-agent granskar du [pris sidan för virtuella](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Azure-datorer för DCsv2-serien. Ta bort Azure-resurser när de inte används. 

1. Gå till [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) och logga in med dina Azure-autentiseringsuppgifter.
1. I Sök fältet skriver du **Fortanix konfidentiella data behandling Node agent**. Välj den app som visas i sökrutan som heter **Fortanix konfidentiella Computing Node agent** för att navigera till erbjudandets start sida. 
     ![Sök på Marketplace](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. Välj **Hämta nu**, Fyll i din information om det behövs och välj **Fortsätt**. Du kommer att omdirigeras till Azure Portal. 
1. Välj **skapa** för att ange distributions sidan för Fortanix-konfidentiell dator hantering.
1. På den här sidan anger du information för att distribuera en virtuell dator. Mer specifikt är den virtuella datorn en DCsv2-serien Intel SGX-aktiverad virtuell dator från Azure med Fortanix Node Agent-programvara installerad. Med Node-agenten kan den konverterade avbildningen köras säkert på Intel SGX-noder i Azure.  Välj den **prenumeration** och **resurs grupp** där du vill distribuera den virtuella datorn och de tillhör ande resurserna. 
 
    > [!NOTE]
    > Det finns begränsningar när du distribuerar virtuella datorer i DCsv2-serien i Azure. Du kan behöva begära kvot för ytterligare kärnor. Läs om [konfidentiella data behandlings lösningar på virtuella Azure-datorer](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solution) för mer information. 

1. Välj en tillgänglig region.
1. Ange ett namn för den virtuella datorn i fältet **nodnamn** . 
1. Ange och användar namn och lösen ord (eller SSH-nyckel) för att autentisera till den virtuella datorn.
1. Lämna standard storleken för OS-disken som 200 och välj en storlek för virtuell dator (Standard_DC4s_v2 räcker för den här själv studie kursen)
1. Klistra in token som genererades tidigare i fältet **koppla token** .

     ![distribuera resurs](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. Välj **Granska + skapa**. Se till att verifierings passen är klar och välj sedan **skapa**. När alla resurser har distribuerats är Compute-noden nu registrerad i enklaven Manager. 

## <a name="run-the-application-image-on-the-compute-node"></a>Kör program avbildningen på Compute-noden
Kör programmet genom att köra följande kommando. Se till att ändra nodens IP-adress, port och konverterade avbildnings namn som indata för ditt specifika program. 
 
I den här självstudien är kommandot som ska köras:     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

vilken 
- *52.152.206.164* är värd-IP för Node-agenten
- *9092* är porten som Node agent lyssnar på
- *fortanix-Private/python-flaskan-SGX* är den konverterade app som finns på fliken avbildningar under kolumnen **avbildnings namn** i tabellen **images** i fortanix-enklaven hantera webb portalen. 
    
## <a name="verify-and-monitor-the-running-application"></a>Verifiera och övervaka det program som körs
1. Gå tillbaka till [Fortanix enklaven Manager](https://em.fortanix.com/console)
1. Se till att du arbetar inuti det **konto** där du registrerade noden
1. Navigera till **hanterings konsolen** genom att välja den översta ikonen i det vänstra navigerings fönstret. 
1. Välj fliken **program**
1. Kontrol lera att det finns ett program som körs med en associerad Compute-nod


## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resurs gruppen, den virtuella datorn och de associerade resurserna. Om du tar bort resurs gruppen avregistreras de noder som är kopplade till den konverterade avbildningen. 

Välj resurs gruppen för den virtuella datorn och välj sedan **ta bort**. Bekräfta resurs gruppens namn för att slutföra borttagningen av resurserna. \

Om du vill ta bort det Fortanix enklaven Manager-konto som du har skapat går du till [sidan konton](https://em.fortanix.com/accounts) i enklaven Manager. Hovra över det konto som du vill ta bort. Välj de lodräta svarta punkterna i det övre högra hörnet och välj **ta bort konto**.

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du Fortanix-verktyg för att konvertera program avbildningen så att den körs ovanpå en konfidentiell dator. Mer information om konfidentiell dator användning av virtuella datorer i Azure finns i [lösningar på Virtual Machines](virtual-machine-solutions.md). 

Mer information om Azures tjänster för konfidentiell behandling finns i [Översikt över Azure konfidentiell dator användning](overview.md)

