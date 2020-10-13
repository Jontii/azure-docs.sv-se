---
title: Ansluta till Azure Cosmos DB med hjälp av kompass
description: Lär dig hur du använder MongoDB kompass för att lagra och hantera data i Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: jasonwhowell
ms.author: jasonh
ms.openlocfilehash: 15b4e6515f98a4d5a65bb5a56bdd5a5f40f90fe8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91408619"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>Använd MongoDB kompass för att ansluta till Azure Cosmos DB s API för MongoDB

Den här självstudien visar hur du använder [MongoDB kompass](https://www.mongodb.com/products/compass) när du lagrar och/eller hanterar data i Cosmos dB. Vi använder Azure Cosmos DBs API för MongoDB för den här genom gången. För dem som inte är välkända är kompass ett GUI för MongoDB. Den används ofta för att visualisera dina data, köra Ad hoc-frågor, tillsammans med hantera dina data.

Cosmos DB är Microsofts globalt distribuerade databas tjänst för flera modeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB.

## <a name="pre-requisites"></a>Förutsättningar

För att ansluta till ditt Cosmos DB-konto med MongoDB kompass måste du:

* Hämta och installera [kompass](https://www.mongodb.com/download-center/compass?jmp=hero)
* Ha information om Cosmos DB- [anslutningssträng](connect-mongodb-account.md)

> [!NOTE]
> För närvarande stöds Azure Cosmos DBs-API för MongoDB Server version 3,2 med den senaste versionen av MongoDB kompass. MongoDB kompass stöds inte ännu med 3,6-Server versionen. 

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Ansluta till Cosmos DB s API för MongoDB

Om du vill ansluta ditt Cosmos DB konto till kompass kan du följa stegen nedan:

1. Hämta anslutnings informationen för ditt Cosmos-konto som kon figurer ATS med Azure Cosmos DB API-MongoDB med hjälp av instruktionerna [här](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="Skärm bild av bladet anslutnings sträng":::

2. Klicka på knappen med texten **Kopiera till Urklipp** bredvid din **primära/sekundära anslutnings sträng** i Cosmos dB. Om du klickar på den här knappen kopieras hela anslutnings strängen till Urklipp.

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="Skärm bild av bladet anslutnings sträng":::

3. Öppna kompassen på Skriv bordet/datorn och klicka på **Anslut** och **Anslut sedan till.**...

4. Kompass identifierar automatiskt en anslutnings sträng i Urklipp och frågar om du vill använda den för att ansluta. Klicka på **Ja** så som visas på skärm bilden nedan.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Skärm bild av bladet anslutnings sträng":::

5. När du klickar på **Ja** i ovanstående steg fylls informationen från anslutnings strängen i automatiskt. Ta bort värdet som fylls i automatiskt i fältet **namn på replik uppsättning** för att se till att det lämnas tomt.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Skärm bild av bladet anslutnings sträng":::

6. Klicka på **Anslut** längst ned på sidan. Ditt Cosmos DB konto och databaser bör nu vara synligt i MongoDB kompass.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.
