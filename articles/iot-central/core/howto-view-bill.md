---
title: Hantera din faktura och konvertera från den kostnads fria pris planen i Azure IoT Central-programmet | Microsoft Docs
description: Som administratör kan du lära dig hur du hanterar din faktura och flyttar från den kostnads fria pris Planen till en standard pris plan i Azure IoT Central-programmet
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 515f5f665e947ff9594cce6dbbaea9b5e0c50ebf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995751"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Hantera din faktura i ett IoT Central program

Den här artikeln beskriver hur du, som administratör, kan hantera Azure IoT Central fakturering. Du kan flytta ditt program från den kostnads fria pris Planen till en standard pris plan och även uppgradera eller nedgradera pris planen.

För att få åtkomst till avsnittet **Administration** måste du vara i *Administratörs* rollen eller ha en *anpassad användar roll* som gör att du kan visa fakturering. Om du skapar ett Azure IoT Central-program tilldelas du automatiskt rollen **administratör** .

## <a name="move-from-free-to-standard-pricing-plan"></a>Flytta från kostnads fri till standard pris plan

- Program som använder den kostnads fria pris Planen är kostnads fria i sju dagar innan de upphör att gälla. För att undvika att förlora data kan du när som helst flytta dem till en standard pris plan innan de upphör att gälla.
- Program som använder en standard pris plan debiteras per enhet, med de två första enheterna kostnads fria, per program.

Läs mer om prissättning på [sidan med prisinformation för Azure IoT Central.](https://azure.microsoft.com/pricing/details/iot-central/)

I avsnittet prissättning kan du flytta ditt program från kostnads fritt till en standard pris plan.

Följ dessa steg för att slutföra självbetjänings processen:

1. Gå till sidan med **priser** i avsnittet **Administration** .

    ![Utvärderings tillstånd](media/howto-view-bill/freetrialbilling.png)

1. Välj **konvertera till en betald plan**.

    ![Konvertera utvärderings version](media/howto-view-bill/convert.png)

1. Välj lämplig Azure Active Directory och sedan Azure-prenumerationen som ska användas för ditt program som använder en betald plan.

1. När du har valt **konvertera** använder programmet nu en betald plan och du börjar debiteras.

> [!Note]
> Som standard konverteras du till en pris sättnings plan med *Standard 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Ändra din program pris plan

Program som använder en standard pris plan debiteras per enhet, med de två första enheterna kostnads fria, per program.

I avsnittet prissättning kan du när som helst uppgradera eller nedgradera din Azure IoT-pris plan.

1. Gå till sidan med **priser** i avsnittet **Administration** .

    ![Uppgradera prcing-plan](media/howto-view-bill/pricing.png)

1. Välj **planen** och välj sedan **Spara** för att uppgradera eller nedgradera.

## <a name="view-your-bill"></a>Visa din faktura

1. Välj lämplig Azure Active Directory och sedan Azure-prenumerationen som ska användas för ditt program som använder en betald plan.

1. När du har valt **konvertera** använder programmet nu en betald plan och du börjar debiteras.

> [!Note]
> Som standard konverteras du till en pris sättnings plan med *Standard 2* .

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar din faktura i Azure IoT Central-programmet är det föreslagna nästa steg att lära dig mer om hur du [anpassar program gränssnittet](howto-customize-ui.md) i Azure IoT Central.