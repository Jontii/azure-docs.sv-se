---
title: Ev4 och Esv4-serien – Azure Virtual Machines
description: Specifikationer för Ev4 och Esv4-seriens virtuella datorer.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 6e35e32c92535a408c8df22d7306895150a59519
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050156"
---
# <a name="ev4-and-esv4-series"></a>Ev4- och Esv4-serien

Ev4 och Esv4-serien körs på Intel &reg; Xeon &reg; platina-8272CL (Cascade Lake) i en Hyper-threadd konfiguration, är idealiska för olika minnes intensiva företags program och funktioner upp till 504GiB RAM-minne. Den har en hållbar all Core Turbo klock hastighet på 3,4 GHz.

> [!NOTE]
> För vanliga frågor, se  [Azure VM-storlekar utan en lokal temporär disk](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Ev4-serien

Ev4-seriens storlekar körs på Intel Xeon &reg; platina-8272CL (överlappande sjö). Instanserna i Ev4-serien är idealiska för minnes intensiva företags program. Virtuella datorer i Ev4-serien har Intel &reg; Hyper-Threading-teknik.

Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer. Använd Esv4-storlekarna om du vill använda Premium Storage-diskar. Pris-och debiterings mätare för Esv4-storlekar är samma som för Ev4-serien.

> [!IMPORTANT]
> Dessa nya storlekar finns för närvarande endast i offentlig för hands version. Du kan registrera dig för dessa Ev4-och Esv4-serier [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU: 195-210

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Endast Fjärrlagring | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Endast Fjärrlagring | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Endast Fjärrlagring | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Endast Fjärrlagring | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Endast Fjärrlagring | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Endast Fjärrlagring | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Endast Fjärrlagring | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Endast Fjärrlagring | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4-serien

Esv4-seriens storlekar körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Instanserna i Esv4-serien är idealiska för minnes intensiva företags program. Virtuella datorer i Evs4-serien har Intel &reg; Hyper-Threading-teknik. Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer.

> [!IMPORTANT]
> Dessa nya storlekar finns för närvarande endast i offentlig för hands version. Du kan registrera dig för dessa Ev4-och Esv4-serier [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU: 195-210

Premium Storage: stöds

Premium Storage caching: stöds

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat data flöde: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Endast Fjärrlagring | 4 | 19000/120 (50) | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Endast Fjärrlagring | 8 | 38500/242 (100) | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Endast Fjärrlagring | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Endast Fjärrlagring | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Endast Fjärrlagring | 32 | 193000/1211 (500) | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Endast Fjärrlagring | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Endast Fjärrlagring | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Endast Fjärrlagring | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [begränsad kärn storlek är tillgänglig](./constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

Pris kalkylator: [pris kalkylator](https://azure.microsoft.com/pricing/calculator/)

Mer information om disk typer: [disk typer](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
