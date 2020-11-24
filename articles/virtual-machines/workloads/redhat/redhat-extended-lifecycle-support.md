---
title: Utökat livscykelstöd för Red Hat Enterprise Linux
description: Läs mer om att lägga till stöd för utökade livscykler för Red Hat Enterprise
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 1f31cc982e5e7f92cfe7e8adf588dd96628f2c6f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95744049"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) Extended Lifecycle support
Den här artikeln innehåller information om Extended Lifecycle-stöd för Red Hat Enterprise-avbildningar:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6-livscykel
Från och med den 30 november 2020 kommer Red Hat Enterprise Linux 6 att uppnå slutet av underhålls fasen. Underhålls fasen följs av fasen Extended Life. Som Red Hat Enterprise Linux 6 över gångar från hela/underhålls faserna rekommenderar vi starkt att du uppgraderar till Red Hat Enterprise Linux 7 eller 8. Om kunderna måste stanna Red Hat Enterprise Linux 6 rekommenderar vi att du lägger till ett ELS-tillägg (Red Hat Enterprise Linux Extended Life Cycle support).

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Steg för att lägga till stöd för utökade livscykler för virtuella datorer i Marketplace betala per användning
1. Fyll i [Els-formuläret som finns här](https://aka.ms/els-form) med din kontakt information och prenumerations information för de virtuella datorer som du vill lägga till Els-stöd för. Pris informationen för Lägg till på är också tillgänglig i formuläret.
1. Azure Red Hat Enterprise Linux-teamet kommer att kontakta dig med en lista över virtuella datorer för ELS support Lägg till inom 1-2 arbets dagar. Läs igenom listan och svara på pris sättnings priset.
1. Azure Red Hat Enterprise Linux-teamet kommer att dela stegen för att lägga till ELS-klient paketet i de virtuella datorerna. Följ stegen som visas i e-postmeddelandet för att fortsätta att ta emot program varu underhåll (fel-och säkerhets korrigeringar) och stöd för Red Hat Enterprise Linux 6.

> [!Note]
> Dela inte stegen för att använda RHEL ELS Lägg till i med någon utanför organisationen. Kontakta för AzureRedHatELS@microsoft.com att få support eller ytterligare frågor.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Jag kör Red Hat Enterprise Linux 6 och kan inte migrera till en senare version för tillfället. Vilka alternativ har jag?
* Fortsätt att köra Red Hat Enterprise Linux 6 och Köp ELS-Add-On (Extended Life Cycle support) för att fortsätta få begränsad program varu underhåll och teknisk support (se avsnittet om uppgradering och pris information nedan).
* Migrera till Red Hat Enterprise Linux 7 eller 8 så snart du kan.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Vilken kostar det extra att använda Red Hat Enterprise Linux utökade ELS-tillägg (Support för livs cykel support)?
Kostnaderna för Extended Lifecycle support finns i [formuläret Els](https://aka.ms/els-form)


## <a name="next-steps"></a>Nästa steg

* Om du vill visa en fullständig lista över RHEL-avbildningar i Azure, se [Red Hat Enterprise Linux (RHEL)-avbildningar som är tillgängliga i Azure](./redhat-imagelist.md).
* Mer information om Azure Red Hat-uppdateringen finns i [Red Hat-uppdaterings infrastruktur för virtuella datorer på begäran RHEL i Azure](./redhat-rhui.md).
* Om du vill veta mer om erbjudandet om RHEL BYOS kan du Red Hat Enterprise Linux läsa mer [i få guld avbildningar för prenumerationer i Azure](./byos.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns i [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata).