---
title: Translator-kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Med Microsoft kan du hantera dina Cognitive Services prenumerationer med dina egna nycklar, som kallas Kundhanterade nycklar (CMK). Den här artikeln beskriver data kryptering i vila för Translator och hur du aktiverar och hanterar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ce7ff6ae134835de23a0d2670e8b4f44783654f8
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079208"
---
# <a name="translator-encryption-of-data-at-rest"></a>Translator-kryptering av data i vila

Translator krypterar automatiskt dina data, som du laddar upp för att bygga anpassade översättnings modeller, när de sparas i molnet, vilket hjälper till att uppfylla organisationens säkerhets-och efterlevnads mål.

## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services kryptering

Data krypteras och dekrypteras med hjälp av [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-kompatibla 256-bitars AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kryptering. Kryptering och dekryptering är transparent, vilket innebär att kryptering och åtkomst hanteras åt dig. Dina data skyddas som standard och du behöver inte ändra din kod eller dina program för att utnyttja krypteringen.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Som standard använder din prenumeration krypteringsnycklar som hanteras av Microsoft. Om du använder en pris nivå som stöder Kundhanterade nycklar kan du se krypterings inställningarna för resursen i avsnittet **kryptering** i [Azure Portal](https://portal.azure.com), som du ser i följande bild.

![Visa krypterings inställningar](../media/cognitive-services-encryption/encryptionblade.png)

För prenumerationer som bara stöder Microsoft-hanterade krypterings nycklar har du inget **krypterings** avsnitt.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Som standard använder din prenumeration krypteringsnycklar som hanteras av Microsoft. Det finns också möjlighet att hantera din prenumeration med dina egna nycklar som kallas Kundhanterade nycklar (CMK). CMK erbjuder större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data. Om CMK har kon figurer ATS för din prenumeration tillhandahålls dubbla kryptering, vilket ger ett sekundärt skydds lager, samtidigt som du kan kontrol lera krypterings nyckeln via din Azure Key Vault.

> [!IMPORTANT]
> Kundhanterade nycklar är tillgängliga för alla pris nivåer för tjänsten Translator. Om du vill begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar [översättnings formuläret för den kund hanterade nyckeln](https://aka.ms/cogsvc-cmk) som det tar cirka 3-5 arbets dagar att ta del av statusen för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med tjänsten Translator måste du skapa en ny översättare-resurs. När du har skapat din Translator-resurs kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

Följ dessa steg om du vill aktivera Kundhanterade nycklar för översättare:

1. Skapa din nya regionala Translator-eller regional Cognitive Services-resurs. Detta kommer inte att fungera med en global resurs.
2. Aktive rad hanterad identitet i Azure Portal och Lägg till din Kundhanterade viktig information.
3. Skapa en ny arbets yta i en anpassad översättare och associera den här prenumerations informationen.

### <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar

Du måste använda Azure Key Vault för att lagra dina Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Den Cognitive Services resursen och nyckel valvet måste finnas i samma region och i samma Azure Active Directory-klient (Azure AD), men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

En ny Cognitive Services resurs krypteras alltid med Microsoft-hanterade nycklar. Det går inte att aktivera Kundhanterade nycklar vid den tidpunkt då resursen skapas. Kundhanterade nycklar lagras i Azure Key Vault och nyckel valvet måste tillhandahållas med åtkomst principer som ger nyckel behörigheter till den hanterade identitet som är kopplad till den Cognitive Services resursen. Den hanterade identiteten är tillgänglig så snart resursen har skapats.

Information om hur du använder Kundhanterade nycklar med Azure Key Vault för Cognitive Services kryptering finns i:

- [Konfigurera Kundhanterade nycklar med Key Vault för Cognitive Services kryptering från Azure Portal](../Encryption/cognitive-services-encryption-keys-portal.md)

Om du aktiverar Kundhanterade nycklar aktive ras även en systemtilldelad hanterad identitet, en funktion i Azure AD. När systemtilldelad hanterad identitet har Aktiver ATS registreras den här resursen med Azure Active Directory. När den hanterade identiteten har registrerats får den åtkomst till den Key Vault som valts under kund hanterade nyckel inställningar. Du kan läsa mer om [hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter tas åtkomst till nyckel valvet bort och alla data som krypteras med kund nycklarna kommer inte längre att vara tillgängliga. Alla funktioner som är beroende av dessa data upphör att fungera. Alla modeller som du har distribuerat kommer också att avdistribueras. Alla överförda data tas bort från den anpassade översättningen. Om de hanterade identiteterna återaktiveras omdistribueras inte modellen åt dig automatiskt.

> [!IMPORTANT]
> Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger. När du konfigurerar Kundhanterade nycklar i Azure Portal tilldelas en hanterad identitet automatiskt under försättsblad. Om du senare flyttar prenumerationen, resurs gruppen eller resursen från en Azure AD-katalog till en annan överförs inte den hanterade identitet som är kopplad till resursen till den nya klienten, så Kundhanterade nycklar kanske inte längre fungerar. Mer information finns i **överföra en prenumeration mellan Azure AD-kataloger** i [vanliga frågor och svar och kända problem med hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra Kundhanterade nycklar i Azure Key Vault

Om du vill aktivera Kundhanterade nycklar måste du använda en Azure Key Vault för att lagra dina nycklar. Du måste aktivera både den **mjuka borttagningen** och **Rensa inte** egenskaperna i nyckel valvet.

Endast RSA-nycklar med storleken 2048 stöds med Cognitive Services kryptering. Mer information om nycklar finns **Key Vault nycklar** i [om Azure Key Vault nycklar, hemligheter och certifikat](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Om hela nyckel valvet tas bort kommer dina data inte längre att visas och alla modeller kommer att tas bort. Alla överförda data tas bort från den anpassade översättningen. 

### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till Kundhanterade nycklar

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) eller [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Genom att återkalla åtkomsten på ett effektivt sätt blockeras åtkomsten till alla data i Cognitive Services-resursen och dina modeller kommer inte att distribueras eftersom krypterings nyckeln inte är tillgänglig från Cognitive Services. Alla överförda data tas också bort från den anpassade översättningen.


## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
