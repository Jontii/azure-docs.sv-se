---
title: Distribuera inbyggd C++-självstudie till HoloLens
description: Snabb start som visar hur du kör den inbyggda C++-självstudien på HoloLens
author: florianborn71
ms.author: flborn
ms.date: 06/08/2020
ms.topic: quickstart
ms.openlocfilehash: 71760e9b54ff3a520f0784ecda4484bb3ea047e3
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892668"
---
# <a name="quickstart-deploy-native-c-sample-to-hololens"></a>Snabb start: Distribuera inbyggt C++-exempel till HoloLens

Den här snabb starten beskriver hur du distribuerar och kör det inbyggda programmet för C++-självstudie på en HoloLens 2.

I den här snabb starten får du lära dig att:

> [!div class="checklist"]
>
>* Bygg själv studie programmet för HoloLens.
>* Ändra ARR-autentiseringsuppgifterna i käll koden.
>* Distribuera och kör exemplet på enheten.

## <a name="prerequisites"></a>Förutsättningar

För att få åtkomst till tjänsten Azure Remote rendering måste du först [skapa ett konto](../../../how-tos/create-an-account.md).

Följande program vara måste vara installerad:

* Windows SDK 10.0.18362.0 [(Hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Den senaste versionen av Visual Studio 2019 [(Hämta)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools för Mixad verklighet](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Mer specifikt är följande *arbets belastnings* installationer obligatoriska:
  * **Skriv bords utveckling med C++**
  * **Universell Windows-plattform (UWP) utveckling**
* GIT [(nedladdning)](https://git-scm.com/downloads)

## <a name="clone-the-arr-samples-repository"></a>Klona plats för ARR-exempel

Som ett första steg ska vi klona git-lagringsplatsen, som tar del av de offentliga exemplen för Azure Remote rendering. Öppna en kommando tolk (Skriv `cmd` in Start-menyn i Windows) och ändra till en katalog där du vill lagra exempel projektet arr.

Kör följande kommandon:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Det sista kommandot skapar en under katalog i katalogen ARR som innehåller de olika exempel projekten för Azure Remote rendering.

Snabb vägledningen för C++ HoloLens finns i under katalogen *NativeCpp/HoloLens*.

## <a name="build-the-project"></a>Bygga projektet

Öppna lösnings filen *HolographicApp. SLN* som finns i under katalogen *NativeCpp/HoloLens* med Visual Studio 2019.

Ändra build-konfigurationen till *Debug* (eller *release*) och *arm64*. Kontrol lera också att fel söknings läget är inställt på *enhet* i stället för *fjärrdatorn*:

![Visual Studio-konfiguration](media/vs-config-native-cpp-tutorial.png)

Eftersom autentiseringsuppgifterna för kontot är hårdkodad i självstudiens källkod ändrar du dem till giltiga autentiseringsuppgifter. Öppna filen `HolographicAppMain.cpp` i Visual Studio och ändra den del där klient delen skapas i konstruktorn klass `HolographicAppMain` :

```cpp
// 2. Create front end
{
    // Users need to fill out the following with their account data and model
    RR::AzureFrontendAccountInfo init;
    init.AccountId = "00000000-0000-0000-0000-000000000000";
    init.AccountKey = "<account key>";
    init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
    m_modelURI = "builtin://Engine";
    m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
    m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
}
```

Ändra särskilt följande värden:
* `init.AccountId` och `init.AccountKey` för att använda dina konto data. Se stycke om hur du [hämtar konto information](../../../how-tos/create-an-account.md#retrieve-the-account-information).
* Region delen av `init.AccountDomain` strängen för andra regioner än `westus2` , t. ex. `"westeurope.mixedreality.azure.com"`
* Dessutom `m_sessionOverride` kan ändras till ett befintligt sessions-ID. Sessioner kan skapas utanför det här exemplet, till exempel genom [att använda PowerShell-skriptet](../../../samples/powershell-example-scripts.md#script-renderingsessionps1) eller använda [sessionen REST API](../../../how-tos/session-rest-api.md#create-a-session) direkt.
Att skapa en session utanför exemplet rekommenderas när exemplet ska köras flera gånger. Om ingen session skickas, kommer exemplet att skapa en ny session vid varje start, vilket kan ta flera minuter.

Nu kan programmet kompileras.

## <a name="launch-the-application"></a>Starta programmet

1. Anslut HoloLens med en USB-kabel till din dator.
1. Aktivera HoloLens och vänta tills Start menyn visas.
1. Starta fel söknings programmet i Visual Studio (F5). Appen installeras automatiskt på enheten.

Exempel appen bör starta och en text panel visas som informerar dig om det aktuella program läget. Statusen vid start är antingen att starta en ny session eller ansluta till en befintlig session. När modell inläsningen har slutförts visas den inbyggda motor modellen i ditt huvud läge. Ocklusion är att motor modellen samverkar korrekt med den snurrande kub som återges lokalt.

 Om du vill starta exemplet en gång senare kan du även hitta det från Start menyn i HoloLens, men Observera att det kan ha ett utgånget sessions-ID som har kompilerats till det.

## <a name="next-steps"></a>Nästa steg

Den här snabb starten baseras på resultatet av en själv studie kurs som förklarar hur du integrerar alla enheter som är relaterade till en aktie *Holographic-app*. Om du vill veta vilka steg som är nödvändiga följer du den här självstudien:

> [!div class="nextstepaction"]
> [Självstudie: integrera fjärrrendering i en HoloLens Holographic-app](../../../tutorials/native-cpp/hololens/integrate-remote-rendering-into-holographic-app.md)
