---
title: Azure-tjänster som har stöd för hanterade identiteter – Azure AD
description: Lista över tjänster som stöder hanterade identiteter för Azure-resurser och Azure AD-autentisering
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 07/09/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 207b4a926e77ff55faad388b6eeaeb221ec252a8
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006788"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet kan du autentisera till en tjänst som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i din kod. Vi håller på att integrera hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kom tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster har stöd för hanterade identiteter för Azure-resurser:


### <a name="azure-api-management"></a>Azure API Management

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |
| Användartilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager mall](../../api-management/api-management-howto-use-managed-service-identity.md)


### <a name="azure-app-service"></a>Azure App Service

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] |
| Användartilldelad | ![Tillgänglig][check] | ![Tillgänglig][check]  | ![Tillgänglig][check]  | ![Tillgänglig][check] |

I följande lista kan du konfigurera hanterad identitet för Azure App Service (i regioner där det finns tillgängligt):

- [Azure-portalen](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager mall](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-aktiverade Kubernetes

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | 
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Azure Arc-aktiverade Kubernetes [stöder för närvarande systemtilldelad identitet](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes). Det hanterade tjänst identitets certifikatet används av alla Azure Arc-aktiverade Kubernetes-agenter för kommunikation med Azure.

### <a name="azure-blueprints"></a>Azure Blueprint

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att använda en hanterad identitet med [Azure-skisser](../../governance/blueprints/overview.md):

- [Azure Portal skiss tilldelning](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API skiss tilldelning](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |


### <a name="azure-container-instances"></a>Azure Container Instances

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Linux: för hands version<br>Windows: inte tillgängligt | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Linux: för hands version<br>Windows: inte tillgängligt | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Container Instances (i regioner där det finns tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Container Registry uppgifter (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure-datautforskaren

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Data Factory v2 (i regioner där det finns):

- [Azure-portalen](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)



### <a name="azure-event-grid"></a>Azure Event Grid 

Hanterad identitets typ |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig  | Inte tillgänglig  | Inte tillgänglig |









### <a name="azure-functions"></a>Azure Functions

Hanterad identitets typ |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] |
| Användartilldelad | ![Tillgänglig][check] | ![Tillgänglig][check]  | ![Tillgänglig][check]  | ![Tillgänglig][check]  |

I följande lista kan du konfigurera hanterad identitet för Azure Functions (i regioner där det finns tillgängligt):

- [Azure-portalen](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager mall](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Data Factory v2 (i regioner där det finns):

- [Azure-portalen](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt i regionen där Azure import export-tjänsten är tillgänglig | Förhandsgranskning | Tillgänglig | Tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] | 
| Användartilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |


Mer information finns i [använda hanterade identiteter i Azure Kubernetes-tjänsten](../../aks/use-managed-identity.md).


### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |
| Användartilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgänglig][check] |


I följande lista kan du konfigurera hanterad identitet för Azure Logic Apps (i regioner där det finns tillgängligt):

- [Azure-portalen](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager mall](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)


### <a name="azure-policy"></a>Azure Policy

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] | ![Tillgänglig][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Policy (i regioner där det finns tillgängligt):

- [Azure-portalen](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)
- [Azure Resource Manager mallar](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Hanterad identitet för Service Fabric program](../../service-fabric/concepts-managed-identity.md) är tillgänglig i alla regioner.

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgängligt | Inte tillgängligt | inte tillgängligt |
| Användartilldelad | ![Tillgänglig][check] | Inte tillgängligt | Inte tillgängligt |Inte tillgängligt |

Se följande lista för att konfigurera hanterad identitet för Azure Service Fabric-program i alla regioner:

- [Azure Resource Manager mall](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | 
| Användartilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |


Mer information finns i [så här aktiverar du systemtilldelad hanterad identitet för Azure våren Cloud Application](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).


### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Förhandsgranskning | Förhandsgranskning | Förhandsgranskning |
| Användartilldelad | ![Tillgänglig][check] | Förhandsgranskning | Förhandsgranskning | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure-portalen](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Förhandsgranskning | Förhandsgranskning | 
| Användartilldelad | ![Tillgänglig][check] | ![Tillgänglig][check] | Förhandsgranskning | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machines (i regioner där det är tillgängligt):

- [Azure-portalen](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure-SDK:er](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Image Builder för Azure VM

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | 
| Användartilldelad | [Tillgängligt i regioner som stöds](../../virtual-machines/windows/image-builder-overview.md#regions) | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

Information om hur du konfigurerar hanterad identitet för Azure VM Image Builder (i regioner där tillgänglig) finns i [Översikt över Image Builder](../../virtual-machines/windows/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Azure SignalR Service

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |
| Användartilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure SignalR service (i regioner där det finns):

- [Azure Resource Manager mall](../../azure-signalr/howto-use-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klient tjänster som använder hanterade identiteter för Azure-resurser.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Se följande lista för att konfigurera åtkomst till Azure Resource Manager:

- [Tilldela åtkomst via Azure Portal](howto-assign-access-portal.md)
- [Tilldela åtkomst via PowerShell](howto-assign-access-powershell.md)
- [Tilldela åtkomst via Azure CLI](howto-assign-access-CLI.md)
- [Tilldela åtkomst via Azure Resource Manager mall](../../role-based-access-control/role-assignments-template.md)

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Tillgänglig][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Tillgänglig][check] |
| Azure Tyskland | `https://management.microsoftazure.de/` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | ![Tillgänglig][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Tillgänglig][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Tillgänglig][check] |
| Azure Tyskland |  `https://vault.microsoftazure.de` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://vault.azure.cn` | ![Tillgänglig][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Tillgänglig][check] |
| Azure Government |  | Inte tillgängligt |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |

### <a name="azure-sql"></a>Azure SQL

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Tillgänglig][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Tillgänglig][check] |
| Azure Tyskland | `https://database.cloudapi.de/` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | ![Tillgänglig][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Tillgänglig][check] |
| Azure Government |  | Inte tillgängligt |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |

### <a name="azure-service-bus"></a>Azure Service Bus

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Tillgänglig][check] |
| Azure Government |  | ![Tillgänglig][check] |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobbar och köer

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Tillgänglig][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Tillgänglig][check] |
| Azure Tyskland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Tillgänglig][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Tillgänglig][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Tillgänglig][check] |
| Azure Tyskland | `https://*.asazure.cloudapi.de` | ![Tillgänglig][check] |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Tillgänglig][check] |

> [!Note]
> Microsoft Power BI [stöder också hanterade identiteter](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Få"
