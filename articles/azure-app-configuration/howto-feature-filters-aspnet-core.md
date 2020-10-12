---
title: Använd funktions filter för att aktivera en funktion för en delmängd av användare
titleSuffix: Azure App Configuration
description: Lär dig hur du använder funktions filter för att aktivera en funktion för en delmängd av användare
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 5b2eb942581f6e4163012b0f767d04c02689bb7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206766"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Använd funktions filter för att aktivera en funktion för en delmängd av användare

Med funktions flaggor kan du aktivera eller inaktivera funktioner i ditt program. En enkel funktions flagga är antingen på eller av. Programmet fungerar alltid på samma sätt. Du kan till exempel distribuera en ny funktion bakom en funktions flagga. När funktions flaggan är aktive rad ser alla användare den nya funktionen. Om du inaktiverar funktions flaggan döljs den nya funktionen.

En _villkorlig funktions flagga_ gör det möjligt att aktivera eller inaktivera funktions flagga dynamiskt. Programmet kan beter sig på olika sätt beroende på funktions flagg kriterier. Anta att du vill visa din nya funktion till en liten delmängd av användarna först. Med en villkorlig funktions flagga kan du aktivera funktions flaggan för vissa användare medan du inaktiverar den för andra. _Funktions filter_ bestämmer funktions flaggans status varje gången den utvärderas.

`Microsoft.FeatureManagement`Biblioteket innehåller två funktions filter:

- `PercentageFilter` aktiverar funktions flagga baserat på en procents ATS.
- `TimeWindowFilter` aktiverar funktions flagga under en angiven tids period.

Du kan också skapa ett eget funktions filter som implementerar [gränssnittet Microsoft. FeatureManagement. IFeatureFilter](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Registrera ett funktions filter

Du registrerar ett funktions filter genom att anropa `AddFeatureFilter` -metoden och ange namnet på funktions filtret. Till exempel registreras följande kod `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Konfigurera ett funktions filter i Azure App konfiguration

Vissa funktions filter har ytterligare inställningar. Aktiverar till exempel `PercentageFilter` en funktion som baseras på en procent andel. Den har en inställning som definierar procent andelen som ska användas.

Du kan konfigurera de här inställningarna för funktions flaggor som definierats i Azure App-konfigurationen. Följ till exempel de här stegen för `PercentageFilter` att aktivera funktions flagga för 50% av begär anden till en webbapp:

1. Följ anvisningarna i [snabb start: Lägg till funktions flaggor i en ASP.net Core app](./quickstart-feature-flag-aspnet-core.md) för att skapa en webbapp med en funktions flagga.

1. Gå till konfigurations arkivet i Azure Portal och klicka på **funktions hanteraren**.

1. Klicka på snabb menyn för den *beta* funktions flagga som du skapade i snabb starten. Klicka på **Redigera**.

    > [!div class="mx-imgBorder"]
    > ![Redigera beta funktions flagga](./media/edit-beta-feature-flag.png)

1. På **redigerings** skärmen väljer du knappen **vid** alternativ om den inte redan är markerad. Klicka sedan på knappen **Lägg till filter** . ( **På** alternativ knappens etikett ändras till läsa **villkorligt**.)

1. I fältet **nyckel** anger du *Microsoft. procent*.

    > [!div class="mx-imgBorder"]
    > ![Lägg till funktions filter](./media/feature-flag-add-filter.png)

1. Klicka på snabb menyn bredvid funktions filter nyckeln. Klicka på **Redigera parametrar**.

    > [!div class="mx-imgBorder"]
    > ![Redigera funktions filter parametrar](./media/feature-flag-edit-filter-parameters.png)

1. Hovra under **namn** rubriken så att text rutor visas i rutnätet. Ange ett **namn** på ett *värde* och ett **värde** på 50. Fältet **värde** anger procent andelen begär Anden som du vill aktivera funktions filtret för.

    > [!div class="mx-imgBorder"]
    > ![Ange funktions filter parametrar](./media/feature-flag-set-filter-parameters.png)

1. Klicka på **Använd** för att återgå till skärmen **Redigera funktions flagga** . Klicka sedan på **tillämpa** igen för att spara inställningarna för funktions flagga.

1. Funktions flaggans **tillstånd** visas nu som *villkor*. Det här läget anger att funktions flaggan kommer att aktive ras eller inaktive ras för varje begäran, baserat på de kriterier som tillämpas av funktions filtret.

    > [!div class="mx-imgBorder"]
    > ![Flagga för villkorlig funktion](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Funktions filter i åtgärd

Om du vill se effekterna av den här funktions flaggan startar du programmet och klickar på **Uppdatera** i webbläsaren flera gånger. Du ser att *beta* -objektet visas i verktygsfältet om 50% av tiden. Den är dold resten av tiden, eftersom `PercentageFilter` inaktiverar *beta* funktionen för en delmängd begär Anden. Följande video visar hur det fungerar i praktiken.

> [!div class="mx-imgBorder"]
> ![PercentageFilter i praktiken](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över funktions hantering](./concept-feature-management.md)
