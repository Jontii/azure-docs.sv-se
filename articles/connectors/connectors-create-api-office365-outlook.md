---
title: Anslut till Office 365 Outlook
description: Automatisera uppgifter och arbets flöden som hanterar e-post, kontakter och kalendrar i Office 365 Outlook med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 07/27/2020
tags: connectors
ms.openlocfilehash: 9b10778e665675e9e033953e2a8b9df16dd636d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400782"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Hantera e-post, kontakter och kalendrar i Office 365 Outlook med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Office 365 Outlook Connector](/connectors/office365connector/)kan du skapa automatiserade uppgifter och arbets flöden som hanterar ditt arbets-eller skol konto genom att skapa Logi Kap par. Du kan till exempel automatisera dessa uppgifter:

* Hämta, skicka och svara på e-post. 
* Schemalägg möten i din kalender.
* Lägg till och redigera kontakter. 

Du kan använda en utlösare för att starta arbets flödet, till exempel när ett nytt e-postmeddelande tas emot, när ett Kalender objekt uppdateras eller när en händelse inträffar i en skillnads tjänst, till exempel Salesforce. Du kan använda åtgärder som svarar på utlösnings händelsen, till exempel skicka ett e-postmeddelande eller skapa en ny kalender händelse. 

> [!NOTE]
> Om du vill automatisera uppgifter för ett- @outlook.com eller @hotmail.com -konto använder du [Outlook.com-anslutningen](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

* Ett [arbets-eller skol konto](https://www.office.com/)

* Den Logic app där du vill komma åt ditt arbets-eller skol konto. För att starta arbets flödet med en Office 365 Outlook-utlösare måste du ha en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). För att lägga till en Office 365 Outlook-åtgärd i ditt arbets flöde måste din Logic app redan ha en utlösare.

## <a name="add-a-trigger"></a>Lägga till en utlösare

En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en händelse som startar arbets flödet i din Logic app. I den här exempel Logic-appen används en "avsöknings utlösare" som söker efter uppdaterade Kalender händelser i ditt e-postkonto baserat på angivet intervall och frekvens.

1. I [Azure Portal](https://portal.azure.com)öppnar du din tomma Logic-app i Logic App Designer.

1. I rutan Sök anger `office 365 outlook` du som filter. Det här exemplet väljer **när en kommande händelse börjar snart**.
   
   ![Välj utlösare för att starta din Logic app](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Om du uppmanas att logga in anger du dina autentiseringsuppgifter för arbetet eller skolan så att din Logic app kan ansluta till ditt konto. Annars, om anslutningen redan finns, anger du informationen för utlösarens egenskaper.

   > [!NOTE]
   > Din anslutning upphör inte förrän den har återkallats, även om du ändrar dina inloggnings uppgifter. Mer information finns i [konfigurations bara livs längder för token i Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   I det här exemplet väljs den kalender som utlösaren kontrollerar, till exempel:

   ![Konfigurera utlösarens egenskaper](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Ange **frekvens** -och **intervall** värden i utlösaren. Om du vill lägga till andra tillgängliga Utlösar egenskaper, t. ex. **tidszon**, väljer du dessa egenskaper i listan **Lägg till ny parameter** .

   Om du till exempel vill att utlösaren ska kontrol lera kalendern var 15: e minut, ange **frekvens** till **minut**och ange **intervall** till `15` . 

   ![Ange frekvens och intervall för utlösaren](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. I verktygsfältet designer väljer du **Spara**.

Lägg nu till en åtgärd som körs när utlösaren utlöses. Du kan till exempel lägga till åtgärden Twilio **Skicka meddelande** , som skickar en text när en kalender händelse startar om 15 minuter.

## <a name="add-an-action"></a>Lägga till en åtgärd

En [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en åtgärd som körs av arbets flödet i din Logic app. Den här exempel på Logic-appen skapar en ny kontakt i Office 365 Outlook. Du kan använda utdata från en annan utlösare eller åtgärd för att skapa kontakten. Anta till exempel att din Logic app använder Dynamics 365-utlösaren **när en post skapas**. Du kan lägga till Office 365 Outlook-åtgärden för att **skapa kontakter** och använda utdata från Salesforce-utlösaren för att skapa den nya kontakten.

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Om du vill lägga till en åtgärd som sista steg i arbets flödet väljer du **nytt steg**. 

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger `office 365 outlook` du som filter. I det här exemplet väljer du **skapa kontakt**.

   ![Välj den åtgärd som ska köras i din Logic app](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Om du uppmanas att logga in anger du dina autentiseringsuppgifter för arbetet eller skolan så att din Logic app kan ansluta till ditt konto. Annars, om anslutningen redan finns, anger du informationen för åtgärdens egenskaper.

   > [!NOTE]
   > Din anslutning upphör inte förrän den har återkallats, även om du ändrar dina inloggnings uppgifter. Mer information finns i [konfigurations bara livs längder för token i Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Det här exemplet väljer mappen kontakter där åtgärden skapar den nya kontakten, till exempel:

   ![Konfigurera åtgärdens egenskaper](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Om du vill lägga till andra tillgängliga åtgärds egenskaper väljer du dessa egenskaper i listan **Lägg till ny parameter** .

1. I verktygsfältet designer väljer du **Spara**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om den här anslutningen, till exempel utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/office365/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)