---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 10d6ef2c90390f08e38726363416493f937de8f2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467604"
---
Här följer storleken på de Azure-objekt som kan skrivas. Se till att alla filer som överförs följer dessa gränser.

| Objekt typ för Azure | Uppladdnings gräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockera BLOB        | 4,75 TB                                                 |
| Sid-BLOB         | 1 TB <br> Alla filer som överförs i Page BLOB-format måste vara 512 byte-justerade (en integral), annars Miss lyckas överföringen. <br> VHD och VHDX är 512 byte-justerade. |
| Azure Files         | 1 TB <br> Alla filer som överförs i Page BLOB-format måste vara 512 byte-justerade (en integral), annars Miss lyckas överföringen. <br> VHD och VHDX är 512 byte-justerade. |

> [!IMPORTANT]
> Att skapa filer (oberoende av lagrings typen) tillåts upp till 5 TB. Men om du skapar en fil vars storlek är större än den uppladdnings gräns som anges i tabellen ovan överförs inte filen. Du måste ta bort filen manuellt för att frigöra utrymme.
