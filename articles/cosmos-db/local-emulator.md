---
title: Installera och utveckla lokalt med Azure Cosmos DB emulator
description: Lär dig hur du installerar och använder Azure Cosmos DB-emulatorn i Windows-, Linux-, macOS-och Windows Docker-miljöer. Med emulatorn kan du utveckla och testa ditt program lokalt utan kostnad, utan att skapa en Azure-prenumeration.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 07a38e106b765fd28a8c3c1115e5fe84744ade62
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91303097"
---
# <a name="install-and-use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Installera och Använd Azure Cosmos-emulatorn för lokal utveckling och testning

Azure Cosmos-emulatorn tillhandahåller en lokal miljö som emulerar Azure Cosmos DB tjänst i utvecklings syfte. Med Azure Cosmos-emulatorn kan du utveckla och testa ditt program lokalt, utan att du behöver skapa en Azure-prenumeration eller debitera några kostnader. När du är nöjd med hur ditt program fungerar i Azure Cosmos-emulatorn kan du växla till att använda ett Azure Cosmos-konto i molnet. Kom igång genom att ladda ned och installera den senaste versionen av [Azure Cosmos-emulatorn](https://aka.ms/cosmosdb-emulator) på den lokala datorn. Den här artikeln beskriver hur du installerar och använder emulatorn på Windows-, Linux-, macOS-och Windows Docker-miljöer.

Du kan utveckla program med Azure Cosmos-emulatorn med [SQL](local-emulator.md#sql-api)-, [Cassandra](local-emulator.md#cassandra-api)-, [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)-, [Gremlin](local-emulator.md#gremlin-api)-och [Table](local-emulator.md#table-api) API-konton. För närvarande stöder data Explorer i emulatorn fullt enbart visning av SQL-data. data som skapats med MongoDB-, Gremlin-/graf-och Cassandra-klientprogram kan inte visas för tillfället. Läs mer i så här [ansluter du till emulator-slutpunkten](#connect-with-emulator-apis) från olika API: er.

## <a name="how-does-the-emulator-work"></a>Hur fungerar emulatorn?

Azure Cosmos-emulatorn tillhandahåller en effektiv emulering av Azure Cosmos DBs tjänsten. Den har stöd för motsvarande funktioner som Azure Cosmos DB, vilket innefattar att skapa data, fråga data, konfigurera och skala behållare och köra lagrade procedurer och utlösare. Du kan utveckla och testa program med Azure Cosmos-emulatorn och distribuera dem till Azure i global skala genom att uppdatera Azure Cosmos DB anslutningens slut punkt.

Även om emuleringen av Azure Cosmos DB-tjänsten är verklighetstrogen så implementeras emulatorn på ett annat sätt än tjänsten. Till exempel använder emulatorn standardkomponenter i operativsystemet som det lokala filsystemet för beständighet och HTTP-protokollstacken för anslutningar. Funktioner som förlitar sig på Azure-infrastrukturen som global replikering, ensiffriga millisekunder för läsningar och skrivningar och justerbara konsekvens nivåer kan inte användas när du använder emulatorn.

Du kan migrera data mellan Azure Cosmos-emulatorn och den Azure Cosmos DB tjänsten med hjälp av [verktyget Azure Cosmos DB datamigrering](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>Skillnader mellan emulatorn och moln tjänsten

Eftersom Azure Cosmos-emulatorn tillhandahåller en emulerad miljö som körs på den lokala Developer-arbetsstationen finns det vissa skillnader i funktionalitet mellan emulatorn och ett Azure Cosmos-konto i molnet:

* För närvarande stöder **Datautforskarens** fönstret i emulatorn endast SQL API-klienter. **Datautforskaren** vyer och åtgärder för Azure Cosmos DB-API: er som MongoDB-, Table-, Graph-och Cassandra-API: er stöds inte fullt ut.

* Emulatorn har endast stöd för ett fast konto och en välkänd primär nyckel. Du kan inte återskapa nyckeln när du använder Azure Cosmos-emulatorn, men du kan ändra standard nyckeln med hjälp av [kommando rads](emulator-command-line-parameters.md) alternativet.

* Med emulatorn kan du bara skapa ett Azure Cosmos-konto i ett [tillhandahållet data flödes](set-throughput.md) läge. för närvarande stöder den inte [Server](serverless.md) lös läge.

* Emulatorn är ingen skalbar tjänst och har inte stöd för ett stort antal behållare. När du använder Azure Cosmos-emulatorn kan du som standard skapa upp till 25 fasta storleks behållare på 400 RU/s (stöds endast med Azure Cosmos DB SDK: er) eller 5 obegränsade behållare. Mer information om hur du ändrar det här värdet finns i [Ange värdet för PartitionCount-värde] emulator-Command-line-Parameters. MD # set-PartitionCount).

* Emulatorn erbjuder inte olika [Azure Cosmos DB konsekvens nivåer](consistency-levels.md) som moln tjänsten gör.

* Emulatorn erbjuder inte [replikering i flera regioner](distribute-data-globally.md).

* Eftersom kopian av din Azure Cosmos-emulator kanske inte alltid är uppdaterad med de senaste ändringarna i Azure Cosmos DBs tjänsten, bör du alltid hänvisa till [Azure Cosmos DB kapacitets planeraren](estimate-ru-with-capacity-planner.md) för att korrekt beräkna data flödes kraven (ru: er) för ditt program.

* Emulatorn stöder en maximal egenskaps storlek på 254 tecken.

## <a name="install-the-emulator"></a>Installera emulatorn

Kontrol lera att du har följande maskinvaru-och program varu krav innan du installerar emulatorn:

* Program varu krav:
  * För närvarande stöds Windows Server 2012 R2, Windows Server 2016, 2019 eller Windows 8, 10-värd-OS. Värd operativ systemet med Active Directory aktiverat stöds inte för närvarande.
  * 64-bitars operativsystem

* Minsta maskin varu krav:
  * 2 GB RAM-minne
  * 10 GB ledigt hårddiskutrymme

* För att installera, konfigurera och köra Azure Cosmos-emulatorn måste du ha administratörs behörighet på datorn. Emulatorn lägger till ett certifikat och anger även brand Väggs reglerna för att kunna köra dess tjänster. Därför behövs administratörs behörighet för att emulatorn ska kunna köra sådana åtgärder.

Kom igång genom att ladda ned och installera den senaste versionen av [Azure Cosmos-emulatorn](https://aka.ms/cosmosdb-emulator) på den lokala datorn. Om du stöter på problem när du installerar emulatorn kan du läsa artikeln [fel sökning i emulatorn](troubleshoot-local-emulator.md) .

Beroende på system kraven kan du köra emulatorn på [Windows](#run-on-windows), [Docker för Windows](#run-on-windows-docker), [Linux eller [MacOS](#run-on-linux-macos) enligt beskrivningen i nästa avsnitt i den här artikeln.

## <a name="check-for-emulator-updates"></a>Sök efter emulator-uppdateringar

Varje version av emulatorn levereras med en uppsättning funktions uppdateringar eller fel korrigeringar. Om du vill se tillgängliga versioner läser du artikeln om [versions anmärkningar för emulator](local-emulator-release-notes.md) .

Om du har använt standardinställningarna efter installationen sparas data som motsvarar emulatorn på%LOCALAPPDATA%\CosmosDBEmulator plats. Du kan konfigurera en annan plats med hjälp av inställningarna för valfria data Sök vägar. Det är som `/DataPath=PREFERRED_LOCATION` [kommando rads parametern](emulator-command-line-parameters.md). Data som skapats i en version av Azure Cosmos-emulatorn är inte garanterat tillgängliga när du använder en annan version. Om du behöver bevara dina data för lång sikt rekommenderar vi att du lagrar dessa data i ett Azure Cosmos-konto i stället för Azure Cosmos-emulatorn.

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Använda emulatorn i Windows

Azure Cosmos-emulatorn installeras på `C:\Program Files\Azure Cosmos DB Emulator` platsen som standard. Starta Azure Cosmos-emulatorn i Windows genom att välja **Start** -knappen eller trycka på Windows-tangenten. Börja skriva **Azure Cosmos-emulatorn**och välj emulatorn från listan över program.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Välj Start-knappen eller tryck på Windows-tangenten, börja skriva Azure Cosmos-emulatorn och välj emulatorn från listan över program":::

När emulatorn har startat visas en ikon i meddelande fältet i aktivitets fältet i Windows. Den öppnar automatiskt Azure Cosmos data Explorer i webbläsaren på denna URL-adress `https://localhost:8081/_explorer/index.html` .

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Avisering om aktivitets fältet i Azure Cosmos DB lokalt emulator":::

Du kan också starta och stoppa emulatorn från kommando rads-eller PowerShell-kommandon. Mer information finns i referens artikeln för [kommando rads verktyget](emulator-command-line-parameters.md) .

Azure Cosmos-emulatorn körs som standard på den lokala datorn ("localhost") som lyssnar på port 8081. Adressen visas som `https://localhost:8081/_explorer/index.html`. Om du stänger Utforskaren och vill öppna den igen senare kan du antingen öppna webb adressen i webbläsaren eller starta den från Azure Cosmos-emulatorn i Windows-ikonen som visas nedan.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Starta Azure Cosmos Local mula data Explorer":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Använda emulatorn på Docker för Windows

Du kan köra Azure Cosmos-emulatorn på Windows Docker-behållaren. Mer information finns i [Docker-hubben](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) för kommandot Docker pull och [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) `Dockerfile` . För närvarande fungerar inte emulatorn på Docker för Oracle Linux. Använd följande instruktioner för att köra emulatorn på Docker för Windows:

1. När du har [Docker för Windows](https://www.docker.com/docker-windows) installerat växlar du till Windows-behållare genom att högerklicka på Docker-ikonen i verktygsfältet och välja **Växla till Windows-behållare**.

1. Hämta därefter emulatoravbildningen från Docker-hubben genom att köra följande kommando i ditt favoritgränssnitt.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Starta avbildningen genom att köra följande kommandon, beroende på kommando raden eller PowerShell-miljön:

   # <a name="command-line"></a>[Kommandorad](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Windows-baserade Docker-avbildningar kanske inte är allmänt kompatibla med varje Windows-värd-OS. Till exempel är standard avbildningen av Azure Cosmos-emulatorn bara kompatibel med Windows 10 och Windows Server 2016. Om du behöver en avbildning som är kompatibel med Windows Server 2019 kör du följande kommando i stället:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   Svaret ser ut ungefär så här:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > När `docker run` du kör kommandot, och om du ser ett port konflikt fel (det vill säga om den angivna porten redan används), skickar du en anpassad port genom att ändra port numren. Du kan till exempel ändra parametern "-p 8081:8081" till "-p 443:8081"

1. Använd nu emulatorns slut punkt och primär nyckel från svaret och importera TLS/SSL-certifikatet till värden. Importera TLS/SSL-certifikatet genom att köra följande steg från en administratörs kommando tolk:

   # <a name="command-line"></a>[Kommandorad](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Om du stänger det interaktiva skalet efter att emulatorn har startats stängs emulatorns behållare. Öppna data Utforskaren igen genom att gå till följande URL i webbläsaren. Emulatorns slutpunkt finns i svarsmeddelandet ovan.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Om du har ett .NET-klient program som körs på en Linux Docker-behållare och om du kör Azure Cosmos-emulatorn på en värddator, använder du anvisningarna i nästa avsnitt för att importera certifikatet till Linux Docker-behållaren.

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Återskapa emulatorns certifikat när det körs på en Docker-behållare

När du kör emulatorn i en Docker-behållare återskapas certifikaten som är kopplade till emulatorn varje gång du stoppar och startar om respektive behållare. På grund av att du måste importera certifikaten igen när varje behållare startas. För att undvika den här begränsningen kan du använda en Docker-filfil för att binda Docker-behållaren till en viss IP-adress och en behållar avbildning.

Du kan till exempel använda följande konfiguration i Docker-filfilen, se till att formatera den enligt ditt krav: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Använda emulatorn på Linux eller macOS

För närvarande kan Azure Cosmos-emulatorn bara köras i Windows. Om du använder Linux eller macOS kan du köra emulatorn på en virtuell Windows-dator som finns i en hypervisor, till exempel paralleller eller VirtualBox.

> [!NOTE]
> Varje gång du startar om den virtuella Windows-datorn som finns i en hypervisor måste du importera certifikatet på nytt eftersom IP-adressen för den virtuella datorn ändras. Import av certifikatet krävs inte om du har konfigurerat den virtuella datorn för att bevara IP-adressen.

Använd följande steg för att använda emulatorn i Linux-eller macOS-miljöer:

1. Kör följande kommando från den virtuella Windows-datorn och anteckna IPv4-adressen:

   ```bash
   ipconfig.exe
   ```

1. I ditt program ändrar du slut punktens URL till att använda IPv4-adressen som returnerades av `ipconfig.exe` i stället för `localhost` .

1. Från den virtuella Windows-datorn startar du Azure Cosmos-emulatorn från kommando raden med hjälp av följande alternativ. Mer information om parametrarna som stöds av kommando raden finns i [kommando rads verktyget för emulatorn](emulator-command-line-parameters.md):

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM +4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. Slutligen måste du lösa processen för certifikat förtroende mellan program som körs på Linux-eller Mac-miljön och emulatorn. Du kan använda något av följande två alternativ för att lösa certifikatet:

   1. [Importera TLS/SSL-certifikatet för emulatorn till Linux-eller Mac-miljön](#import-certificate) eller
   2. [Inaktivera TLS/SSL-verifiering i programmet](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>Alternativ 1: importera emulerings-TLS/SSL-certifikatet

I följande avsnitt visas hur du importerar TLS/SSL-certifikatet för emulator till Linux-och macOS-miljöer.

#### <a name="linux-environment"></a>Linux-miljö

Om du arbetar med Linux, .NET-reläer på OpenSSL för att utföra verifieringen:

1. [Exportera certifikatet i PFX-format](local-emulator-export-ssl-certificates.md#export-emulator-certificate). Alternativet PFX är tillgängligt när du väljer att exportera den privata nyckeln.

1. Kopiera PFX-filen till din Linux-miljö.

1. Konvertera PFX-filen till en CRT-fil

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Kopiera CRT-filen till den mapp som innehåller anpassade certifikat i din Linux-distribution. Vanligt vis på Debian-distributioner finns det på `/usr/local/share/ca-certificates/` .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Uppdatera TLS/SSL-certifikaten som kommer att uppdatera `/etc/ssl/certs/` mappen.

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS-miljö

Använd följande steg om du arbetar med Mac:

1. [Exportera certifikatet i PFX-format](local-emulator-export-ssl-certificates.md#export-emulator-certificate). Alternativet PFX är tillgängligt när du väljer att exportera den privata nyckeln.

1. Kopiera PFX-filen till din Mac-miljö.

1. Öppna Access-programmet för *nyckel ringar* och importera PFX-filen.

1. Öppna listan över certifikat och identifiera det som har namnet `localhost` .

1. Öppna snabb menyn för det specifika objektet, Välj *Hämta objekt* och under *förtroende*  >  *när du använder det här certifikat* alternativet väljer du *alltid förtroende*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Öppna snabb menyn för det specifika objektet, välj Hämta objekt och under förtroende – när du använder det här certifikat alternativet väljer du alltid förtroende":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>Alternativ 2: inaktivera SSL-verifiering i programmet

Att inaktivera SSL-validering rekommenderas endast i utvecklings syfte och bör inte utföras när de körs i en produktions miljö. I följande exempel visas hur du inaktiverar SSL-validering för .NET och Node.js program.

# <a name="net-standard-21"></a>[.NET standard 2.1 +](#tab/ssl-netstd21)

För alla program som körs i ett ramverk som är kompatibelt med .NET standard 2,1 eller senare kan vi utnyttja `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET standard 2,0](#tab/ssl-netstd20)

För alla program som körs i ett ramverk som är kompatibelt med .NET standard 2,0 kan vi utnyttja `CosmosClientOptions.HttpClientFactory` :

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

För Node.js program kan du ändra `package.json` filen för att ange `NODE_TLS_REJECT_UNAUTHORIZED` när programmet startas:

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>Aktivera åtkomst till emulatorn i ett lokalt nätverk

Om du har flera datorer som använder ett enda nätverk och du konfigurerar emulatorn på en dator och vill komma åt den från en annan dator. I så fall måste du aktivera åtkomst till emulatorn i ett lokalt nätverk.

Du kan köra emulatorn på ett lokalt nätverk. Om du vill aktivera nätverks åtkomst anger du `/AllowNetworkAccess` alternativet på [kommando raden](emulator-command-line-parameters.md), vilket även kräver att du anger `/Key=key_string` eller `/KeyFile=file_name` . Du kan använda `/GenKeyFile=file_name` för att skapa en fil med en slumpmässig nyckel längst fram. Sedan kan du skicka det till `/KeyFile=file_name` eller `/Key=contents_of_file` .

Om du vill aktivera nätverks åtkomst för första gången ska användaren stänga av emulatorn och ta bort emulatorns data katalog *%localappdata%\CosmosDBEmulator*.

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>Autentisera anslutningar när du använder emulator

Precis som med Azure Cosmos DB i molnet måste varje begäran som du gör mot Azure Cosmos-emulatorn autentiseras. Azure Cosmos-emulatorn har endast stöd för säker kommunikation via TLS. Azure Cosmos-emulatorn har stöd för ett enda fast konto och en välkänd autentiseringsnyckel för primär nyckel autentisering. Det här kontot och nyckeln är de enda autentiseringsuppgifterna som tillåts för användning med Azure Cosmos-emulatorn. De är:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Den primära nyckeln som stöds av Azure Cosmos-emulatorn är avsedd för användning med emulatorn. Du kan inte använda ditt produktions Azure Cosmos DB konto och nyckel med Azure Cosmos-emulatorn.

> [!NOTE]
> Om du har startat emulatorn med alternativet/Key använder du den genererade nyckeln i stället för standard nyckeln `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` . Mer information om alternativet/Key finns i [kommando rads verktyg referens.](emulator-command-line-parameters.md)

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>Ansluta till olika API: er med emulatorn

### <a name="sql-api"></a>API för SQL

När du har Azure Cosmos-emulatorn som körs på Skriv bordet kan du använda alla [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) : er som stöds eller [Azure Cosmos DB REST API](/rest/api/cosmos-db/) för att interagera med emulatorn. Azure Cosmos-emulatorn innehåller också en inbyggd data Utforskare som gör att du kan skapa behållare för SQL API eller Azure Cosmos DB för mongo DB API. Med hjälp av data Utforskaren kan du Visa och redigera objekt utan att skriva någon kod.

```csharp
// Connect to the Azure Cosmos emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

När du har Azure Cosmos-emulatorn igång på Skriv bordet kan du använda [Azure Cosmos DBS API för MongoDB](mongodb-introduction.md) för att interagera med emulatorn. Starta emulatorn från [kommando tolken](emulator-command-line-parameters.md) som administratör med "/EnableMongoDbEndpoint". Använd sedan följande anslutnings sträng för att ansluta till MongoDB-API-kontot:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabell-API

När du har Azure Cosmos-emulatorn som körs på Skriv bordet kan du använda [Azure Cosmos DB tabell-API SDK](table-storage-how-to-use-dotnet.md) för att interagera med emulatorn. Starta emulatorn från [kommando tolken](emulator-command-line-parameters.md) som administratör med "/EnableTableEndpoint". Kör sedan följande kod för att ansluta till tabell-API-kontot:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra-API

Starta emulatorn från en administratörs [kommando tolk](emulator-command-line-parameters.md) med "/EnableCassandraEndpoint". Du kan också ställa in miljövariabeln `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` .

1. [Installera python 2,7](https://www.python.org/downloads/release/python-2716/)

1. [Installera Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

1. Kör följande kommandon i ett vanligt kommando tolks fönster:

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. I CQLSH-gränssnittet kör du följande kommandon för att ansluta till Cassandra-slutpunkten:

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>Gremlin-API

Starta emulatorn från en administratörs [kommando tolk](emulator-command-line-parameters.md)med "/EnableGremlinEndpoint". Alternativt kan du också ställa in miljövariabeln `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [Installera Apache-tinkerpop-Gremlin-Console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

1. Från emulatorns data Utforskare skapar du en databas "DB1" och en samling "coll1". för partitionsnyckel väljer du "/name"

1. Kör följande kommandon i ett vanligt kommando tolks fönster:

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. I Gremlin-gränssnittet kör du följande kommandon för att ansluta till Gremlin-slutpunkten:

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>Avinstallera den lokala emulatorn

Använd följande steg för att avinstallera emulatorn:

1. Avsluta alla öppna instanser av den lokala emulatorn genom att högerklicka på ikonen för **Azure Cosmos-emulatorn** i system fältet och välj sedan **Avsluta**. Det kan ta någon minut för alla instanser att avslutas.

1. Skriv **appar & funktioner** i sökrutan i Windows och välj **appar & funktioner (Systeminställningar)** resultat.

1. I listan över appar bläddrar du till **Azure Cosmos DB emulatorn**, markerar den, klickar på **Avinstallera**, bekräftar och väljer **Avinstallera** igen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder den lokala emulatorn för en kostnads fri lokal utveckling. Nu kan du fortsätta till nästa artiklar:

* [Exportera Azure Cosmos-emulatorns certifikat för användning med Java, python och Node.js appar](local-emulator-export-ssl-certificates.md)
* [Använd kommando rads parametrar och PowerShell-kommandon för att styra emulatorn](emulator-command-line-parameters.md)
* [Felsöka problem med emulatorn](troubleshoot-local-emulator.md)
