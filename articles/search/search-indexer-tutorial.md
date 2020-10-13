---
title: C#-självstudie indexera Azure SQL-data
titleSuffix: Azure Cognitive Search
description: I den här C#-själv studie kursen ansluter du till Azure SQL Database, extraherar sökbara data och läser in den i ett Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e04c7da40719f77ca478f2ce577688af773f523d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399235"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>Självstudie: indexera Azure SQL-data med .NET SDK

Konfigurera en [indexerare](search-indexer-overview.md) för att extrahera sökbara data från Azure SQL Database och skicka den till ett Sök index i Azure kognitiv sökning. 

I den här självstudien används C# och [.NET SDK](/dotnet/api/overview/azure/search) för att utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en data källa som ansluter till Azure SQL Database
> * Skapa en indexerare
> * Köra en indexerare för att läsa in data i ett index
> * Fråga ett index som ett verifierings steg

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Du kan använda den kostnads fria tjänsten för den här självstudien. En kostnads fri Sök tjänst begränsar dig till tre index, tre indexerare och tre data källor. I den här kursen skapar du en av varje. Innan du börjar bör du kontrol lera att du har utrymme på tjänsten för att godkänna de nya resurserna.

## <a name="download-files"></a>Ladda ned filer

Käll koden för den här självstudien finns i mappen [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) i GitHub-lagringsplatsen [Azure-samples/search-dotNet-kom-igång](https://github.com/Azure-Samples/search-dotnet-getting-started) .

## <a name="1---create-services"></a>1 – skapa tjänster

I den här självstudien används Azure Kognitiv sökning för indexering och frågor, och Azure SQL Database som en extern data källa. Skapa om möjligt båda tjänsterna i samma region och resurs grupp för närhet och hanterbarhet. I praktiken kan Azure SQL Database finnas i vilken region som helst.

### <a name="start-with-azure-sql-database"></a>Börja med Azure SQL Database

I det här steget skapar du en extern data källa på Azure SQL Database som en indexerare kan crawla. Du kan använda Azure Portal och filen *Hotels. SQL* från exempel hämtningen för att skapa data uppsättningen i Azure SQL Database. Azure Kognitiv sökning använder sammanslagna rad uppsättningar, till exempel en som genereras från en vy eller fråga. SQL-filen i exempellösningen skapar och fyller i en enskild tabell.

Om du har en befintlig Azure SQL Database resurs kan du lägga till hotell tabellen till den från och med steg 4.

1. [Logga](https://portal.azure.com/)in på Azure Portal.

1. Hitta eller skapa en **SQL Database**. Du kan använda standardinställningarna och den lägsta prisnivån. En fördel jämfört med att skapa en server är att du kan ange namn och lösenord för administratörsanvändaren, vilket krävs för att skapa och läsa in tabeller i ett senare steg.

   :::image type="content" source="media/search-indexer-tutorial/indexer-new-sqldb.png" alt-text="Ny databassida" border="false":::

1. Klicka på **Granska + skapa** för att distribuera den nya servern och databasen. Vänta tills servern och databasen har distribuerats.

1. Klicka på **Frågeredigeraren (för hands version)** i navigerings fönstret och ange användar namn och lösen ord för Server administratören. 

   Om åtkomst nekas kopierar du klientens IP-adress från fel meddelandet och klickar sedan på länken **Ange server brand vägg** för att lägga till en regel som tillåter åtkomst från klient datorn med hjälp av klientens IP-adress för intervallet. Det kan ta flera minuter innan regeln börjar gälla.

1. I Frågeredigeraren klickar du på **Öppna fråga** och navigerar till platsen för *Hotels. SQL* -filen på den lokala datorn. 

1. Markera filen och klicka på **Öppna**. Skriptet bör se ut ungefär som på följande skärmbild:

   :::image type="content" source="media/search-indexer-tutorial/sql-script.png" alt-text="Ny databassida" border="false":::

1. Klicka på **Kör** för att köra frågan. Nu bör du se ett meddelande om att frågan lyckades för tre rader i resultatfönstret.

1. Om du vill returnera en raduppsättning från den här tabellen kan du köra följande fråga som ett verifieringssteg:

    ```sql
    SELECT * FROM Hotels
    ```

1. Kopiera ADO.NET-anslutningssträngen för databasen. Under **Inställningar**  >  **anslutnings strängar**kopierar du anslutnings strängen ADO.net, som liknar exemplet nedan.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Du kommer att behöva den här anslutnings strängen i nästa övning, konfigurera din miljö.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Nästa komponent är Azure Kognitiv sökning, som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnads fria nivån för att slutföra den här genom gången. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Hämta en Admin API-nyckel och URL för Azure Kognitiv sökning

API-anrop kräver tjänst-URL och en åtkomst nyckel. En Sök tjänst skapas med båda, så om du har lagt till Azure-Kognitiv sökning till din prenumeration följer du dessa steg för att få den information som krävs:

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

1. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten. Det finns två utbytbara administratörs nycklar, som tillhandahålls för affärs kontinuitet om du behöver rulla en över. Du kan använda antingen den primära eller sekundära nyckeln på begär Anden för att lägga till, ändra och ta bort objekt.

   :::image type="content" source="media/search-get-started-postman/get-url-key.png" alt-text="Ny databassida" border="false":::

## <a name="2---set-up-your-environment"></a>2 – Konfigurera din miljö

1. Starta Visual Studio och öppna **DotNetHowToIndexers. SLN**.

1. I Solution Explorer öppnar du **appsettings.jspå** för att ange anslutnings information.

1. För `searchServiceName` , om den fullständiga URL: en är " https://my-demo-service.search.windows.net ", är tjänst namnet som ska tillhandahållas "min-demo-service".

1. För `AzureSqlConnectionString` , liknar sträng formatet följande: `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. I anslutnings strängen ser du till att anslutnings strängen innehåller ett giltigt lösen ord. Medan databasen och användar namnen kommer att kopieras, måste lösen ordet anges manuellt.

## <a name="3---create-the-pipeline"></a>3 – skapa pipelinen

Indexerare kräver ett data käll objekt och ett index. Relevant kod finns i två filer:

  + **Hotel.cs**, som innehåller ett schema som definierar indexet
  + **Program.cs**, som innehåller funktioner för att skapa och hantera strukturer i din tjänst

### <a name="in-hotelcs"></a>I hotel.cs

Indexschemat definierar fältsamlingen, inklusive attribut som anger tillåtna åtgärder, till exempel om ett fält är fulltextsökbart, filtrerbart eller sorterbart som i följande fältdefinition för HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Ett schema kan även innehålla andra element, till exempel poängprofiler för att ge högre sökpoäng, anpassade analysverktyg och andra konstruktioner. Men för vårt ändamål har schemat en enkel definition med endast de fält som finns i exempeldatauppsättningarna.

### <a name="in-programcs"></a>I Program.cs

Huvud programmet innehåller logik för att skapa en klient, ett index, en data källa och en indexerare. Koden söker efter och tar bort befintliga resurser med samma namn, under förutsättning att du kan köra det här programmet flera gånger.

Objektet datakällobjektet konfigureras med inställningar som är speciella för Azure SQL Database resurser, inklusive [delvis eller stegvis indexering](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) för att utnyttja de inbyggda [funktionerna för ändrings identifiering](/sql/relational-databases/track-changes/about-change-tracking-sql-server) i Azure SQL. Demon hotell-databasen i Azure SQL har en "mjuk borttagning"-kolumn med namnet **IsDeleted**. När den här kolumnen har angetts till sant i databasen, tar indexeraren bort motsvarande dokument från Azure Kognitiv sökning-indexet.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Ett indexerare-objekt är plattforms-oberoende, där konfiguration, schemaläggning och anrop är desamma oavsett källa. Den här exempel indexeraren innehåller ett schema, ett återställnings alternativ som rensar indexerings historiken och anropar en metod för att skapa och köra indexeraren direkt.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="4---build-the-solution"></a>4 – Bygg lösningen

Tryck på F5 för att skapa och köra lösningen. Programmet körs i felsökningsläge. I ett konsolfönster rapporteras status för varje åtgärd.

   :::image type="content" source="media/search-indexer-tutorial/console-output.png" alt-text="Ny databassida" border="false":::

Din kod körs lokalt i Visual Studio, ansluter till din Sök tjänst på Azure, som i sin tur ansluter till Azure SQL Database och hämtar data uppsättningen. Med det här många åtgärder finns det flera möjliga punkter av felet. Om ett fel uppstår kontrollerar du först följande villkor:

+ Informationen för den söktjänstanslutning som du anger. Den är begränsad till tjänstnamnet i den här kursen. Om du har angett en fullständig URL avbryts åtgärderna när index skapas och det uppstår ett anslutningsfel.

+ Informationen för databasanslutningen i **appsettings.json**. Den bör vara den ADO.NET-anslutningssträng som du fick från portalen och ha ändrats så att den innehåller ett användarnamn och ett lösenord som är giltiga för din databas. Användarkontot måste ha behörighet att hämta data. Din lokala klient-IP-adress måste vara tillåten åtkomst.

+ Resursbegränsningar. Kom ihåg att den kostnads fria nivån har en gräns på 3 index, indexerare och data källor. När maxgränsen har uppnåtts för en tjänst går det inte att skapa nya objekt.

## <a name="5---search"></a>5-Sök

Använd Azure Portal för att kontrol lera att objektet skapas och Använd sedan **Sök Utforskaren** för att fråga indexet.

1. [Logga](https://portal.azure.com/)in på Azure Portal och på sidan **Översikt** för Search-tjänsten öppnar du varje lista i tur och ett för att kontrol lera att objektet har skapats. **Index**, **indexerare**och **data källor** kommer att ha "Hotels", "Azure-SQL-Indexer" och "Azure-SQL".

   :::image type="content" source="media/search-indexer-tutorial/tiles-portal.png" alt-text="Ny databassida" border="false":::

1. Välj hotell indexet. **Sök Utforskaren** är den första fliken på sidan hotell. 

1. Klicka på **Sök** för att skicka en tom fråga. 

   De tre posterna i ditt index returneras som JSON-dokument. Sökutforskaren returnerar dokument i JSON så att du kan se hela strukturen.

   :::image type="content" source="media/search-indexer-tutorial/portal-search.png" alt-text="Ny databassida" border="false":::
   
1. Ange sedan en söksträng: `search=river&$count=true`. 

   Den här frågan kör en fulltextsökning på termen `river`, och resultatet innefattar en räkning av matchande dokument. Att returnera antalet matchande dokument är användbart i testscenarier när du har ett stort index med tusentals eller miljontals dokument. I det här fallet är det bara ett dokument som matchar frågan.

1. Slutligen anger du en söksträng som begränsar JSON-utdata till intresseområden: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Svaret på frågan begränsas till valda fält, vilket ger mer koncisa utdata.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I de tidiga experiment stegen i utvecklingen är den mest praktiska metoden för design upprepning att ta bort objekten från Azure Kognitiv sökning och tillåta att koden återskapas. Resursnamn är unika. Om du tar bort ett objekt kan du återskapa det med samma namn.

Exempel koden för den här självstudien kontrollerar om det finns befintliga objekt och tar bort dem så att du kan köra koden igen.

Du kan också använda portalen för att ta bort index, indexerare och data källor.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i SQL Database indexering, tar vi en närmare titt på indexerings konfigurationen.

> [!div class="nextstepaction"]
> [Konfigurera en SQL Database indexerare](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)