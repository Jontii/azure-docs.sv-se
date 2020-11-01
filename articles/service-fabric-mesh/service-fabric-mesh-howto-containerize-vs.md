---
title: Containeranpassa en befintlig .NET-app för Service Fabric Mesh
description: Lägg till Service Fabric nät behållar Dirigerings stöd till ASP.NET-och konsol projekt som använder det fullständiga .NET-ramverket.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: 0f236292fff0d0e806e6eec32e1e058cbf67545c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93144485"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Containeranpassa en befintlig .NET-app för Service Fabric Mesh

Den här artikeln visar hur du lägger till stöd för Service Fabric Mesh-containerorkestrering i en befintlig .NET-app.

I Visual Studio 2017 kan du lägga till stöd för containeranpassning i ASP.NET- och konsolprojekt som använder fullständiga .NET Framework.

> [!NOTE]
> .NET **Core** -projekt stöds inte för närvarande.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Se till att du har [konfigurerat utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Det omfattar att installera Service Fabric-runtime, SDK, Docker, Visual Studio 2017, och skapa ett lokalt kluster.

## <a name="open-an-existing-net-app"></a>Öppna en befintlig .NET-app

Öppna appen där du vill lägga till stöd för containerorkestrering.

Om du vill prova ett exempel kan du använda [eShop](https://github.com/MikkelHegn/ContainersSFLab)-kodexemplet. Resten av den här artikeln förutsätter att vi använder det projektet, även om du kan använda de här anvisningarna i ett eget projekt.

Hämta en kopia av **eShop** -projektet:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

När det har laddats ned går du till Visual Studio 2017 och öppnar **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln** .

## <a name="add-container-support"></a>Lägg till containerstöd
 
Lägg till stöd för containerorkestrering i ett befintligt ASP.NET- eller konsolprojekt med hjälp av Service Fabric Mesh-verktygen på följande sätt:

Högerklicka på projektnamnet i Visual Studio Solution Explorer (i exemplet **eShopLegacyWebForms** ) och välj sedan **Add** > **Container Orchestrator Support** (Lägg till > Stöd för containerorkestrering).
Dialogrutan **Add Container Orchestrator Support** (Lägg till stöd för containerorkestrering) visas.

![Dialogrutan Add Container Orchestrator Support (Lägg till stöd för containerorkestrering) i Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Välj **Service Fabric Mesh** i listrutan och klicka på **OK** .


>[!NOTE]
> Från och med den 2 november 2020 [gäller nedladdnings hastighets gränserna](https://docs.docker.com/docker-hub/download-rate-limit/) för anonyma och autentiserade begär anden till Docker Hub från Docker-fri Plans konton och tillämpas med IP-adress. Mer information finns i [autentisera med Docker Hub](https://docs.microsoft.com/azure/container-registry/buffer-gate-public-content#authenticate-with-docker-hub).
>
> För att undvika begränsad hastighet kontrollerar du att standardvärdet `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` i din Dockerfile ersätts med `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

Verktyget kontrollerar sedan att Docker är installerat, lägger till en Dockerfile i projektet och hämtar en Docker-avbildning för projektet.  
Ett Service Fabric Mesh-programprojekt läggs till i lösningen. Det innehåller dina Mesh-publiceringsprofiler och -konfigurationsfiler. Projektets namn är detsamma som ditt projektnamn, med ”Application” på slutet, till exempel **eShopLegacyWebFormsApplication** . 

I det nya Mesh-projektet visas två mappar som du bör känna till:
- **App Resources** (Appresurser) som innehåller YAML-filer som beskriver ytterligare Mesh-resurser, till exempel nätverket.
- **Service Resources** (Tjänstresurser) som innehåller en service.yaml-fil som beskriver hur appen ska köras när den distribueras.

När stöd för containerorkestrering har lagts till i appen kan du trycka på **F5** för att felsöka .NET-appen i det lokala Service Fabric Mesh-klustret. Här är ASP.NET-appen eShop som körs i ett Service Fabric Mesh-kluster: 

![eShop-app](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Nu kan du publicera appen till Azure Service Fabric Mesh.

## <a name="next-steps"></a>Nästa steg

Se hur du publicerar en app till Service Fabric Mesh: [Självstudie – Distribuera ett Service Fabric Mesh-program](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)