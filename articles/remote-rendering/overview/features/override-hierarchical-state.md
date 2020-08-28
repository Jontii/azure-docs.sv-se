---
title: Åsidosätta hierarkiskt tillstånd
description: Förklarar begreppet hierarkiskt tillstånd för åsidosättning av komponenter.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 99f57c212dfc44d84640224b1526ab770fe97230
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009465"
---
# <a name="hierarchical-state-override"></a>Åsidosätta hierarkiskt tillstånd

I många fall är det nödvändigt att ändra utseendet på delar i en [modell](../../concepts/models.md)dynamiskt, till exempel dölja under diagram eller byta delar till genomskinlig åter givning. Att ändra material för varje del som ingår är inte praktiskt eftersom det måste iterera över hela scenen och hantera material kloning och tilldelning på varje nod.

Använd för att göra det här användnings fallet med minsta möjliga belastning `HierarchicalStateOverrideComponent` . Den här komponenten implementerar hierarkiska tillstånds uppdateringar på godtyckliga grenar i scen diagrammet. Det innebär att ett tillstånd kan definieras på vilken nivå som helst i scen diagrammet och det trickles nedåt i hierarkin tills den antingen åsidosätts av ett nytt tillstånd eller tillämpas på ett löv objekt.

Du kan till exempel överväga modellen för en bil och du vill byta hela bilen så att den blir genomskinlig, förutom den inre motor delen. Det här användnings fallet omfattar bara två instanser av komponenten:

* Den första komponenten tilldelas modellens rotnod och aktiverar transparent åter givning för hela bilen.
* Den andra komponenten tilldelas till motorns rotnod och åsidosätter statusen igen genom att uttryckligen stänga av se--läge.

## <a name="features"></a>Funktioner

Den fasta uppsättning tillstånd som kan åsidosättas är:

* **`Hidden`**: Respektive maskor i scen diagrammet är dolda eller visas.
* **`Tint color`**: Ett renderat objekt kan färgtonas med dess enskilda färg och färgton. Bilden nedan visar färg tonens RIM i ett hjul.
  
  ![Färg nyans](./media/color-tint.png)

* **`See-through`**: Geometrin återges som överordnad, till exempel för att visa de inre delarna av ett objekt. Följande bild visar hela bilen som återges i se-läge, förutom den röda bromsen Caliper:

  ![Se igenom](./media/see-through.png)

  > [!IMPORTANT]
  > Alternativet för att se igenom fungerar bara när *TileBasedComposition* [åter givnings läge](../../concepts/rendering-modes.md) används.

* **`Selected`**: Geometrin återges med en [markerings disposition](outlines.md).

  ![Markerings disposition](./media/selection-outline.png)

* **`DisableCollision`**: Geometrin är undantagen från [rums frågor](spatial-queries.md). **`Hidden`** Flaggan påverkar inte flaggan kollisioner, så dessa två flaggor anges ofta tillsammans.

## <a name="hierarchical-overrides"></a>Hierarkiska åsidosättningar

`HierarchicalStateOverrideComponent`Kan kopplas på flera nivåer i en Object-hierarki. Eftersom det bara kan finnas en komponent av varje typ på en entitet, hanterar var och en av `HierarchicalStateOverrideComponent` de olika tillstånden för dolda, se-genom, valda, färg nyanser och kollisioner.

Varje tillstånd kan därför ställas in på något av följande:

* `ForceOn` -tillstånd är aktiverat för alla nät på och under den här noden
* `ForceOff` -statusen är inaktive rad för alla nät på och under den här noden
* `InheritFromParent` -statusen påverkas inte av den här åsidosättning-komponenten

Du kan ändra tillstånd direkt eller via `SetState` funktionen:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Färgton

`tint color`Åsidosättningen är något speciellt i att det finns både ett på/av/på/av/Ärv-tillstånd och en färgad egenskap. Alfa delen av färg tons färgen definierar viktningen för tonings effekterna: om värdet är 0,0 visas inte färg tonen och om värdet är 1,0 kommer objektet att återges med en ren färgton-färg. För in-mellan-värden kommer den slutliga färgen att blandas med färg tonen. Färg tons färgen kan ändras per bild punkt för att uppnå en färganimering.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

En instans av `HierarchicalStateOverrideComponent` sig lägger inte till mycket körnings kostnader. Men det är alltid bra att hålla antalet aktiva komponenter låga. Till exempel, när du implementerar ett urvals system som markerar det valda objektet, rekommenderar vi att du tar bort komponenten när markeringen tas bort. Att hålla komponenterna runt med neutrala funktioner kan snabbt lägga till.

Transparent åter givning placerar mer arbets belastning på serverns GPU än standard åter givning. Om stora delar av scen diagrammet är växlat för att *Visa genom*att många lager av geometrin är synliga, kan det bli en Flask hals i prestandan. Samma sak gäller för objekt med [markerings kon tur](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Nästa steg

* [Beskrivs](../../overview/features/outlines.md)
* [Renderingsmodeller](../../concepts/rendering-modes.md)
* [Spatiala frågor](../../overview/features/spatial-queries.md)
