---
title: inkludera fil
description: inkludera fil
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: b7bdeeedaac65f67a3224e824c19e8cad794682b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507199"
---
#### <a name="to-create-a-new-service"></a>Skapa en ny tjänst

1.  Logga in på Azure Portal på den här URL: en med hjälp av dina Microsoft-konto autentiseringsuppgifter <https://portal.azure.com/> . Om du distribuerar enheten i myndighets portalen loggar du in på: <https://portal.azure.us/>

2.  I Azure Portal klickar du på **+ skapa en** &gt; **Storage** &gt; **virtuell serie**för resurs lagring StorSimple.

    ![Skapa ny tjänst](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  Gör följande på bladet **StorSimple Enhetshanteraren** som öppnas:

    1.  Ange ett unikt **resursnamn** för tjänsten. Resurs namnet är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 2 och 50 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

    2.  Välj en **prenumeration** från listrutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet syns inte om du bara har en prenumeration.

    3.  För **resurs grupp**väljer du en befintlig eller skapar en ny grupp. Mer information finns i avsnittet om [Azure-resursgrupper](/azure/azure-resource-manager/management/manage-resource-groups-portal).

    4.  Ange en **plats** för din tjänst. Mer information om vilka tjänster som är tillgängliga i vilken region finns i [Azure-regioner](https://azure.microsoft.com/regions/#services) . I allmänhet väljer du en **plats** närmast den geografiska region där du vill distribuera enheten. Du kan också ta med följande i beräkningarna:

        -   Om du har befintliga arbets belastningar i Azure som du även tänker distribuera med din StorSimple-enhet rekommenderar vi att du använder det data centret.

        -   Din StorSimple-Enhetshanteraren och Azure-lagring kan finnas på två olika platser. I så fall måste du skapa StorSimple Device Manager-kontot och Azure-lagringskontot separat. Om du vill skapa ett Azure Storage-konto går du till Azure Storage i Azure Portal och följer stegen som beskrivs i [skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). När du har skapat kontot lägger du till det till StorSimple Device Manager-tjänsten genom att följa stegen i [Konfigurera ett nytt lagringskonto för tjänsten](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service).

        -   Om du distribuerar den virtuella enheten i myndighets portalen är StorSimple Enhetshanteraren-tjänsten tillgänglig i Iowa-och US-platser i USA.

    5.  Välj **skapa ett nytt Azure Storage-konto** för att automatiskt skapa ett lagrings konto med tjänsten. Ange ett **lagrings konto namn**. Avmarkera kryssrutan om du behöver ha din data på en annan plats.

    6.  Markera **Fäst på instrumentpanelen** om du vill skapa en snabblänk till tjänsten på instrumentpanelen.

    7.  Skapa StorSimple Device Manager genom att klicka på **Skapa**.

        ![Skapa ny tjänst](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Du dirigeras till **tjänstens** landnings sida. Det tar några minuter att skapa tjänsten. När tjänsten har skapats, kommer du att meddelas och tjänstens status kommer att ändras till **Aktiv**.


