---
title: Vanliga frågor och svar med hanterade identiteter – Azure AD
description: Kända problem med hanterade identiteter för Azure-resurser.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 08/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 4bcd36a1ce38d4d9eb6a0faec470f7427852894b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260228"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Vanliga frågor och svar med hanterade identiteter för Azure-resurser

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).


### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hur kan du hitta resurser som har en hanterad identitet?

Du kan hitta listan över resurser som har en systemtilldelad hanterad identitet med hjälp av följande Azure CLI-kommando: 

`az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table`




### <a name="do-managed-identities-have-a-backing-app-object"></a>Har Managed identiteter ett säkerhetskopierat app-objekt?

Nej. Hanterade identiteter och Azure AD App registreringar är inte samma sak i katalogen. 

Appregistreringar har två komponenter: ett program objekt och ett huvud objekt för tjänsten. Hanterade identiteter för Azure-resurser har bara en av dessa komponenter: ett huvud objekt för tjänsten. 

Hanterade identiteter har inget program objekt i katalogen, vilket är det som ofta används för att bevilja app-behörigheter för MS Graph. I stället måste MS Graph-behörigheter för hanterade identiteter beviljas direkt till tjänstens huvud namn.  

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Fungerar hanterade identiteter för Azure-resurser med Azure Cloud Services?

Nej, det finns inga planer på att stödja hanterade identiteter för Azure-resurser i Azure Cloud Services.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Vad är den autentiseringsuppgift som är associerad med en hanterad identitet? Hur länge är det giltigt och hur ofta det roteras?

> [!NOTE]
> Hur hanterade identiteter autentiserar är en intern implementerings information som kan ändras utan föregående meddelande.

Hanterade identiteter använder certifikatbaserad autentisering. Varje hanterad identitets autentiseringsuppgift har ett förfallo datum på 90 dagar och den har registrerats efter 45 dagar.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Vad är säkerhets gränserna för hanterade identiteter för Azure-resurser?

Säkerhets gränserna för identiteten är den resurs som den är kopplad till. Till exempel är säkerhets gränserna för en virtuell dator med hanterade identiteter för Azure-resurser aktiverade den virtuella datorn. All kod som körs på den virtuella datorn kan anropa de hanterade identiteterna för Azure-resursernas slut punkt och begära token. Det är samma erfarenhet av andra resurser som har stöd för hanterade identiteter för Azure-resurser.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Vilken identitet kommer att IMDS standardvärdet om du inte anger identiteten i begäran?

- Om systemtilldelad hanterad identitet är aktive rad och ingen identitet anges i begäran, kommer IMDS att använda den hanterade identiteten som standard.
- Om systemtilldelad hanterad identitet inte är aktive rad och det bara finns en tilldelad hanterad identitet, kommer IMDS att använda den enskilda användaren som tilldelats den hanterade identiteten. 
- Om systemtilldelad hanterad identitet inte är aktive rad och det finns flera tilldelade hanterade identiteter, måste du ange en hanterad identitet i begäran.



### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Kommer Managed identiteter att återskapas automatiskt om jag flyttar en prenumeration till en annan katalog?

Nej. Om du flyttar en prenumeration till en annan katalog måste du återskapa dem manuellt och ge Azure-roll tilldelningar igen.
- För systemtilldelade hanterade identiteter: inaktivera och återaktivera. 
- För användare som tilldelats hanterade identiteter: ta bort, återskapa och koppla dem igen till nödvändiga resurser (t. ex. virtuella datorer)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Kan jag använda en hanterad identitet för att få åtkomst till en resurs i en annan katalog/klient organisation?

Nej. Hanterade identiteter stöder för närvarande inte scenarier mellan kataloger. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Vilka Azure RBAC-behörigheter krävs för hanterade identiteter på en resurs? 

- Systemtilldelad hanterad identitet: du behöver Skriv behörighet över resursen. För virtuella datorer behöver du till exempel Microsoft.Compute/virtualMachines/write. Den här åtgärden ingår i de resursbaserade inbyggda rollerna som [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Användardefinierad hanterad identitet: du behöver Skriv behörighet över resursen. För virtuella datorer behöver du till exempel Microsoft.Compute/virtualMachines/write. Förutom roll tilldelningen [hanterad identitets operatör](../../role-based-access-control/built-in-roles.md#managed-identity-operator) över den hanterade identiteten.



## <a name="known-issues"></a>Kända problem

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automation-skript" Miss lyckas vid försök att exportera schema för hanterade identiteter för Azure-resursers tillägg

När hanterade identiteter för Azure-resurser är aktiverade på en virtuell dator visas följande fel vid försök att använda funktionen "Automation-skript" för den virtuella datorn eller resurs gruppen:

![Hanterade identiteter för Azure-resurser Automation skript export fel](./media/msi-known-issues/automation-script-export-error.png)

Hanterade identiteter för VM-tillägget för Azure-resurser (planerat för utfasning i januari 2019) stöder för närvarande inte möjligheten att exportera sitt schema till en mall för en resurs grupp. Det innebär att den genererade mallen inte visar konfigurations parametrar för att aktivera hanterade identiteter för Azure-resurser på resursen. Dessa avsnitt kan läggas till manuellt genom att följa exemplen i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av mallar](qs-configure-template-windows-vm.md).

När schema export funktionen blir tillgänglig för hanterade identiteter för VM-tillägget för Azure-resurser (planerat för utfasning i januari 2019) visas den i [Exportera resurs grupper som innehåller VM-tillägg](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Den virtuella datorn kan inte startas efter att ha flyttats från resurs gruppen eller prenumerationen

Om du flyttar en virtuell dator i körnings läge fortsätter den att köras under flytten. Om den virtuella datorn stoppas och startas om när den har flyttats, kommer den dock inte att starta. Det här problemet uppstår eftersom den virtuella datorn inte uppdaterar referensen till de hanterade identiteterna för Azure-resursers identitet och fortsätter att peka på den i den gamla resurs gruppen.

**Lösning** 
 
Utlös en uppdatering på den virtuella datorn så att den kan hämta korrekta värden för hanterade identiteter för Azure-resurser. Du kan göra en ändring av en VM-egenskap för att uppdatera referensen till de hanterade identiteterna för Azures resurs identitet. Du kan till exempel ange ett nytt tagg värde på den virtuella datorn med följande kommando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Det här kommandot anger en ny tagg "fixVM" med värdet 1 på den virtuella datorn. 
 
Genom att ange den här egenskapen uppdateras VM med rätt hanterade identiteter för resurs-URI för Azure-resurser och sedan bör du kunna starta den virtuella datorn. 
 
När den virtuella datorn har startats kan taggen tas bort med hjälp av följande kommando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```



### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Överföra en prenumeration mellan Azure AD-kataloger

Hanterade identiteter uppdateras inte när en prenumeration flyttas/överförs till en annan katalog. Det innebär att alla befintliga systemtilldelade eller användarspecifika hanterade identiteter bryts. 

Lösning för hanterade identiteter i en prenumeration som har flyttats till en annan katalog:

 - För systemtilldelade hanterade identiteter: inaktivera och återaktivera. 
 - För användare som tilldelats hanterade identiteter: ta bort, återskapa och koppla dem igen till nödvändiga resurser (t. ex. virtuella datorer)

Mer information finns i [Överföra en Azure-prenumeration till en annan Azure AD-katalog (förhandsversion)](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Flytta en användardefinierad hanterad identitet till en annan resurs grupp/prenumeration

Det finns inte stöd för att flytta en användardefinierad hanterad identitet till en annan resurs grupp.