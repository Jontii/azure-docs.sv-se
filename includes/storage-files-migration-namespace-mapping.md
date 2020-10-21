---
title: Mappa en mappstruktur till en Azure File Sync-topologi
description: Mappa en befintlig fil-och mappstruktur till Azure-filresurser för användning med Azure File Sync. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 80e04ec06edc7169f0a4318c2c94de34dda9d96a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331102"
---
I det här steget ska du utvärdera hur många Azure-filresurser du behöver. En enda Windows Server-instans (eller kluster) kan synkronisera upp till 30 Azure-filresurser.

Du kan ha fler mappar på dina volymer som du för närvarande delar ut lokalt som SMB-resurser till dina användare och appar. Det enklaste sättet är att Envision en lokal resurs som mappar 1:1 till en Azure-filresurs. Om du har ett litet nummer, under 30 för en enda Windows Server-instans, rekommenderar vi en 1:1-mappning.

Om du har fler resurser än 30 är det ofta onödigt att mappa en lokal resurs 1:1 till en Azure-filresurs. Överväg följande alternativ.

#### <a name="share-grouping"></a>Dela gruppering

Om din HR-avdelning (till exempel) har totalt 15 resurser kan du överväga att lagra alla HR-data i en enda Azure-filresurs. Att lagra flera lokala resurser i en Azure-filresurs förhindrar inte att du skapar vanliga 15 SMB-resurser på den lokala Windows Server-instansen. Det innebär bara att du ordnar rotmapparna för dessa 15 resurser som undermappar under en gemensam mapp. Du synkroniserar sedan den här gemensamma mappen med en Azure-filresurs. På så sätt behövs bara en enda Azure-filresurs i molnet för den här gruppen lokala resurser.

#### <a name="volume-sync"></a>Volym synkronisering

Azure File Sync stöder synkronisering av roten för en volym till en Azure-filresurs. Om du synkroniserar rotmappen kommer alla undermappar och filer att gå till samma Azure-filresurs.

Synkronisering av volymens rot är inte alltid det bästa svaret. Det finns fördelar med att synkronisera flera platser. Om du till exempel gör det kan du behålla antalet objekt som ligger lägre per synkroniserings omfång. Medan vi testar Azure-filresurser och Azure File Sync med 100 000 000 objekt (filer och mappar) per resurs, är det en bra idé att prova och behålla siffran under 20 eller 30 000 000 i en enda resurs. Att ställa in Azure File Sync med ett lägre antal objekt är inte bara bra för filsynkronisering. Ett lägre antal objekt fördelar också scenarier som dessa:

* Inledande genomsökning av moln innehållet innan namn området kan börja visas på en Azure File Sync aktive rad Server kan bli snabbare.
* Det går snabbare att återställa på moln sidan från en ögonblicks bild av Azure-filresurs.
* Haveri beredskap för en lokal server kan påskynda avsevärt.
* Ändringar som görs direkt i en Azure-filresurs (utanför synkronisering) kan identifieras och synkroniseras snabbare.

> [!TIP]
> Om du är osäker på hur många filer och mappar du har, kan du kolla TreeSize-verktyget från sylt Software GmbH.

#### <a name="a-structured-approach-to-a-deployment-map"></a>En strukturerad metod till en distributions karta

Innan du distribuerar moln lagring i ett senare steg är det viktigt att skapa en karta mellan lokala mappar och Azure-filresurser. Den här mappningen informerar sedan hur många och vilka Azure File Sync *Synkronisera grupp* resurser som du kommer att etablera. En Sync-grupp samband till Azure-filresursen och mappen på servern tillsammans och upprättar en synkroniserad anslutning.

För att fatta beslut om hur många Azure-filresurser du behöver kan du läsa följande begränsningar och bästa metoder. På så sätt kan du optimera kartan.

