---
title: Tilldela en roll till en moln grupp i Azure Active Directory | Microsoft Docs
description: Tilldela en Azure AD-roll till en roll tilldelnings grupp i Azure Portal, PowerShell eller Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28df0e420183239eae21aa18c807bb82b4ec7649
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377677"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Tilldela en roll till en moln grupp i Azure Active Directory

I det här avsnittet beskrivs hur en IT-administratör kan tilldela Azure Active Directory (Azure AD) rollen till en Azure AD-grupp.

## <a name="using-azure-ad-admin-center"></a>Använda Azure AD administrations Center

Att tilldela en grupp till en Azure AD-roll liknar att tilldela användare och tjänstens huvud namn förutom att endast grupper som kan tilldelas roller kan användas. I Azure Portal visas endast grupper som är roll tilldelnings bara.

1. Logga in på [administrations centret för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.

1. Välj **Azure Active Directory**  >  **roller och administratörer**och välj den roll som du vill tilldela.

1. På sidan ** _roll namn_*_ väljer du > _* Lägg till tilldelning**.

   ![Lägg till den nya roll tilldelningen](./media/groups-assign-role/add-assignment.png)

1. Välj gruppen. Endast de grupper som kan tilldelas till Azure AD-roller visas.

    [![Endast grupper som kan tilldelas en ny roll tilldelning visas.](./media/groups-assign-role/eligible-groups.png "Endast grupper som kan tilldelas en ny roll tilldelning visas.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. Välj **Lägg till**.

Mer information om hur du tilldelar roll behörigheter finns i [tilldela roller administratörer och icke-administratörer till användare](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="using-powershell"></a>Använda PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Skapa en grupp som kan tilldelas rollen

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>Hämta roll definitionen för den roll som du vill tilldela

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Skapa en roll tilldelning

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Använda Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Skapa en grupp som kan tilldelas Azure AD-roll

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>Hämta roll definitionen

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Skapa roll tilldelningen

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Nästa steg

- [Använd moln grupper för att hantera roll tilldelningar](groups-concept.md)
- [Felsökningsroller tilldelade till molngrupper](groups-faq-troubleshooting.md)
