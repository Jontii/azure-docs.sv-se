---
title: Hanterad identitet i Synapse-arbetsytan
description: En artikel som förklarar hanterad identitet i Azure dataSynapses-arbetsytan
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e3c2b2ce5bd9af60d6d1fd4ac066681ee8473521
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249458"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Azure Synapse-arbetsytan hanterad identitet (för hands version)

I den här artikeln får du lära dig om hanterad identitet i Azure dataSynapses-arbetsytan.

## <a name="managed-identities"></a>Hanterade identiteter

Hanterad identitet för Azure-resurser är en funktion i Azure Active Directory. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda funktionen för hanterad identitet för att autentisera till alla tjänster som stöder Azure AD-autentisering.

Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare kallades Hanterad tjänstidentitet (MSI). Mer information finns i [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

## <a name="azure-synapse-workspace-managed-identity"></a>Hanterad identitet för Azure Synapse-arbetsytan

En systemtilldelad hanterad identitet skapas för din Azure Synapse-arbetsyta när du skapar arbets ytan.

>[!NOTE]
>Den här arbets ytans hanterade identitet kallas hanterad identitet genom resten av det här dokumentet.

I Azure Synapse används den hanterade identiteten för att dirigera pipeliner. Den hanterade identitets livs cykeln är direkt knuten till Azure dataSynapses-arbetsytan. Om du tar bort Azure dataSynapses-arbetsytan rensas även den hanterade identiteten.

Arbets ytans hanterade identitet måste ha behörighet att utföra åtgärder i pipelinen. Du kan använda objekt-ID: t eller din Azure Synapse-arbetsyta för att hitta den hanterade identiteten när du beviljar behörigheter.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Hämta hanterad identitet i Azure Portal

Du kan hämta den hanterade identiteten i Azure Portal. Öppna din Azure Synapse-arbetsyta i Azure Portal och välj **Översikt** i det vänstra navigerings fältet. Objekt-ID: t för den hanterade identiteten visas på huvud skärmen.

![Objekt-ID för hanterad identitet](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

Den hanterade identitets informationen visas också när du skapar en länkad tjänst som stöder autentisering med hanterad identitet från Azure Synapse Studio.

Starta **Azure Synapse Studio** och välj fliken **Hantera** i det vänstra navigerings fältet. Välj sedan **länkade tjänster** och välj alternativet **+ ny** för att skapa en ny länkad tjänst.

![Skapa länkad tjänst 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

I fönstret **ny länkad tjänst** skriver du *Azure Data Lake Storage Gen2*. Välj resurs typen **Azure Data Lake Storage Gen2** i listan nedan och välj **Fortsätt**.

![Skapa länkad tjänst 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

I nästa fönster väljer du **hanterad identitet** för **autentiseringsmetod**. Du ser **namnet** och **objekt-ID: t**för den hanterade identiteten.

![Skapa länkad tjänst 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [du beviljar behörigheter till Azure DataSynapses-hanterad identitet](./how-to-grant-workspace-managed-identity-permissions.md)
