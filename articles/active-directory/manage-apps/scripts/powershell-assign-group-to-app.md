---
title: PowerShell-exempel – tilldela grupp till en Application Proxy-app
description: PowerShell-exempel som tilldelar en grupp till ett Azure Active Directory (Azure AD) Application Proxy-program.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 21dfb5794513ac3d811e34f64d00ecdba8db9de8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659207"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>Tilldela en grupp till ett enskilt Azure AD-programproxy-program

Med det här PowerShell-skript exemplet kan du tilldela en specifik grupp till ett Azure Active Directory (Azure AD) Application Proxy-program.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Det här exemplet kräver [AzureAD v2 PowerShell för Graph module](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) eller [AzureAD v2 PowerShell för för hands versionen](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) av (AzureADPreview).

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>Förklaring av skript

| Kommando | Kommentarer |
|---|---|
| [New-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/New-azureadgroupapproleassignment?view=azureadps-2.0) | Tilldelar en program roll en grupp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD PowerShell-modulen finns i [Översikt över Azure AD PowerShell-modulen](/powershell/azure/active-directory/overview?view=azureadps-2.0).

Andra PowerShell-exempel för Application Proxy finns i [Azure AD PowerShell-exempel för azure AD-programproxy](../application-proxy-powershell-samples.md).