---
title: Den virtuella Azure-datorn svarar inte när säkerhets principen tillämpades på systemet
description: Den här artikeln innehåller steg för att lösa problem där inläsnings skärmen fastnar när den virtuella datorn inte svarar när säkerhets principen tillämpas på systemet på en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84908224"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>Den virtuella Azure-datorn svarar inte när säkerhets principen tillämpades på systemet

Den här artikeln innehåller steg för att lösa problem där operativ systemet låser sig och slutar svara när en säkerhets princip tillämpas på en virtuell Azure-dator.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar det operativ system som fastnat när du startar med meddelandet:

> ' Tillämpar säkerhets princip på systemet.

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Skärm bild av Start skärmen för Windows Server 2012 R2 har fastnat.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Skärm bild av Start skärmen för Windows Server 2012 R2 har fastnat.":::

## <a name="cause"></a>Orsak

Det finns en mängd olika av möjliga orsaker till det här problemet. Du kommer inte att kunna känna till källan förrän en minnes dumpnings analys har utförts.

## <a name="resolution"></a>Lösning

### <a name="process-overview"></a>Process översikt

1. [Skapa och få åtkomst till en virtuell reparations dator](#create-and-access-a-repair-vm)
2. [Aktivera serie konsol och samling av minnes dum par](#enable-serial-console-and-memory-dump-collection)
3. [Återskapa den virtuella datorn](#rebuild-the-vm)
4. [Samla in minnesdumpen](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Skapa och få åtkomst till en virtuell reparations dator

1. Använd [steg 1-3 i reparations kommandona för virtuella datorer](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att förbereda en reparations-VM.
2. Använd Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Aktivera serie konsol och samling av minnes dum par

Kör det här skriptet om du vill aktivera samling av minnes dum par och seriella konsoler:

1. Öppna en kommando tolk med förhöjd behörighet (kör som administratör).
2. Visa en lista med data från BCD-arkivet och identifiera start programmets identifierare, som du kommer att använda i nästa steg.

     1. För en virtuell dator i generation 1, anger du följande kommando och noterar identifieraren:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        I kommandot ersätter du \<BOOT PARTITON> med bokstaven för partitionen på den anslutna disken som innehåller startmappen.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Skärm bild av Start skärmen för Windows Server 2012 R2 har fastnat." /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Ta bort skadad OS-disk:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>Återskapa den virtuella datorn

Använd [steg 5 i reparations kommandona för virtuella datorer](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att bygga upp den virtuella datorn igen.

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpen

För att lösa det här problemet behöver du först samla in minnesdumpen för kraschen och kontakta supporten med minnesdumpen. Samla in dumpfilen genom att följa dessa steg:

1. Koppla OS-disken till en ny virtuell reparations dator:

    - Använd [steg 1-3 i reparations kommandona för virtuella datorer](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) för att förbereda en ny reparations-VM.
    - Använd Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

2. Leta rätt på dumpfilen och skapa en supportbegäran:

    - På den reparera virtuella datorn går du till Windows-mappen på den anslutna OS-disken. Om operativsystemdisken har enhetsbeteckningen `F` går du till `F:\Windows`.
    - Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.
    - Om du har problem med att hitta Memory. dmp-filen kanske du vill använda [icke-maskbart avbrott (NMI) i serie konsolen i](serial-console-windows.md#use-the-serial-console-for-nmi-calls) stället. Du kan följa guiden för att [skapa en kraschdump-fil med NMI-anrop](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Nästa steg

Om du har problem när du använder principer för lokala användare och grupper ser du [att den virtuella datorn inte svarar när du tillämpar Grupprincip principer för lokala användare och grupper](unresponsive-vm-apply-group-policy.md)