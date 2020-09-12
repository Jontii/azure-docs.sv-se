---
title: Översikt över objekt replikering
titleSuffix: Azure Storage
description: Objekt replikeringen kopierar asynkront block blobbar mellan ett käll lagrings konto och ett mål konto. Använd objekt replikering för att minimera svars tiden för Läs begär Anden, för att öka effektiviteten för beräknings arbets belastningar, optimera data distribution och minimera kostnaderna.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 0d03b2708bfd4aac2565b303ddce44f50be65ef9
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89612337"
---
# <a name="object-replication-for-block-blobs"></a>Objekt replikering för block-blobar

Objekt replikeringen kopierar asynkront block blobbar mellan ett käll lagrings konto och ett mål konto. Vissa scenarier som stöds av objekt replikering är:

- **Minimerar svars tid.** Objekt replikering kan minska svars tiden för Läs begär Anden genom att göra det möjligt för klienter att använda data från en region som ligger närmare fysisk närhet.
- **Öka effektiviteten för beräknings arbets belastningar.** Med objekt replikering kan beräknings arbets belastningar bearbeta samma uppsättningar block blobbar i olika regioner.
- **Optimering av data distribution.** Du kan bearbeta eller analysera data på en och samma plats och sedan replikera bara resultaten till ytterligare regioner.
- **Optimera kostnader.** När dina data har repliker ATS kan du minska kostnaderna genom att flytta den till Arkiv nivån med hanterings principer för livs cykel.

Följande diagram visar hur objekt replikeringen replikerar block blobbar från ett käll lagrings konto i en region till mål konton i två olika regioner.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram som visar hur objekt replikering fungerar":::

Information om hur du konfigurerar objekt replikering finns i [Konfigurera objekt replikering](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Förhandskrav för objektreplikering

Objekt replikering kräver att följande Azure Storage funktioner också är aktiverade:

- [Ändra feed](storage-blob-change-feed.md): måste vara aktiverat på käll kontot. Information om hur du aktiverar ändrings flöden finns i [Aktivera och inaktivera ändrings flödet](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [BLOB-versioner](versioning-overview.md): måste vara aktive rad på både käll-och mål kontona. Information om hur du aktiverar versions hantering finns i [Aktivera och hantera BLOB-versioner](versioning-enable.md).

Att aktivera ändrings flöde och blob-versioner kan medföra ytterligare kostnader. Mer information finns på [sidan med Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="object-replication-policies-and-rules"></a>Principer och regler för objekt replikering

När du konfigurerar objekt replikering skapar du en replikeringsprincip som anger käll lagrings kontot och mål kontot. En replikeringsprincip innehåller en eller flera regler som anger en käll behållare och en mål behållare och anger vilka block-blobar som ska replikeras i käll behållaren.

När du har konfigurerat objekt replikering kontrollerar Azure Storage ändrings flödet för käll kontot med jämna mellanrum och replikerar eventuellt Skriv-eller borttagnings åtgärder till mål kontot. Replikeringsfördröjning beror på storleken på den Block-Blob som replikeras.

> [!IMPORTANT]
> Eftersom block BLOB-data replikeras asynkront synkroniseras inte käll kontot och mål kontot omedelbart. Det finns för närvarande inget service avtal för hur lång tid det tar att replikera data till mål kontot.

### <a name="replication-policies"></a>Replikeringsprinciper

När du konfigurerar objekt replikering skapas en replikeringsprincip på både käll kontot och mål kontot via Azure Storage resurs leverantören. Replikeringsprincipen identifieras av ett princip-ID. Principen på käll-och mål kontona måste ha samma princip-ID för att replikeringen ska kunna ske.

Ett lagrings konto kan fungera som käll konto för upp till två mål konton. Käll-och mål kontona kan finnas i samma region eller i olika regioner. De kan också finnas i olika prenumerationer och i olika Azure Active Directory-klienter (Azure AD). Det går bara att skapa en replikeringsprincip för varje käll konto/mål konto par.

### <a name="replication-rules"></a>Regler för replikering

Reglerna för replikering anger hur Azure Storage kommer att replikera blobbar från en käll behållare till en mål behållare. Du kan ange upp till 10 regler för replikering för varje replikeringsprincip. Varje replikeringsprincip definierar en enda käll-och mål behållare, och varje käll-och mål behållare kan bara användas i en regel.

När du skapar en replikeringsprincip, kopieras som standard bara nya block blobbar som senare läggs till i käll behållaren. Du kan ange att både nya och befintliga block-blobbar ska kopieras, eller så kan du definiera en anpassad kopierings omfattning som kopierar block-blobbar som skapats från en angiven tid till och med.

Du kan också ange ett eller flera filter som en del av en replikeringsprincip för att filtrera block blobbar efter prefix. När du anger ett prefix kopieras bara blobbar som matchar det prefixet i käll behållaren till mål behållaren.

Käll-och mål behållarna måste finnas innan du kan ange dem i en regel. När du har skapat replikeringsprincipen blir målcontainern skrivskyddad. Försök att skriva till målcontainern misslyckas med felkoden 409 (konflikt). Du kan dock anropa åtgärden [Ange BLOB-nivå](/rest/api/storageservices/set-blob-tier) på en BLOB i mål behållaren för att flytta den till Arkiv nivån. Mer information om Arkiv nivån finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](storage-blob-storage-tiers.md#archive-access-tier)lag rings nivåer.

## <a name="billing"></a>Fakturering 

Objekt replikering medför ytterligare kostnader för Läs-och skriv transaktioner mot käll-och mål kontona, samt utgående kostnader för replikering av data från käll kontot till mål kontot och Läs kostnader för att bearbeta ändrings flödet.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera objektreplikering](object-replication-configure.md)
- [Ändra stöd för feed i Azure Blob Storage](storage-blob-change-feed.md)
- [Aktivera och hantera BLOB-versioner](versioning-enable.md)
