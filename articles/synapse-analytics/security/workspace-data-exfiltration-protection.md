---
title: Data exfiltrering Protection för Azure Synapse Analytics-arbetsytor
description: Den här artikeln förklarar data exfiltrering Protection i Azure Synapse Analytics
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 71210cdcc2b3758a59a1b41816e6468556e94808
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518268"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Data exfiltrering Protection för Azure Synapse Analytics-arbetsytor
Den här artikeln förklarar data exfiltrering Protection i Azure Synapse Analytics

## <a name="securing-data-egress-from-synapse-workspaces"></a>Skydda utgående data från Synapse-arbetsytor
Azure Synapse Analytics-arbetsytor stöder aktivering av data exfiltrering-skydd för arbets ytor. Med exfiltrering Protection kan du skydda dina Azure-resurser och organisationers känsliga data till platser utanför organisationens omfattning. När arbets ytan skapas kan du välja att konfigurera arbets ytan med ett hanterat virtuellt nätverk och ytterligare skydd mot data exfiltrering. När en arbets yta skapas med ett [hanterat virtuellt nätverk](./synapse-workspace-managed-vnet.md)distribueras data integrering och Spark-resurser i det hanterade virtuella nätverket. Arbets ytans dedikerade SQL-pooler och SQL-pooler utan server har funktioner för flera klienter och därför måste finnas utanför det hanterade virtuella nätverket. För arbets ytor med data exfiltrering-skydd kommunicerar resurser i det hanterade virtuella nätverket alltid över [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md) och SYNAPSE-SQL-resurserna kan bara ansluta till auktoriserade Azure-resurser (mål för godkända hanterade privata slut punkts anslutningar från arbets ytan). 

>[!Note]
>Du kan inte ändra konfigurationen för arbets ytan för hanterat virtuellt nätverk och data exfiltrering-skydd när arbets ytan har skapats.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Hantera Synapse-arbetsytans data utgående till godkända mål
När arbets ytan har skapats med data exfiltrering har Aktiver ATS kan ägarna till arbets ytans resurs hantera listan över godkända Azure AD-klienter för arbets ytan. Användare med [rätt behörigheter](./synapse-workspace-access-control-overview.md) på arbets ytan kan använda Synapse Studio för att skapa hanterade anslutnings begär Anden för privata slut punkter till resurser i arbets ytans godkända Azure AD-klienter. Skapande av hanterad privat slut punkt blockeras om användaren försöker skapa en privat slut punkts anslutning till en resurs i en klient som inte är godkänd.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Exempel arbets yta med data exfiltrering Protection aktiverat
Låt oss använda ett exempel för att illustrera data exfiltrering Protection för Synapse-arbetsytor. Contoso har Azure-resurser i klient organisationen A och klient B och det finns behov av att dessa resurser ansluter säkert. En Synapse-arbetsyta har skapats i klient organisationen A med klient B tillagt som en godkänd Azure AD-klient. Diagrammet visar privata slut punkts anslutningar till Azure Storage konton i klient organisationen A och klient B som har godkänts av lagrings kontots ägare. Diagrammet visar också blockerad skapande av privat slut punkt. Skapandet av den här privata slut punkten blockerades eftersom det riktade sig mot ett Azure Storage konto i den Fabrikam Azure AD-klienten, som inte är en godkänd Azure AD-klient för Contosos arbets yta. 
:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Det här diagrammet visar hur data exfiltrering Protection implementeras för Synapse-arbetsytor":::

>[!IMPORTANT]
>Resurser i andra klienter än arbets ytans klient organisation måste inte ha blockerade brand Väggs regler på plats för att SQL-poolerna ska kunna ansluta till dem. Resurser inom arbets ytans hanterade virtuella nätverk, till exempel Spark-kluster, kan ansluta via hanterade privata länkar till brand Väggs skyddade resurser.
## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en arbets yta med data exfiltrering Protection aktiverat](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

Läs mer om [hanterad arbets yta Virtual Network](./synapse-workspace-managed-vnet.md)

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)

[Skapa hanterade privata slut punkter till dina data källor](./how-to-create-managed-private-endpoints.md)
