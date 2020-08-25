---
title: Transformera och skydda ditt API med Azure API Management | Microsoft Docs
description: Lär dig hur du skyddar ditt API med kvoter och begränsningsprinciper (frekvensbegränsning).
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 4c3cc572dd9629605414cd88d7735c2b31f92249
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "85851259"
---
# <a name="transform-and-protect-your-api"></a>Transformera och skydda ditt API

I kursen visas hur du kan transformera ditt API så att det inte avslöjar någon privat serverdelsinformation. Du vill kanske dölja information om teknikstacken som körs på serverdelen. Kanske vill du också dölja de ursprungliga URL:er som visas i brödtexten i API:ets HTTP-svar och istället omdirigera dem till APIM-gatewayen.

Den här självstudiekursen beskriver också hur du enkelt kan skydda ditt serverdels-API genom att konfigurera frekvensbegränsningar med Azure API Management. Du vill kanske t.ex. begränsa det antal gånger som API:et anropas, så att det inte överutnyttjas av utvecklarna. Mer information finns i [API Management-principer](api-management-policies.md)

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> -   Omvandla ett API och ta bort svarshuvuden
> -   Ersätt ursprungliga URL:er i API-svarets brödtext med URL:er för APIM-gatewayen
> -   Skydda ett API genom att lägga till en princip för frekvensbegränsningar (begränsning)
> -   Testa omvandlingarna

![Principer](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Förutsättningar

-   Lär dig [Azure API Management-terminologin](api-management-terminology.md).
-   Förstå [begreppet principer i Azure API Management](api-management-howto-policies.md).
-   Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
-   Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Omvandla ett API och ta bort svarshuvuden

Det här avsnittet visar hur du kan dölja HTTP-huvuden som du inte vill visa för användarna. Följande huvuden tas bort i HTTP-svaret i det här exemplet:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testa det ursprungliga svaret

Visa det ursprungliga svaret:

1. I din APIM-tjänstinstans väljer du **APIs** (API:er) under **API MANAGEMENT** (API-HANTERING).
2. Klicka på **Demo Conference API** i API-listan.
3. Klicka på fliken **Test** överst på skärmen.
4. Välj åtgärden **GetSpeakers**.
5. Klicka på knappen **Skicka** längst ned på skärmen.

Det ursprungliga svaret ska se ut så här:

![Principer](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Ange en transformationsprincip

![Ange utgående princip](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. Välj **Demokonferens-API**.
2. Överst på skärmen väljer du fliken **Design**.
3. Välj **alla åtgärder**.
4. I avsnittet **utgående bearbetning** klickar du på **</>** ikonen.
5. Placera markören inuti det ** &lt; utgående &gt; ** elementet.
6. Klicka på **+ Konfigurera HTTP-huvud** två gånger under **Transformationsprinciper** i det högra fönstret (så infogas två principkodavsnitt).

   ![Principer](./media/transform-api/transform-api.png)

7. Ändra **\<outbound>** koden så att den ser ut så här:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   ![Principer](./media/transform-api/set-policy.png)

8. Klicka på knappen **Spara**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Ersätt ursprungliga URL:er i API-svarets brödtext med URL:er för APIM-gatewayen

I det här avsnittet visas hur du kan dölja de ursprungliga URL:er som visas i brödtexten i API:ets HTTP-svar och istället omdirigera dem till APIM-gatewayen.

### <a name="test-the-original-response"></a>Testa det ursprungliga svaret

Visa det ursprungliga svaret:

1. Välj **Demokonferens-API**.
2. Klicka på fliken **Test** överst på skärmen.
3. Välj åtgärden **GetSpeakers**.
4. Klicka på knappen **Skicka** längst ned på skärmen.

    Som du kan se så ser det ursprungliga svaret ut så här:

    ![Principer](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Ange en transformationsprincip

1.  Välj **Demokonferens-API**.
2.  Välj **alla åtgärder**.
3.  Överst på skärmen väljer du fliken **Design**.
4.  I avsnittet **utgående bearbetning** klickar du på **</>** ikonen.
5.  Placera markören inuti det ** &lt; utgående &gt; ** elementet och klicka på knappen **Visa kodfragment** i det övre högra hörnet.
6.  I det högra fönstret, under **omvandlings principer**, klickar du på **maskera URL: er i innehåll**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Skydda ett API genom att lägga till en princip för frekvensbegränsningar (begränsning)

I det här avsnittet visas hur du lägger till skydd för ditt serverdels-API genom att konfigurera frekvensbegränsningar. Du vill kanske t.ex. begränsa det antal gånger som API:et anropas, så att det inte överutnyttjas av utvecklarna. I det här exemplet har gränsen angetts till 3 anrop per 15 sekunder för varje prenumerations-ID. Efter 15 sekunder kan en utvecklare försöka anropa API: et igen.

![Ange princip för inkommande](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1.  Välj **Demokonferens-API**.
2.  Välj **alla åtgärder**.
3.  Överst på skärmen väljer du fliken **Design**.
4.  I avsnittet **Inkommande bearbetning** klickar du på ikonen **</>**.
5.  Placera markören inuti det ** &lt; inkommande &gt; ** elementet.
6.  Klicka på **+ Begränsa anropsfrekvens per nyckel** under **Principer för åtkomstbegränsning** i det högra fönstret.
7.  Ändra din **hastighets begränsning efter nyckel** kod (i- **\<inbound\>** elementet) till följande kod:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testa omvandlingarna

Nu ser dina principer ut så här i kodredigeraren:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <find-and-replace from="://conferenceapi.azurewebsites.net:443" to="://apiphany.azure-api.net/conference"/>
        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

I resten av det här avsnittet testas principtransformationer som du anger i den här övningen.

### <a name="test-the-stripped-response-headers"></a>Testa borttagna svarshuvuden

1. Välj **Demokonferens-API**.
2. Välj fliken **Test**.
3. Klicka på åtgärden **GetSpeakers**.
4. Tryck på **Skicka**.

    Som du ser så har rubrikerna tagits bort:

    ![Principer](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Testa den ersatta URL:en

1. Välj **Demokonferens-API**.
2. Välj fliken **Test**.
3. Klicka på åtgärden **GetSpeakers**.
4. Tryck på **Skicka**.

    Som du ser har URL:en ersatts.

    ![Principer](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Testa frekvensgränsen (begränsningen)

1. Välj **Demokonferens-API**.
2. Välj fliken **Test**.
3. Klicka på åtgärden **GetSpeakers**.
4. Tryck på **Skicka** tre gånger i rad.

    När du har skickat förfrågan 3 gånger får du svaret **429 För många förfrågningar**.

5. Vänta 15 sekunder eller så och tryck på **Skicka** igen. Den här gången bör få svaret **200 OK**.

    ![Begränsning](./media/transform-api/test-throttling.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> -   Omvandla ett API och ta bort svarshuvuden
> -   Ersätt ursprungliga URL:er i API-svarets brödtext med URL:er för APIM-gatewayen
> -   Skydda ett API genom att lägga till en princip för frekvensbegränsningar (begränsning)
> -   Testa omvandlingarna

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Övervaka ditt API](api-management-howto-use-azure-monitor.md)
