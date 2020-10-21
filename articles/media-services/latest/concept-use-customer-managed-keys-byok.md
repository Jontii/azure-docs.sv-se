---
title: Ta med din egen nyckel (kund hanterade nycklar) med Media Services
description: Du kan använda en kund hanterad nyckel (det vill säga ta med din egen nyckel) med Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: a89ff56eb9e0f0a29b5b1fed7543c5f718425e51
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326040"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Ta med din egen nyckel (Kundhanterade nycklar) med Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) är ett Azure wide-initiativ som hjälper kunderna att flytta sina arbets belastningar till molnet. Med kundens hanterade nycklar kan kunder följa regler för efterlevnad av efterlevnad och förbättra klient isoleringen av en tjänst. Att ge kunderna kontroll över krypterings nycklar är ett sätt att minimera onödig åtkomst och kontroll och bygga tillförlitlighet i Microsoft-tjänster.

## <a name="keys-and-key-management"></a>Nycklar och nyckel hantering

Du kan använda din egen nyckel med Media Services när du använder API: et för Media Services 2020-05-01. En standard konto nyckel skapas för alla konton som är krypterade med en system nyckel som ägs av Media Services. När du använder din egen nyckel krypteras konto nyckeln med din nyckel. Innehålls nycklar krypteras av konto nyckeln. JobInputHttp-URL: er och verifierings nycklar för symmetriska token krypteras också.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="En kund hanterad nyckel ersätter en hanterad nyckel":::

Media Services använder den hanterade identiteten för Media Services kontot för att läsa din nyckel från en Key Vault som ägs av dig. Media Services kräver att Key Vault finns i samma region som kontot och att det har aktiverat skydd mot borttagning och rensning.

Din nyckel kan vara en 2048-, 3072-eller en 4096 RSA-nyckel och både HSM-och program varu nycklar stöds.

> [!NOTE]
> Det finns inte stöd för EG-nycklar.

Du kan ange ett nyckel namn och en nyckel version, eller bara ett nyckel namn. Om du bara använder ett nyckel namn kommer Media Services att använda den senaste nyckel versionen. Nya versioner av kund nycklar identifieras automatiskt och konto nyckeln krypteras igen.

> [!WARNING]
> Media Services övervakar åtkomsten till kund nyckeln. Om kund nyckeln blir otillgänglig (t. ex. om nyckeln har tagits bort eller om Key Vault har tagits bort eller om åtkomst tilldelningen har tagits bort), kommer Media Services att överföra kontot till status för kund nyckel otillgängligt (inaktive ring av kontot). Kontot kan dock tas bort i det här läget. De enda åtgärder som stöds är kontona GET, LIST och DELETE. alla andra begär Anden (kodning, direkt uppspelning och så vidare) kommer inte att fungera förrän åtkomst till konto nyckeln har återställts.

## <a name="tutorial"></a>Självstudier
Prova själv studie kursen [Använd Kundhanterade nycklar eller ta med din egen nyckel (BYOK) med Media Services](tutorial-byok.md) för att lära dig hur du konfigurerar och använder Kundhanterade nycklar med Postman och Azure REST API.

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Använd kundens hanterade nycklar eller ta med din egen nyckel (BYOK) med Media Services](tutorial-byok.md)
