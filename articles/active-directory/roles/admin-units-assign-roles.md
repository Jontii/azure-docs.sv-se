---
title: Tilldela och lista roller med den administrativa enhetens omfattning – Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för att begränsa omfånget för roll tilldelningar i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfae813f01d3e7a08e18cde76e5c26ca253a371f
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026606"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Tilldela begränsade roller till en administrativ enhet

I Azure Active Directory (Azure AD), för mer detaljerad administrativ kontroll, kan du tilldela användare till en Azure AD-roll med en omfattning som är begränsad till en eller flera administrativa enheter.

Information om hur du förbereder för att använda PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Kom igång](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Tillgängliga roller

Roll  |  Beskrivning
----- |  -----------
Administratör för autentisering  |  Har åtkomst att visa, ange och återställa information om autentiseringsinformation för användare som inte är administratörer i den tilldelade administrativa enheten.
Grupp administratör  |  Kan hantera alla aspekter av grupper och grupp inställningar, till exempel namn och förfallo principer, i den tilldelade administrativa enheten.
Support administratör  |  Kan återställa lösen ord för administratörer som inte har administratörer och supportavdelningen i den tilldelade administrativa enheten.
Licens administratör  |  Kan endast tilldela, ta bort och uppdatera licens tilldelningar inom den administrativa enheten.
Lösen ords administratör  |  Kan återställa lösen ord för icke-administratörer och lösen ords administratörer inom den tilldelade administrativa enheten.
Användaradministratör  |  Kan hantera alla aspekter av användare och grupper, inklusive att återställa lösen ord för begränsade administratörer inom den tilldelade administrativa enheten.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Säkerhets objekt som kan tilldelas en omfattnings roll

Följande säkerhets objekt kan tilldelas en roll med en administrativ enhets omfattning:

* Användare
* Roll tilldelnings bara moln grupper (förhands granskning)
* Tjänstens huvudnamn (SPN)

## <a name="assign-a-scoped-role"></a>Tilldela en omfattnings roll

Du kan tilldela en omfattnings roll med hjälp av Azure Portal, PowerShell eller Microsoft Graph.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal går du till **Azure AD** .

1. Välj **administrativa enheter** och välj sedan den administrativa enhet som du vill tilldela användar roll omfånget till. 

1. I den vänstra rutan väljer du **roller och administratörer** för att visa en lista över alla tillgängliga roller.

   ![Skärm bild av fönstret "roll och administratörer" där du kan välja en administrativ enhet vars roll omfång du vill tilldela.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Välj den roll som ska tilldelas och välj sedan **Lägg till tilldelningar** . 

1. I fönstret **Lägg till tilldelningar** väljer du en eller flera användare som ska tilldelas rollen.

   ![Välj rollen som definitions område och välj sedan Lägg till tilldelningar](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Information om hur du tilldelar en roll på en administrativ enhet genom att använda Azure AD Privileged Identity Management (PIM) finns i [tilldela Azure AD-roller i PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Använd PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

Du kan ändra det markerade avsnittet efter behov för den aktuella miljön.

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Visa en lista över omfångs administratörer i en administrativ enhet

Du kan visa en lista över omfångs administratörer med hjälp av Azure Portal, PowerShell eller Microsoft Graph.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Du kan visa alla roll tilldelningar som skapats med en administrativ enhets omfattning i [avsnittet administrativa enheter i Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. I Azure Portal går du till **Azure AD** .

1. I det vänstra fönstret väljer du **administrativa enheter** och väljer sedan den administrativa enheten för listan med roll tilldelningar som du vill visa. 

1. Välj **roller och administratörer** och öppna sedan en roll för att Visa tilldelningarna i den administrativa enheten.

### <a name="use-powershell"></a>Använd PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

Du kan ändra det markerade avsnittet efter behov för din speciella miljö.

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Nästa steg

- [Använda molngrupper för att hantera rolltilldelningar](groups-concept.md)
- [Felsöka roller tilldelade till moln grupper](groups-faq-troubleshooting.md)
