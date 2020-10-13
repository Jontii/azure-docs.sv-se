---
title: Placering av en PostgreSQL-Server grupp på Kubernetes-klusternoderna
description: Förklarar hur PostgreSQL-instanser som utgör en PostgreSQL-storskalig Server grupp placeras på noderna i Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b69eb076c727a4383b7459ef914ac79dca31c84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628425"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure Arc Enabled PostgreSQL, placering av Server grupp för storskalig Server grupp

I den här artikeln får vi ett exempel på hur du kan illustrera hur PostgreSQL-instanser av Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper placeras på de fysiska noderna i det Kubernetes-kluster som är värdar för dem. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Konfiguration

I det här exemplet använder vi ett Azure Kubernetes service-kluster (AKS) som har fyra fysiska noder. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="4 Node AKS-kluster i Azure Portal":::

Visa en lista över de fysiska noderna i Kubernetes-klustret genom att köra kommandot:

```console
kubectl get nodes
```

Som visar de fyra fysiska noderna i Kubernetes-klustret:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

Arkitekturen kan representeras som:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="4 Node AKS-kluster i Azure Portal":::

Kubernetes-klustret är värd för en Azure Arc-datakontrollant och en Azure Arc-aktiverad PostgreSQL-Server grupp. Den här server gruppen består av tre PostgreSQL-instanser: en koordinator och två arbetare.

Ange poddar med kommandot:

```console
kubectl get pods -n arc3
```
Som ger följande utdata:

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          9h
postgres01-1         3/3     Running   0          9h
postgres01-2         3/3     Running   0          9h
```
Var och en av dessa poddar är värd för en PostgreSQL-instans. Tillsammans bildar de Azure Arc-aktiverade PostgreSQL-Server gruppen:

```output
Pod name    Role in the server group
postgres01-0            Coordinator
postgres01-1    Worker
postgres01-2    Worker
```

## <a name="placement"></a>Placering
Nu ska vi titta på hur Kubernetes placerar poddar i Server gruppen. Beskriv varje Pod och identifiera på vilken fysisk nod i Kubernetes-klustret som de placeras. För koordinatorn till exempel kör du följande kommando:

```console
kubectl describe pod postgres01-0 -n arc3
```

Som ger följande utdata:

```output
Name:         postgres01-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

När vi kör det här kommandot för varje poddar sammanfattar vi den aktuella placeringen som:

| Server grupp roll|Pod för Server grupp|Kubernetes fysiska nod som värd för Pod |
|--|--|--|
| Företag|postgres01-1|AKS-agentpoolegenskap-42715708-vmss000002 |
| Företag|postgres01-2|AKS-agentpoolegenskap-42715708-vmss000003 |

Och Observera också i beskrivningen av poddar, namnen på de behållare som varje Pod-värd är värd för. För den andra arbets tagaren kör du till exempel följande kommando:

```console
kubectl describe pod postgres01-2 -n arc3
```

Som ger följande utdata:

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Varje Pod som är en del av Azure Arc-aktiverade PostgreSQL för skalnings Server gruppen värd för följande tre behållare:

|Containrar|Beskrivning
|----|----|
|`Fluentbit` |Data * logg insamlare: https://fluentbit.io/
|`Postgres`|PostgreSQL instans del av Azure Arc-aktiverade PosgreSQL för storskalig Server grupp
|`Telegraf` |Mått insamlare: https://www.influxdata.com/time-series-platform/telegraf/

Arkitekturen ser ut så här:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="4 Node AKS-kluster i Azure Portal":::

Det innebär att varje PostgreSQL-instans som utgörs av Azure Arc-aktiverade PostgreSQL för storskalig Server grupp finns på en viss fysisk värd i Kubernetes-behållaren. Det här är den bästa konfigurationen för att få ut mesta möjliga prestanda av Azure-bågen aktiverade PostgreSQL för storskalig Server grupp som varje roll (koordinator och arbetare) använder resurserna för varje fysisk nod. Dessa resurser delas inte mellan flera PostgreSQL-roller.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Skala ut Azure Arc Enabled PostgreSQL-skalning

