---
title: Åtkomst Azure Cosmos DB API för Cassandra från Azure Databricks
description: Den här artikeln beskriver hur du arbetar med Azure Cosmos DB API för Cassandra från Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 30bd3187973de204f27a3be3862351550d6a56f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85260482"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Åtkomst Azure Cosmos DB API för Cassandra data från Azure Databricks

Den här artikeln beskriver hur du workwith Azure Cosmos DB API för Cassandra från Spark på [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Krav

* [Etablera ett Azure Cosmos DB API för Cassandra konto](create-cassandra-dotnet.md#create-a-database-account)

* [Granska grunderna för att ansluta till Azure Cosmos DB API för Cassandra](cassandra-spark-generic.md)

* [Etablera ett Azure Databricks kluster](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Granska kod exemplen för att arbeta med API för Cassandra](cassandra-spark-generic.md#next-steps)

* [Använd cqlsh för verifiering om du föredrar det](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **API för Cassandra instans konfiguration för Cassandra-koppling:**

  Anslutningen för API för Cassandra kräver att Cassandra anslutnings information initieras som en del av Spark-kontexten. När du startar en Databricks Notebook är Spark-kontexten redan initierad och det är inte lämpligt att stoppa och initiera om den. En lösning är att lägga till API för Cassandra instans konfigurationen på en kluster nivå i kluster Spark-konfigurationen. Detta är en engångs aktivitet per kluster. Lägg till följande kod i Spark-konfigurationen som ett blankstegsavgränsad nyckel värde par:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Lägga till nödvändiga beroenden

* **Cassandra Spark-anslutning:** -för att integrera Azure Cosmos DB API för Cassandra med Spark bör Cassandra-anslutningen kopplas till Azure Databricks klustret. Så här kopplar du klustret:

  * Granska Databricks runtime-versionen, Spark-versionen. Hitta sedan de [maven-koordinater](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) som är kompatibla med Cassandra Spark-anslutningsprogrammet och koppla den till klustret. Se artikeln ["Ladda upp ett maven-paket eller Spark-paket"](https://docs.databricks.com/user-guide/libraries.html) för att bifoga anslutnings biblioteket till klustret. Till exempel är maven-koordinaten för "Databricks Runtime version 4,3", "Spark 2.3.1" och "Scala 2,11" `spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB API för Cassandra-/regionsspecifika bibliotek:** – en anpassad anslutnings fabrik krävs för att konfigurera återförsöks principen från Cassandra Spark-anslutningsprogrammet till Azure Cosmos DB API för Cassandra. Lägg till `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [maven-koordinaterna](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) för att koppla biblioteket till klustret.

## <a name="sample-notebooks"></a>Exempel på notebook-filer

En lista med Azure Databricks [exempel på bärbara datorer](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) finns i GitHub-lagrings platsen som du kan hämta. De här exemplen innehåller information om hur du ansluter till Azure Cosmos DB API för Cassandra från Spark och utföra olika CRUD-åtgärder på data. Du kan också [Importera alla antecknings böcker](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) till din Databricks-kluster arbets yta och köra den. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Komma åt Azure Cosmos DB API för Cassandra från Spark Scala-program

Spark-program körs som automatiserade processer på Azure Databricks skickas till klustret med hjälp av [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html)) och schemaläggs för att köras genom Azure Databricks jobb.

Följande är länkar som hjälper dig att komma igång med att skapa Spark Scala-program för att interagera med Azure Cosmos DB API för Cassandra.
* [Så här ansluter du till Azure Cosmos DB API för Cassandra från ett Spark Scala-program](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Så här kör du ett Spark Scala-program som ett automatiserat jobb på Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Fullständig lista över kod exempel för att arbeta med API för Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Nästa steg

Kom igång med att [skapa ett API för Cassandra konto, en databas och en tabell](create-cassandra-api-account-java.md) med hjälp av ett Java-program.
