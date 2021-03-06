---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 61af1f1a97e0ffad0332a34034f5dd1d007d1a5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901233"
---
| Resurs | Gräns |
| --- | --- |
| Virtuella datorer per [prenumeration](https://azure.microsoft.com/pricing/) |25 000<sup>1</sup> per region. |
| Totalt antal VM-kärnor per [prenumeration](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| Totalt antal kärnor per [prenumeration](https://azure.microsoft.com/pricing/) i Azure-VM |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| VM per serie, till exempel Dv2 och F, kärnor per [prenumeration](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per region. Kontakta supporten för att öka gränsen. |
| [Tillgänglighets uppsättningar](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per prenumeration |2 500 per region. |
| Virtuella datorer per tillgänglighets uppsättning | 200 |
| [Placerings grupper för närhet](../articles/virtual-machines/windows/proximity-placement-groups-portal.md) per [resurs grupp](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 | 
| Certifikat per tillgänglighets uppsättning | 199<sup>2</sup> |
| Certifikat per prenumeration |Obegränsad<sup>3</sup> |

<sup>1</sup> standard gränserna varierar beroende på kategori typ, t. ex. kostnads fri utvärderings version och betala per användning, och per serie, till exempel Dv2, F och G. Standardvärdet för Enterprise-avtal-prenumerationer är till exempel 350.

<sup>2</sup> egenskaper som t. ex. offentliga SSH-nycklar skickas också som certifikat och räknas mot den här gränsen. Använd [Azure Key Vault tillägget för Windows](../articles/virtual-machines/extensions/key-vault-windows.md) eller [Azure Key Vault tillägget för Linux](../articles/virtual-machines/extensions/key-vault-linux.md) för att installera certifikat för att kringgå den här gränsen.

<sup>3</sup> med Azure Resource Manager lagras certifikat i Azure Key Vault. Antalet certifikat är obegränsat för en prenumeration. Det finns en gräns på 1 MB för certifikat per distribution, som består av antingen en enskild virtuell dator eller en tillgänglighets uppsättning.



> [!NOTE]
> Virtuella dator kärnor har en regional total gräns. De har också en gräns för regionala serier per storlek, till exempel Dv2 och F. Dessa gränser tillämpas separat. Anta till exempel att en prenumeration i regionen USA, östra har en gräns för totalt antal VM-kärnor på 30, en gräns för antal kärnor i A-serien på 30 och en gräns för antal kärnor i D-serien på 30. Den här prenumerationen kan distribuera 30 a1-VM: ar eller 30 D1-datorer, eller en kombination av de två som inte överskrider totalt 30 kärnor. Ett exempel på en kombination är 10 a1 VM och 20 D1-datorer.  
> <!-- -->
>