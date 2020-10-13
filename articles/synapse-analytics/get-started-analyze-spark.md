---
title: 'Självstudie: kom igång med Spark'
description: I den här självstudien får du lära dig att analysera data med Apache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: f03f98a1a1f36900d65e132a3420ac037214aaff
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893548"
---
# <a name="analyze-with-apache-spark"></a>Analysera med Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-spark"></a>Analysera NYC taxi-data i Blob Storage med Spark

I den här självstudien får du lära dig de grundläggande stegen för att läsa in och analysera data med Apache Spark för Azure-Synapse.

1. I **data** hubben klickar du på **Lägg till en ny resurs**(plus knappen ovanför **länkad**)  >> **Bläddra bland exempel**. Hitta **NYC taxi & limousine provision-Yellow taxi resor** och klicka på den. Längst ned på sidan klickar du på **Fortsätt** och sedan på **Lägg till data uppsättning**. Nu i **data** hubb under **länkad** markering högerklickar du på **Azure Blob Storage >> exempel data uppsättningar >> nyc_tlc_yellow** och väljer **ny antecknings bok**
1. Då skapas en ny antecknings bok med följande kod:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. I antecknings boken väljer du en spark-pool på menyn **Anslut till**
1. Klicka på **Kör** i cellen

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Läs in NYC taxi-data till Spark nyctaxi-databasen

Det finns data som är tillgängliga i en tabell i **SQLDB1**. Läs in den i en spark-databas med namnet **nyctaxi**.

1. Gå till **utveckla** hubben i Synapse Studio.
1. Välj **+**  >  **antecknings bok**.
1. Ange värdet **koppla till** värde till **Spark1**överst i antecknings boken.
1. Välj **Lägg till kod** för att lägga till en kod cell i antecknings boken och klistra sedan in följande text:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Gå till **data** hubben, högerklicka på **databaser**och välj sedan **Uppdatera**. Du bör se dessa databaser:

    - **SQLDB1** (SQL-pool)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analysera NYC taxi-data med Spark och Notebooks

1. Återgå till din bärbara dator.
1. Skapa en ny kod cell och ange följande text. Kör sedan cellen för att visa de NYC taxi-data som vi läste in i **nyctaxi** Spark-databasen.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Kör följande kod för att utföra samma analys som tidigare med SQL-poolen **SQLDB1**. Den här koden sparar resultatet av analysen i en tabell med namnet **nyctaxi. passengercountstats** och visualiserar resultaten.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. I cell resultaten väljer du **diagram** för att visa data som visualiseras.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Anpassa data visualisering med Spark och Notebook

Du kan styra hur diagram ska återges med hjälp av antecknings böcker. Följande kod visar ett enkelt exempel. Den använder populära bibliotek **matplotlib** och **Seaborn**. Koden återger samma typ av linje diagram som de SQL-frågor vi körde tidigare.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Läsa in data från en spark-tabell till en SQL-adresspool

Tidigare kopierade data från SQL-adresspoolen **SQLDB1. dbo. resan** till Spark-tabellen **nyctaxi. resa**. Därefter aggregerade vi data till Spark-tabellen **nyctaxi. passengercountstats**med Spark. Nu ska vi kopiera data från **nyctaxi. passengercountstats** till en SQL-pool med namnet **SQLDB1. dbo. passengercountstats**.

Kör följande cell i antecknings boken. Den sammanställda Spark-tabellen kopieras tillbaka till tabellen SQL-pool.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera data med SQL på begäran](get-started-analyze-sql-on-demand.md)


