---
title: Om Azure Migrate
description: Läs mer om tjänsten Azure Migrate.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 72f2dfb3e5e05466892d838af4e1c84c855b94a9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "86122089"
---
# <a name="about-azure-migrate"></a>Om Azure Migrate

Den här artikeln ger en snabb översikt över Azure Migrates tjänsten.

Azure Migrate tillhandahåller en central hubb för att utvärdera och migrera till lokala Azure-servrar, infrastruktur, program och data. Det ger följande:

- **Plattform för enhetlig migrering**: en enda portal för att starta, köra och spåra migreringen till Azure.
- **Olika verktyg**: en uppsättning verktyg för utvärdering och migrering. Azure Migrate verktyg inkluderar Server utvärdering och Azure Migrate: Server-migrering. Azure Migrate integreras också med andra Azure-tjänster och-verktyg och med oberoende program varu leverantörer (ISV)-erbjudanden.
- **Utvärdering och migrering**: i Azure Migrate hubb kan du utvärdera och migrera:
    - **Servrar**: utvärdera lokala servrar och migrera dem till virtuella Azure-datorer eller Azure VMware-lösning (AVS) (för hands version).
    - **Databaser**: utvärdera lokala databaser och migrera dem till Azure SQL Database eller till SQL-hanterad instans.
    - **Webb program**: utvärdera lokala webb program och migrera dem till Azure App Service med hjälp av Azure App Service Migration Assistant.
    - **Virtuella skriv bord**: utvärdera din lokala virtuella Skriv bords infrastruktur (VDI) och migrera den till Windows Virtual Desktop i Azure.
    - **Data**: Migrera stora mängder data till Azure snabbt och kostnads effektivt med Azure Data Box produkter.

## <a name="integrated-tools"></a>Integrerade verktyg

Azure Migrate Hub innehåller följande verktyg:

