---
title: Översikt
description: Ta reda på hur Azure App Service hjälper dig att utveckla och hantera webbappar.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 1b72224441741990a1fc94400dfe718ea9d1b0b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961490"
---
# <a name="app-service-overview"></a>Översikt över App Service

*Azure App Service* är en HTTP-baserad tjänst som är värd för webb program, REST API: er och mobila Server delar. Du kan utveckla i det språk du föredrar, oavsett om det är .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python. Program körs och skalas enkelt i både Windows-och [Linux](#app-service-on-linux)-baserade miljöer.

App Services lägger inte bara till kraften hos Microsoft Azure i ditt program, t.ex. säkerhet, belastningsutjämning, automatisk skalning och automatiserad hantering. Du kan också dra nytta av dess DevOps-funktioner, till exempel kontinuerlig distribution från Azure DevOps, GitHub, Docker Hub och andra källor, paket hantering, mellanlagrings miljöer, anpassad domän och TLS/SSL-certifikat. 

Med App Service betalar du för de Azure-beräkningsresurer du använder. De beräknings resurser som du använder bestäms av _App Service plan_ som du kör apparna på. Mer information finns i [Översikt över Azure App Service planer](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Varför använda Azure Apptjänst?

Här följer några funktioner i App Service:

* **Flera språk och ramverk** – App Service har förstklassigt stöd för ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP och Python. Du kan också köra [PowerShell och andra skript och körbara filer](webjobs-create.md) som bakgrundstjänster.
* **Hanterad produktions miljö** – App Service automatiskt [korrigering och underhåll av operativ system-och språk ramverk](overview-patch-os-runtime.md) . Ägna tid åt att skriva Fantastiska appar och låta Azure bekymra dig om plattformen.
* **Skapa behållare och Docker** – hantera din app och vara värd för en anpassad Windows-eller Linux-behållare i App Service. Köra appar med flera behållare med Docker Compose. Migrera dina Docker-färdigheter direkt till App Service.
* **DevOps-optimering** – Konfigurera [kontinuerlig integrering och distribution](deploy-continuous-deployment.md) med Azure DevOps, GitHub, BitBucket, Docker Hub eller Azure Container Registry. Flytta upp uppdateringar via [test- och mellanlagringsmiljöer](deploy-staging-slots.md). Hantera dina appar i App Service med [Azure PowerShell](/powershell/azure/) eller det [plattformsoberoende kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli).
* **Global skala med hög tillgänglighet** – skala [upp](manage-scale-up.md) och [ned](../azure-monitor/platform/autoscale-get-started.md) manuellt och automatiskt. Hantera appar var som helst i Microsofts globala datacenterinfrastruktur med hög tillgänglighet tack vare [serviceavtalet](https://azure.microsoft.com/support/legal/sla/app-service/) för App Service.
* **Anslutningar till SaaS-plattformar och lokala data** – välj bland fler än 50 [anslutningsappar](../connectors/apis-list.md) för företagssystem (till exempel SAP), SaaS-tjänster (till exempel Salesforce) och internettjänster (till exempel Facebook). Åtkomst till lokala data via [hybridanslutningar ](app-service-hybrid-connections.md) och [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Säkerhet och efterlevnad** – App Service [uppfyller ISO, SOC och PCI](https://www.microsoft.com/en-us/trustcenter). Autentisera användare med [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md)eller [Microsoft-konto](configure-authentication-provider-microsoft.md). Skapa [IP-adressbegränsningar](app-service-ip-restrictions.md) och [hantera tjänstidentiteter](overview-managed-identity.md).
* **Programmallar** – välj från en omfattande lista med programmallar i [Azure Marketplace](https://azure.microsoft.com/marketplace/), till exempel WordPress, Joomla och Drupal.
* **Visual Studio och Visual Studio Code integration** – dedikerade verktyg i Visual Studio och Visual Studio Code effektiviserar arbetet med att skapa, distribuera och felsöka.
* **API och mobila funktioner** – App Service ger nyckelfärdigt CORS-stöd för RESTful API-scenarier, och förenklar mobilappscenarier genom att möjliggöra autentisering, synkronisering av offlinedata, push-meddelanden med mera.
* **Serverlös kod** – kör ett kodfragment eller skript på begäran utan att explicit behöva etablera eller hantera infrastruktur, och betala bara för den beräkningstid som koden faktiskt använder (se [Azure Functions](../azure-functions/index.yml)).

Förutom App Service erbjuder Azure andra tjänster som kan användas till att hantera webbplatser och webbappar. För de flesta scenarier är App Service det bästa valet.  För mikrotjänstens arkitektur bör du tänka på [Azure våren-Cloud Service](../spring-cloud/index.yml) eller [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric).  Om du behöver mer kontroll över de virtuella datorer som koden körs på, bör du tänka på [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Mer information om hur du väljer mellan dessa Azure-tjänster hittar du i [Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="app-service-on-linux"></a>App Service på Linux

App Service kan också vara värd för webbappar internt i Linux för program stackar som stöds. Den kan också köra anpassade Linux-behållare (kallas även Web App for Containers).

### <a name="built-in-languages-and-frameworks"></a>Inbyggda språk och ramverk

App Service på Linux stöder ett antal språkspecifika inbyggda avbildningar. Distribuera bara din kod. Språk som stöds är: Node.js, Java (JRE 8 & JRE 11), PHP, python, .NET Core och Ruby. Kör [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) för att visa de senaste språken och de versioner som stöds. Om körningen av programmet kräver att programmet inte stöds i de inbyggda avbildningarna kan du distribuera det med en anpassad behållare.

### <a name="limitations"></a>Begränsningar

- App Service på Linux stöds inte på den [delade](https://azure.microsoft.com/pricing/details/app-service/plans/) pris nivån. 
- Du kan inte mixa Windows-och Linux-appar i samma App Service plan.  
- I samma resurs grupp kan du inte mixa Windows-och Linux-appar i samma region.
- Azure Portal visar endast funktioner som för närvarande fungerar för Linux-appar. När funktionerna är aktiverade är de aktiverade på portalen.
- När den distribueras till inbyggda avbildningar allokeras din kod och ditt innehåll till en lagrings volym för webb innehåll, som backas upp av Azure Storage. Disk fördröjningen för den här volymen är högre och mer variabel än svars tiden för behållar systemet. Appar som kräver tung skrivskyddad åtkomst till innehållsfiler kan dra nytta av alternativet för anpassad behållare, som placerar filer i behållarens fil system i stället för på innehålls volymen.

## <a name="next-steps"></a>Nästa steg

Skapa din första webbapp.

> [!div class="nextstepaction"]
> [ASP.NET Core (på Windows eller Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (i Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (på Windows eller Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (på Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (på Windows eller Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (på Windows eller Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (på Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (i Windows eller Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [Anpassad behållare (Windows eller Linux)](tutorial-custom-container.md)