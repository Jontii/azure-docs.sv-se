---
title: Unity Game-objekt och-komponenter
description: Beskriver Unity-metoder för att arbeta med entiteter och komponenter för fjär åter givning.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 662c28196b06f5fbe49f69cb7145fdd33805e000
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019053"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Interagera med spelobjekt och komponenter i Unity

ARR (Azure Remote rendering) är optimerat för stora antal objekt (se [begränsningar](../../reference/limits.md)). Även om det är möjligt att hantera stora och komplexa hierarkier på värden, är det omöjligt att replikera dem på enheter med låg belastning.

När en modell läses in på värden speglar därför Azure-fjärrrenderingen informationen om modell strukturen på klient enheten (vilket kommer att ådra sig nätverks trafik), men replikerar inte objekten och komponenterna i enhet. I stället förväntar vi dig att begära de enhets spels objekt och komponenter som krävs manuellt, så att du kan begränsa omkostnaderna till vad som verkligen behövs. På så sätt får du mer kontroll över prestanda på klient sidan.

Det innebär att unions integreringen av Azure fjärrrendering kommer att ha ytterligare funktioner för att replikera fjär åter givnings strukturen på begäran.

## <a name="load-a-model-in-unity"></a>Läs in en modell i Unity

När du läser in en modell får du en referens till det inlästa modellens rot objekt. Den här referensen är inte ett enhets spel objekt, men du kan göra det till ett med hjälp av tilläggs metoden `Entity.GetOrCreateGameObject()` . Funktionen förväntar sig ett argument av typen `UnityCreationMode` . Om du skickar så `CreateUnityComponents` fylls det nya enhets spels objektet i med proxy-komponenter för alla komponenter för Fjärrrendering som finns på värden. Vi rekommenderar att, men för att föredra `DoNotCreateUnityComponents` , behålla den lägsta omkostnaden.

### <a name="load-model-with-task"></a>Läs in modell med aktivitet

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Läs in modell med enhets driv rutiner

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>Läs in modell med väntande mönster

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

I kod exemplen ovan används modell inläsnings Sök vägen via SAS eftersom den inbyggda modellen har lästs in. Att adressera modellen via BLOB-behållare (med `LoadModelAsync` och `LoadModelParams` ) fungerar helt analogously.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

När du skapar ett enhets spels objekt läggs en `RemoteEntitySyncObject` komponent till i Game-objektet implicit. Den här komponenten används för att synkronisera enhets omvandlingen till servern. Som standard `RemoteEntitySyncObject` behöver användaren uttryckligen anropa för att `SyncToRemote()` Synkronisera lokal enhets status till servern. Aktivera `SyncEveryFrame` kommer att synkronisera objektet automatiskt.

Objekt med en `RemoteEntitySyncObject` kan ha sina fjärrnoder instansierade och visas i Unity-redigeraren via **:::no-loc text="Show children":::** knappen.

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Omslutnings komponenter

[Komponenter](../../concepts/components.md) som är kopplade till fjärrstyrda entiteter exponeras för enhets hantering via proxy `MonoBehavior` s. Dessa proxyservrar representerar fjärrkomponenten i enhet och vidarebefordrar alla ändringar av värden.

Använd tilläggs metoden för att skapa proxy Remote rendering-komponenter `GetOrCreateArrComponent` :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Kopplade livs längder

Livs längden för en fjärran sluten [entitet](../../concepts/entities.md) och ett enhets spels objekt är kopplade när de är kopplade till en `RemoteEntitySyncObject` . Om du anropar `UnityEngine.Object.Destroy(...)` med ett sådant spel objekt tas även den fjärranslutna entiteten bort.

Om du vill förstöra enhetens spel objekt utan att påverka fjärrentiteten måste du först anropa `Unbind()` `RemoteEntitySyncObject` .

Samma sak gäller för alla proxy-komponenter. Om du bara vill förstöra en representation på klient sidan måste du `Unbind()` först anropa proxy-komponenten:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Remote Rendering för Unity](unity-setup.md)
* [Självstudie: ändra fjär entiteter i enhet](../../tutorials/unity/manipulate-models/manipulate-models.md)
