---
title: Använda Microsoft Defender för slut punkts licens som ingår i Azure Security Center
description: Lär dig mer om Microsoft Defender för slut punkt och att distribuera den från Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: memildin
ms.openlocfilehash: 026425c78934209a2d258d50a5c7f51feeebd63b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275391"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Skydda dina slut punkter med Security Center Integrated EDR-lösning: Microsoft Defender för slut punkt

Microsoft Defender för slut punkt är en holistisk, Cloud-levererad slut punkts säkerhets lösning. Huvud funktionerna är:

- Riskfylld sårbarhets hantering och utvärdering 
- Minska attackytan
- Beteende-och moln driven skydd
- Slut punkts identifiering och-svar (EDR)
- Automatisk undersökning och reparation
- Hanterade jakt tjänster

> [!TIP]
> Startades ursprungligen som **Windows Defender ATP**, den här EDR-produkten har bytt namn till 2019 som **Microsoft Defender ATP**.
>
> Vid antändning 2020 lanserade vi [Microsoft Defender XDR Suite](https://www.microsoft.com/security/business/threat-protection) och den här EDR-komponenten har bytt namn till **Microsoft Defender för slut punkt**.


## <a name="availability"></a>Tillgänglighet

| Aspekt                          | Information                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versions tillstånd:                  | Allmänt tillgänglig (GA)                                                                                                                                                                                                                                                                                      |
| Priset                        | Kräver [Azure Defender för servrar](security-center-pricing.md)                                                                                                                                                                                                                                             |
| Plattformar som stöds:            | ![Ja](./media/icons/yes-icon.png) Azure-datorer som kör Windows<br>![Ja](./media/icons/yes-icon.png) Azure Arc-datorer som kör Windows|
| Versioner av Windows som stöds:  | Defender för slut punkten är inbyggt i Windows 10 1703 (och senare) och Windows Server 2019.<br>Security Center stöder identifiering på Windows Server 2016, 2012 R2 och 2008 R2 SP1.<br>Server slut punkts övervakning med denna integrering har inaktiverats för Office 365 GCC-kunder. |
| Nödvändiga roller och behörigheter: | Aktivera/inaktivera integrering: **säkerhets administratör** eller **ägare**<br>Så här visar du MDATP-aviseringar i Security Center: **säkerhets läsare**, **läsare**, **resurs grupps deltagare**, **resurs grupp ägare**, **säkerhets administratör**, **prenumerations ägare**eller **prenumerations deltagare**                         |
| Moln                         | ![Ja](./media/icons/yes-icon.png) Kommersiella moln.<br>![Nej](./media/icons/no-icon.png) GCC kunder som kör arbets belastningar i globala Azure-moln<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nej](./media/icons/no-icon.png) Kina gov, andra gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |


## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Microsoft Defender för slut punkts funktioner i Security Center

Microsoft Defender för slut punkt innehåller:

- **Avancerade sensorer efter intrångs avkänning**. Defender för slut punkts sensorer för Windows-datorer samlar in en omfattande uppsättning beteende signaler.

- **Analys baserad, molnbaserad identifiering efter intrång**. Defender för slut punkten anpassas snabbt för att ändra hot. Den använder avancerade analyser och Big data. Den förstärks av kraften hos Intelligent Security Graph med signaler över Windows, Azure och Office för att identifiera okända hot. Den innehåller aviseringar som kan åtgärdas och gör det möjligt att svara snabbt.

- **Hotinformation**. Defender för slut punkten genererar aviseringar när den identifierar angrepps verktyg, tekniker och procedurer. Den använder data som genererats av Microsoft Threat Hunters och säkerhets team, som tillhandahålls av våra partner.

Genom att integrera Defender för slut punkt med Security Center kan du dra nytta av följande ytterligare funktioner:

- **Automatiserad onboarding**. Security Center aktiverar automatiskt Microsoft Defender för slut punkts sensor för alla Windows-servrar som övervakas av Security Center (om de inte kör Windows Server 2019).

