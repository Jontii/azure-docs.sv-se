---
title: Reflektioner av himmel
description: Beskriver hur du konfigurerar miljö kartor för himmelsblå-reflektioner
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 39e3b41d49ad06e5dbe5164809a6743da8dedae5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613749"
---
# <a name="sky-reflections"></a>Reflektioner av himmel

I Azure Remote rendering används en himmel-textur för att lätta objekt realistiska. För förhöjda verklighets program bör den här texturen likna din verkliga miljö, för att göra objekt övertygande. I den här artikeln beskrivs hur du ändrar luft rummets struktur.

> [!NOTE]
> Den himmelsblå texturen kallas även för en *miljö karta*. Dessa villkor används utbytbart.

## <a name="object-lighting"></a>Objekt belysning

Med Azure Remote rendering används PBR ( *fysiskt baserad åter givning* ) för realistiska belysnings beräkningar. Även om du kan lägga till [ljusa källor](lights.md) i din scen, har den största effekten att använda en lämplig luft rummets struktur.

Bilderna nedan visar resultat av ljus olika ytor med en himmel-struktur:

| Grovhet  | 0                                        | 0,25                                          | 0,5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Icke-metall  | ![Dielectric, grovhet = 0](media/dielectric-0.png)   | ![Dielectric, grovhet = 0,25](media/dielectric-0.25.png)  | ![Dielectric, grovhet = 0,5](media/dielectric-0.5.png)  | ![Dielectric, grovhet = 0,75](media/dielectric-0.75.png)  | ![Dielectric, grovhet = 1](media/dielectric-1.png)  |
| BMR      | ![Metall, grovhet = 0](media/metallic-0.png)  | ![Metall, grovhet = 0,25](media/metallic-0.25.png)    | ![Metall, grovhet = 0,5](media/metallic-0.5.png)    | ![Metall, grovhet = 0,75](media/metallic-0.75.png)    | ![Metall, grovhet = 1](media/metallic-1.png)    |

Mer information om belysnings modellen finns i kapitlet om [material](../../concepts/materials.md) .

> [!IMPORTANT]
> Azure fjärrrendering använder endast den himmelsblå texturen för belysnings modeller. Den återger inte luft rummet som en bakgrund eftersom förhöjda verklighets program redan har en korrekt bakgrund – den verkliga världen.

## <a name="changing-the-sky-texture"></a>Ändra luft rummets struktur

Om du vill ändra miljö kartan behöver du bara [läsa in en struktur](../../concepts/textures.md) och ändra sessionens `SkyReflectionSettings` :

```cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!\n");
            }
        });
}

```

Observera att `LoadTextureFromSASAsync` varianten används ovan eftersom en inbyggd textur har lästs in. Om du läser in från [länkade BLOB-lagringar](../../how-tos/create-an-account.md#link-storage-accounts)använder du `LoadTextureAsync` varianten.

## <a name="sky-texture-types"></a>Typer av luft rummets struktur

Du kan använda både *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* -och *2D-texturer* som miljö kartor.

Alla texturer måste vara i ett [textur format som stöds](../../concepts/textures.md#supported-texture-formats). Du behöver inte ange mipmaps för himmelsblå texturer.

### <a name="cube-environment-maps"></a>Kub miljö kartor

För referens är här en cubemap som inte är figursatt:

![En figursatt cubemap](media/Cubemap-example.png)

Använd `AzureSession.Actions.LoadTextureAsync` /  `LoadTextureFromSASAsync` med `TextureType.CubeMap` för att läsa in cubemap texturer.

### <a name="sphere-environment-maps"></a>Sfär miljö kartor

När du använder en 2D-struktur som en miljö karta måste bilden vara i [sfäriskt koordinat utrymme](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![En himmel-bild i sfäriska koordinater](media/spheremap-example.png)

Använd `AzureSession.Actions.LoadTextureAsync` med `TextureType.Texture2D` för att läsa in sfäriska miljö kartor.

## <a name="built-in-environment-maps"></a>Inbyggda miljö kartor

Azure Remote rendering innehåller några inbyggda miljö kartor som alltid är tillgängliga. Alla inbyggda miljö kartor är cubemaps.

|Identifierare                         | Beskrivning                                              | Exemplet                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Olika rand lampor, ljus inomhus bas belysning    | ![Skybox används för att lätta ett objekt](media/autoshop.png)
|builtin://BoilerRoom               | Ljus inomhus-inställning, flera fönster lampor      | ![BoilerRoom Skybox som används för att lätta ett objekt](media/boiler-room.png)
|builtin://ColorfulStudio           | Variera färg lampor i mellanliggande inlednings inställning  | ![ColorfulStudio Skybox som används för att lätta ett objekt](media/colorful-studio.png)
|builtin://Hangar                   | Måttligt ljus omgivnings ljus                     | ![SmallHangar Skybox som används för att lätta ett objekt](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Dim-inlednings inställning med ljust mörk kontrast              | ![IndustrialPipeAndValve Skybox som används för att lätta ett objekt](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Dagtid, ljust fönster Area ljust     | ![Lebombo Skybox som används för att lätta ett objekt](media/lebombo.png)
|builtin://SataraNight              | Mörkt natt himmel och mark med många omgivande lampor   | ![SataraNight Skybox som används för att lätta ett objekt](media/satara-night.png)
|builtin://SunnyVondelpark          | Ljus solljus och skugga kontrast                      | ![SunnyVondelpark Skybox som används för att lätta ett objekt](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Ta bort luft rummets ljus med måttlig belysning            | ![Syferfontein Skybox som används för att lätta ett objekt](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Måttligt varierande sol och skugga                         | ![TearsOfSteelBridge Skybox som används för att lätta ett objekt](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Kväll solnedgång tänd Dusk                    | ![VeniceSunset Skybox som används för att lätta ett objekt](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Ljusa, Lush och vita ljusa toner, nedtonad mark | ![WhippleCreekRegionalPark Skybox som används för att lätta ett objekt](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Dagtid med ljust omgivande underlag                 | ![WinterRiver Skybox som används för att lätta ett objekt](media/winter-river.png)
|builtin://DefaultSky               | Samma som TearsOfSteelBridge                               | ![DefaultSky Skybox som används för att lätta ett objekt](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>API-dokumentation

* [C# RemoteManager. SkyReflectionSettings-egenskap](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.skyreflectionsettings)
* [C++ RemoteManager:: SkyReflectionSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#skyreflectionsettings)

## <a name="next-steps"></a>Nästa steg

* [Lampor](../../overview/features/lights.md)
* [Material](../../concepts/materials.md)
* [Bakgrunder](../../concepts/textures.md)
* [Kommando rads verktyget TexConv](../../resources/tools/tex-conv.md)
