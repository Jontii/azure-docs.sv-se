---
title: Skydda en databas
description: Tips för att skydda en databas och utveckla lösningar i en Synapse för SQL-pool.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 06f62fd656357e16396a0458a9afee12dcfa507f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629377"
---
# <a name="secure-a-database-in-azure-synapse"></a>Skydda en databas i Azure Synapse

> [!div class="op_single_selector"]
>
> * [Säkerhets översikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

I den här artikeln får du stegvisa anvisningar om hur du skyddar din Synapse SQL-pool. I synnerhet hjälper den här artikeln dig att komma igång med resurser för att begränsa åtkomst, skydda data och övervaka aktiviteter på en databas som har skapats med SQL-poolen.

## <a name="connection-security"></a>Anslutningssäkerhet

Anslutningssäkerhet avser hur du begränsar och säkrar anslutningar till databasen med hjälp av brandväggsregler och krypterad anslutning.

Brand Väggs regler används av både den [logiska SQL-servern](../../azure-sql/database/logical-servers.md) och dess databaser för att avvisa anslutnings försök från IP-adresser som inte har godkänts explicit. Om du vill tillåta anslutningar från ditt program eller klient datorns offentliga IP-adress måste du först skapa en brand Väggs regel på server nivå med hjälp av Azure Portal, REST API eller PowerShell.

Som bästa praxis bör du begränsa de IP-adressintervall som tillåts via brand väggen på server nivå så mycket som möjligt.  Om du vill komma åt SQL-poolen från den lokala datorn kontrollerar du att brand väggen på nätverket och den lokala datorn tillåter utgående kommunikation på TCP-port 1433.  

Azure Synapse Analytics använder IP-brandvägg på server nivå. Den har inte stöd för IP-brandväggs regler på databas nivå. Mer information finns i se [Azure SQL Database brand Väggs regler](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Anslutningar till SQL-poolen är krypterade som standard.  Ändring av anslutnings inställningar för att inaktivera kryptering ignoreras.

## <a name="authentication"></a>Autentisering

Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL-poolen stöder för närvarande SQL Server autentisering med ett användar namn och lösen ord och med Azure Active Directory.

När du skapade servern för din databas angav du en "Server administratör"-inloggning med ett användar namn och lösen ord. Med dessa autentiseringsuppgifter kan du autentisera till valfri databas på servern som databasens ägare, eller "dbo" genom SQL Server autentisering.

Men som bästa praxis bör din organisations användare använda ett annat konto för att autentisera. På så sätt kan du begränsa de behörigheter som beviljats för programmet och minska riskerna med skadlig aktivitet, om program koden är utsatt för en SQL-attack.

Om du vill skapa en SQL Server autentiserad användare ansluter du till **huvud** databasen på servern med Server administratörs inloggningen och skapar en ny server inloggning.  Det är en bra idé att även skapa en användare i huvud databasen. Genom att skapa en användare i Master kan en användare logga in med hjälp av verktyg som SSMS utan att ange ett databas namn.  Det gör det också möjligt för dem att använda Object Explorer för att visa alla databaser på en server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Anslut sedan till din **SQL-adresspool** med Server Administratörs inloggning och skapa en databas användare baserat på Server inloggningen som du skapade.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Om du vill ge en användare behörighet att utföra ytterligare åtgärder, till exempel skapa inloggningar eller skapa nya databaser, tilldelar du användaren till- `Loginmanager` och- `dbmanager` rollerna i huvud databasen.

Mer information om dessa ytterligare roller och autentisering till en SQL Database finns [i hantera databaser och inloggningar i Azure SQL Database](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Mer information om hur du ansluter med hjälp av Azure Active Directory finns i [ansluta med hjälp av Azure Active Directory autentisering](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Auktorisering

Auktorisering syftar på vad du kan göra i en databas när du är autentiserad och ansluten. Behörigheterna för auktorisering bestäms av roll medlemskap och behörigheter. Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Du kan använda följande lagrade procedurer för att hantera roller:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet.

Det finns sätt att ytterligare begränsa vad en användare kan göra i databasen:

* Med detaljerade [behörigheter](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kan du styra vilka åtgärder du kan utföra på enskilda kolumner, tabeller, vyer, scheman, procedurer och andra objekt i databasen. Använd detaljerade behörigheter för att få ut mesta möjliga kontroll och bevilja de lägsta behörigheter som krävs.
* Andra [databas roller](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) än db_datareader och db_datawriter kan användas för att skapa mer kraftfulla program användar konton eller mindre kraftfulla hanterings konton. De inbyggda fasta databas rollerna ger ett enkelt sätt att bevilja behörigheter, men kan resultera i att ge fler behörigheter än vad som behövs.
* [Lagrade procedurer](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kan användas för att begränsa de åtgärder som kan utföras i databasen.

I följande exempel beviljas Läs behörighet till ett användardefinierat schema.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Hantering av databaser och servrar från Azure Portal eller med hjälp av Azure Resource Manager-API: t styrs av ditt användar kontos roll tilldelningar för portalen. Mer information finns i [rollbaserad åtkomst kontroll i Azure Portal](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Kryptering

Transparent datakryptering (TDE) skyddar mot hot mot skadlig aktivitet genom att kryptera och dekryptera data i vila. När du krypterar din databas krypteras tillhör ande säkerhets kopior och transaktionsloggfiler utan att det krävs några ändringar i dina program. Transparent datakryptering (TDE) krypterar lagringen av en hel databas med hjälp av en symmetrisk nyckel kallad databaskrypteringsnyckeln.

I SQL Database skyddas databas krypterings nyckeln av ett inbyggt Server certifikat. Det inbyggda Server certifikatet är unikt för varje server. Microsoft roterar dessa certifikat automatiskt minst var 90: e dag. Den krypteringsalgoritm som används är AES-256. En allmän beskrivning av TDE finns [Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Du kan kryptera databasen med hjälp av [Azure Portal](sql-data-warehouse-encryption-tde.md) eller [T-SQL](sql-data-warehouse-encryption-tde-tsql.md).

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du ansluter till ditt lager med olika protokoll finns i [Anslut till SQL-pool](../sql/connect-overview.md).
