---
title: Använd Apache Kafka MirrorMaker – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Kafka-MirrorMaker för att spegla ett Kafka-kluster i AzureEvent-hubbar.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: d1ec20a32ef27856483492212608e20e82725f58
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369530"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Använd Kafka-MirrorMaker med Event Hubs för Apache Kafka

Den här självstudien visar hur du speglar en Kafka-Broker i en Event Hub med hjälp av Kafka MirrorMaker.

   ![Kafka MirrorMaker med Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Konfigurera ett Kafka-kluster
> * Konfigurera Kafka-MirrorMaker
> * Kör Kafka-MirrorMaker

## <a name="introduction"></a>Introduktion
En stor övervägande för modern moln skalnings program är möjligheten att uppdatera, förbättra och ändra infrastrukturen utan att avbryta tjänsten. Den här självstudien visar hur en Event Hub-och Kafka-MirrorMaker kan integrera en befintlig Kafka-pipeline i Azure genom att "spegla" Kafka-indataströmmen i Event Hubss tjänsten. 

Med en Azure Event Hubs Kafka-slutpunkt kan du ansluta till Azure Event Hubs med Kafka-protokollet (Kafka-klienter). Genom att göra minimala ändringar i ett Kafka-program kan du ansluta till Azure Event Hubs och dra nytta av fördelarna med Azure-eko systemet. Event Hubs stöder för närvarande Kafka-versionerna 1,0 och senare.

## <a name="prerequisites"></a>Krav

För att kunna följa den här självstudien måste du ha:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Hämta](https://maven.apache.org/download.cgi) och [Installera](https://maven.apache.org/install.html) ett maven-binärt Arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

En Event Hubs-namnrymd krävs för att skicka och ta emot från Event Hubs-tjänster. Instruktioner för att skapa ett namn område och en Event Hub finns i [skapa en Event Hub](event-hubs-create.md) . Se till att kopiera Event Hubs anslutnings strängen för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har en Event Hubs anslutnings sträng klonar du Azure-Event Hubs för Kafka-lagringsplatsen och navigerar till `mirror-maker` undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Konfigurera ett Kafka-kluster

Använd [snabb starts guiden för Kafka](https://kafka.apache.org/quickstart) för att konfigurera ett kluster med önskade inställningar (eller använda ett befintligt Kafka-kluster).

## <a name="configure-kafka-mirrormaker"></a>Konfigurera Kafka-MirrorMaker

Kafka-MirrorMaker aktiverar "spegling" av en data ström. Med käll-och mål Kafka-kluster ser MirrorMaker till att meddelanden som skickas till käll klustret tas emot av både käll-och mål kluster. Det här exemplet visar hur du speglar ett Kafka-kluster med en mål händelse hubb. Det här scenariot kan användas för att skicka data från en befintlig Kafka-pipeline till Event Hubs utan att avbryta data flödet. 

Mer detaljerad information om Kafka-MirrorMaker finns i [Kafka speglings-/MirrorMaker-guiden](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Om du vill konfigurera Kafka MirrorMaker ger du det ett Kafka-kluster som dess konsument/källa och en Event Hub som dess tillverkare/destination.

#### <a name="consumer-configuration"></a>Konsument konfiguration

Uppdatera konsument konfigurations filen `source-kafka.config` , som talar om för MirrorMaker egenskaperna för käll Kafka-klustret.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Producer-konfiguration

Uppdatera nu konfigurations filen för producenten `mirror-eventhub.config` , som talar om för MirrorMaker att de duplicerade (eller "speglade") data ska skickas till Event Hubs tjänsten. Mer specifikt, ändra `bootstrap.servers` och `sasl.jaas.config` för att peka på Event Hubs Kafka-slutpunkten. Den Event Hubs tjänsten kräver säker kommunikation (SASL), som uppnås genom att ställa in de sista tre egenskaperna i följande konfiguration: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Ersätt `{YOUR.EVENTHUBS.CONNECTION.STRING}` med anslutnings strängen för din Event Hubs-namnrymd. Anvisningar om hur du hämtar anslutnings strängen finns i [Hämta en Event Hubs anslutnings sträng](event-hubs-get-connection-string.md). Här är ett exempel på en konfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Kör Kafka-MirrorMaker

Kör Kafka MirrorMaker-skriptet från rot Kafka-katalogen med de nyligen uppdaterade konfigurationsfilerna. Se till att antingen kopiera config-filerna till rot katalogen Kafka eller uppdatera Sök vägarna i följande kommando.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

För att kontrol lera att händelser når händelsehubben, se ingångs statistik i [Azure Portal](https://azure.microsoft.com/features/azure-portal/)eller kör en konsument mot händelsehubben.

När MirrorMaker körs tas alla händelser som skickas till Kafka-klustret emot av både Kafka-klustret och den speglade Event-hubben. Genom att använda MirrorMaker och en Event Hubs Kafka-slutpunkt kan du migrera en befintlig Kafka-pipeline till den hanterade Azure Event Hubs-tjänsten utan att ändra det befintliga klustret eller avbryta pågående data flöde.

## <a name="samples"></a>Exempel
Se följande exempel på GitHub:

- [Exempel kod för den här självstudien på GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka-MirrorMaker som körs på en Azure Container instance](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Nästa steg
Mer information om Event Hubs för Kafka finns i följande artiklar:  

- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka ansluta med en Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Ansluta Akka-dataströmmar till en händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka Developer Guide för Azure Event Hubs](apache-kafka-developer-guide.md)