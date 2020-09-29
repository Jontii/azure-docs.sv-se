---
title: Kontrol lera åtkomsten till lagrings kontot för SQL på begäran (för hands version)
description: Beskriver hur SQL på begäran (för hands version) får åtkomst Azure Storage och hur du kan kontrol lera lagrings åtkomst för SQL på begäran i Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 182ab55f8e86d972293222f8a3bcf32dada89328
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449459"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Kontrol lera åtkomsten till lagrings kontot för SQL på begäran (för hands version)

En SQL-fråga på begäran läser filer direkt från Azure Storage. Behörigheter för att komma åt filerna i Azure Storage styrs på två nivåer:
- **Lagrings nivå** – användaren bör ha behörighet att komma åt underliggande lagringsfiler. Lagrings administratören ska tillåta att Azure AD-huvudobjektet läser/skriver filer, eller genererar SAS-nyckel som används för åtkomst till lagring.
- **SQL Service Level** -användaren ska ha `SELECT` behörighet att läsa data från den [externa tabellen](develop-tables-external-tables.md) eller `ADMINISTER BULK ADMIN` behörighet att köra `OPENROWSET` och även behörighet att använda autentiseringsuppgifter som ska användas för åtkomst till lagring.

Den här artikeln beskriver de typer av autentiseringsuppgifter som du kan använda och hur sökning efter autentiseringsuppgifter registreras för SQL-och Azure AD-användare.

## <a name="supported-storage-authorization-types"></a>Typer av lagringspooler som stöds

En användare som har loggat in på en SQL-resurs på begäran måste ha behörighet att komma åt och fråga filerna i Azure Storage om filerna inte är offentligt tillgängliga. Du kan använda tre typer av autentisering för att komma åt icke-offentlig lagring – [användar identitet](?tabs=user-identity), [delad åtkomst-signatur](?tabs=shared-access-signature)och [hanterad identitet](?tabs=managed-identity).

> [!NOTE]
> **Azure AD-vidarekoppling** är standard beteendet när du skapar en arbets yta.

### <a name="user-identity"></a>[Användar identitet](#tab/user-identity)

**Användar identitet**, som även kallas "Azure AD-vidarekoppling", är en typ av auktorisering där identiteten för den Azure AD-användare som loggade in på begäran används för att ge åtkomst till data. Innan du får åtkomst till data måste Azure Storages administratören bevilja behörighet till Azure AD-användaren. Som anges i tabellen nedan, stöds den inte för SQL-användargruppen.

> [!IMPORTANT]
> Du måste ha rollen som ägare/deltagare/läsare för Storage BLOB-rollen för att kunna använda din identitet för att komma åt data.
> Även om du är ägare till ett lagrings konto behöver du fortfarande lägga till dig själv i en av lagrings BLOB-datarollerna.
>
> Läs mer om åtkomst kontroll i Azure Data Lake Store Gen2 genom att granska [åtkomst kontrollen i Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) artikeln.
>

### <a name="shared-access-signature"></a>[Signatur för delad åtkomst](#tab/shared-access-signature)

**Signaturen för delad åtkomst (SAS)** ger delegerad åtkomst till resurser i ett lagrings konto. Med SAS kan en kund bevilja klienter åtkomst till resurser i ett lagrings konto utan att dela konto nycklar. SAS ger detaljerad kontroll över vilken typ av åtkomst du beviljar till klienter som har en SAS, inklusive giltighets intervall, beviljade behörigheter, acceptabelt IP-adressintervall och acceptabelt protokoll (https/http).

Du kan få en SAS-token genom att gå till **Azure Portal-> lagrings konto-> signatur för delad åtkomst-> konfigurera behörigheter – > generera SAS och anslutnings sträng.**

