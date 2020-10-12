---
title: Övervaka data flöden för mappning
description: Övervaka mappning av data flöden visuellt i Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/19/2020
ms.openlocfilehash: 77dda42b27aa6f5fb505fe65667876523cb3f5d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650901"
---
# <a name="monitor-data-flows"></a>Övervaka data flöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du har slutfört skapandet och fel sökningen av ditt data flöde vill du schemalägga att ditt data flöde ska köras enligt ett schema inom ramen för en pipeline. Du kan schemalägga pipelinen från Azure Data Factory med utlösare. Eller så kan du använda alternativet utlösare nu från Azure Data Factory pipeline Builder för att köra en körning med en körning för att testa ditt data flöde inom pipeline-kontexten.

När du kör din pipeline kan du övervaka pipelinen och alla aktiviteter som finns i pipelinen, inklusive data flödes aktiviteten. Klicka på ikonen övervaka i panelen till vänster Azure Data Factory användar gränssnitt. Du kan se en skärm som liknar den nedan. Med de markerade ikonerna kan du öka detalj nivån i aktiviteterna i pipelinen, inklusive data flödes aktiviteten.

![Övervakning av data flöde](media/data-flow/mon001.png "Dataflödesövervakning")

Du ser statistik på den här nivån även körnings tiderna och-statusen. Körnings-ID: t på aktivitets nivån skiljer sig från körnings-ID: t på pipelin nivån. Körnings-ID: t på den föregående nivån är för pipelinen. Genom att välja glasögon får du djupgående information om din data flödes körning.

![Övervakning av data flöde](media/data-flow/monitoring-details.png "Dataflödesövervakning")

När du befinner dig i vyn övervakning av grafisk nod kan du se en förenklad visnings version av data flödes diagrammet.

![Övervakning av data flöde](media/data-flow/mon003.png "Dataflödesövervakning")

Här är en video översikt över övervakning av prestanda för dina data flöden från fönstret ADF Monitoring:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Visa körnings planer för data flöde

När ditt data flöde körs i Spark, fastställer Azure Data Factory optimala kod Sök vägar baserat på hela ditt data flöde. Dessutom kan sökvägar för körning ske på olika skalbara noder och datapartitioner. Därför representerar övervaknings diagrammet utformningen av ditt flöde, med hänsyn till körnings vägen för dina transformeringar. När du väljer enskilda noder kan du se "grupperingar" som representerar kod som har körts tillsammans i klustret. De tids inställningar och räknare som du ser representerar dessa grupper i stället för de enskilda stegen i din design.

![Övervakning av data flöde](media/data-flow/mon004.png "Dataflödesövervakning")

* När du väljer det öppna utrymmet i övervaknings fönstret, visar statistik i det nedre fönstret tidtagnings-och rad antal för varje mottagare och de omvandlingar som ledde till mottagar data för omvandlings härkomst.

* När du väljer enskilda transformeringar får du ytterligare feedback på den högra panelen som visar partitionerings statistik, kolumn antal, snedhet (hur jämnt är data som distribueras mellan partitioner) och toppar (hur höga data är).

* När du väljer mottagaren i vyn Node kan du se kolumnen härkomst. Det finns tre olika metoder som kolumner samlas in i data flödet för att hamna i mottagaren. De är:

  * Beräknad: du använder kolumnen för villkorlig bearbetning eller inom ett uttryck i ditt data flöde, men du kan inte landa den i mottagaren
  * Härlett: kolumnen är en ny kolumn som du har genererat i ditt flöde, det vill säga den fanns inte i källan
  * Mappad: kolumnen kommer från källan och din mappas till fältet mottagare
  * Data flödes status: aktuell status för din körning
  * Kluster start tid: hur lång tid det tar att hämta JIT Spark Compute-miljön för data flödes körning
  * Antal transformeringar: hur många omvandlings steg som körs i ditt flöde
  
![Övervakning av data flöde](media/data-flow/monitornew.png "Data flödes övervakning New")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Total bearbetnings tid för mottagare jämfört med omvandlings tid för transformering

Varje omvandlings steg innehåller en total tid för fasen att slutföras med varje partitions körnings tid sammanlagt tillsammans. När du klickar på mottagaren visas "mottagar bearbetnings tid". Den här tiden inkluderar summan av Transformations tiden *plus* i/O-tiden det tog att skriva data till mål lagret. Skillnaden mellan bearbetnings tiden för mottagaren och det totala antalet omvandlingar är i/O-tiden för att skriva data.

Du kan också se detaljerad tids inställning för varje partitions omvandlings steg om du öppnar JSON-utdata från din data flödes aktivitet i vyn ADF pipeline-övervakning. JSON innehåller millisekund-timing för varje partition, medan vyn UX-övervakning är en sammanställd tids inställning för partitionerna som läggs till tillsammans:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="post-processing-time"></a>Bearbetnings tid för inlägg

När du väljer en omvandlings ikon för mottagare i kartan visas en ytterligare data punkt med namnet "efter bearbetnings tid" längst ned i den högra panelen. Detta är den mängd tid som krävs för att köra jobbet på Spark-klustret *efter* att dina data har lästs in, omvandlats och skrivits. Den här tiden kan vara att stänga anslutningspooler, stänga av driv rutin, ta bort filer, sammanföra filer osv. När du utför åtgärder i ditt flöde, t. ex. "flytta filer" och "utdata till en enskild fil", kommer du troligen att se en ökning i värdet efter bearbetnings tid.
  
## <a name="monitor-icons"></a>Övervaka ikoner

Den här ikonen innebär att Transformations data redan har cachelagrats i klustret, så tids inställningarna och körnings Sök vägen har tagit detta i beräkningen:

![Övervakning av data flöde](media/data-flow/mon005.png "Dataflödesövervakning")

Du ser också gröna cirkel ikoner i omvandlingen. De representerar antalet handfat som data flödar in i.
