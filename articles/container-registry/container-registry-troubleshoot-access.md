---
title: Felsöka nätverks problem med registret
description: Symptom, orsaker och lösningar på vanliga problem vid åtkomst till ett Azure Container Registry i ett virtuellt nätverk eller bakom en brand vägg
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: f84b11418344bfeaf790377c1d8644fbc7d7d636
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743377"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Felsöka nätverks problem med registret

Den här artikeln hjälper dig att felsöka problem som kan uppstå vid åtkomst till ett Azure Container Registry i ett virtuellt nätverk eller bakom en brand vägg. 

## <a name="symptoms"></a>Symtom

Kan innehålla ett eller flera av följande:

* Det går inte att push-överföra eller hämta bilder och du får ett fel meddelande `dial tcp: lookup myregistry.azurecr.io`
* Det går inte att push-överföra eller hämta bilder och du får Azure CLI-fel `Could not connect to the registry login server`
* Det går inte att hämta avbildningar från registret till Azure Kubernetes service eller någon annan Azure-tjänst
* Det går inte att komma åt ett register bakom en HTTPS-proxy och du får ett fel meddelande `Error response from daemon: login attempt failed with status: 403 Forbidden`
* Det gick inte att konfigurera inställningar för virtuellt nätverk och du får ett fel meddelande `Failed to save firewall and virtual network settings for container registry`
* Det går inte att öppna eller Visa register inställningar i Azure Portal eller hantera registret med hjälp av Azure CLI
* Det går inte att lägga till eller ändra inställningar för virtuellt nätverk eller offentliga åtkomst regler
* ACR-aktiviteter kan inte skicka eller ta emot bilder
* Azure Security Center kan inte skanna bilder i registret, eller genomsöknings resultat visas inte i Azure Security Center

## <a name="causes"></a>Orsaker

