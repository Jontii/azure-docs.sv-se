---
title: 'Självstudie: komma igång med att analysera data i lagrings konton'
description: I den här självstudien får du lära dig hur du analyserar data som finns i ett lagrings konto.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: a0d5c758873413e549b31e3ec4cc41791fc8c371
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667411"
---
# <a name="analyze-data-in-a-storage-account"></a>Analysera data i ett lagrings konto

I den här självstudien får du lära dig hur du analyserar data som finns i ett lagrings konto.

## <a name="overview"></a>Översikt

Hittills har vi täckt scenarier där data finns i databaser på arbets ytan. Nu ska vi visa hur du arbetar med filer i lagrings konton. I det här scenariot använder vi det primära lagrings kontot för arbets ytan och behållaren som vi angav när du skapade arbets ytan.

* Namnet på lagrings kontot: **contosolake**
* Namnet på behållaren i lagrings kontot: **användare**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Skapa CSV-och Parquet-filer i ditt lagrings konto

Kör följande kod i en bärbar dator. Den skapar en CSV-fil och en Parquet-fil i lagrings kontot.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analysera data i ett lagrings konto

1. I Synapse Studio går du till **data** hubben och väljer sedan **länkad**.
1. Gå till **Storage Accounts**-  >  **arbetsytan (Primary-contosolake)**.
1. Välj **användare (primär)**. Du bör se mappen **NYCTaxi** . Inuti bör du se två mappar som heter **PassengerCountStats.csv** och **PassengerCountStats. Parquet**.
1. Öppna mappen **PassengerCountStats. Parquet** . Inuti ser du en Parquet-fil med ett namn som `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Högerklicka på **. Parquet**och välj sedan **ny antecknings bok**. Den skapar en antecknings bok som har en cell som den här:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Kör cellen.
1. Högerklicka på Parquet-filen inuti och välj sedan **nytt SQL-skript**  >  **Markera de 100 översta raderna**. Det skapar ett SQL-skript som detta:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    I fönstret skript är fältet **Anslut till** inställt på **SQL på begäran**.

1. Kör skriptet.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Dirigera aktiviteter med pipelines](get-started-pipelines.md)
