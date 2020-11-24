---
title: Konfigurera Log Analytics arbets yta för Azure Monitor for VMs
description: Beskriver hur du skapar och konfigurerar arbets ytan Log Analytics som används av Azure Monitor for VMs.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: a4380ae7fdb523f8b2f5bc14fefa094b6d96e547
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95750542"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Konfigurera Log Analytics arbets yta för Azure Monitor for VMs
Azure Monitor for VMs samlar in data från en eller flera Log Analytics arbets ytor i Azure Monitor. Innan du onboarding agents måste du skapa och konfigurera en arbets yta. I den här artikeln beskrivs kraven för arbets ytan och för att konfigurera den för Azure Monitor for VMs.

## <a name="overview"></a>Översikt
En enda prenumeration kan använda valfritt antal arbets ytor beroende på dina krav. det enda kravet på arbets ytan är att det finns på en plats som stöds och konfigureras med *VMInsights* -lösningen.

När arbets ytan har kon figurer ATS kan du använda något av de tillgängliga alternativen för att installera de nödvändiga agenterna på VM och VMSS och ange en arbets yta där data ska skickas. Azure Monitor for VMs samlar in data från en konfigurerad arbets yta i prenumerationen.

> [!NOTE]
> När du aktiverar Azure Monitor for VMs på en enskild virtuell dator eller VMSS med hjälp av Azure Portal får du möjlighet att välja en befintlig arbets yta eller skapa en ny. *VMInsights* -lösningen kommer att installeras på den här arbets ytan om den inte redan finns. Du kan sedan använda den här arbets ytan för andra agenter.


## <a name="create-log-analytics-workspace"></a>Skapa Log Analytics-arbetsyta

>[!NOTE]
>Den information som beskrivs i det här avsnittet gäller även för den [tjänstkarta lösningen](service-map.md). 

Åtkomst till Log Analytics arbets ytor i Azure Portal från menyn **Log Analytics arbets ytor** .

[![Logga Anlytics-arbetsytor](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Du kan skapa en ny Log Analytics arbets yta med någon av följande metoder. Mer information om hur du avgör hur många arbets ytor som du bör använda i din miljö och hur du utformar deras åtkomst strategi finns i [Designa din distribution av Azure Monitor loggar](../platform/design-logs-deployment.md) .


* [Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>Regioner som stöds

Azure Monitor for VMs stöder Log Analytics arbets ytor i följande regioner, även om du kan övervaka virtuella datorer i vilken region som helst. De virtuella datorerna är inte begränsade till de regioner som stöds av Log Analytics arbets ytan.

- USA, västra centrala
- USA, västra
- USA, västra 2
- USA, södra centrala
- East US
- USA, östra 2
- Central US
- USA, norra centrala
- US Gov AZ
- US Gov va
- Kanada, centrala
- Storbritannien, södra
- Norra Europa
- Europa, västra
- Asien, östra
- Sydostasien
- Indien, centrala
- Japan, östra
- Australien, östra
- Australien, sydöstra

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll
Om du vill aktivera och komma åt funktionerna i Azure Monitor for VMs måste du ha [rollen Log Analytics Contributor](../platform/manage-access.md#manage-access-using-azure-permissions) i arbets ytan. Om du vill visa prestanda-, hälso-och kart data måste du ha [rollen övervaknings läsare](../platform/roles-permissions-security.md#built-in-monitoring-roles) för den virtuella Azure-datorn. Mer information om hur du styr åtkomsten till en Log Analytics arbets yta finns i [hantera arbets ytor](../platform/manage-access.md).

## <a name="add-vminsights-solution-to-workspace"></a>Lägg till VMInsights-lösning till arbets ytan
Innan en Log Analytics arbets yta kan användas med Azure Monitor for VMs måste den ha *VMInsights* -lösningen installerad. Metoderna för att konfigurera arbets ytan beskrivs i följande avsnitt.

> [!NOTE]
> När du lägger till *VMInsights* -lösningen i arbets ytan börjar alla befintliga virtuella datorer som är anslutna till arbets ytan att skicka data till InsightsMetrics. Data för de andra data typerna samlas inte in förrän du lägger till Dependency Agent till de befintliga virtuella datorerna som är anslutna till arbets ytan.

### <a name="azure-portal"></a>Azure Portal
Det finns tre alternativ för att konfigurera en befintlig arbets yta med hjälp av Azure Portal. Var och en beskrivs nedan.

Om du vill konfigurera en enskild arbets yta går du till alternativet **Virtual Machines** på **Azure Monitor** -menyn, väljer **andra onboarding-alternativ** och **konfigurerar sedan en arbets yta**. Välj en prenumeration och en arbets yta och klicka sedan på **Konfigurera**.

[![Konfigurera arbetsyta](media/vminsights-enable-at-scale-policy/configure-workspace.png)](media/vminsights-enable-at-scale-policy/configure-workspace.png#lightbox)

Om du vill konfigurera flera arbets ytor väljer du fliken **konfiguration av arbets yta** på menyn **Virtual Machines** på **Monitor** -menyn i Azure Portal. Ange filter värden om du vill visa en lista över befintliga arbets ytor. Markera kryss rutan bredvid varje arbets yta som du vill aktivera och klicka sedan på **Konfigurera valda** .

[![Konfiguration av arbetsyta](media/vminsights-enable-at-scale-policy/workspace-configuration.png)](media/vminsights-enable-at-scale-policy/workspace-configuration.png#lightbox)


När du aktiverar Azure Monitor for VMs på en enskild virtuell dator eller VMSS med hjälp av Azure Portal får du möjlighet att välja en befintlig arbets yta eller skapa en ny. *VMInsights* -lösningen kommer att installeras på den här arbets ytan om den inte redan finns. Du kan sedan använda den här arbets ytan för andra agenter.

[![Aktivera enskild virtuell dator i portalen](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager-mall
Azure Resource Manager mallar för Azure Monitor for VMs finns i en arkivfil (. zip) som du kan [Ladda ned från vår GitHub-lagrings platsen](https://aka.ms/VmInsightsARMTemplates). Detta inkluderar en mall med namnet **ConfigureWorkspace** som konfigurerar en Log Analytics arbets yta för Azure Monitor for VMS. Du distribuerar den här mallen med någon av standard metoderna, inklusive PowerShell-och CLI-kommandona nedan: 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>Nästa steg
- Se [onboard agents för att Azure Monitor for VMS](vminsights-enable-overview.md) ansluta agenter till Azure Monitor for VMS.
- Se [mål för övervaknings lösningar i Azure Monitor (för hands version)](solution-targeting.md) om du vill begränsa mängden data som skickas från en lösning till arbets ytan.
