---
title: Installera Azure IoT Edge för Linux i Windows | Microsoft Docs
description: Azure IoT Edge Installationsinstruktioner för Linux-behållare i Windows 10, Windows Server och Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: c3a23e0c2546da55f977d589eb38607994d3902b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88611798"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Använda IoT Edge i Windows för att köra Linux-behållare

Testa IoT Edge moduler för Linux-enheter med hjälp av en Windows-dator.

I ett produktions scenario ska Windows-enheter endast köra Windows-behållare. Ett vanligt utvecklings scenario är dock att använda en Windows-dator för att bygga IoT Edge moduler för Linux-enheter. Med IoT Edge runtime för Windows kan du köra Linux-behållare i **testnings-och utvecklings** syfte.

Den här artikeln innehåller anvisningar för att installera Azure IoT Edge runtime med hjälp av Linux-behållare på ditt Windows x64-system (AMD/Intel). Läs mer om installations programmet för IoT Edge runtime, inklusive information om alla installations parametrar, i [installera Azure IoT Edge runtime i Windows](how-to-install-iot-edge-windows.md).

För information om vad som ingår i den senaste versionen av IoT Edge, se [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Förutsättningar

Använd det här avsnittet för att se om din Windows-enhet har stöd för IoT Edge och förbereda den för en behållar motor före installationen.

### <a name="supported-windows-versions"></a>Windows-versioner som stöds

Azure IoT Edge med Linux-behållare kan köras på alla versioner av Windows som uppfyller [kraven för Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Om du vill installera IoT Edge på en virtuell dator aktiverar du kapslad virtualisering och allokerar minst 2 GB minne. Hur du aktiverar kapslad virtualisering är olika beroende på vilken hypervisor du använder. För Hyper-V kan virtuella datorer i generation 2 ha kapslad virtualisering aktiverat som standard. För VMware finns det en växling för att aktivera funktionen på den virtuella datorn.

### <a name="prepare-the-container-engine"></a>Förbereda behållar motorn

Azure IoT Edge förlitar sig på en [OCI-kompatibel](https://www.opencontainers.org/) container motor. Den största konfigurations skillnaden mellan att köra Windows-och Linux-behållare på en Windows-dator är att den IoT Edge installationen innehåller en Windows container runtime, men du måste ange en egen runtime för Linux-behållare innan du installerar IoT Edge.

Om du vill konfigurera en Windows-dator för att utveckla och testa behållare för Linux-enheter kan du använda [Docker Desktop](https://www.docker.com/docker-windows) som behållar motor. Du måste installera Docker och konfigurera den så att den [använder Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) innan du installerar IoT Edge.  

Om din IoT Edge enhet är en Windows-dator kontrollerar du att den uppfyller [system kraven](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) för Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Installera IoT Edge på en ny enhet

>[!NOTE]
>Azure IoT Edge program varu paket omfattas av licens villkoren som finns i paketen (i licens katalogen). Läs licens villkoren innan du använder paketet. Din installation och användning av paketet utgör ditt godkännande av dessa villkor. Om du inte accepterar licens villkoren ska du inte använda paketet.

Ett PowerShell-skript laddar ned och installerar Azure IoT Edge Security daemon. Security daemon startar sedan den första av två körnings moduler, IoT Edge agent, som möjliggör fjärrdistributioner av andra moduler.

När du installerar IoT Edge runtime för första gången på en enhet måste du etablera enheten med en identitet från en IoT-hubb. En enskild IoT Edge enhet kan tillhandahållas manuellt med hjälp av en enhets anslutnings sträng från IoT Hub. Du kan också använda enhets etablerings tjänsten för att etablera enheter automatiskt, vilket är användbart när du har många enheter att konfigurera.

Du kan läsa mer om de olika installations alternativen och parametrarna i artikeln [installera Azure IoT Edge runtime i Windows](how-to-install-iot-edge-windows.md). När du har Docker-skrivbordet installerat och konfigurerat för Linux-behållare, deklareras den huvudsakliga installations skillnaden Linux med parametern **-containern** . Exempel:

1. Registrera en ny IoT Edge enhet och hämta anslutnings strängen för enheten om du inte redan gjort det. Kopiera anslutnings strängen och Använd den senare i det här avsnittet. Du kan utföra det här steget med följande verktyg:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Kör PowerShell som administratör.

   >[!NOTE]
   >Använd en AMD64-session av PowerShell för att installera IoT Edge, inte PowerShell (x86). Om du inte är säker på vilken typ av session du använder kör du följande kommando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Kommandot **Deploy-IoTEdge** kontrollerar att Windows-datorn finns på en version som stöds, aktiverar funktionen containers och laddar sedan ned Moby Runtime (som inte används för Linux-behållare) och IoT Edge Runtime. Standardvärdet för Windows-behållare är att deklarera Linux som önskat behållar operativ system.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. I det här läget kan IoT core-enheter startas om automatiskt. Andra Windows 10-eller Windows Server-enheter kan bli ombedd att starta om. Om så är fallet startar du om enheten nu. När enheten är klar kör du PowerShell som administratör igen.

5. Kommandot **Initialize-IoTEdge** konfigurerar IoT Edge runtime på din dator. Kommandot är standardvärdet för manuell etablering med en enhets anslutnings sträng. Deklarera Linux som det önskade behållar operativ systemet igen.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. När du uppmanas till det anger du den enhets anslutnings sträng som du hämtade i steg 1. Enhets anslutnings strängen kopplar den fysiska enheten med ett enhets-ID i IoT Hub.

   Enhets anslutnings strängen har följande format och ska inte innehålla citat tecken: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Kontrol lera status för den IoT Edge tjänsten:

```powershell
Get-Service iotedge
```

Undersök tjänst loggar från de senaste 5 minuterna:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Kör en automatisk kontroll av de vanligaste konfigurations-och nätverks felen:

```powershell
iotedge check
```

Lista med moduler som körs. När en ny installation har slutförts är den enda modul som du bör se **edgeAgent**. När du har [distribuerat IoT Edge moduler](how-to-deploy-modules-portal.md) för första gången, kommer den andra systemmodulen, **edgeHub**, att starta även på enheten.

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge enhet som har installerats med körnings miljön kan du [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md).

Om du har problem med att installera IoT Edge korrekt, kan du kolla in [fel söknings](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).
