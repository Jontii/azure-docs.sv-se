---
title: Kluster versioner som stöds i Azure Service Fabric
description: Lär dig mer om kluster versioner i Azure Service Fabric, inklusive en länk till de senaste versionerna från Service Fabric teamets blogg.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: e3194a59607563656f6624dabf51e9369e9258ee
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843251"
---
# <a name="supported-service-fabric-versions"></a>Service Fabric versioner som stöds

Se till att klustret alltid kör en Azure Service Fabric-version som stöds. I minst 60 dagar efter att vi tillkännaget lanseringen av en ny version av Service Fabric, avslutas stöd för den tidigare versionen. Du hittar meddelanden om nya versioner i [Service Fabric teamets blogg](https://azure.microsoft.com/updates/?product=service-fabric).

För en viss version av Service Fabric runtime kan du använda de angivna eller äldre versionerna av SDK/NuGet-paketen. Nyare versioner av paketen stöds inte och kan ha problem som riktar sig mot äldre kluster eftersom de kan ha funktioner eller protokoll ändringar som inte stöds i dessa miljöer.

I följande dokument finns mer information om hur du håller klustret med en Service Fabric version som stöds:

- [Uppgradera ett Azure Service Fabric-kluster](service-fabric-cluster-upgrade.md)
- [Uppgradera den Service Fabric version som körs på det fristående Windows Server-klustret](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Versioner som stöds

I följande tabell visas de versioner av Service Fabric och deras support slutdatum.

| Service Fabric körning i klustret | Kan uppgradera direkt från kluster version |Kompatibel SDK-eller NuGet-paket version | Slut på support |
| --- | --- |--- | --- |
| Alla kluster versioner före 5.3.121 | 5.1.158.* |Mindre än eller lika med version 2,3 |20 januari 2017 |
| 5,3. * | 5.1.158.* |Mindre än eller lika med version 2,3 |24 februari 2017 |
| 5,4. * | 5.1.158.* |Mindre än eller lika med version 2,4 |10 maj 2017       |
| 5,5. * | 5.4.164.* |Mindre än eller lika med version 2,5 |10 augusti 2017    |
| 5,6. * | 5.4.164.* |Mindre än eller lika med version 2,6 |13 oktober 2017   |
| 5,7. * | 5.4.164.* |Mindre än eller lika med version 2,7 |15 december 2017  |
| 6,0. * | 5.6.205.* |Mindre än eller lika med version 2,8 |30 mars 2018     |
| 6,1. * | 5.7.221.* |Mindre än eller lika med version 3,0 |15 juli 2018      |
| 6,2. * | 6.0.232.* |Mindre än eller lika med version 3,1 |26 oktober 2018   |
| 6,3. * | 6.1.480.* |Mindre än eller lika med version 3,2 |31 mars 2019  |
| 6,4. * | 6.2.301.* |Mindre än eller lika med version 3,3 |15 september 2019 |
| 6,5. * | 6.4.617.* |Mindre än eller lika med version 3,4 |Den 1 augusti 2020 |
| 7.0.466.* | 6.4.664.* |Mindre än eller lika med version 4,0|1 november 2020  |
| 7.0.466.* | 6,5. * |Mindre än eller lika med version 4,0|1 november 2020 |
| 7.0.470.* | 7.0.466.* |Mindre än eller lika med version 4,0 |1 november 2020  |
| 7.0.472.* | 7.0.466.* |Mindre än eller lika med version 4,0 |1 november 2020  |
| 7.0.478.* | 7.0.466.* |Mindre än eller lika med version 4,0 |1 november 2020  |
| 7.1.409.* | 7.0.466.* |Mindre än eller lika med version 4,1 |Aktuell version, så inget slutdatum |
| 7.1.417.* | 7.0.466.* |Mindre än eller lika med version 4,1 |Aktuell version, så inget slutdatum |
| 7.1.428.* | 7.0.466.* |Mindre än eller lika med version 4,1 |Aktuell version, så inget slutdatum |
| 7.1.456.* | 7.0.466.* |Mindre än eller lika med version 4,1 |Aktuell version, så inget slutdatum |
| 7.1.458.* | 7.0.466.* |Mindre än eller lika med version 4,1 |Aktuell version, så inget slutdatum |
| 7.1.459.* | 7.0.466.* |Mindre än eller lika med version 4,1 |Aktuell version, så inget slutdatum |

## <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de operativ system som stöds för de Service Fabric-versioner som stöds.

| Operativsystem | Tidigaste Service Fabric version som stöds |
| --- | --- |
| Windows Server 2012 R2 | Alla versioner |
| Windows Server 2016 | Alla versioner |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |
| Linux Ubuntu 18,04 | 7.1 |

## <a name="supported-version-names"></a>Versions namn som stöds

I följande tabell visas versions namnen för Service Fabric och deras motsvarande versions nummer.

| Versions namn | Windows-versions nummer | Linux-versions nummer |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | NA |
| 5,3 CU1 | 5.3.204.9494 | NA |
| 5,3 CU2 | 5.3.301.9590 | NA |
| 5,3 CU3 | 5.3.311.9590 | NA |
| 5,4 CU2 | 5.4.164.9494 | NA |
| 5,5 CU1 | 5.5.216.0    | NA |
| 5,5 CU2 | 5.5.219.0    | NA |
| 5,5 CU3 | 5.5.227.0    | NA |
| 5,5 CU4 | 5.5.232.0    | NA |
| 5,6 RTO | 5.6.204.9494 | NA |
| 5,6 CU2 | 5.6.210.9494 | NA |
| 5,6 CU3 | 5.6.220.9494 | NA |
| 5,7 RTO | 5.7.198.9494 | NA |
| 5,7 CU4 | 5.7.221.9494 | NA |
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | NA |
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | NA |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | NA |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | NA |
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7,1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7,1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7,1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7,1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7,1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7,1 CU6 | 7.1.459.9590 | 7.1.455.1 |