> [!IMPORTANT]
> När en SAS-token skapas, innehåller den ett frågetecken ("?") i början av token. Om du vill använda token i SQL på begäran måste du ta bort frågetecknet (?) när du skapar en autentiseringsuppgift. Exempel:
>
> SAS-token:? sa = 2018-03-28&SS = bfqt&SRT = SCO&SP = rwdlacup&se = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = https&sig = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

Om du vill aktivera åtkomst med hjälp av en SAS-token måste du skapa en databas-eller Server begränsad autentiseringsuppgift 

### <a name="managed-identity"></a>[Hanterad identitet](#tab/managed-identity)

**Hanterad identitet** kallas även MSI. Det är en funktion i Azure Active Directory (Azure AD) som tillhandahåller Azure-tjänster för SQL på begäran. Dessutom distribueras en automatiskt hanterad identitet i Azure AD. Den här identiteten kan användas för att auktorisera begäran om data åtkomst i Azure Storage.

Innan du får åtkomst till data måste Azure Storages administratören bevilja behörighet till hanterad identitet för att komma åt data. Att bevilja behörigheter till hanterad identitet görs på samma sätt som när du beviljar behörighet till andra Azure AD-användare.

### <a name="anonymous-access"></a>[Anonym åtkomst](#tab/public-access)

Du kan komma åt offentligt tillgängliga filer som placerats på Azure Storage-konton som [tillåter anonym åtkomst](/azure/storage/blobs/storage-manage-access-to-resources).

---

### <a name="supported-authorization-types-for-databases-users"></a>Auktoriserade typer som stöds för databas användare

I tabellen nedan hittar du tillgängliga typer av autentisering:

