---
title: Så här svarar du på Azure Defender för Key Vault aviseringar
description: Lär dig mer om de steg som krävs för att svara på aviseringar från Azure Defender för Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 6a40ffab652a6ae8671d6cd1c0dd9336764ce2c4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791944"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Svara på Azure Defender för Key Vault-aviseringar
När du får en avisering från Azure Defender för Key Vault rekommenderar vi att du undersöker och svarar på aviseringen enligt beskrivningen nedan. Azure Defender för Key Vault skyddar program och autentiseringsuppgifter, så även om du är van vid det program eller den användare som utlöste aviseringen är det viktigt att kontrol lera att situationen omger varje avisering.  

Varje avisering från Azure Defender för Key Vault innehåller följande element:

- Objekt-ID
- Användarens huvud namn eller IP-adress för den misstänkta resursen

> [!TIP]
> Vissa fält kanske inte är tillgängliga, baserat på vilken *typ* av åtkomst som har inträffat. Om ditt nyckel valv till exempel har öppnats av ett program ser du inte ett associerat användar huvud namn. Om trafiken kommer från utanför Azure visas inget objekt-ID.

## <a name="step-1-contact"></a>Steg 1. Kontakt

1. Kontrol lera om trafiken kommer från din Azure-klient. Om Key Vault-brandväggen är aktive rad är det troligt att du har gett åtkomst till användaren eller programmet som utlöste aviseringen.
1. Om du inte kan verifiera källan för trafiken fortsätter du till [steg 2. Omedelbar minskning](#step-2-immediate-mitigation).
1. Om du kan identifiera källan för trafiken i din klient organisation kan du kontakta användaren eller ägaren av programmet. 

> [!CAUTION]
> Azure Defender för Key Vault har utformats för att hjälpa till att identifiera misstänkt aktivitet som orsakas av stulna autentiseringsuppgifter. Stäng **inte** bara aviseringen eftersom du känner igen användaren eller programmet. Kontakta ägaren till programmet eller användaren och kontrol lera att aktiviteten var giltig. Du kan skapa en undertrycks regel för att eliminera brus vid behov. Läs mer i [utelämna aviseringar från Azure Defender](alerts-suppression-rules.md).


## <a name="step-2-immediate-mitigation"></a>Steg 2. Omedelbar minskning 
Om du inte känner igen användaren eller programmet, eller om du tror att åtkomsten inte har godkänts:

- Om trafiken kommer från en okänd IP-adress:
    1. Aktivera Azure Key Vault brand väggen enligt beskrivningen i [konfigurera Azure Key Vault brand väggar och virtuella nätverk](../key-vault/general/network-security.md).
    1. Konfigurera brand väggen med betrodda resurser och virtuella nätverk.

- Om källan till aviseringen var ett obehörigt program eller misstänkt användare:
    1. Öppna nyckel valvets inställningar för åtkomst princip.
    1. Ta bort motsvarande säkerhets objekt eller begränsa vilka åtgärder som säkerhets objekt ska utföra.  

- Om källan till aviseringen har en Azure Active Directory roll i din klient organisation:
    1. Kontakta administratören.
    1. Fastställ om du behöver minska eller återkalla Azure Active Directory behörigheter.

## <a name="step-3-identify-impact"></a>Steg 3. Identifiera påverkan 
När påverkan har begränsats bör du undersöka hemligheterna i ditt nyckel valv som påverkas:
1. Öppna sidan "säkerhet" på Azure Key Vault och visa den utlöst aviseringen.
1. Välj den speciella avisering som har utlösts.
    Granska listan över de hemligheter som har öppnats och tidsstämpeln.
1. Om du har aktiverat diagnostiska loggar för nyckel valvet, granskar du tidigare åtgärder för motsvarande anropare IP, användarens huvud namn eller objekt-ID.  

## <a name="step-4-take-action"></a>Steg 4. Vidta åtgärd 
När du har kompilerat listan med hemligheter, nycklar och certifikat som har öppnats av den misstänkta användaren eller programmet bör du rotera dessa objekt direkt.

1. Berörda hemligheter bör inaktive ras eller tas bort från ditt nyckel valv.
1. Om autentiseringsuppgifterna användes för ett specifik program:
    1. Kontakta administratören för programmet och be dem granska sin miljö för all användning av de komprometterade autentiseringsuppgifterna, sedan de komprometterades.
    1. Om de komprometterade autentiseringsuppgifterna användes ska program ägaren identifiera den information som har använts och minimera påverkan.


## <a name="next-steps"></a>Nästa steg

På den här sidan förklaras processen att svara på en avisering från Azure Defender för Key Vault. För relaterad information, se följande sidor:

- [Introduktion till Azure Defender för Key Vault](defender-for-key-vault-introduction.md)
- [Ignorera aviseringar från Azure Defender](alerts-suppression-rules.md)
- [Exportera Security Center data kontinuerligt](continuous-export.md)