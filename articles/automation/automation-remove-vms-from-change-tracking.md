---
title: Ta bort virtuella datorer från Azure Automation Ändringsspårning och inventering
description: Den här artikeln beskriver hur du tar bort virtuella datorer från Ändringsspårning och inventering.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84169462"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Ta bort virtuella datorer från Ändringsspårning och inventering

När du är klar med att distribuera ändringar av virtuella datorer i din miljö kan du ta bort dem från [ändringsspårning-och inventerings](change-tracking.md) funktionen.

## <a name="to-remove-your-vms"></a>Ta bort dina virtuella datorer

1. Från ditt Automation-konto väljer du **ändrings spårning** eller **inventering** under **konfigurations hantering**.

2. Använd följande kommando för att identifiera UUID för en virtuell dator som du vill ta bort från hanteringen.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. I arbets ytan Log Analytics under **Allmänt**går du till sparade sökningar efter omfattnings konfigurationen `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. För den sparade sökningen `MicrosoftDefaultComputerGroup` klickar du på ellipsen till höger och väljer **Redigera**. 

5. Ta bort UUID för den virtuella datorn.

6. Upprepa stegen för alla andra virtuella datorer som ska tas bort.

7. Spara den sparade sökningen när du är klar med att redigera den. 

## <a name="next-steps"></a>Nästa steg

* Information om hur du fortsätter att arbeta med Ändringsspårning och inventering finns i [hantera ändringsspårning och inventering](change-tracking-file-contents.md).
* Information om hur du löser allmänna funktions problem finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).