| Auktoriseringstyp                    | *SQL-användare*    | *Azure AD-användare*     |
| ------------------------------------- | ------------- | -----------    |
| [Användar identitet](?tabs=user-identity#supported-storage-authorization-types)       | Stöds inte | Stöds      |
| [SÄKERHETS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Stöds     | Stöds      |
| [Hanterad identitet](?tabs=managed-identity#supported-storage-authorization-types) | Stöds inte | Stöds      |

### <a name="supported-storages-and-authorization-types"></a>Lagrings enheter och verifierings typer som stöds

Du kan använda följande kombinationer av auktoriserings-och Azure Storage typer:

| Auktoriseringstyp  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SÄKERHETS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Stöds\*      | Stöds inte   | Stöds\*     |
| [Hanterad identitet](?tabs=managed-identity#supported-storage-authorization-types) | Stöds      | Stöds        | Stöds     |
| [Användar identitet](?tabs=user-identity#supported-storage-authorization-types)    | Stöds\*      | Stöds\*        | Stöds\*     |

\* SAS-token och Azure AD-identitet kan användas för att få åtkomst till lagring som inte skyddas med brand väggen.

> [!IMPORTANT]
> Vid åtkomst till lagring som skyddas med brand väggen kan endast hanterad identitet användas. Du måste [tillåta betrodda Microsoft-tjänster... Ange](../../storage/common/storage-network-security.md#trusted-microsoft-services) och [tilldela uttryckligen en Azure-roll](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) till den [systemtilldelade hanterade identiteten](../../active-directory/managed-identities-azure-resources/overview.md) för den resurs instansen. I det här fallet motsvarar åtkomst omfånget för instansen den Azure-roll som tilldelats den hanterade identiteten.
>

## <a name="credentials"></a>Autentiseringsuppgifter

Om du vill fråga en fil som finns i Azure Storage måste din SQL-slutpunkt på begäran ha en autentiseringsuppgift som innehåller autentiseringsinformationen. Två typer av autentiseringsuppgifter används:
- AUTENTISERINGSUPPGIFTER på server nivå används för ad hoc-frågor som körs med hjälp av `OPENROWSET` funktion. Namnet på autentiseringsuppgiften måste matcha lagrings-URL: en.
- DATABASens begränsade AUTENTISERINGSUPPGIFTER används för externa tabeller. En extern tabell refererar till `DATA SOURCE` den autentiseringsuppgift som ska användas för åtkomst till lagring.

Om du vill tillåta att en användare skapar eller släpper en autentiseringsuppgift, kan administratören bevilja/neka behörighet att ändra AUTENTISERINGSUPPGIFTER till en användare:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Databas användare som har åtkomst till extern lagring måste ha behörighet att använda autentiseringsuppgifter.

### <a name="grant-permissions-to-use-credential"></a>Bevilja behörighet att använda autentiseringsuppgifter

Om du vill använda autentiseringsuppgifterna måste användaren ha `REFERENCES` behörighet för en speciell autentiseringsuppgift. Om du vill bevilja en behörighet för en `REFERENCES` storage_credential för en specific_user kör du:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

För att säkerställa en smidig Azure AD-direktautentisering har alla användare som standard rätt att använda `UserIdentity` autentiseringsuppgiften.

## <a name="server-scoped-credential"></a>Autentiseringsuppgifter för Server omfång

Autentiseringsuppgifter för Server omfång används när SQL login-anrop `OPENROWSET` fungerar utan `DATA_SOURCE` att läsa filer på vissa lagrings konton. Namnet på serverns begränsade autentiseringsuppgifter **måste** matcha URL: en för Azure Storage. Du lägger till en autentiseringsuppgift genom att köra [skapa autentiseringsuppgifter](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Du måste ange ett namn argument för AUTENTISERINGSUPPGIFTER. Den måste matcha antingen en del av sökvägen eller hela sökvägen till data i lagringen (se nedan).

> [!NOTE]
> `FOR CRYPTOGRAPHIC PROVIDER`Argumentet stöds inte.

Namnet på Server nivåns AUTENTISERINGSUPPGIFTER måste matcha den fullständiga sökvägen till lagrings kontot (och eventuellt container) i följande format: `<prefix>://<storage_account_path>/<storage_path>` . Lagrings konto Sök vägar beskrivs i följande tabell:

| Extern data Källa       | Prefix | Sökväg till lagrings konto                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>. dfs.core.windows.net              |

Autentiseringsuppgifter för Server omfång ger åtkomst till Azure Storage med följande autentiseringstyper:

### <a name="user-identity"></a>[Användar identitet](#tab/user-identity)

Azure AD-användare kan komma åt alla filer i Azure Storage om de har `Storage Blob Data Owner` , `Storage Blob Data Contributor` eller- `Storage Blob Data Reader` rollen. Azure AD-användare behöver inte autentiseringsuppgifter för att komma åt lagringen. 

SQL-användare kan inte använda Azure AD-autentisering för åtkomst till lagring.

### <a name="shared-access-signature"></a>[Signatur för delad åtkomst](#tab/shared-access-signature)

Följande skript skapar en autentiseringsuppgift på server nivå som kan användas av `OPENROWSET` funktion för att få åtkomst till alla filer i Azure Storage med SAS-token. Skapa den här autentiseringsuppgiften för att aktivera SQL-huvudobjektet som kör `OPENROWSET` funktionen för att läsa filer som skyddas med SAS-nyckel på Azure Storage som matchar URL: en i autentiseringsuppgiften.

Exchange <*mystorageaccountname*> med ditt faktiska lagrings konto namn och <*mystorageaccountcontainername*> med det faktiska behållar namnet:

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Hanterad identitet](#tab/managed-identity)

Följande skript skapar en autentiseringsuppgift på server nivå som kan användas av `OPENROWSET` funktionen för att få åtkomst till alla filer i Azure Storage med hjälp av arbets ytans hanterade identiteter.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Offentlig åtkomst](#tab/public-access)

Databasens begränsade autentiseringsuppgifter krävs inte för att tillåta åtkomst till offentligt tillgängliga filer. Skapa [data källa utan databasens begränsade autentiseringsuppgifter](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) för att få åtkomst till offentligt tillgängliga filer i Azure Storage.

---

## <a name="database-scoped-credential"></a>Autentiseringsuppgifter för databas omfattning

Autentiseringsuppgifter för databasens omfattning används när en huvud anrops `OPENROWSET` funktion med `DATA_SOURCE` eller väljer data från [extern tabell](develop-tables-external-tables.md) som inte kommer åt offentliga filer. Databasens begränsade autentiseringsuppgifter behöver inte matcha namnet på lagrings kontot. Den används explicit i DATA källan som definierar lagrings platsen.

Autentiseringsuppgifter för databasens omfång ger åtkomst till Azure Storage med följande autentiseringstyper:

### <a name="azure-ad-identity"></a>[Azure AD-identitet](#tab/user-identity)

Azure AD-användare kan komma åt alla filer i Azure Storage om de har minst `Storage Blob Data Owner` , `Storage Blob Data Contributor` eller `Storage Blob Data Reader` roll. Azure AD-användare behöver inte autentiseringsuppgifter för att komma åt lagringen.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

SQL-användare kan inte använda Azure AD-autentisering för åtkomst till lagring.

### <a name="shared-access-signature"></a>[Signatur för delad åtkomst](#tab/shared-access-signature)

Följande skript skapar en autentiseringsuppgift som används för att komma åt filer på lagrings platsen med SAS-token som anges i autentiseringsuppgiften. Skriptet skapar ett exempel på en extern data källa som använder denna SAS-token för att komma åt lagringen.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Hanterad identitet](#tab/managed-identity)

Följande skript skapar en databas-begränsade autentiseringsuppgifter som kan användas för att personifiera den aktuella Azure AD-användaren som hanterad identitet för tjänsten. Skriptet skapar ett exempel på en extern data källa som använder arbets ytans identitet för att komma åt lagringen.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Den databasbaserade autentiseringsuppgiften behöver inte matcha namnet på lagrings kontot eftersom det används explicit i DATA källan som definierar lagrings platsen.

### <a name="public-access"></a>[Offentlig åtkomst](#tab/public-access)

Databasens begränsade autentiseringsuppgifter krävs inte för att tillåta åtkomst till offentligt tillgängliga filer. Skapa [data källa utan databasens begränsade autentiseringsuppgifter](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) för att få åtkomst till offentligt tillgängliga filer i Azure Storage.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Autentiseringsuppgifter för databas omfånget används i externa data källor för att ange vilken autentiseringsmetod som ska användas för att få åtkomst till den här lagringen:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Exempel

### <a name="access-a-publicly-available-data-source"></a>**Åtkomst till en offentligt tillgänglig data Källa**

Använd följande skript för att skapa en tabell som har åtkomst till offentligt tillgängliga data källor.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

Databas användaren kan läsa innehållet i filerna från data källan med hjälp av en extern tabell eller en [OpenRowSet](develop-openrowset.md) -funktion som refererar till data källan:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Åtkomst till en data källa med hjälp av autentiseringsuppgifter**

Ändra följande skript för att skapa en extern tabell som får åtkomst till Azure Storage med SAS-token, Azure AD-identitet för användare eller hanterad identitet för arbets ytan.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

Databas användaren kan läsa innehållet i filerna från data källan med hjälp av en [extern tabell](develop-tables-external-tables.md) eller en [OpenRowSet](develop-openrowset.md)  -funktion som refererar till data källan:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Nästa steg

Artiklarna i listan nedan hjälper dig att lära dig hur frågar olika typer av mappar, filtyper och hur du skapar och använder vyer:

- [Fråga en enkel CSV-fil](query-single-csv-file.md)
- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)
- [Fråga efter vissa filer](query-specific-files.md)
- [Efterfråga Parquet-filer](query-parquet-files.md)
- [Skapa och använda vyer](create-use-views.md)
- [Efterfråga JSON-filer](query-json-files.md)
- [Efterfråga kapslade Parquet-typer](query-parquet-nested-types.md)