Nu ska vi skala ut för att lägga till en tredje arbetsnoden i Server gruppen och observera vad som händer. En fjärde PostgreSQL-instans skapas som kommer att finnas i en fjärde pod.
För att skala ut kör du kommandot:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Som ger följande utdata:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Lista de Server grupper som distribueras i data styrenheten för Azure-bågen och kontrol lera att Server gruppen nu körs med tre arbetare. Kör kommandot:

```console
azdata arc postgres server list
```

Och Observera att det har skalats ut från två arbetare till tre arbets tagare:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Som vi gjorde tidigare, Observera att Server gruppen nu använder totalt fyra poddar:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          11h
postgres01-1         3/3     Running   0          11h
postgres01-2         3/3     Running   0          11h
postgres01-3         3/3     Running   0          5m2s
```

Och Beskriv den nya Pod för att identifiera vilka av de fysiska noderna i Kubernetes-klustret som den är värd för.
Kör kommandot:

```console
kubectl describe pod postgres01-3 -n arc3
```

Så här identifierar du namnet på noden värd:

```output
Name:         postgres01-3
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

Placeringen av PostgreSQL-instanserna på de fysiska noderna i klustret är nu:

|Server grupp roll|Pod för Server grupp|Kubernetes fysiska nod som värd för Pod
|-----|-----|-----
|Fungerar|postgres01-0|AKS-agentpoolegenskap-42715708-vmss000000
|Företag|postgres01-1|AKS-agentpoolegenskap-42715708-vmss000002
|Företag|postgres01-2|AKS-agentpoolegenskap-42715708-vmss000003
|Företag|postgres01-3|AKS-agentpoolegenskap-42715708-vmss000000

Och Observera att Pod för den nya arbetaren (postgres01-3) har placerats på samma nod som koordinatorn. 

Arkitekturen ser ut så här:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="4 Node AKS-kluster i Azure Portal":::

Varför placeras inte den nya arbets tagaren/Pod på den återstående fysiska noden i Kubernetes Cluster AKS-agentpoolegenskap-42715708-vmss000003?

Orsaken är att den sista fysiska noden i Kubernetes-klustret faktiskt är värd för flera poddar som är värdar för ytterligare komponenter som krävs för att köra Azure Arc-aktiverade data tjänster. Kubernetes bedömde att den bästa kandidaten – vid schemaläggning – som värd för den ytterligare arbetaren är den fysiska AKS-agentpoolegenskap-42715708-vmss000000-noden. 

Använda samma kommandon som ovan. Vi ser vad varje fysisk nod är värd för:

|Andra poddar namn\* |Användning|Kubernetes fysiska nod som värd för poddar
|----|----|----
|Start program – jh48b|Det här är en tjänst som hanterar inkommande begär Anden för att skapa, redigera och ta bort anpassade resurser, till exempel SQL-hanterade instanser, PostgreSQL för storskaliga Server grupper och data kontroller|AKS-agentpoolegenskap-42715708-vmss000003
|kontroll – gwmbs||AKS-agentpoolegenskap-42715708-vmss000002
|controldb-0|Det här är det kontroll enhets data lager som används för att lagra konfiguration och tillstånd för datakontrollanten.|AKS-agentpoolegenskap-42715708-vmss000001
|controlwd-zzjp7|Det här är tjänsten kontrollanten Watch hund som håller ett öga på tillgängligheten för datakontrollanten.|AKS-agentpoolegenskap-42715708-vmss000000
|logsdb-0|Det här är en elastisk Sök instans som används för att lagra alla loggar som samlats in över alla Arc Data Services-poddar. ElasticSearch tar emot data från `Fluentbit` behållare för varje Pod|AKS-agentpoolegenskap-42715708-vmss000003
|logsui-5fzv5|Det här är en Kibana-instans som finns ovanpå den elastiska Sök databasen för att presentera ett gränssnitt för Log Analytics.|AKS-agentpoolegenskap-42715708-vmss000003
|metricsdb-0|Det här är en InfluxDB-instans som används för att lagra alla mått som samlas in över alla Arc Data Services-poddar. InfluxDB tar emot data från `Telegraf` behållaren för varje Pod|AKS-agentpoolegenskap-42715708-vmss000000
|metricsdc-47d47|Det här är en daemonset som distribueras på alla Kubernetes-noder i klustret för att samla in mått på noder på radnivå.|AKS-agentpoolegenskap-42715708-vmss000002
|metricsdc-864kj|Det här är en daemonset som distribueras på alla Kubernetes-noder i klustret för att samla in mått på noder på radnivå.|AKS-agentpoolegenskap-42715708-vmss000001
|metricsdc-l8jkf|Det här är en daemonset som distribueras på alla Kubernetes-noder i klustret för att samla in mått på noder på radnivå.|AKS-agentpoolegenskap-42715708-vmss000003
|metricsdc-nxm4l|Det här är en daemonset som distribueras på alla Kubernetes-noder i klustret för att samla in mått på noder på radnivå.|AKS-agentpoolegenskap-42715708-vmss000000
|metricsui-4fb7l|Det här är en Grafana-instans som finns ovanpå InfluxDB-databasen för att presentera ett användar gränssnitt för övervakning av instrument paneler.|AKS-agentpoolegenskap-42715708-vmss000003
|mgmtproxy-4qppp|Det här är ett proxy-lager för webbprogramproxy som är framför Grafana-och Kibana-instanserna.|AKS-agentpoolegenskap-42715708-vmss000002

