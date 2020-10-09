---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "80628429"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regional tillgänglighet
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portalen


1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till den disk som du vill ögonblicks bild.
1. På din disk väljer du **skapa en ögonblicks bild**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Skärm bild. Din disks blad, med * * + skapa ögonblicks bild * * markerat, vilket är vad du måste välja.":::

1. Välj den resurs grupp som du vill använda och ange ett namn.
1. Välj **stegvis** och välj **Granska + skapa**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Skärm bild. Din disks blad, med * * + skapa ögonblicks bild * * markerat, vilket är vad du måste välja.":::

1. Välj **Skapa**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Skärm bild. Din disks blad, med * * + skapa ögonblicks bild * * markerat, vilket är vad du måste välja.":::

## <a name="next-steps"></a>Nästa steg

Om du vill se exempel kod som demonstrerar den differentiella kapaciteten för stegvisa ögonblicks bilder med hjälp av .NET, se [Kopiera Azure-Managed disks säkerhets kopior till en annan region med differentiella funktioner i stegvisa ögonblicks bilder](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
