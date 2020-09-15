---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 91f91b1260cc445f90c2608fc5259ad61acd37ac
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533114"
---
Här är en lista över de lagrings konton som stöds och lagrings typer för den Data Box-enhet enheten. En fullständig lista över alla typer av lagrings konton och deras fullständiga funktioner finns i [typer av lagrings konton](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

I följande tabell visas de lagrings konton som stöds för import order.

| **Lagrings konto/lagrings typer som stöds** | **Blockblob** |**Sid-BLOB*** |**Azure Files** |**Kommentarer**|
| --- | --- | -- | -- | -- |
| Klassisk standard | J | J | J |
| Generell användning v1-standard  | J | J | J | Både frekvent och låg frekvent stöds.|
| Generell användning v1 Premium  |  | Y| | |
| Generell användning v2 standard  | J | J | J | Både frekvent och låg frekvent stöds.|
| Generell användning v2 Premium  |  |Y | | |
| Blob Storage Standard |Y | | |Både frekvent och låg frekvent stöds. |

\**-Data som laddats upp till sid-blobar måste vara 512 byte justerade, till exempel virtuella hård diskar.*

I följande tabell visas de lagrings konton som stöds för export order.

| **Lagrings konto/lagrings typer som stöds** | **Blockblob** |**Sid-BLOB*** |**Azure Files** |**Åtkomst nivåer som stöds**|
| --- | --- | -- | -- | -- |
| Klassisk standard | J | J | J | |
| Generell användning v1-standard  | J | J | J | Frekvent, låg frekvent|
| Generell användning v1 Premium  |  | Y| | |
| Generell användning v2 standard  | J | J | J | Frekvent, låg frekvent|
| Generell användning v2 Premium  |  |Y | | |
| Blob Storage Standard |Y | | |Frekvent, låg frekvent |
| Blockera Blob Storage Premium |Y | | |Frekvent, låg frekvent |
| Page Blob Storage Premium | |Y | | |

> [!IMPORTANT]
> - För generella konton har Data Box-enhet inte stöd för kö-, tabell-och disk lagrings typer för import order. För export order stöder Data Box-enhet inte kö-, tabell-, disk-och Azure Data Lake gen 2-lagrings typer för allmänna-syfte-konton.
> - Data Box-enhet har inte stöd för att lägga till blobar för Blob Storage och blockera Blob Storage konton.
> - Data Box-enhet stöder inte Premium File Storage-konton.
> - Data som överförs till sid-blobar måste vara 512 byte justerade, till exempel virtuella hård diskar.
> - Högst 80 TB kan exporteras.
> - Fil historik och blob-ögonblicksbilder exporteras inte.


