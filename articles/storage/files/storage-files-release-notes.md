---
title: Viktig information för Azure File Sync agenten | Microsoft Docs
description: Läs viktig information för Azure File Sync-agenten där du kan centralisera organisationens fil resurser i Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 9/24/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 0b99ce2afcdb5fd7462827fb9893e34577fc6c02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371209"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Viktig information om Azure File Sync-agenten
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Dina Windows Server-installationer omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln innehåller viktig information om versioner av Azure File Sync-agenten som stöds.

## <a name="supported-versions"></a>Versioner som stöds
Följande Azure File Sync agent versioner stöds:

| Gränser | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| V 10.1-version – [KB4522411](https://support.microsoft.com/en-us/help/4522411)| 10.1.0.0 | 5 juni 2020 | Stöds |
| Samlad uppdatering för 2020 maj – [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 maj 2020 | Stöds |
| V10-version – [KB4522409](https://support.microsoft.com/en-us/help/4522409)| 10.0.0.0 | 9 april 2020 | Stöds |
| Samlad uppdatering från december 2019 – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 december 2019 | Agent versionen som stöds upphör att gälla den 16 februari 2021 |
| V9-version – [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 december 2019 | Agent versionen som stöds upphör att gälla den 16 februari 2021 |
| V8-version – [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 oktober 2019 | Agent versionen som stöds upphör att gälla den 12 januari 2021 |

## <a name="unsupported-versions"></a>Versioner som inte stöds
Följande Azure File Sync agent versioner har upphört att gälla och stöds inte längre:

| Gränser | Agentversionsnummer | Utgivningsdatum | Status |
|----|----------------------|--------------|------------------|
| V7-version | 7.0.0.0 - 7.2.0.0 | E.t. | Stöds inte-agent versioner har upphört att gälla den 1 september 2020 |
| V6-version | 6.0.0.0 - 6.3.0.0 | E.t. | Stöds inte-agent versioner upphörde att gälla den 21 april 2020 |
| Version V5 | 5.0.2.0 - 5.2.0.0 | E.t. | Stöds inte-agent versioner har upphört att gälla den 18 mars 2020 |
| V4-version | 4.0.1.0 - 4.3.0.0 | E.t. | Stöds inte-agent versioner upphörde att gälla den 6 november 2019 |
| V3-version | 3.1.0.0 - 3.4.0.0 | E.t. | Stöds inte-agent versioner har upphört att gälla den 19 augusti 2019 |
| För GA-agenter | 1.1.0.0 – 3.0.13.0 | E.t. | Stöds inte-agent versioner har upphört att gälla den 1 oktober 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10100"></a>10.1.0.0 för agent version
Följande versions information gäller version 10.1.0.0 av den Azure File Sync agenten som publicerades den 5 juni 2020. De här anteckningarna är utöver de versions anteckningar som anges för version 10.0.0.0 och 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Stöd för privata Azure-slutpunkter
    - Synkronisering av trafik till tjänsten Storage Sync kan nu skickas till en privat slut punkt. Detta möjliggör tunnel trafik över en ExpressRoute eller VPN-anslutning. Mer information finns i [konfigurera Azure File Sync nätverks slut punkter](https://docs.microsoft.com/azure/storage/files/storage-sync-files-networking-endpoints).
- Mått för synkroniserade filer visar nu förloppet medan en stor synkronisering körs, i stället för i slutet.
- Diverse Tillförlitlighets förbättringar för agent installation, moln nivåer, synkronisering och telemetri

## <a name="agent-version-10020"></a>10.0.2.0 för agent version
Följande viktiga information gäller version 10.0.2.0 av den Azure File Sync agent som lanserades den 19 maj 2020. De här anteckningarna är utöver de versions anteckningar som anges för version 10.0.0.0.

Problem korrigerat i den här versionen:  
- FileSyncSvc-agenten (Storage Sync agent) kraschar ofta när Azure File Sync v10-agenten har installerats.

> [!Note]  
>Den här versionen har inte överförts till servrar som är konfigurerade att uppdateras automatiskt när en ny version blir tillgänglig. Om du vill installera den här uppdateringen använder du Microsoft Update eller Microsoft Update katalog (se [KB4522412](https://support.microsoft.com/help/4522412) för Installationsinstruktioner).

## <a name="agent-version-10000"></a>Agent version 10.0.0.0
Följande viktiga information gäller version 10.0.0.0 av Azure File Sync agent (lanserades den 9 april 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Förbättrat synkroniserings förlopp i portalen
    - Med v10 agent-versionen börjar Azure Portal snart att visa vilken typ av synkroniseringsanslutning som körs. T.ex. inledande hämtning, normal hämtning, bakgrunds återkallande (snabba haveri återställnings fall) och liknande. 

- Förbättrad Portal upplevelse för moln nivåer
    - Om du har filer som inte kan gå till nivån eller återkalla kan du nu visa fel skikts fel i egenskaperna för Server slut punkten.
    - Ytterligare information om moln nivåer är tillgänglig för en server slut punkt:
        - Storlek på lokal cache
        - Användnings effektivitet för cache
        - Princip information för moln skikt: volym storlek, Aktuellt ledigt utrymme eller senaste åtkomst tid för den äldsta filen i det lokala cacheminnet.
    - De här ändringarna kommer att leverera i Azure Portal strax efter den första agent versionen av v10.

- Stöd för att flytta synkroniseringstjänsten för lagring och/eller lagrings konto till en annan Azure Active Directory klient
    - Azure File Sync stöder nu att flytta synkroniseringstjänsten för lagring och/eller lagrings kontot till en annan resurs grupp, prenumeration eller Azure AD-klient.
    
- Diverse förbättringar av prestanda och tillförlitlighet
    - Ändrings identifieringen på Azure-filresursen kan Miss lyckas om det virtuella nätverket (VNET) och brand Väggs regler har kon figurer ATS på lagrings kontot.
    - Minskad minnes användning som är kopplad till återkallning. 
    - Bättre prestanda när du använder cmdleten [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
    - Andra förbättringar av ökad tillförlitlighet. 
    
### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten Storage Sync och/eller lagrings kontot kan flyttas till en annan resurs grupp, prenumeration eller Azure AD-klient. När lagrings tjänsten för synkronisering eller lagrings kontot har flyttats måste du ge Microsoft. StorageSync-programmet åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > När du skapar moln slut punkten måste Storage Sync-tjänsten och lagrings kontot finnas i samma Azure AD-klient. När moln slut punkten har skapats kan tjänsten Storage Sync och lagrings kontot flyttas till olika Azure AD-klienter.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.

## <a name="agent-version-9100"></a>9.1.0.0 för agent version
Följande viktiga information gäller version 9.1.0.0 av Azure File Sync agent som lanserades den 12 december 2019. De här anteckningarna är utöver de versions anteckningar som anges för version 9.0.0.0.

Problem korrigerat i den här versionen:  
- Synkroniseringen Miss lyckas med ett av följande fel efter uppgraderingen till Azure File Sync agent version 9,0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>9.0.0.0 för agent version
Följande viktig information gäller version 9.0.0.0 av Azure File Sync agent (lanserades 2 december 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Support för självbetjänings återställning
    - Användarna kan nu återställa sina filer med hjälp av funktionen för tidigare versioner. Innan v9-versionen stöds inte den tidigare versions funktionen på volymer där moln skiktning är aktiverat. Den här funktionen måste vara aktive rad för varje volym separat, på vilken en slut punkt med aktive rad moln nivå finns. Mer information finns i  
[Återställning via självbetjäning genom tidigare versioner och VSS (tjänsten Volume Shadow Copy)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Stöd för större fil resurs storlekar 
    - Azure File Sync stöder nu upp till 64TiB-och 100 000 000-filer i ett enda, syncing-namnområde.  
 
- Stöd för datadeduplicering på Server 2019 
    - Datadeduplicering stöds nu med moln nivåer som är aktiverade på Windows Server 2019. Windows Update- [KB4520062](https://support.microsoft.com/help/4520062) måste vara installerat för att stödja datadeduplicering på volymer med moln nivåer. 
 
- Förbättrad minsta fil storlek för en fil till nivå 
    - Den minsta fil storleken för en fil till-nivån baseras nu på fil systemets kluster storlek (dubbelt fil systemets kluster storlek). Som standard är till exempel fil systemets NTFS-storlek 4KB, den resulterande minsta fil storleken för en fil till nivån är 8 KB. 
 
- Test-cmdlet för nätverks anslutning 
    - Som en del av Azure File Sync konfiguration måste flera tjänst slut punkter kontaktas. De har sina egna DNS-namn som måste vara tillgängliga för-servern. Dessa URL: er är också speciella för regionen som en server är registrerad på. När en server har registrerats kan anslutnings test cmdlet (PowerShell och Server registrerings verktyg) användas för att testa kommunikationen med alla URL: er som är kopplade till den här servern. Den här cmdleten kan hjälpa till att felsöka när ofullständig kommunikation förhindrar att servern fungerar fullt ut med Azure File Sync och kan användas för att finjustera konfigurationer för proxy och brand väggar.  
 
        Kör följande PowerShell-kommandon för att köra testet för nätverks anslutning: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Ta bort Server slut punkts förbättring när moln skiktning är aktiverat 
    - Som tidigare resulterar det inte i att ta bort filer i Azure-filresursen om du tar bort en server slut punkt. Beteendet för referens punkter på den lokala servern har dock ändrats. Referens punkter (pekare till filer som inte är lokala på servern) tas nu bort när du tar bort en server slut punkt. De fullständigt cachelagrade filerna är kvar på servern. Den här förbättringen gjordes för att förhindra [överblivna filer på nivån](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) när en server slut punkt togs bort. Om Server slut punkten återskapas, kommer referens punkterna för de nivåbaserade filerna att återskapas på servern.  
 
- Förbättringar av prestanda och tillförlitlighet 
    - Minskat återställnings problem. Åter kallelse storleken justeras nu automatiskt baserat på nätverks bandbredden. 
    - Förbättrad nedladdnings prestanda när du lägger till en ny server i en Sync-grupp. 
    - Mindre filer synkroniseras inte på grund av begränsnings konflikter. 
    - Filerna går inte att flytta till nivån eller återkallas i vissa fall, om Server slut punkten är en volym monterings punkt.
    
### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- DACL-delen av en säkerhetsbeskrivning om den är större än 2 kB. (Det här är endast ett problem om du har mer än ca 40 åtkomstkontrollposter på ett enskilt objekt.)
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåbaserade filer blir otillgängliga om filerna inte återkallas innan Server slut punkten tas bort. Återskapar Server slut punkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Server slut punkten togs bort, eller om moln slut punkten togs bort, kommer filer som inte återkallats att bli oanvändbara. Mer information finns i [skiktade filer är inte tillgängliga på servern när du har tagit bort en server slut punkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.
- Filer kan Miss lyckas på nivån om pagefile.sys finns på en volym som har aktiverat moln skiktning. pagefile.sys bör finnas på en volym där moln nivå inaktive ras.

## <a name="agent-version-8000"></a>8.0.0.0 för agent version
Följande viktig information gäller version 8.0.0.0 av Azure File Sync agent (lanserades den 8 oktober 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Förbättringar och problem som åtgärdas

- Återställa prestanda förbättringar
    - Snabbare återställnings tider för återställning klar genom Azure Backup. Återställda filer synkroniseras tillbaka till Azure File Sync servrar mycket snabbare. 
- Förbättrad Portal upplevelse för moln nivåer  
    - Om du har på nivå av filer som inte kan återkallas kan du nu Visa återställnings felen i egenskaperna för Server slut punkten. Dessutom visar Server slut punkt hälsan ett fel och en minskning av stegen om moln skikts filter driv rutinen inte har lästs in på servern.
- Enklare agent installation
    - Az\AzureRM PowerShell-modulen krävs inte längre för att registrera servern som gör installationen enklare och snabbt.
- Diverse förbättringar av prestanda och tillförlitlighet

### <a name="evaluation-tool"></a>Utvärderings verktyg
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av verktyget för Azure File Sync utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som kontrollerar eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Anvisningar för installation och användning finns i avsnittet [utvärderings verktyg](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) i planerings guiden. 

### <a name="agent-installation-and-server-configuration"></a>Agentinstallation och serverkonfiguration
Mer information om hur du installerar och konfigurerar Azure File Sync-agenten med Windows Server finns i [Planera för en Azure File Sync distribution](storage-sync-files-planning.md) och [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

- Agent installations paketet måste installeras med utökade behörigheter (admin).
- Agenten stöds inte för distributions alternativet Nano Server.
- Agenten stöds endast på Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2.
- Agenten kräver minst 2 GiB minne. Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
- Tjänsten Storage Sync agent (FileSyncSvc) stöder inte Server slut punkter som finns på en volym som har SVI-katalogen (System Volume information) komprimerad. Den här konfigurationen leder till oväntade resultat.

### <a name="interoperability"></a>Samverkan
- Program som antivirus, program för säkerhetskopiering och andra program som har åtkomst till nivåindelade filer kan orsaka oönskade återkallanden om de inte respekterar attributet offline och hoppar över att läsa innehållet i filerna. Mer information finns i [felsöka Azure File Sync](storage-sync-files-troubleshoot.md).
- Fil gallren i Hanteraren för fil server resurser (FSRM) kan orsaka oändliga synkroniseringsfel när filer blockeras på grund av fil gallret.
- Körning av Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Azure File Sync agenten bör installeras efter att du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="sync-limitations"></a>Synkroniseringsbegränsningar
Följande objekt synkroniseras inte, men resten av systemet fortsätter att fungera normalt:
- Filer med tecken som inte stöds. Se [fel söknings guide](storage-sync-files-troubleshoot.md#handling-unsupported-characters) för en lista med tecken som inte stöds.
- Filer eller kataloger som slutar med en punkt.
- Sökvägar som är längre än 2 048 tecken.
- DACL-delen av en säkerhetsbeskrivning om den är större än 2 kB. (Det här är endast ett problem om du har mer än ca 40 åtkomstkontrollposter på ett enskilt objekt.)
- SACL-delen av en säkerhetsbeskrivning som används för granskning.
- Utökade attribut.
- Alternativa dataströmmar.
- Referenspunkter.
- Hårda länkar.
- Komprimering (om det angetts på en serverfil) bevaras inte när ändringar synkroniseras till filen från andra slutpunkter.
- Alla filer som krypterats med EFS (eller andra typer av kryptering från användarläget) som förhindrar att tjänsten läser data.

    > [!Note]  
    > Azure File Sync krypterar alltid data under överföring. Vilande data är alltid krypterade i Azure.
 
### <a name="server-endpoint"></a>Server slut punkt
- En serverslutpunkt kan endast skapas på en NTFS-volym. ReFS, FAT, FAT32 och andra filsystem stöds inte av Azure File Sync för närvarande.
- Nivåbaserade filer blir otillgängliga om filerna inte återkallas innan Server slut punkten tas bort. Återskapar Server slut punkten för att återställa åtkomsten till filerna. Om 30 dagar har passerat sedan Server slut punkten togs bort, eller om moln slut punkten togs bort, kommer filer som inte återkallats att bli oanvändbara. Mer information finns i [skiktade filer är inte tillgängliga på servern när du har tagit bort en server slut punkt](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.
- Redundansklustring stöds endast med klustrade diskar, inte med klusterdelade volymer (CSV).
- Serverslutpunkter får inte vara kapslade. De får dock finnas på samma volym parallellt med varandra.
- Lagra inte en operativ system-eller program växlings fil på en server slut punkts plats.
- Server namnet i portalen uppdateras inte om servern byter namn.

### <a name="cloud-endpoint"></a>Moln slut punkt
- Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras för en moln slut punkt var 24: e timme. Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering.
- Tjänsten för synkronisering av lagring och/eller lagrings kontot kan flyttas till en annan resurs grupp eller prenumeration i den befintliga Azure AD-klienten. Om lagrings kontot har flyttats måste du ge hybrid File Sync tjänst åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync har inte stöd för att flytta prenumerationen till en annan Azure AD-klient.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln
- Om en nivåindelad fil kopieras till en annan plats med Robocopy så kommer den kopierade filen inte att vara nivåindelad. Offline-attributet kan anges eftersom Robocopy felaktigt tar med det attributet i kopieringsåtgärder.
- När du kopierar filer med Robocopy kan du använda alternativet/MIR för att bevara tidsstämplar för filer. Detta säkerställer att äldre filer går fortare än vid nyligen öppnade filer.
