---
title: Azures säkerhets bas linje för Azure Application Gateway
description: Azures säkerhets bas linje för Azure Application Gateway
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b470fa72518f805c10403931280bdec96bda0fbe
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226547"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>Azures säkerhets bas linje för Azure Application Gateway

Azures säkerhets bas linje för Azure Application Gateway innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: kontrol lera att alla Virtual Network Azure Application Gateway-undernätet har en nätverks säkerhets grupp (NSG) som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Nätverks säkerhets grupper stöds på Azure Application Gateway, men det finns vissa begränsningar och krav som måste följas för att din NSG och Azure Application-Gateway ska fungera som förväntat.

* [Förstå begränsningar och krav för att använda NSG: er med Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: för de nätverks säkerhets grupper (NSG: er) som är associerade med dina Azure Application Gateway-undernät aktiverar du NSG Flow-loggar och skickar loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Obs! det finns vissa fall där NSG Flow-loggar som är kopplade till dina Azure Application Gateway-undernät inte visar trafik som har tillåtits. Om konfigurationen matchar följande scenario visas inte tillåten trafik i dina NSG-flödes loggar:
- Du har distribuerat Application Gateway v2
- Du har en NSG på Application Gateway-undernätet
- Du har aktiverat NSG Flow-loggar på den NSG

* [Så här aktiverar du NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Så här aktiverar och använder du Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Vanliga frågor och svar om diagnostik och loggning för Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker. WAF baseras på regler från OWASP (Open Web Application Security Project) Core Rule Set 3,1 (endast WAF_v2), 3,0 och 2.2.9.

* [Förstå Azure Application Gateway-funktioner](https://docs.microsoft.com/azure/application-gateway/features)

* [Förstå Azure-WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Så här distribuerar du Azure-WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS standard skydd på dina virtuella Azure-nätverk som är kopplade till dina produktions instanser av Azure Application Gateway för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga IP-adresser.

* [Så här konfigurerar du DDoS-skydd](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Förstå Azure Security Center integrerad Hot information](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: för de nätverks säkerhets grupper (NSG: er) som är associerade med dina Azure Application Gateway-undernät aktiverar du NSG Flow-loggar och skickar loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Obs! det finns vissa fall där NSG Flow-loggar som är kopplade till dina Azure Application Gateway-undernät inte visar trafik som har tillåtits. Om konfigurationen matchar följande scenario visas inte tillåten trafik i dina NSG-flödes loggar:
- Du har distribuerat Application Gateway v2
- Du har en NSG på Application Gateway-undernätet
- Du har aktiverat NSG Flow-loggar på den NSG

* [Så här aktiverar du NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Så här aktiverar och använder du Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Vanliga frågor och svar om diagnostik och loggning för Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker. WAF baseras på regler från OWASP (Open Web Application Security Project) Core Rule Set 3,1 (endast WAF_v2), 3,0 och 2.2.9.

Alternativt finns det flera Marketplace-alternativ som Barracuda-WAF för Azure som är tillgängliga på Azure Marketplace, som omfattar funktioner för ID/IP-adresser.

* [Förstå Azure Application Gateway-funktioner](https://docs.microsoft.com/azure/application-gateway/features)

* [Förstå Azure-WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Så här distribuerar du Azure-WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Förstå moln tjänsten Barracuda WAF](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: Distribuera Azure Application Gateway för webb program med HTTPS/SSL aktiverat för betrodda certifikat.

* [Så här distribuerar du Application Gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [Så här konfigurerar du Application Gateway att använda HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. GatewayManager) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

För nätverks säkerhets grupper (NSG: er) som är kopplade till dina Azure Application Gateway-undernät måste du tillåta inkommande Internet trafik på TCP-portarna 65503-65534 för Application Gateway v1 SKU, och TCP-portarna 65200-65535 för v2-SKU: n med mål under nätet som valfri och källa som GatewayManager service tag. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. Dessa portar är skyddade (låsta) av Azure-certifikat. Externa entiteter, inklusive kunder av dessa gateways, kan inte kommunicera med dessa slut punkter.

* [Förstå och använda service märken](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Översikt över Azure Application Gateway-konfiguration](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar som är relaterade till dina Azure Application Gateway-distributioner. Använd Azure Policy alias i namn området "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Application gateways, virtuella Azure-nätverk och nätverks säkerhets grupper. Du kan också använda inbyggd princip definition.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer i en enda skiss definition. Du kan enkelt använda skissen för nya prenumerationer, miljöer och finjustera kontroll och hantering genom versions hantering.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här skapar du en Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper (NSG: er) som är kopplade till ditt Azure Application Gateway-undernät samt andra resurser som rör nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda definitioner av Azure-principer som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Så här skapar du en Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks inställningar och resurser som är relaterade till dina Azure Application Gateway-distributioner. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks inställningar eller resurser äger rum.

* [Visa och hämta Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Så här skapar du aviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft underhåller den tids källa som används för Azure-resurser som Azure App Service.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) som utförs på kontroll Plans nivån för din Azure Application Gateway och relaterade resurser, till exempel nätverks säkerhets grupper (NSG: er) som används för att skydda Azure Application Gateway-undernätet.

Förutom aktivitets loggar kan du konfigurera diagnostikinställningar för dina Azure Application Gateway-distributioner. diagnostikinställningar används för att konfigurera strömnings export av plattforms loggar och mät värden för en resurs till valfri plats (lagrings konton, Event Hubs och Log Analytics).

Azure Application Gateway erbjuder även inbyggd integrering med Azure Application insikter. Application Insights samlar in logg-, prestanda-och fel data. Application Insights identifierar automatiskt prestanda avvikelser och innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå hur dina webb program används. Du kan aktivera kontinuerlig export för att exportera telemetri från Application Insights till en central plats för att bevara data längre än standard kvarhållningsperioden.

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Så här aktiverar du Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [Så här konfigurerar du kontinuerlig export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) som utförs på kontroll Plans nivån för din Azure Application Gateway och relaterade resurser, till exempel nätverks säkerhets grupper (NSG: er) som används för att skydda Azure Application Gateway-undernätet.

Förutom aktivitets loggar kan du konfigurera diagnostikinställningar för dina Azure Application Gateway-distributioner. diagnostikinställningar används för att konfigurera strömnings export av plattforms loggar och mät värden för en resurs till valfri plats (lagrings konton, Event Hubs och Log Analytics).

Azure Application Gateway erbjuder även inbyggd integrering med Azure Application insikter. Application Insights samlar in logg-, prestanda-och fel data. Application Insights identifierar automatiskt prestanda avvikelser och innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå hur dina webb program används. Du kan aktivera kontinuerlig export för att exportera telemetri från Application Insights till en central plats för att bevara data längre än standard kvarhållningsperioden.

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Så här aktiverar du Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [Så här konfigurerar du kontinuerlig export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

* [Ange logg lagrings parametrar för Log Analytics arbets ytor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Aktivera Azure Activity Log-diagnostikinställningar samt diagnostikinställningar för Azure Application Gateway och skicka loggarna till en Log Analytics arbets yta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på insamlade data.

Använd Azure Monitor för nätverk för en omfattande vy av hälsa och mått för alla distribuerade nätverks resurser, inklusive Azure Application gatewayer.

Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part.

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Använda Azure Monitor för nätverk](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) v2 SKU framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker. WAF baseras på regler från OWASP (Open Web Application Security Project) Core Rule Set 3,1 (endast WAF_v2), 3,0 och 2.2.9.

Aktivera diagnostikinställningar för Azure aktivitets logg samt diagnostikinställningar för Azure-WAF och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på insamlade data. Du kan skapa aviseringar baserat på Log Analytics arbets ytans frågor.

Använd Azure Monitor för nätverk för en omfattande vy av hälsa och mått för alla distribuerade nätverks resurser, inklusive Azure Application gatewayer. I konsolen Azure Monitor för nätverk kan du Visa och skapa aviseringar för Azure Application Gateway.

* [Så här distribuerar du Azure-WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Så här aktiverar du diagnostikinställningar för Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Använda Azure Monitor för nätverk](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

* [Så här skapar du aviseringar i Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) v2 framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker.

Konfigurera diagnostikinställningar för dina Azure Application Gateway-distributioner. diagnostikinställningar används för att konfigurera strömnings export av plattforms loggar och mät värden för en resurs till valfri plats (lagrings konton, Event Hubs och Log Analytics).

* [Så här distribuerar du Azure-WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Konfigurera diagnostikinställningar för Azure-WAF](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Application Gateway bearbetar eller skapar inte tillgängliga DNS-relaterade loggar för användare.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för IaaS beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

* [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: kontroll Plans åtkomst till Azure Application Gateway styrs via Azure Active Directory (AD). Azure AD har inte begreppet standard lösen ord.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

* [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Använda Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd en Azure App-registrering (tjänstens huvud namn) för att hämta en token som kan användas för att interagera med dina Azure Application-gatewayer via API-anrop.

* [Så här anropar du Azure REST API: er](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Registrera klient programmet (tjänstens huvud namn) med Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Information om Azure Recovery Services API](https://docs.microsoft.com/rest/api/recoveryservices/)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering.

* [Så här aktiverar du MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Övervaka identitet och åtkomst i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Så här aktiverar du MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

* [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du namngivna platser i Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (AAD) som central autentiserings-och auktoriserings system. AAD skyddar data genom att använda stark kryptering för data i vila och under överföring. AAD är också salter, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

* [Så här skapar och konfigurerar du en AAD-instans](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

* [Förstå Azure AD repor ting](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Så här använder du granskningar av Azure Identity Access](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har till gång till inloggnings aktiviteter, gransknings-och risk händelse logg källor i Azure AD, vilket gör att du kan integrera med alla Siem/övervaknings verktyg.

Du kan effektivisera processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

* [Så här integrerar du Azures aktivitets loggar i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure AD Identity Protection-och risk identifierings funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

* [Visa Azure AD-riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: ej tillämpligt; Customer Lockbox kan inte användas för Azure Application Gateway.

* [Lista över Customer Lockbox tjänster som stöds](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Kontrol lera att alla Virtual Network Azure Application Gateway-undernätet har en nätverks säkerhets grupp (NSG) som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Nätverks säkerhets grupper stöds på Azure Application Gateway, men det finns vissa begränsningar och krav som måste följas för att din NSG och Azure Application-Gateway ska fungera som förväntat.

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Förstå begränsningar och krav för att använda NSG: er med Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: kontrol lera att alla Virtual Network Azure Application Gateway-undernätet har en nätverks säkerhets grupp (NSG) som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Begränsa utgående trafik till endast betrodda platser för att minska risken för data exfiltrering. Nätverks säkerhets grupper stöds på Azure Application Gateway, men det finns vissa begränsningar och krav som måste följas för att din NSG och Azure Application-Gateway ska fungera som förväntat.

* [Förstå begränsningar och krav för att använda NSG: er med Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Konfigurera kryptering från slut punkt till slut punkt med TLS för dina Azure Application-gatewayer.

* [Så här konfigurerar du end-to-end-TLS med hjälp av Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: inte tillämpligt, Azure Application Gateway lagrar inte kund information i vila.

Microsoft hanterar den underliggande infrastrukturen för Azure Application Gateway och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra åtkomsten till Azure Application Gateway-kontroll planet (Azure Portal).

* [Så här konfigurerar du Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: ej tillämpligt; Azure Application Gateway lagrar inte kund information.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktion Azure Application Gateway-instanser samt andra kritiska eller relaterade resurser.

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: inte tillgänglig för tillfället. sårbarhets bedömning i Azure Security Center är ännu inte tillgängligt för Azure Application Gateway.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Granska säkerhets kontroller som är tillgängliga för Azure Application Gateway för att minska säkerhets risker för tjänst konfigurationen.

* [Funktions täckning (inklusive sårbarhets bedömning) för Azure PaaS Services](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ännu inte tillgänglig; sårbarhets bedömning i Azure Security Center är ännu inte tillgängligt för Azure Application Gateway.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Granska säkerhets kontroller som är tillgängliga för Azure Application Gateway för att minska säkerhets risker för tjänst konfigurationen.

* [Funktions täckning (inklusive sårbarhets bedömning) för Azure PaaS Services](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: ännu inte tillgänglig; sårbarhets bedömning i Azure Security Center är ännu inte tillgängligt för Azure Application Gateway.

Underliggande plattform som genomsöks och korrigeras av Microsoft. Granska säkerhets kontroller som är tillgängliga för Azure Application Gateway för att minska säkerhets risker för tjänst konfigurationen.

* [Funktions täckning (inklusive sårbarhets bedömning) för Azure PaaS Services](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

* [Så här skapar du frågor med Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Förstå Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: definiera godkända Azure-resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här skapar du frågor med Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Kontrol lera att alla Virtual Network Azure Application Gateway-undernätet har en nätverks säkerhets grupp (NSG) som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Nätverks säkerhets grupper stöds på Azure Application Gateway, men det finns vissa begränsningar och krav som måste följas för att din NSG och Azure Application-Gateway ska fungera som förväntat.

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Förstå begränsningar och krav för att använda NSG: er med Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar som är relaterade till dina Azure Application Gateway-distributioner. Använd Azure Policy alias i namn området "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Application gateways, virtuella Azure-nätverk och nätverks säkerhets grupper. Du kan också använda inbyggd princip definition.

* [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Förstå Azure Policys effekter](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade definitioner av Azure-principer använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

* [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation om Azure databaser](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Network" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. Network" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure-resurser.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure Application Gateway med en automatiskt hanterad identitet i Azure Active Directory (AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

Använd Azure Key Vault för att lagra certifikat på ett säkert sätt. Azure Key Vault är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och SSL-certifikat. Azure Application Gateway stöder integrering med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Detta stöd är begränsat till Application Gateway v2-SKU: n.

* [Så här konfigurerar du SSL-avslutning med Key Vault certifikat med hjälp av Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure Application Gateway med en automatiskt hanterad identitet i Azure Active Directory (AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

Använd Azure Key Vault för att lagra certifikat på ett säkert sätt. Azure Key Vault är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och SSL-certifikat. Azure Application Gateway stöder integrering med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Detta stöd är begränsat till Application Gateway v2-SKU: n.

* [Så här konfigurerar du SSL-avslutning med Key Vault certifikat med hjälp av Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) v2 framför viktiga webb program för ytterligare inspektion av inkommande trafik. Brand vägg för webbaserade program (WAF) är en tjänst (funktion i Azure Application Gateway) som tillhandahåller centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. Azure WAF hjälper dig att skydda dina Azure App Service-webbappar genom att inspektera inkommande webb trafik för att blockera attacker som SQL-injektering, skript körning över flera webbplatser, överföringar av skadlig kod och DDoS-attacker.

Konfigurera diagnostikinställningar för dina Azure Application Gateway-distributioner. diagnostikinställningar används för att konfigurera strömnings export av plattforms loggar och mät värden för en resurs till valfri plats (lagrings konton, Event Hubs och Log Analytics).

* [Så här distribuerar du Azure-WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Konfigurera diagnostikinställningar för Azure-WAF](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: ej tillämpligt; Azure Application Gateway lagrar inte kund information.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: när du använder Azure WebApplication-BRANDVÄGGEN (WAF) kan du konfigurera WAF-principer. En WAF-princip består av två typer av säkerhets regler: anpassade regler som har skapats av kunden och hanterade regel uppsättningar som är en samling av Azure-hanterade förkonfigurerade uppsättningar med regler. Azure-hanterade regel uppsättningar ger ett enkelt sätt att distribuera skydd mot en gemensam uppsättning säkerhetshot. Eftersom sådana rulesets hanteras av Azure uppdateras reglerna vid behov för att skydda mot nya attack-signaturer.

* [Förstå Azure-hanterade WAF regel uppsättningar](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-policy)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Azure Application Gateway lagrar inte kund information. Men om du använder anpassade Azure policy-definitioner, använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

Azure DevOps Services utnyttjar många av funktionerna i Azure Storage för att säkerställa att data är tillgängliga i händelse av maskin varu problem, avbrott i tjänsten eller region haverien. Azure DevOps-teamet följer dessutom procedurer för att skydda data från oavsiktlig eller skadlig borttagning.

* [Förstå data tillgänglighet i Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation om Azure databaser](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: säkerhetskopiera kund hanterade certifikat inom Azure Key Vault.

* [Säkerhetskopiera Key Vault-certifikat i Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa återställning av säkerhetskopierade kund hanterade certifikat.

* [Återställa Key Vault-certifikat](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: se till att mjuk borttagning är aktiverat för Azure Key Vault. Med mjuk borttagning kan du återställa borttagna nyckel valv och valv objekt, till exempel nycklar, hemligheter och certifikat.

* [Använda Azure Key Vault ' mjuk borttagning '](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Bygg ut en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla personal roller och faser för incident hantering/hantering från identifiering till granskning efter incidenten.

* [Konfigurera automatisering av arbets flöden i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov.

* [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

* [Så här ställer du in Azure Security Center säkerhets kontakt](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviserings indikatorn.

* [Så här konfigurerar du kontinuerlig export](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Strömma aviseringar till Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

* [Konfigurera automatisering av arbets flöden och Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: 

* [Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