- **En ruta i glaset**. I Security Center-konsolen visas Microsoft Defender för slut punkts varningar. Om du vill undersöka ytterligare använder du Microsoft Defender för slut punktens egna Portal sidor där du kan se ytterligare information som varnings process trädet och incident diagrammet. Du kan också se en detaljerad dator tids linje som visar varje beteende för en historisk period på upp till sex månader.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender för slutpunktens egna Security Center" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender för plats för slut punkts klient

När du använder Azure Security Center för att övervaka dina servrar skapas automatiskt en Microsoft Defender för slut punkts klient. Data som samlas in av Defender för slut punkten lagras på den geo-platsen för klient organisationen som identifieras under etableringen. Kund information – i pseudonymized form – kan också lagras i de centrala lagrings-och bearbetnings systemen i USA. 

När du har konfigurerat platsen kan du inte ändra den. Om du behöver flytta dina data till en annan plats kontaktar du Microsoft Support för att återställa klienten.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Aktivera Microsoft Defender för slut punkts integrering

1. Aktivera **Azure Defender för servrar**. Se [prissättning för Azure Security Center](security-center-pricing.md#enable-azure-defender).

    > [!NOTE]
    > Du skyddar dina Azure Arc-aktiverade datorer genom att följa anvisningarna i [snabb start: ansluta hybrid dator med Azure Arc-aktiverade servrar](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).

1. Om du redan har licensierat och distribuerat Microsoft Defender för slut punkter på dina servrar, tar du bort den med hjälp av proceduren som beskrivs i [avpublicera Windows-servrar](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers).
1. Från Security Center menyn väljer du **pris & inställningar**.
1. Välj den prenumeration som du vill ändra.
1. Välj **hot identifiering**.
1. Välj **Tillåt Windows Defender ATP för att komma åt mina data**och välj **Spara**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Microsoft Defender för slutpunktens egna Security Center":::

    Azure Security Center kommer automatiskt att publicera dina servrar till Microsoft Defender för slut punkt. Onboarding kan ta upp till 24 timmar.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Åtkomst till Microsoft Defender för slut punkts portalen

1. Se till att användar kontot har de behörigheter som krävs. [Läs mer](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Kontrol lera om du har en proxy eller brand vägg som blockerar anonym trafik. Defender för slut punkts sensorn ansluter från system kontexten, så anonym trafik måste tillåtas. För att förhindra åtkomst till Defender för slut punkts portalen, följ instruktionerna i [ge åtkomst till tjänst-URL: er i proxyservern](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Öppna [Microsoft Defender Security Center-portalen](https://securitycenter.windows.com/). Läs mer om portalens funktioner och ikoner i [Microsoft Defender Security Center Portal-översikt](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Skicka en test avisering

Så här skapar du en ofarlig Microsoft Defender för slut punkts test avisering:

1. Skapa en mapp ' C:\test-MDATP-test '.
1. Använd fjärr skrivbord för att få åtkomst till antingen en virtuell Windows Server 2012 R2-dator eller en virtuell Windows Server 2016-dator.
1. Öppna ett kommandotolksfönster.
1. Kopiera och kör följande kommando i kommando tolken. Kommando tolks fönstret stängs automatiskt.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Microsoft Defender för slutpunktens egna Security Center":::

1. Om kommandot lyckas visas en ny avisering på instrument panelen för Azure Security Center och Microsoft Defender för slut punkts portalen. Den här aviseringen kan ta några minuter innan den visas.
1. Om du vill granska aviseringen i Security Center går du till **säkerhets varningar**  >  ,**misstänkt PowerShell-kommandorad**.
1. I fönstret undersökning väljer du länken för att gå till Microsoft Defender för slut punkts portalen.


## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Vanliga frågor och svar om Security Centers integrerade Microsoft Defender för slut punkt

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Vilka är licens kraven för Microsoft Defender för slut punkten?

Defender för slut punkt ingår utan extra kostnad med **Azure Defender för servrar**. Alternativt kan den köpas separat för 50 datorer eller mer.


### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Hur gör jag för att växlar från ett EDR-verktyg från tredje part?

Fullständiga instruktioner för att byta från en slut punkts lösning som inte kommer från Microsoft finns i dokumentationen för Microsoft Defender för slut punkt: [Översikt över migrering](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Nästa steg

- [Plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md)
- [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.