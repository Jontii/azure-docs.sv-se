---
title: inkludera fil
description: inkludera fil
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009815"
---
> [!NOTE]
> En webbapp kan ta lång tid efter 20 minuters inaktivitet. och endast förfrågningar till den faktiska webb programmet kan återställa timern. Om du visar appens konfiguration i Azure Portal eller gör förfrågningar till webbplatsen för avancerade verktyg ( `https://<app_name>.scm.azurewebsites.net` ) återställs inte timern. Om du ställer in att webbappen ska köra kontinuerliga eller schemalagda WebJobs (timer-trigger) aktiverar du inställningen **Always on** på webbappens Azure- **konfiguration** för att säkerställa att webbjobben körs på ett tillförlitligt sätt. Den här funktionen är endast tillgänglig på [pris nivåerna](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Basic, standard och Premium.
