---
title: Grov omlokalisering i Unity
description: Djupgående förklaring av hur du skapar och hittar fäst punkter med grov omlokalisering i C#.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/19/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 42a237caf03d4d3fb6dd9aa70668cbb84ff40072
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008632"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-c"></a>Skapa och hitta ankare med grov omlokalisering i C #

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++-/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Molnbaserade ankare kan associeras på enheten, placera sensor data med de ankare som du skapar. Den här informationen kan också användas för att snabbt avgöra om det finns några fäst punkter i närheten av enheten. Mer information finns i [grov omlokalisering](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Förutsättningar

Se till att du har följande för att slutföra den här guiden:

- Grundläggande kunskaper om C#.
- Läs igenom [översikten över Azures spatiala ankare](../overview.md).
- Slutfört ett av [snabb starterna på fem minuter](../index.yml).
- Läs igenom [instruktionen skapa och hitta ankare](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```csharp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Allow GPS
sensorProvider.Sensors.GeoLocationEnabled = true;

// Allow WiFi scanning
sensorProvider.Sensors.WifiEnabled = true;

// Allow a set of known BLE beacons
sensorProvider.Sensors.BluetoothEnabled = true;
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```csharp
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();
nearDeviceCriteria.DistanceInMeters = 5;
nearDeviceCriteria.MaxResultCount = 25;

// Set the session's locate criteria
AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]