* En server med Azure File Sync agent installerad kan synkroniseras med upp till 30 Azure-filresurser.
* En Azure-filresurs distribueras i ett lagrings konto. Det gör lagrings kontot till ett skal mål för prestanda siffror som IOPS och data flöde. 

  Två standard-(ej Premium) Azure-filresurser kan teoretiskt fylla den maximala prestanda som ett lagrings konto kan leverera. Om du endast planerar att bifoga Azure File Sync till dessa fil resurser, skapar inte ett problem med att gruppera flera Azure-filresurser till samma lagrings konto. Granska prestanda målen i Azure-filresursen om du vill ha djupare insikt i relevanta mått. 

  Om du planerar att lyfta en app till Azure som ska använda Azure-filresursen internt kan du behöva mer prestanda från Azure-filresursen. Om detta är en möjlighet, även i framtiden, är det bäst att mappa en Azure-filresurs till ett eget lagrings konto.
* Det finns en gräns på 250 lagrings konton per prenumeration i en enda Azure-region.

> [!TIP]
> Med den här informationen i åtanke blir det ofta nödvändigt att gruppera flera mappar på översta nivån på dina volymer i en gemensam, ny rot Katalog. Sedan synkroniserar du den nya rot katalogen och alla mappar som du har grupperat i den till en enda Azure-filresurs. Med den här metoden kan du hålla dig inom gränsen på 30 Azure File resurs-synkroniseringar per server.
>
> Den här grupperingen under en gemensam rot har ingen påverkan på åtkomst till dina data. Dina ACL: er förblir i befintligt skick. Du behöver bara justera resurs Sök vägar (t. ex. SMB-eller NFS-resurser) du kan ha på servermappar som du nu har ändrat till en gemensam rot. Inga andra ändringar.

> [!IMPORTANT]
> Den viktigaste skalnings vektorn för Azure File Sync är antalet objekt (filer och mappar) som måste synkroniseras.

Azure File Sync stöder synkronisering av upp till 100 000 000 objekt till en enda Azure-filresurs. Den här gränsen kan överskridas och visar bara hur Azure File Sync-grupptester regelbundet.

Det är en bra idé att hålla antalet objekt per omfånget för synkronisering låg. Det är en viktig faktor att överväga i mappningen av mappar till Azure-filresurser. Medan vi testar Azure-filresurser och Azure File Sync med 100 000 000 objekt (filer och mappar) per resurs, är det en bra idé att prova och behålla siffran under 20 eller 30 000 000 i en enda resurs. Dela upp ditt namn område i flera resurser om du börjar överskrida de här talen. Du kan fortsätta att gruppera flera lokal resurser i samma Azure-filresurs, så länge som du håller dig till ungefär så här. På så vis får du utrymme att växa.

I din situation är det möjligt att en uppsättning mappar kan synkroniseras logiskt till samma Azure-filresurs (med den nya, gemensamma rotmappen som nämns ovan). Men det kan fortfarande vara bättre att gruppera mappar så att de synkroniseras till två i stället för en Azure-filresurs. Du kan använda den här metoden för att hålla reda på hur många filer och mappar per fil resurs som bal anse ras på servern.

#### <a name="create-a-mapping-table"></a>Skapa en mappnings tabell

:::row:::
    :::column:::
        [![Ett exempel på en mappnings tabell. Hämta filen nedan för att uppleva och använda innehållet i den här avbildningen.](media/storage-files-migration-namespace-mapping/namespace-mapping.png)](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Använd en kombination av de tidigare begreppen för att avgöra hur många Azure-filresurser du behöver, och vilka delar av dina befintliga data som kommer att få plats i Azure-filresursen.
        
        Skapa en tabell som registrerar dina tankar så att du kan referera till den när det behövs. Att hålla ordning på är viktigt, eftersom det kan vara enkelt att förlora information om din kart plan när du konfigurerar många Azure-resurser på en gång. För att hjälpa dig att skapa en fullständig mappning kan du ladda ned en Microsoft Excel-fil som en mall.

[//]: # (HTML visas som det enda sättet att lägga till en kapslad tabell med två kolumner med fungerande bild tolkning och text/hyperlänk på samma rad.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Hämta en mall för namn områdes mappning.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
