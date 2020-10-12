---
title: Ta bort roll tilldelningar från en grupp i Azure Active Directory | Microsoft Docs
description: Förhandsgranska anpassade Azure AD-roller för att delegera identitets hantering. Hantera Azure-roller i Azure Portal, PowerShell eller Graph API.
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
ms.openlocfilehash: 15312159bc0487f7d03c06fa947f69b1f6f9600c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513408"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Ta bort roll tilldelningar från en grupp i Azure Active Directory

Den här artikeln beskriver hur en IT-administratör kan ta bort Azure AD-roller tilldelade till grupper. I Azure Portal kan du nu ta bort både direkta och indirekta roll tilldelningar för en användare. Om en användare har tilldelats en roll av ett grupp medlemskap, tar du bort användaren från gruppen för att ta bort roll tilldelningen.

## <a name="using-azure-admin-center"></a>Använda Azure administrations Center

1. Logga in på [administrations centret för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.

1. Välj **roller och administratörer**  >  ***roll namn***.

1. Välj den grupp som du vill ta bort roll tilldelningen från och välj **ta bort tilldelning**.

   ![Ta bort en roll tilldelning från en vald grupp.](./media/roles-groups-remove-assignment/remove-assignment.png)

1. Välj **Ja**när du uppmanas att bekräfta åtgärden.

## <a name="using-powershell"></a>Använda PowerShell

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Skapa en grupp som kan tilldelas rollen

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Hämta den roll definition som du vill tilldela gruppen

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Skapa en roll tilldelning

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>Ta bort roll tilldelningen

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Använda Microsoft Graph API

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Skapa en grupp som kan tilldelas en Azure AD-roll

```powershell
POST https://graph.microsoft.com/beta/groups

{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
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

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>Skapa roll tilldelningen

```powershell
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<Id of role definition>",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Ta bort roll tilldelning

```powershell
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/<Id of role assignment>
```

## <a name="next-steps"></a>Nästa steg

- [Använd moln grupper för att hantera roll tilldelningar](roles-groups-concept.md)
- [Felsökningsroller tilldelade till molngrupper](roles-groups-faq-troubleshooting.md)
