---
title: Operativ system som stöds, container engines-Azure IoT Edge
description: Lär dig vilka operativ system som kan köra Azure IoT Edge daemon och körning, och vilka behållar motorer som stöds för dina produktions enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a08fc25083ecd6917f658f27cb954391228adf7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188708"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge system som stöds

Den här artikeln innehåller information om vilka system och komponenter som stöds av IoT Edge, oavsett om de är officiellt eller i för hands versionen.

Om du får problem när du använder tjänsten Azure IoT Edge kan du söka efter support på flera sätt. Prova någon av följande kanaler för support:

**Rapporterings buggar** – merparten av utvecklingen som ingår i Azure IoT Edge produkten sker i projektet IoT Edge projekt med öppen källkod. Buggar kan rapporteras på [sidan problem](https://github.com/azure/iotedge/issues) i projektet. Du kan snabbt göra sitt sätt från projektet i till produkt uppdateringar.

**Microsofts kund support team** – användare som har ett [support](https://azure.microsoft.com/support/plans/) avtal kan engagera Microsofts kund support team genom att skapa ett Support ärende direkt från [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Funktions förfrågningar** – Azure IoT Edge produkten spårar funktions förfrågningar via produktens [röst sida](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Behållar motorer

Azure IoT Edge moduler implementeras som behållare, så IoT Edge behöver en behållar motor för att starta dem. Microsoft tillhandahåller en behållar motor, Moby-motor, för att uppfylla det här kravet. Den här behållar motorn är baserad på Moby projekt med öppen källkod. Docker CE och Docker EE är andra populära behållar motorer. De är också baserade på Moby projekt med öppen källkod och är kompatibla med Azure IoT Edge. Microsoft tillhandahåller stöd för bästa prestanda för system som använder dessa behållar motorer. Microsoft kan dock inte leverera korrigeringar för problem i dem. Av den anledningen rekommenderar Microsoft att använda Moby-motor i produktions system.

<br>
<center>

![Moby-motorn som container runtime](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Operativsystem

Azure IoT Edge körs på de flesta operativ system som kan köra behållare. alla dessa system stöds dock inte. Operativ system grupperas i nivåer som representerar den nivå av support användare som kan förväntas.

* Nivå 1-system stöds. För system på nivå 1 är Microsoft:
  * har det här operativ systemet i automatiserade tester
  * tillhandahåller installations paket för dem
* Nivå 2-system är kompatibla med Azure IoT Edge och kan användas relativt enkelt. För nivå 2-system:
  * Microsoft har utfört informella tester på plattformarna eller känner av en partner som kör Azure IoT Edge på plattformen
  * Installations paket för andra plattformar kan fungera på dessa plattformar

Värd operativ systemets familj måste alltid matcha den familj av gäst operativ systemet som används i en moduls behållare. Med andra ord kan du bara använda Linux-behållare på Linux-och Windows-behållare i Windows. När du använder Windows stöds endast process isolerade behållare, inte för Hyper-V-isolerade behållare.  

<br>
<center>

![Värd operativ system matchar gäst operativ system](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Nivå 1

De system som anges i följande tabell stöds av Microsoft, antingen allmänt tillgängliga eller i offentlig för hands version, och testas med varje ny version. 

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS-storlek |  | ![Raspberry Pi OS-utsträckning + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig för hands version  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18,04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Offentlig för hands version |
| [Windows 10 IoT Enterprise](/windows/iot-core/windows-iot-enterprise), build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Core](/windows/iot-core/windows-iot-core), build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](/windows-server/get-started-19/rel-notes-19), build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](/windows/iot-core/windows-server), build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

De Windows-operativsystem som anges ovan är kraven för enheter som kör Windows-behållare i Windows, vilket är den enda konfiguration som stöds för produktion. Azure IoT Edge installations paket för Windows tillåter användning av Linux-behållare i Windows. den här konfigurationen är dock endast för utveckling och testning. 

### <a name="tier-2"></a>Nivå 2

De system som anges i följande tabell anses vara kompatibla med Azure IoT Edge, men testas eller underhålls inte aktivt av Microsoft.

| Operativsystem | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18,04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Vind floden 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspberry Pi OS-Buster <sup>1</sup> |  | ![Raspberry Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20,04 <sup>2</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20,04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20,04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10-system, inklusive Raspberry Pi OS-Buster, använder en version av OpenSSL som IoT Edge inte stöder. Använd följande kommando för att installera en tidigare version innan du installerar IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

<sup>2</sup> Debian 9-paketen från [Azure IoT Edge releases lagrings platsen](https://github.com/Azure/azure-iotedge/releases) bör fungera direkt med Ubuntu 20,04.

## <a name="releases"></a>Versioner

IoT Edge release assets och viktig information finns på sidan med [Azure-iotedge-versioner](https://github.com/Azure/azure-iotedge/releases) . Det här avsnittet visar information från de viktiga anteckningarna som hjälper dig att visualisera komponenterna i varje version enklare.

IoT Edge-komponenter kan installeras eller uppdateras individuellt och är bakåtkompatibla med komponenter från äldre versioner. I följande tabell visas de komponenter som ingår i varje version:

| Frisläpp | Security daemon | Edge Hub<br>Edge-agent | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.10** | 1.0.10 | 1.0.10 | 1.0.10 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

IoT Edge använder Microsoft. Azure. devices. client SDK. Mer information finns i [Azure IoT C# SDK GitHub-lagrings platsen](https://github.com/Azure/azure-iot-sdk-csharp) eller [Azure SDK för .net-referens innehåll](/dotnet/api/overview/azure/iot/client). I följande lista visas den version av klient-SDK: n som varje version testas mot:

* **IoT Edge 1.0.10**: klient-SDK-1.28.0
* **IoT Edge 1.0.9**: klient-SDK-1.21.1
* **IoT Edge 1.0.8**: klient-SDK-1.20.3
* **IoT Edge 1.0.7**: klient-SDK-1.20.1
* **IoT Edge 1.0.6**: klient-SDK-1.17.1
* **IoT Edge 1.0.5**: klient-SDK-1.17.1

## <a name="virtual-machines"></a>Virtual Machines

Azure IoT Edge kan köras i virtuella datorer. Att använda en virtuell dator som en IoT Edge enhet är vanligt när kunder vill utöka den befintliga infrastrukturen med Edge Intelligence. Familjen för värddatorns virtuella dator operativ system måste matcha den familj av gäst operativ systemet som används i en moduls behållare. Detta krav är detsamma som när Azure IoT Edge körs direkt på en enhet. Azure IoT Edge är oberoende av den underliggande Virtualization-tekniken och fungerar i virtuella datorer som drivs av plattformar som Hyper-V och vSphere.

<br>
<center>

![Azure IoT Edge i en virtuell dator](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Minsta system krav

Azure IoT Edge körs bra på enheter så litet som en Raspberry-Pi3 till maskin vara på server nivå. Att välja rätt maskin vara för ditt scenario beror på de arbets belastningar som du vill köra. Att göra det slutliga enhets beslutet kan vara komplicerat. Du kan dock enkelt starta prototyper av en lösning på traditionella bärbara och Station ära datorer.

Upplevelsen under prototypen hjälper dig att hjälpa dig att hjälpa dig med din slutliga enhets val Frågor som du bör ta hänsyn till:

* Hur många moduler finns i arbets belastningen?
* Hur många lager bevarar dina modulers behållare resurser?
* I vilket språk har dina moduler skrivits?
* Hur mycket data kommer dina moduler att bearbetas?
* Behöver dina moduler all specialiserad maskin vara för att påskynda sina arbets belastningar?
* Vilka är de önskade prestanda egenskaperna för din lösning?
* Vilken är din maskin varu budget?