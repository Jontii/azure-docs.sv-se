---
title: Felsöka din lokala Azure Service Fabric Cluster-installation
description: Den här artikeln beskriver en uppsättning förslag för fel sökning av ditt lokala utvecklings kluster
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 76ca0bb7b81b3896538f08ff2ef52ed1ac6b363f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091614"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Felsöka konfigurationen av ditt lokala utvecklingskluster
Om du stöter på ett problem när du interagerar med ditt lokala Azure Service Fabric Development-kluster kan du läsa följande förslag för möjliga lösningar.

## <a name="cluster-setup-failures"></a>Kluster konfigurations fel
### <a name="cannot-clean-up-service-fabric-logs"></a>Det går inte att rensa Service Fabric loggar
#### <a name="problem"></a>Problem
När du kör DevClusterSetup-skriptet visas följande fel:

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>Lösning
Stäng det aktuella PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör. Nu kan du köra skriptet.

## <a name="cluster-connection-failures"></a>Kluster anslutnings fel

### <a name="type-initialization-exception"></a>Typ initierings undantag
#### <a name="problem"></a>Problem
När du ansluter till klustret i PowerShell visas fel TypeInitializationException för system. Fabric. Common. AppTrace.

#### <a name="solution"></a>Lösning
Path-variabeln angavs inte korrekt under installationen. Logga ut från Windows och logga in igen. Detta uppdaterar din sökväg.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Kluster anslutningen Miss lyckas med "objektet är stängt"
#### <a name="problem"></a>Problem
Ett anrop till Connect-ServiceFabricCluster Miss lyckas med ett fel som detta:

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>Lösning
Stäng det aktuella PowerShell-fönstret och öppna ett nytt PowerShell-fönster som administratör.

### <a name="fabric-connection-denied-exception"></a>Undantag vid nekad Fabric-anslutning
#### <a name="problem"></a>Problem
När du felsöker från Visual Studio får du ett FabricConnectionDeniedException-fel.

#### <a name="solution"></a>Lösning
Det här felet uppstår vanligt vis när du försöker starta en tjänst värd process manuellt.

Kontrol lera att du inte har angett några tjänst projekt som start projekt i din lösning. Endast Service Fabric program projekt ska anges som start projekt.

> [!TIP]
> Om det går att använda det lokala klustret på ett onormalt sätt i följande installation, kan du återställa det med det lokala kluster hanterarens program i system fältet. Detta tar bort det befintliga klustret och konfigurerar ett nytt. Observera att alla distribuerade program och associerade data tas bort.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Förstå och felsöka ditt kluster med system hälso rapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualisera ditt kluster med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