> \* Suffixet på pod namn varierar beroende på andra distributioner. Dessutom visas en lista med de poddar som finns i Kubernetes-namnområdet för Azure Arc-datakontrollanten.

Arkitekturen ser ut så här:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="4 Node AKS-kluster i Azure Portal":::

Det innebär att koordinator-noderna (Pod 1) för den Azure-Arc-aktiverade postgres Server gruppen delar samma fysiska resurser som den tredje arbetsnoden (Pod 4) i Server gruppen. Det är acceptabelt som koordinator-noden använder vanligt vis mycket små resurser i jämförelse med hur en arbetsnoden kan använda. Från detta kan du härleda att du noga bör välja:
- storleken på Kubernetes-klustret och egenskaperna för var och en av dess fysiska noder (minne, vCore)
- antalet fysiska noder i Kubernetes-klustret
- de program eller arbets belastningar som du är värd för Kubernetes-klustret.

Indirekt för många arbets belastningar i Kubernetes-klustret kan bero på att Server gruppen Azure Arc är aktiverat PostgreSQL. Om detta händer, kommer du inte att ha nytta av möjligheten att skala horisontellt. Den prestanda du får utanför systemet är inte bara på placeringen eller fysiska egenskaper för fysiska noder eller lagrings systemet. Den prestanda du får också om hur du konfigurerar var och en av de resurser som körs i Kubernetes-klustret (inklusive Azure Arc Enabled PostgreSQL storskalig), till exempel de begär Anden och gränser som du anger för minne och vCore. Mängden arbets belastning som du kan vara värd för ett visst Kubernetes-kluster är i förhållande till egenskaperna hos Kubernetes-klustret, typen av arbets belastningar, antalet användare, hur Kubernetes-klustrets åtgärder är klara...

## <a name="scale-out-aks"></a>Skala ut AKS

Vi demonstrerar att skala horisontellt både AKS-klustret och den PostgreSQL-storskaliga Azure-Arc-servern är ett sätt att dra nytta av det mesta av den höga prestandan hos Azure Arc-aktiverad PostgreSQL-skalning.
Nu ska vi lägga till en femte nod i AKS-klustret:

:::row:::
    :::column:::
        Före
    :::column-end:::
    :::column:::
        Efter
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="4 Node AKS-kluster i Azure Portal":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="4 Node AKS-kluster i Azure Portal":::
    :::column-end:::
:::row-end:::

Arkitekturen ser ut så här:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="4 Node AKS-kluster i Azure Portal":::

Nu ska vi titta på vilken poddar av Arc data Controller-namnområdet som finns på den nya AKS fysiska noden genom att köra kommandot:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

Vi uppdaterar åter givningen av arkitekturen i vårt system:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="4 Node AKS-kluster i Azure Portal":::

Vi kan se att den nya fysiska noden i Kubernetes-klustret bara är värd för de mått Pod som krävs för Azure båg Data Services. Observera att vi i det här exemplet bara fokuserar på namn området för data styrenheten Arc, vi inte representerar de andra poddar.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Skala ut Azure Arc Enabled PostgreSQL-skalning igen