* En klient brand vägg eller proxy förhindrar åtkomst [lösning](#configure-client-firewall-access)
* Åtkomst regler för offentliga nätverk i registret förhindra åtkomst- [lösning](#configure-public-access-to-registry)
* Konfiguration av virtuellt nätverk förhindrar åtkomst [lösning](#configure-vnet-access)
* Du försöker integrera Azure Security Center eller vissa andra Azure-tjänster med ett register som har en privat slut punkt, en tjänst slut punkt eller regler för offentliga IP-åtkomst- [lösningar](#configure-service-access)

## <a name="further-diagnosis"></a>Ytterligare diagnos 

Kör kommandot [AZ ACR check-Health](/cli/azure/acr#az-acr-check-health) för att få mer information om hälso tillståndet för register miljön och eventuellt åtkomst till ett mål register. Diagnostisera till exempel vissa nätverks anslutningar eller konfigurations problem. 

Se [kontrol lera hälso tillståndet för ett Azure Container Registry](container-registry-check-health.md) för kommando exempel. Om fel rapporteras läser du [fel referensen](container-registry-health-error-reference.md) och följande avsnitt för rekommenderade lösningar.

> [!NOTE]
> Vissa nätverks anslutnings problem kan också uppstå när det finns problem med autentisering eller auktorisering av registret. Se [Felsöka register inloggning](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Möjliga lösningar

### <a name="configure-client-firewall-access"></a>Konfigurera klient brand Väggs åtkomst

För att få åtkomst till ett register från bakom en klient brand vägg eller proxyserver, konfigurera brand Väggs regler för att komma åt registrets offentliga REST-och data slut punkter. Om [dedikerade data slut punkter](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) är aktiverade, behöver du regler för att komma åt:

* REST-slutpunkt: `<registryname>.azurecr.io`
* Data slut punkter: `<registry-name>.<region>.data.azurecr.io`

För ett geo-replikerat register konfigurerar du åtkomst till data slut punkten för varje regional replik.

Se till att både Docker-klienten och Docker daemon är konfigurerade för proxy-beteende bakom en HTTPS-proxy.

Register resurs loggar i ContainerRegistryLoginEvents-tabellen kan hjälpa till att diagnostisera en försöks anslutning som är blockerad.

Relaterade länkar:

* [Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg](container-registry-firewall-access-rules.md)
* [Konfiguration av HTTP/HTTPS-proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Geo-replikeringi Azure Container Registry](container-registry-geo-replication.md)
* [Azure Container Registry loggar för diagnostisk utvärdering och granskning](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Konfigurera offentlig åtkomst till registret

Om du har åtkomst till ett register via Internet bekräftar du att registret tillåter offentligt nätverks åtkomst från klienten. Som standard tillåter ett Azure Container Registry åtkomst till de offentliga register slut punkterna från alla nätverk. Ett register kan begränsa åtkomsten till valda nätverk eller valda IP-adresser. 

Om registret är konfigurerat för ett virtuellt nätverk med en tjänst slut punkt inaktive ras även åtkomst via tjänst slut punkten om du inaktiverar offentlig nätverks åtkomst. Om ditt register har kon figurer ATS för ett virtuellt nätverk med en privat länk gäller inte reglerna för IP-nätverk för registrets privata slut punkter. 

Relaterade länkar:

* [Konfigurera regler för offentliga IP-nätverk](container-registry-access-selected-networks.md)
* [Anslut privat till ett Azure Container Registry med hjälp av Azure Private Link](container-registry-private-link.md)
* [Begränsa åtkomsten till ett behållar register med hjälp av en tjänst slut punkt i ett virtuellt Azure-nätverk](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Konfigurera VNet-åtkomst

Bekräfta att det virtuella nätverket har kon figurer ATS med antingen en privat slut punkt för privat länk eller en tjänst slut punkt (för hands version). Det finns för närvarande inte stöd för en Azure skydds-slutpunkt.

Granska NSG-regler och service märken som används för att begränsa trafik från andra resurser i nätverket till registret. 

Om en tjänst slut punkt till registret har kon figurer ATS bekräftar du att en nätverks regel har lagts till i registret som tillåter åtkomst från det nätverks under nätet. Tjänst slut punkten stöder bara åtkomst från virtuella datorer och AKS-kluster i nätverket.

Om du vill begränsa åtkomsten till registret med hjälp av ett virtuellt nätverk i en annan Azure-prenumeration, se till att du registrerar `Microsoft.ContainerRegistry` resurs leverantören i den prenumerationen. [Registrera resurs leverantören](../azure-resource-manager/management/resource-providers-and-types.md) för Azure Container Registry med hjälp av Azure Portal, Azure CLI eller andra Azure-verktyg.

Om Azure-brandväggen eller en liknande lösning har kon figurer ATS i nätverket kontrollerar du att utgående trafik från andra resurser, till exempel ett AKS-kluster, är aktive rad för att uppnå register slut punkterna.

Om en privat slut punkt har kon figurer ATS bekräftar du att DNS matchar registrets offentliga FQDN, till exempel *myregistry.azurecr.io* i registrets privata IP-adress. Använd ett nätverks verktyg som `dig` eller `nslookup` för DNS-sökning.

Relaterade länkar:

* [Anslut privat till ett Azure Container Registry med hjälp av Azure Private Link](container-registry-private-link.md)
* [Begränsa åtkomsten till ett behållar register med hjälp av en tjänst slut punkt i ett virtuellt Azure-nätverk](container-registry-vnet.md)
* [Nödvändiga utgående nätverks regler och FQDN för AKS-kluster](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: fel sökning av DNS-matchning](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Tjänst taggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Konfigurera tjänst åtkomst

Azure Security Center kan för närvarande inte utföra [avbildnings sårbarhets sökning](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) i ett register som begränsar åtkomsten till privata slut punkter, valda undernät eller IP-adresser. Resurser av följande tjänster kan inte heller komma åt ett behållar register med nätverks begränsningar:

* Azure DevOps Services 
* Azure Container Instances
* Azure Container Registry Tasks

Om åtkomst eller integrering av dessa Azure-tjänster med ditt behållar register krävs tar du bort nätverks begränsningen. Ta till exempel bort registrets privata slut punkter eller ta bort eller ändra registrets offentliga åtkomst regler.

Relaterade länkar:

* [Azure Container Registry avbildnings genomsökning av Security Center](../security-center/azure-container-registry-integration.md)
* Ge [feedback](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Konfigurera regler för offentliga IP-nätverk](container-registry-access-selected-networks.md)
* [Anslut privat till ett Azure Container Registry med hjälp av Azure Private Link](container-registry-private-link.md)


## <a name="advanced-troubleshooting"></a>Avancerad felsökning

Om [samlingen av resurs loggar](container-registry-diagnostics-audit-logs.md) är aktive rad i registret, granskar du ContainterRegistryLoginEvents-loggen. Den här loggen lagrar autentiserings händelser och status, inklusive inkommande identitet och IP-adress. Fråga [efter autentiseringsfel i loggen.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Relaterade länkar:

* [Loggar för diagnostisk utvärdering och granskning](container-registry-diagnostics-audit-logs.md)
* [Vanliga frågor om container Registry](container-registry-faq.md)
* [Azures säkerhets bas linje för Azure Container Registry](security-baseline.md)
* [Bästa metoder för Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nästa steg

Om du inte löser problemet här kan du läsa följande alternativ.

* Andra fel söknings avsnitt för registret innehåller:
  * [Felsöka inloggning av registret](container-registry-troubleshoot-login.md) 
  * [Felsöka registerprestanda](container-registry-troubleshoot-performance.md)
* Alternativ för [Community-support](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A](https://docs.microsoft.com/answers/products/)
* [Öppna en supportbegäran](https://azure.microsoft.com/support/create-ticket/)


