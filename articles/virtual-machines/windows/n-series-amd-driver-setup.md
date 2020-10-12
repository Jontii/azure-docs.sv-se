---
title: Konfiguration av Azure N-seriens AMD GPU-drivrutin för Windows
description: Konfigurera AMD GPU-drivrutiner för virtuella datorer i N-serien som kör Windows Server eller Windows i Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 74827e78017ad3540709fa0e671762a985976cda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999011"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installera AMD GPU-drivrutiner för virtuella datorer i N-serien som kör Windows

För att kunna dra nytta av GPU-funktionerna i de nya virtuella Azure NVv4-serierna som kör Windows, måste AMD GPU-drivrutinerna vara installerade. [Tillägget för AMD GPU-drivrutinen](../extensions/hpccompute-amd-gpu-windows.md) installerar AMD GPU-drivrutiner på en virtuell NVv4-serie. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure PowerShell eller Azure Resource Manager mallar. Mer information om vilka operativ system och distributions steg som stöds finns i [dokumentationen för AMD GPU-drivrutinen](../extensions/hpccompute-amd-gpu-windows.md) .

Om du väljer att installera AMD GPU-drivrutiner manuellt, innehåller den här artikeln stöd för operativ system, driv rutiner och installations-och verifierings steg.

Endast GPU-drivrutiner som publicerats av Microsoft stöds på virtuella NVv4-datorer. Installera inte GPU-drivrutiner från någon annan källa.

Grundläggande specifikationer, lagrings kapacitet och disk information finns i storlekar för [GPU Windows VM](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

| Operativsystem | Drivrutinen |
| -------- |------------- |
| Windows 10 Enterprise multi-session-build 1903 <br/><br/>Windows 10 – build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Driv rutins installation

1. Anslut via fjärr skrivbord till varje virtuell NVv4-serien.

2. Om du behöver avinstallera den tidigare driv rutins versionen kan du hämta verktyget för AMD-rensning [här](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) använder du inte det verktyg som medföljer den tidigare versionen av driv rutinen.

3. Hämta och installera den senaste driv rutinen.

4. Starta om den virtuella datorn.

## <a name="verify-driver-installation"></a>Verifiera installation av driv rutin

Du kan kontrol lera driv rutins installationen i Enhetshanteraren. I följande exempel visas lyckad konfiguration av kortet Radeon Instinct MI25 på en virtuell Azure-NVv4.
<br />
![Egenskaper för GPU-drivrutin](./media/n-series-amd-driver-setup/device-manager.png)

Du kan använda dxdiag för att kontrol lera GPU-bildskärms egenskaper inklusive video-RAM. I följande exempel visas en 1/2-partition av kortet Radeon Instinct MI25 på en virtuell Azure-NVv4.
<br />
![Egenskaper för GPU-drivrutin](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Om du kör Windows 10 version 1903 eller senare kommer dxdiag inte att visa någon information på fliken "Visa". Använd alternativet "Spara all information" längst ned och utdatafilen visar information om AMD MI25 GPU.

![Egenskaper för GPU-drivrutin](./media/n-series-amd-driver-setup/dxdiag-details.png)