**Verktyg** | **Utvärdera och migrera** | **Information**
--- | --- | ---
**Azure Migrate: Server utvärdering** | Utvärdera servrar. | Identifiera och utvärdera lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar som förbereder migrering till Azure.
**Azure Migrate: Server-migrering** | Migrera servrar. | Migrera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, andra virtualiserade datorer och virtuella datorer i offentliga moln till Azure.
**Data Migration Assistant** | Utvärdera SQL Server-databaser för migrering till Azure SQL Database, Azure SQL-hanterad instans eller virtuella Azure-datorer som kör SQL Server. | Data Migration Assistant hjälper till att hitta potentiella problem med att blockera migrering. Den identifierar funktioner som inte stöds, nya funktioner som kan dra nytta av dig efter migreringen och rätt sökväg för migrering av databasen. [Läs mer](/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Migrera lokala databaser till virtuella Azure-datorer som kör SQL Server-, Azure SQL Database-eller SQL-hanterade instanser. | [Läs mer](../dms/dms-overview.md) om Database migration service.
**Movere** | Utvärdera servrar. | [Läs mer](#movere) om superkraft.
**Migreringsverktyg för webbapp** | Utvärdera lokala webbappar och migrera dem till Azure. |  Använd Azure App Service Migration Assistant för att utvärdera lokala webbplatser för migrering till Azure App Service.<br/><br/> Använd Migration Assistant för att migrera .NET-och PHP-webbappar till Azure. [Läs mer](https://appmigration.microsoft.com/) om Azure App Service Migration Assistant.
**Azure Data Box** | Migrera offline-data. | Använd Azure Data Box produkter för att flytta stora mängder offlinedata till Azure. [Läs mer](../databox/index.yml).

> [!NOTE]
> Om du befinner dig i Azure Government kan inte externa integrerade verktyg och ISV-erbjudanden skicka data till Azure Migrate-projekt. Du kan använda verktyg oberoende av varandra.

## <a name="isv-integration"></a>ISV-integrering

Azure Migrate integreras med flera ISV-erbjudanden. 

**ISV**    | **Funktion**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrera servrar.
[Cloudamize](https://www.cloudamize.com/platform) | Utvärdera servrar.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Utvärdera och migrera servrar.
[Device42](https://docs.device42.com/) | Utvärdera servrar.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Utvärdera VDI.
[Rack](https://go.microsoft.com/fwlink/?linkid=2102735) | Migrera servrar.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Utvärdera servrar.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Utvärdera servrar och databaser.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Server bedömnings verktyg

Verktyget Azure Migrate: Server bedömning identifierar och utvärderar lokala virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar för migrering till Azure. 

Så här fungerar verktyget:

- **Azure-beredskap**: Bedöm om lokala datorer är klara för migrering till Azure.
- **Azure-storlek**: uppskattar storleken på virtuella Azure-datorer eller antalet Azure VMware-noder efter migreringen.
- **Kostnads uppskattning i Azure**: uppskattar kostnader för att köra lokala servrar i Azure.
- Beroende **analys**: identifierar beroenden mellan servrar och optimerings strategier för att flytta beroende servrar till Azure. Lär dig mer om Server utvärdering med [beroende analys](concepts-dependency-visualization.md).

Server utvärderingen använder en förenklad [Azure Migrate](migrate-appliance.md) -enhet som du distribuerar lokalt.

- Installationen körs på en virtuell dator eller fysisk server. Du kan enkelt installera det med en Hämtad mall.
- Enheten identifierar lokala datorer. Den skickar också kontinuerligt metadata och prestanda data till Azure Migrate.
- Identifiering av enheter är agenten. Inget har installerats på identifierade datorer.
- Efter installationen av enheten kan du samla identifierade datorer i grupper och köra utvärderingar för varje grupp.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: Migreringsverktyg för Server

Med verktyget Azure Migrate: Migreringsverktyg kan du migrera till Azure:

**Flyttar** | **Information**
--- | ---
Lokala virtuella VMware-datorer | Migrera virtuella datorer till Azure med hjälp av en agent utan agent eller en agent-baserad migrering.<br/><br/> Vid migrering utan Agent använder Server Migration en Azure Migrate-enhet som du distribuerar lokalt. Det är samma typ av apparat som du använder för Server utvärdering.<br/><br/> För agent-baserad migrering använder Server utvärdering en replikeringsprincip.
Lokala virtuella Hyper-V-datorer | Migrera virtuella datorer till Azure.<br/><br/> Server utvärderingen använder leverantörs agenter som är installerade på Hyper-V-värden för migreringen.
Lokala fysiska servrar | Du kan migrera fysiska datorer till Azure. Du kan också migrera andra virtualiserade datorer och virtuella datorer från andra offentliga moln genom att behandla dem som virtuella datorer i syfte att migrera. | Server utvärderingen använder en replikeringsfil för migreringen.


## <a name="selecting-assessment-and-migration-tools"></a>Välja verktyg för bedömning och migrering

I Azure Migrate hubb väljer du det verktyg som du vill använda för utvärdering eller migrering och lägger till det i ett Azure Migrate-projekt. Om du lägger till ett ISV-verktyg eller en driv kraft:

- Kom igång genom att skaffa en licens eller registrera dig för en kostnads fri utvärderings version genom att följa anvisningarna i verktyget. Varje ISV eller verktyg anger verktygs licensiering.
- Varje verktyg har ett alternativ för att ansluta till Azure Migrate. Följ anvisningarna för verktyget för att ansluta.
- Spåra migreringen över alla verktyg i Azure Migrate projektet.

## <a name="movere"></a>Movere

Superkraft är en SaaS-plattform (Software as a Service). Det ökar Business Intelligence genom att noggrant presentera hela IT-miljöer inom en och samma dag. Organisationer och företag växer, förändras och digitalt optimeras. I takt med att de gör det förser den med den säkerhet som krävs för att kunna se och kontrol lera deras miljöer, oavsett plattform, program eller geografi.

Microsoft har [förvärvat](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) sin förflyttning och säljs inte längre som ett fristående erbjudande. Superkraft är tillgängligt via Microsoft Solution Assessment och Microsoft Cloud ekonomi program. [Läs mer](https://www.movere.io) om superkraft.

Vi rekommenderar att du även tittar på Azure Migrate, vår inbyggda migration service. Azure Migrate tillhandahåller en central hubb för att förenkla din migrering av molnet. Navet har omfattande stöd för arbets belastningar som fysiska och virtuella servrar, databaser och program. Med början till slut kan du enkelt spåra framsteg i hela identifieringen, utvärderingen och migreringen.

Med både Azure-och partners inbyggda ISV-verktyg har Azure Migrate många olika funktioner, bland annat:

- Identifiering av virtuella och fysiska servrar.
- Prestanda-baserad behörighet.
- Kostnads planering.
- Import-baserade utvärderingar.
- Beroende analys av program utan agent.

Om du behöver hjälp med att komma igång har Microsoft kvalificerad [Azure expert Managed Service Providers](https://azure.microsoft.com/partners) som hjälper dig. Kolla in [Azure Migrate webbplats](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns två versioner av tjänsten Azure Migrate.

- **Aktuell version**: Använd den här versionen för att skapa Azure Migrate projekt, identifiera lokala datorer och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md) om vad som är nytt i den här versionen.
- **Tidigare version**: den tidigare versionen av Azure Migrate stöder endast utvärdering av lokala virtuella VMware-datorer. Om du använde den tidigare versionen bör du nu använda den aktuella versionen. Du kan inte längre skapa Azure Migrate projekt med hjälp av den tidigare versionen. Och vi rekommenderar att du inte gör nya identifieringar med den.

    Om du vill komma åt befintliga projekt i Azure Portal söker du efter och väljer **Azure Migrate**. **Azure Migrate** -instrumentpanelen har ett meddelande och en länk för att komma åt gamla Azure Migrate-projekt.

## <a name="next-steps"></a>Nästa steg

- Prova våra självstudier för att utvärdera virtuella [VMware-datorer](tutorial-prepare-vmware.md), [virtuella Hyper-V-datorer](tutorial-prepare-hyper-v.md)eller [fysiska servrar](tutorial-prepare-physical.md).
- [Läs vanliga frågor och svar](resources-faq.md) om Azure Migrate.
