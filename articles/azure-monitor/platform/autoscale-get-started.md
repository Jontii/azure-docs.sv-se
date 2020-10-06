---
title: Kom igång med autoskalning i Azure
description: Lär dig hur du skalar din resurs-webbapp, moln tjänst, virtuell dator eller skalnings uppsättning för virtuella datorer i Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: b8d16b4e112c9aebe86c60dc01d380d591fc7624
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743530"
---
# <a name="get-started-with-autoscale-in-azure"></a>Kom igång med autoskalning i Azure
I den här artikeln beskrivs hur du konfigurerar inställningarna för autoskalning för resursen i Microsoft Azure-portalen.

Azure Monitor autoskalning gäller endast för [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)-, [Cloud Services](https://azure.microsoft.com/services/cloud-services/)-, [App Service-Web Apps-](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](../../api-management/api-management-key-concepts.md).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Upptäck inställningarna för automatisk skalning i din prenumeration

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Du kan identifiera alla resurser för vilka automatisk skalning är tillämpligt i Azure Monitor. Använd följande steg för att göra en steg-för-steg-genom gång:

1. Öppna [Azure Portal.][1]
1. Klicka på ikonen Azure Monitor i det vänstra fönstret.
  ![Öppna Azure Monitor][2]
1. Klicka på **autoskalning** för att visa alla resurser för vilka autoskalning är tillämpligt, tillsammans med deras aktuella status för autoskalning.
  ![Upptäck autoskalning i Azure Monitor][3]

Du kan använda filter fönstret överst för att begränsa listan till att välja resurser i en speciell resurs grupp, vissa resurs typer eller en speciell resurs.

För varje resurs hittar du aktuella instans antal och status för autoskalning. Status för autoskalning kan vara:

- **Inte konfigurerad**: du har inte aktiverat autoskalning än för den här resursen.
- **Aktive rad**: du har aktiverat autoskalning för den här resursen.
- **Inaktive rad**: du har inaktiverat autoskalning för den här resursen.

## <a name="create-your-first-autoscale-setting"></a>Skapa din första inställning för autoskalning

Nu ska vi gå igenom en enkel steg-för-steg-genom gång för att skapa din första inställning för automatisk skalning.

1. Öppna bladet för **autoskalning** i Azure Monitor och välj en resurs som du vill skala. (Följande steg använder en App Service plan som är associerad med en webbapp. Du kan [skapa din första ASP.NET-webbapp i Azure på fem minuter.][4])
1. Observera att det aktuella instans antalet är 1. Klicka på **Aktivera autoskalning**.
  ![Skalnings inställning för ny webbapp][5]
1. Ange ett namn för skalnings inställningen och klicka sedan på **Lägg till en regel**. Observera de skalnings regel alternativ som öppnas som ett kontext fönster på den högra sidan. Som standard anger detta alternativet att skala antalet instanser med 1 om resursens procent andel överstiger 70 procent. Låt standardvärdena vara kvar och klicka på **Lägg till**.
  ![Skapa skalnings inställning för en webbapp][6]
1. Nu har du skapat din första skalnings regel. Observera att UX rekommenderar bästa praxis och anger att "det rekommenderas att ha minst en skala i regeln". Så här gör du:

    a. Klicka på **Lägg till en regel**.

    b. Ange **operatorn** till **mindre än**.

    c. Ange **tröskelvärdet** till **20**.

    d. Ange **åtgärden** för att **minska antalet med**.

   Nu bör du ha en skalnings inställning som skalar ut/skalar i baserat på CPU-användning.
   ![Skala baserat på CPU][8]
1. Klicka på **Spara**.

Grattis! Nu har du skapat din första skalnings inställning för att Autoskala din webbapp baserat på CPU-användning.

> [!NOTE]
> Samma steg gäller för att komma igång med en skalnings uppsättning för virtuella datorer eller en moln tjänst roll.

## <a name="other-considerations"></a>Ytterligare överväganden
### <a name="scale-based-on-a-schedule"></a>Skala baserat på ett schema
Förutom skalning baserat på CPU kan du ställa in din skala på olika sätt för vissa dagar i veckan.

1. Klicka på **Lägg till ett skalnings villkor**.
1. Om du ställer in skalnings läget och reglerna är samma som standard villkoret.
1. Välj **upprepa vissa dagar** för schemat.
1. Välj dagar och start-/slut tid för när skalnings villkoret ska tillämpas.

![Skalnings villkor baserat på schema][9]
### <a name="scale-differently-on-specific-dates"></a>Skala annorlunda på vissa datum
Förutom skalning baserat på CPU kan du ställa in skalan på olika sätt för vissa datum.

1. Klicka på **Lägg till ett skalnings villkor**.
1. Om du ställer in skalnings läget och reglerna är samma som standard villkoret.
1. Välj **Ange start-/slutdatum** för schemat.
1. Välj start-/slutdatum och start-/slut tid för när skalnings villkoret ska tillämpas.

![Skalnings villkor baserat på datum][10]

### <a name="view-the-scale-history-of-your-resource"></a>Visa resursens skalnings historik
När din resurs skalas upp eller ned loggas en händelse i aktivitets loggen. Du kan visa skalnings historiken för din resurs under de senaste 24 timmarna genom att växla till fliken **körnings historik** .

![Körningshistorik][11]

Om du vill visa hela skalnings historiken (i upp till 90 dagar) väljer du **Klicka här för att visa mer information**. Aktivitets loggen öppnas med autoskalning förvald för din resurs och kategori.

### <a name="view-the-scale-definition-of-your-resource"></a>Visa skalnings definitionen för din resurs
Autoskalning är en Azure Resource Manager resurs. Du kan visa skalnings definitionen i JSON genom att växla till fliken **JSON** .

![Skalnings definition][12]

Du kan göra ändringar i JSON direkt, om det behövs. Ändringarna visas när du har sparat dem.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Inaktivera autoskalning och skala dina instanser manuellt
Det kan finnas tillfällen när du vill inaktivera den aktuella skalnings inställningen och manuellt skala din resurs.

Klicka på knappen **inaktivera autoskalning** överst.
![Inaktivera autoskalning][13]

> [!NOTE]
> Det här alternativet inaktiverar konfigurationen. Du kan dock gå tillbaka till den när du har aktiverat autoskalning igen.

Nu kan du ange antalet instanser som du vill skala till manuellt.

![Ange manuell skalning][14]

Du kan alltid återgå till autoskalning genom att klicka på **Aktivera autoskalning** och sedan **Spara**.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Dirigera trafik till felfria instanser (App Service)

När du skalar ut till flera instanser kan App Service utföra hälso kontroller på dina instanser för att endast dirigera trafik till de felfria instanserna. Det gör du genom att öppna portalen till App Service och sedan välja **hälso kontroll** under **övervakning**. Välj **Aktivera** och ange en giltig URL-sökväg till programmet, till exempel `/health` eller `/api/health` . Klicka på **Spara**.

Om du vill aktivera funktionen med ARM-mallar anger du `healthcheckpath` egenskapen för `Microsoft.Web/sites` resursen till hälso kontroll Sök vägen på platsen, till exempel: `"/api/health/"` . Om du vill inaktivera funktionen anger du egenskapen tillbaka till den tomma strängen `""` .

### <a name="health-check-path"></a>Hälso kontroll Sök väg

Sökvägen måste svara inom två minuter med en status kod mellan 200 och 299 (inklusive). Om sökvägen inte svarar inom två minuter eller returnerar en status kod utanför intervallet, betraktas instansen som "ej felfri". Hälso kontrollen integreras med App Service funktioner för autentisering och auktorisering, systemet når slut punkten även om dessa secuity-funktioner är aktiverade. Om du använder ett eget autentiseringspaket måste sökvägen till hälso kontrollen tillåta anonym åtkomst. Om webbplatsen bara har HTTP**s**– aktive rad skickas Healthcheck-begäran via http**s**.

Hälso kontroll Sök vägen bör kontrol lera de kritiska komponenterna i ditt program. Om ditt program till exempel är beroende av en databas och ett meddelande system bör hälso kontrollens slut punkt ansluta till dessa komponenter. Om programmet inte kan ansluta till en kritisk komponent, ska sökvägen returnera en svars kod på 500 nivå för att indikera att appen inte är felfri.

#### <a name="security"></a>Säkerhet 

Utvecklings grupper i stora företag måste ofta följa säkerhets kraven för de exponerade API: erna. För att skydda Healthcheck-slutpunkten bör du först använda funktioner som [IP-begränsningar](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules), [klient certifikat](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)eller en Virtual Network för att begränsa åtkomsten till programmet. Du kan skydda själva Healthcheck-slutpunkten genom att kräva att den `User-Agent` inkommande begäran matchar `ReadyForRequest/1.0` . Användar agenten kan inte ha förfalskning eftersom begäran redan skyddats av de tidigare säkerhetsfunktionerna.

### <a name="behavior"></a>Beteende

När hälso kontroll Sök vägen anges skickar App Service pinga sökvägen på alla instanser. Om en lyckad svarskod inte tas emot efter fem pingar anses den instansen vara "ej felfri". Felaktiga instansen kommer att uteslutas från belastnings Utjämnings rotationen. När du skalar upp eller ut kommer App Service att pinga hälso kontroll Sök vägen för att se till att de nya instanserna är klara för begär Anden.

De återstående felfria instanserna kan uppleva ökad belastning. För att undvika att de återstående instanserna blir överbelastade är inte fler än hälften av instanserna uteslutna. Till exempel, om en App Services plan skalas ut till 4 instanser och 3 av vilka inte är felfri, kommer den högst 2 att uteslutas från belastningsutjämnarens rotation. De andra 2 instanserna (1 felfri och 1 är inte felfria) fortsätter att ta emot begär Anden. I värsta fall där alla instanser är felaktiga kommer ingen att undantas.

Om en instans inte är felfri i en timme ersätts den med en ny instans. Högst en instans kommer att ersättas per timme, med högst tre instanser per dag per App Service plan.

### <a name="monitoring"></a>Övervakning

När du har angett din program hälso kontroll Sök väg kan du övervaka webbplatsens hälso tillstånd med hjälp av Azure Monitor. Från **hälso kontroll** bladet i portalen klickar du på **måtten** i det övre verktygsfältet. Då öppnas ett nytt blad där du kan se platsens historiska hälso status och skapa en ny varnings regel. Mer information om övervakning av dina platser [finns i hand boken för Azure Monitor](../../app-service/web-sites-monitor.md).

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitets logg avisering för att övervaka alla åtgärder för autoskalning av motorn i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitets logg avisering för att övervaka alla misslyckade skalnings-i/skala ut-åtgärder i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
