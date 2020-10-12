---
title: Vi flyttar virtuella dator avbildningar till hanterad disk lagring på Azure Marketplace
description: För att tillhandahålla snabbare, mer tillförlitlig lagring och support för nya funktioner och funktioner i Marketplace flyttar vi virtuella Marketplace-avbildningar till hanterad disk lagring.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: iqshahmicrosoft
ms.author: iqshah
ms.openlocfilehash: 814a4012ace95e84e75bcb4071c549822b2b0633
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182436"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Vi flyttar virtuella dator avbildningar på Azure Marketplace till hanterad disk lagring

För att tillhandahålla snabbare, mer tillförlitlig lagring och support för nya funktioner och funktioner i Marketplace flyttar vi virtuella Marketplace-avbildningar till hanterad disk lagring.

Från och med den 2 januari 2020 kommer vi att flytta VM-avbildningar till hanterad disk lagring i faser. I den första fasen flyttar vi bara bilder utan nya distributioner eller virtuella datorer som körs under de föregående 90 dagarna. Innan vi flyttar någon av bilderna skickar vi ett e-postmeddelande för att låta utgivaren se vilka bilder som flyttas och när de flyttas.

Utgivare eller konsumenter behöver inte vidta några åtgärder och användarna påverkas inte. Marketplace-erbjudanden är tillgängliga och kunder kan fortfarande distribuera hanterade virtuella datorer från dessa avbildningar, under och efter flytten.

Om du har några frågor kan du [kontakta oss](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Skulle användarna av mina VM-avbildningar uppleva ett avbrott?

Användare av de virtuella dator avbildningarna kommer inte att drabbas av avbrott. 

I den första fasen kommer vi bara att flytta VM-avbildningar som inte har några virtuella datorer som körs. Eftersom det inte finns några användare för de här avbildningarna påverkas ingen påverkan. För de efterföljande faserna påverkas inte användarna.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Hur lång tid tar det för processen att slutföras?

Det kan ta upp till 24 timmar att slutföra migreringen.

### <a name="do-i-need-to-take-any-action"></a>Behöver jag utföra någon åtgärd?

Nej. Utgivare eller konsumenter behöver inte vidta några åtgärder.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>Måste jag uppdatera mitt system för att anropa API: er för moln portalen på ett annat sätt när de har flyttats till hanterad disk lagring?

Nej. Dina befintliga API-anrop kommer att fortsätta att fungera.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Skulle alla mina VM-avbildningar flyttas till den hanterade disken på samma tidpunkt?

Vi kommer att flytta alla dina VM-avbildningar på samma dag. Vi meddelar dig när de har flyttats.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Kan jag begära att schemalägga flyttningen av mina VM-avbildningar till en senare tidpunkt?

Vi rekommenderar att du flyttar bilderna på det schemalagda datumet. Men om du har problem kan du kontakta oss för att ändra flytten.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Kan jag publicera uppdateringar till mina VM-avbildningar under flytten?

Det går inte att uppdatera VM-avbildningarna under flytten.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Ändras publicerings processen när min VM-avbildning flyttas till den hanterade disken?

Nej, publicerings processen är oförändrad. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Kan utgivaren flytta sina erbjudanden till den hanterade disken?

Nej, utgivaren kan inte flytta sina erbjudanden till den hanterade disken. De måste vänta och avbildningarna kommer att flyttas automatiskt. Vi kommer att skicka meddelanden till utgivaren innan vi gör några ändringar.
