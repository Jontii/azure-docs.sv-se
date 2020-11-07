---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 320fa542f2b786f0a256c22f2d2eb299c476dcae
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94362772"
---
Följande tabeller innehåller kvoter och begränsningar som är begränsade till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Information om Event Hubs priser finns i [Event Hubs prissättning](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="common-limits-for-all-tiers"></a>Vanliga gränser för alla nivåer
Följande begränsningar är gemensamma för alla nivåer. 

| Gräns |  Kommentarer | Värde |
| --- |  --- | --- |
| Antal Event Hubs namn rymder per prenumeration |- |100 |
| Antal Event Hub per namnrymd | Efterföljande begär Anden om att skapa en ny händelsehubben avvisas. |10 |
| Antal partitioner per Event-hubb |- |32 |
| Storlek på ett Event Hub-namn |- | 256 tecken |
| Storlek på ett konsument grupp namn |- | 256 tecken |
| Antal icke-epok mottagare per konsument grupp |- |5 |
| Antal auktoriseringsregler per namnrymd | Efterföljande begär Anden om att skapa auktoriseringsregler avvisas.|12 |
| Antal anrop till GetRuntimeInformation-metoden |  - | 50 per sekund | 
| Antal virtuella nätverk (VNet) och IP config-regler | - | 128 | 


## <a name="basic-vs-standard-tiers"></a>Basic vs. standard-nivåer
I följande tabell visas de gränser som kan vara olika för nivåerna Basic och standard. 

| Gräns | Kommentarer | Basic | Standard |
|---|---|--|---|
| Maximal storlek för Event Hubs händelse| &nbsp; | 256 kB | 1 MB |
| Antal konsument grupper per Event-hubb | &nbsp; |1 |20 |
| Antal AMQP-anslutningar per namnrymd | Efterföljande begär Anden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |100 |5 000|
| Högsta kvarhållningsperiod för händelse data | &nbsp; |1 dag |1-7 dagar |
| Maximalt antal data flödes enheter |Att överskrida den här gränsen gör att dina data begränsas och genererar en [Server upptagen undantag](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Om du vill begära ett större antal data flödes enheter för en standard-nivå kan du skicka en [support förfrågan](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Ytterligare data flödes enheter](../articles/event-hubs/event-hubs-auto-inflate.md) är tillgängliga i block om 20 på ett dedikerat köp. |20 | 20 | 

## <a name="dedicated-tier-vs-standard-tier"></a>Dedikerad nivå kontra standard nivå
Event Hubs Dedicated erbjudandet debiteras till ett fast månads pris, med minst fyra timmars användning. Den dedikerade nivån innehåller alla funktioner i standard planen, men med kapacitet för företags skala och gränser för kunder med krävande arbets belastningar. 

Läs det här [dokumentet](https://docs.microsoft.com/azure/event-hubs/event-hubs-dedicated-cluster-create-portal) om hur du skapar dedikerat Event Hubs kluster med Azure Portal.

| Funktion | Standard | Dedikerad |
| --- |:---|:---|
| Bandbredd | 20 antal (upp till 40 antal) | 20 CUs |
| Namnrymder |  1 | 50 per CU |
| Event Hubs |  10 per namnrymd | 1000 per namnrymd |
| Ingress-händelser | Betala per miljon händelser | Ingår |
| Meddelande storlek | 1 000 000 byte | 1 000 000 byte |
| Partitioner | 32 per Event-hubb | 1024 per Event-hubb<br/>2000 per CU |
| Konsumentgrupper | 20 per Event-hubb | Ingen gräns per CU, 1000 per Event-hubb |
| Brokered Connections | 1 000 ingår, 5 000 max | 100 KB ingår och Max |
| Kvarhållning av meddelanden | 7 dagar, 84 GB ingår per data flödes enheter | 90 dagar, 10 TB inkluderat per CU |
| Capture | Betala per timme | Ingår |


## <a name="schema-registry-limitations"></a>Begränsningar för schema registret

### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Begränsningar som är desamma för **standard** -och **dedikerade** nivåer 
| Funktion | Gräns | 
|---|---|--|
| Maximal längd på ett schema grupps namn | 50 |  
| Maximal längd för ett schema namn | 100 |    
| Storlek i byte per schema | 1 MB |   
| Antal egenskaper per schema grupp | 1024 |
| Storlek i byte per grupp egenskaps nyckel | 256 | 
| Storlek i byte per grupp egenskaps värde | 1024 | 


### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Begränsningar som skiljer sig mellan **standard** -och **dedikerade** nivåer 

| Gräns | Standard | Dedikerad | 
|---|---|--|---|
| Storlek på schema registret (namnrymd) i megabyte | 25 |  1024 |
| Antal schema grupper i ett schema register eller namnrymd | 1 – exklusive standard gruppen | 1000 |
| Antal schema versioner i alla schema grupper | 25 | 10000 |





