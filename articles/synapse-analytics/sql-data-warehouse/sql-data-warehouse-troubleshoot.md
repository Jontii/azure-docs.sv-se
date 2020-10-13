---
title: Felsöka Synapse SQL
description: Felsöka Synapse SQL i Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bce5e14db133ad55a2035c0c8074486ed1b8a6ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460515"
---
# <a name="troubleshooting-synapse-sql-in-azure-synapse-analytics"></a>Felsöka Synapse SQL i Azure Synapse Analytics

Den här artikeln innehåller vanliga fel söknings problem i Synapse SQL.

## <a name="connecting"></a>Ansluta

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Inloggningen misslyckades för användaren NT AUTHORITY\ANONYMOUS LOGON. (Microsoft SQL Server, fel: 18456) | Det här felet uppstår när en Azure AD-användare försöker ansluta till huvud databasen, men inte har en användare i Master.  Du kan åtgärda det här problemet genom att antingen ange den SQL-pool som du vill ansluta till vid anslutnings tiden eller lägga till användaren i huvud databasen.  Mer information finns i [säkerhets översikts](sql-data-warehouse-overview-manage-security.md) artikeln. |
| Serverns huvudnamn MyUserName kan inte komma åt huvuddatabasen i den aktuella säkerhetskontexten. Det går inte att öppna användarens standarddatabas. Det gick inte att logga in. Inloggningen misslyckades för användaren MyUserName. (Microsoft SQL Server, fel: 916) | Det här felet uppstår när en Azure AD-användare försöker ansluta till huvud databasen, men inte har en användare i Master.  Du kan åtgärda det här problemet genom att antingen ange den SQL-pool som du vill ansluta till vid anslutnings tiden eller lägga till användaren i huvud databasen.  Mer information finns i [säkerhets översikts](sql-data-warehouse-overview-manage-security.md) artikeln. |
| CTAIP-fel                                                  | Det här felet kan inträffa när en inloggning har skapats på SQL Database huvud databasen, men inte i den aktuella SQL-databasen.  Om du stöter på det här felet kan du ta en titt på artikeln [säkerhets översikt](sql-data-warehouse-overview-manage-security.md) .  Den här artikeln beskriver hur du skapar en inloggning och användare i huvud databasen och hur du skapar en användare i en SQL-databas. |
| Blockerad av brand väggen                                          | SQL-pooler skyddas av brand väggar för att se till att endast kända IP-adresser har åtkomst till en databas. Brand väggarna är säkra som standard, vilket innebär att du måste uttryckligen aktivera och IP-adresser eller adress intervall innan du kan ansluta.  Konfigurera brand väggen för åtkomst genom att följa stegen i [Konfigurera serverns brand Väggs åtkomst för klientens IP-adress](create-data-warehouse-portal.md) i [etablerings anvisningarna](create-data-warehouse-portal.md). |
| Det går inte att ansluta med verktyget eller driv rutinen                           | Synapse SQL-pool rekommenderar att du använder [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [SSDT för Visual Studio](sql-data-warehouse-install-visual-studio.md)eller [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md) för att fråga dina data. Mer information om driv rutiner och hur du ansluter till Azure Synapse finns i [driv rutiner för Azure Synapse](sql-data-warehouse-connection-strings.md) och [ansluta till Azure Synapse](sql-data-warehouse-connect-overview.md) -artiklar. |

## <a name="tools"></a>Verktyg

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Azure AD-användare saknas i Visual Studio Object Explorer           | Detta är ett känt fel.  Som en lösning kan du Visa användare i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Mer information om hur du använder Azure Active Directory med Synapse SQL-poolen finns i [autentisering med Azure Synapse](sql-data-warehouse-authentication.md) . |
| Manuell skript användning med skript guiden eller anslutning via SSMS är långsam, svarar inte eller skapar fel | Se till att användarna har skapats i huvud databasen. I skript alternativ ser du också till att motor versionen är inställd på "Microsoft Azure Synapse Analytics Edition" och att motor typen är "Microsoft Azure SQL Database". |
| Generera skript Miss lyckas i SSMS                               | Det går inte att skapa ett skript för SQL-poolen Synapse om alternativet "skapa skript för beroende objekt" är inställt på "true". Som en lösning måste användarna manuellt gå till **verktyg-> alternativ->SQL Server Object Explorer-> skapa skript för beroende alternativ och ange värdet FALSKT** |

## <a name="performance"></a>Prestanda

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Felsöka prestanda fel sökning                            | Om du försöker Felsöka en viss fråga börjar du med [att lära dig hur du övervakar dina frågor](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| Problem med TempDB-utrymme | [Övervaka](sql-data-warehouse-manage-monitor.md#monitor-tempdb) användning av tempdb-utrymme.  Vanliga orsaker till att slut på TempDB-utrymme körs är:<br>-Inte tillräckligt med resurser allokeras till den fråga som orsakar att data spiller till TempDB.  Se [hantering av arbets belastning](resource-classes-for-workload-management.md) <br>– Statistik saknas eller är inaktuell och orsakar onödig data förflyttning.  Se [underhålla tabell statistik](sql-data-warehouse-tables-statistics.md) för information om hur du skapar statistik<br>– TempDB-utrymme tilldelas per service nivå.  Om [du skalar SQL-poolen](sql-data-warehouse-manage-compute-overview.md#scaling-compute) till en högre DWU-inställning allokeras mer tempdb-utrymme.|
| Dåliga frågor och prestanda är ofta en följd av statistik som saknas | Den vanligaste orsaken till dåliga prestanda är brist på statistik för dina tabeller.  Mer information om hur du skapar statistik och varför de är viktiga för dina prestanda finns i [underhålla tabell statistik](sql-data-warehouse-tables-statistics.md) . |
| Låg concurrency/frågor i kö                             | Det är viktigt att förstå [arbets belastnings hanteringen](resource-classes-for-workload-management.md) för att förstå hur du balanserar minnesallokering med samtidighet. |
| Så här implementerar du bästa metoder                              | Den bästa platsen för att lära dig hur du kan förbättra prestandan för frågor är [SYNAPSE SQL pool Best Practices](sql-data-warehouse-best-practices.md) . |
| Förbättra prestanda med skalning                      | Lösningen för att förbättra prestanda är ibland att helt enkelt lägga till mer beräknings kraft för dina frågor genom [att skala SQL-poolen](sql-data-warehouse-manage-compute-overview.md). |
| Dåliga frågeresultat på grund av dålig index kvalitet     | Vissa gånger kan frågor sakta ned på grund av [dålig kolumn kvalitet i columnstore-index](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  Se den här artikeln för mer information och hur du [bygger om index för att förbättra segment kvaliteten](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>System hantering

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| MSG 40847: det gick inte att utföra åtgärden eftersom servern skulle överskrida den tillåtna kvoten för databas transaktions enheten på 45000. | Du kan antingen minska [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) för den databas som du försöker skapa eller [begära en kvot ökning](sql-data-warehouse-get-started-create-support-ticket.md). |
| Undersöka utrymmes användningen                              | Se [tabell storlekar](sql-data-warehouse-tables-overview.md#table-size-queries) för att förstå systemets utrymmes användning. |
| Hjälp med att hantera tabeller                                    | Mer information om hur du hanterar tabeller finns i artikeln [Översikt över](sql-data-warehouse-tables-overview.md) tabeller.  Den här artikeln innehåller också länkar till mer detaljerade ämnen som [tabell data typer](sql-data-warehouse-tables-data-types.md), [distribuera en tabell](sql-data-warehouse-tables-distribute.md), [indexera en tabell](sql-data-warehouse-tables-index.md),  [partitionera en tabell](sql-data-warehouse-tables-partition.md), [underhålla tabell statistik](sql-data-warehouse-tables-statistics.md) och [temporära tabeller](sql-data-warehouse-tables-temporary.md). |
| Förlopps indikatorn för transparent data kryptering (TDE) uppdateras inte i Azure Portal | Du kan visa statusen för TDE via [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Skillnader från SQL Database

| Problem                                 | Lösning                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| SQL Database funktioner som inte stöds     | Se [tabell funktioner som inte stöds](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| SQL Database data typer som inte stöds   | Se [data typer som inte stöds](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| TA bort och uppdatera begränsningar         | Se [uppdaterings lösningar](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements), [ta bort lösningar](sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements) och [Använd CTAs för att arbeta runt en uppdaterings-och borttagnings-syntax som inte stöds](sql-data-warehouse-develop-ctas.md). |
| MERGE-instruktionen stöds inte      | Se [sammanfognings lösningar](sql-data-warehouse-develop-ctas.md#replace-merge-statements).                  |
| Begränsningar för lagrade procedurer          | Se [begränsningar för lagrade](sql-data-warehouse-develop-stored-procedures.md#limitations) procedurer för att förstå vissa begränsningar för lagrade procedurer. |
| UDF: er stöder inte SELECT-instruktioner | Detta är en aktuell begränsning i vår UDF: er.  Se [skapa funktion](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för den syntax som vi stöder. |

## <a name="next-steps"></a>Nästa steg

Om du vill ha hjälp med att hitta en lösning på problemet kan du göra något av följande resurser.

* [Bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Funktionsbegäranden](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videor](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md)
* [Sidan Microsoft Q&en fråga](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)