Den femte fysiska noden är inte värd för någon arbets belastning än. När vi skalar ut den Azure Arc-aktiverade PostgreSQL-Kubernetes optimerar placeringen av den nya PostgreSQL-Pod och ska inte samordna den på fysiska noder som redan är värdar för fler arbets belastningar. Kör följande kommando för att skala den aktiverade PostgreSQL-skalningen i Azure-bågen från 3 till 4 arbetare. I slutet av åtgärden kommer Server gruppen att skapas och distribueras över fem PostgreSQL-instanser, en koordinator och fyra arbets tagare.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Som ger följande utdata:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Lista de Server grupper som distribueras i datakontrollanten och kontrol lera att Server gruppen nu körs med fyra arbets tagare:

```console
azdata arc postgres server list
```

Och Observera att det har skalats ut från tre till fyra arbetare. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Som vi gjorde tidigare använder nu Server gruppen fyra poddar:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          13h
postgres01-1         3/3     Running   0          13h
postgres01-2         3/3     Running   0          13h
postgres01-3         3/3     Running   0          179m
postgres01-4         3/3     Running   0          3m13s
```

Server gruppens form är nu:

|Server grupp roll|Pod för Server grupp
|----|-----
|Fungerar|postgres01-0
|Företag|postgres01-1
|Företag|postgres01-2
|Företag|postgres01-3
|Företag|postgres01-4

Nu ska vi beskriva postgres01-4-Pod för att identifiera i vilken fysisk nod den finns:

```console
kubectl describe pod postgres01-4 -n arc3
```

Och titta på vad poddar IT kör:

|Server grupp roll|Pod för Server grupp| Pod
|----|-----|------
|Fungerar|postgres01-0|AKS-agentpoolegenskap-42715708-vmss000000
|Företag|postgres01-1|AKS-agentpoolegenskap-42715708-vmss000002
|Företag|postgres01-2|AKS-agentpoolegenskap-42715708-vmss000003
|Företag|postgres01-3|AKS-agentpoolegenskap-42715708-vmss000000
|Företag|postgres01-4|AKS-agentpoolegenskap-42715708-vmss000004

Och arkitekturen ser ut så här:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="4 Node AKS-kluster i Azure Portal":::

Kubernetes schemalades den nya PostgreSQL-Pod på den minst inlästa fysiska noden i Kubernetes-klustret.

## <a name="summary"></a>Sammanfattning

För att få ut mesta möjliga av skalbarheten och prestanda för skalning av Azure Arc-aktiverade Server grupper, bör du undvika resurs konkurrens i Kubernetes-klustret:
- mellan Azure-bågen aktiverade PostgreSQL för storskalig Server grupp och andra arbets belastningar som finns i samma Kubernetes-kluster
- mellan alla PostgreSQL-instanser som utgör Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper

Du kan åstadkomma detta på flera sätt:
- Skala ut både Kubernetes och Azure Arc Enabled postgres Scale: Överväg att skala vågrätt Kubernetes-kluster på samma sätt som du skalar den Azure Arc-aktiverade PostgreSQL-gruppen för storskalig Server. Lägg till en fysisk nod i klustret för varje arbets tagare som du lägger till i Server gruppen.
- Skala ut Azure Arc Enabled postgres storskaligt utan att skala ut Kubernetes: genom att ange rätt resurs begränsningar (begär ande och begränsningar för minnes-och vCore) på arbets belastningarna som finns i Kubernetes (Azure Arc Enabled PostgreSQL storskalig) aktiverar du samarbets belastningen på Kubernetes och minskar risken för resurs konkurrens. Du måste se till att de fysiska egenskaperna för de fysiska noderna i Kubernetes-klustret kan respektera de resurs begränsningar som du definierar. Du bör också se till att jämvikten förblir när arbets belastningarna utvecklas med tiden eller att fler arbets belastningar läggs till i Kubernetes-klustret.
- Använd Kubernetes-mekanismerna (Pod Selector, Affinity, anti-Affinity) för att påverka placeringen av poddar.

## <a name="next-steps"></a>Nästa steg

[Skala ut din Azure-Arc Enabled PostgreSQL Scale Server Group genom att lägga till fler arbetsnoder](scale-out-postgresql-hyperscale-server-group.md)
