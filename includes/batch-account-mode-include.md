---
title: inkludera fil
description: inkludera fil
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 51b687dc2a6264eb12362616429746c9b182c3b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "68323898"
---
> [!NOTE]
> När du skapar ett Batch-konto kan du välja mellan två *poolallokeringslägen*: **användarprenumeration** och **Batch-tjänst**. I de flesta fall bör du använda standardläget Batch-tjänst. I det här läget allokeras pooler i bakgrunden i Azure-hanterade prenumerationer. I det alternativa användarprenumerationsläget skapas virtuella Batch-datorer och andra resurser direkt i din prenumeration när en pool skapas. Användarprenumerationsläge krävs om du vill skapa Batch-pooler med [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/). Du måste registrera din prenumeration med Azure Batch och koppla kontot till ett Azure Key Vault för att kunna skapa ett Batch-konto i läget Användarprenumeration.