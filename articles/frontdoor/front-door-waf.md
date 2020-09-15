---
title: Skala snabbt och skydda ett webb program med Azures front dörr och Azure Web Application-brandväggen (WAF) | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder brand vägg för webbaserade program med din Azure-frontend
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1958481193b66c8cec2cb6a1ac6648a6900d70ac
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531210"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Självstudie: snabbt skala och skydda ett webb program med Azures frontend-dörr och Azure WebApplication-brandvägg (WAF)

Många webb program har en snabb ökning av trafiken i de senaste veckorna relaterade till COVID-19. Dessutom underhåller dessa webb program också en överbelastning i skadlig trafik, inklusive denial of Service-attacker. Ett effektivt sätt att hantera båda dessa behov, skala ut för trafik toppar och skydda mot attacker, är att konfigurera Azure-frontend med Azure WAF som en acceleration, cachelagring och säkerhets nivå framför ditt webb program. Den här artikeln innehåller rikt linjer för hur du snabbt får den här Azure-startdörren med Azure WAF-installation för alla webb program som körs i eller utanför Azure. 

Vi använder Azure CLI för att konfigurera WAF i den här självstudien, men alla dessa steg stöds också fullt ut i Azure Portal, Azure PowerShell, Azure ARM och Azure REST-API: er. 

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa en frontend-dörr.
> - Skapa en Azure WAF-princip.
> - Konfigurera rulesets för WAF-princip.
> - Koppla WAF-princip till front dörr
> - Konfigurera anpassat domän

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Anvisningarna i den här bloggen använder kommando rads gränssnittet för Azure (CLI). Visa den här guiden för att [komma igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Tips: ett enkelt & snabbt sätt att komma igång med Azure CLI är med [bash i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Kontrol lera att tillägget för front dörren har lagts till i Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

Obs! mer information om de kommandon som anges nedan finns i [Azure CLI-referensen för front dörren](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="create-an-azure-front-door-afd-resource"></a>Skapa en AFD-resurs (Azure frontend dörr)

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-adress**: Server dels adressen är det fullständigt kvalificerade domän namnet (FQDN) för det program som du vill skydda. Till exempel myapplication.contoso.com

**--godkända**protokoll: de godkända protokollen anger vilka protokoll som du vill att AFD ska stödja för ditt webb program. Ett exempel skulle bli--godkänt-protokoll http https.

**--Name**: Ange ett namn för din AFD-resurs

**--resurs grupp**: den resurs grupp som du vill placera den här AFD-resursen i.  Mer information om resurs grupper finns i hantera resurs grupper i Azure

I svaret som du kommer att köra det här kommandot söker du efter nyckeln hostName och noterar värdet så att det används i ett senare steg. HostName är DNS-namnet för den AFD-resurs som du har skapat

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Skapa en Azure WAF-profil som ska användas med resurser i Azures frontend-dörr

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--namn Ange ett namn för din Azure WAF-princip

--resurs grupp den resurs grupp som du vill placera den här WAF-resursen i. 

CLI-koden ovan skapar en WAF-princip som är aktive rad och är i förebyggande läge. 

Obs! Du kanske också vill skapa WAF i identifierings läge och se hur det identifierar & loggning av skadliga begär Anden (och inte blockerar) innan du bestämmer dig för att ändra till skydds läge.

I svaret som du får från att köra det här kommandot, letar du efter nyckeln "ID" och noterar värdet så att det används i ett senare steg. ID-fältet ska ha formatet

/Subscriptions/**prenumerations-ID**/ResourceGroups/**resurs grupp namn**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF princip namn**

## <a name="add-managed-rulesets-to-this-waf-policy"></a>Lägg till hanterade rulesets i den här WAF-principen

I en WAF-princip kan du lägga till hanterade rulesets som är en uppsättning regler som skapats och hanteras av Microsoft och som utgör ut ur Box-skyddet mot alla typer av hot. I det här exemplet lägger vi till två sådana rulesets (1) standard-ruleset som skyddar mot vanliga webbhoten och (2) bot Protection-ruleset, som skyddar mot skadlig robotar

(1) Lägg till standard-ruleset

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Lägg till robot Manager-ruleset

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--princip – namnge det namn du angav för din Azure WAF-resurs

--resurs grupp den resurs grupp som du har placerat den här WAF-resursen i.

## <a name="associate-the-waf-policy-with-the-afd-resource"></a>Koppla WAF-principen till AFD-resursen

I det här steget ska vi associera WAF-principen som vi har skapat med AFD-resursen som är framför ditt webb program.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--namnge det namn du angav för din AFD-resurs

--resurs grupp den resurs grupp som du har placerat i Azures resurs för front dörr i.

--Ange det här är den plats där du vill uppdatera attributet WebApplicationFirewallPolicyLink för frontendEndpoint som är associerat med din AFD-resurs med den nyligen skapade WAF-principen. Du hittar ID för WAF-principen från det svar du fick från steg #2 ovan

Obs! exemplet ovan är för det fall där du inte använder en anpassad domän om du

Om du inte använder några anpassade domäner för att komma åt dina webb program kan du hoppa över steg #5. I så fall kommer du att ge slutanvändarna det värdnamn du fick i steg #1 för att navigera till ditt webb program

## <a name="configure-custom-domain-for-your-web-application"></a>Konfigurera en anpassad domän för ditt webb program

Det anpassade domän namnet för ditt webb program (det som kunder använder för att referera till ditt program, till exempel www.contoso.com), pekade på den plats där det kördes innan AFD infördes. Efter den här ändringen av arkitektur som lägger till AFD + WAF till fram till programmet, ska DNS-posten som motsvarar den anpassade domänen nu peka på den här AFD-resursen. Detta kan göras genom att mappa om posten i DNS-servern till AFD-värdnamnet som du noterade i steg #1.

De olika stegen för att uppdatera dina DNS-poster beror på din DNS-tjänstleverantör, men om du använder Azure DNS för att vara värd för ditt DNS-namn kan du läsa mer i dokumentationen om [hur du uppdaterar en DNS-post](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) och pekar på AFD-värdnamnet. 

En viktig kommentar här är att om du behöver dina användare att navigera till din webbplats med hjälp av zon Apex, till exempel contoso.com, måste du använda Azure DNS och dess [alias är post typen](https://docs.microsoft.com/azure/dns/dns-alias) som värd för ditt DNS-namn. 

Dessutom måste du också uppdatera AFD-konfigurationen för att [lägga till den här anpassade domänen](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) så att AFD förstår mappningen.

Slutligen, om du använder en anpassad domän för att komma åt ditt webb program och vill aktivera HTTPS-protokollet, måste du ha [certifikat för din anpassade domän konfiguration i AFD](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="lock-down-your-web-application"></a>Låsa ditt webb program

En valfri metod att följa är att se till att endast AFD kanter kan kommunicera med ditt webb program. Den här åtgärden ser till att ingen kan kringgå AFD-skydd och komma åt dina program direkt. Du kan göra det här låset genom att gå till [avsnittet Vanliga frågor och svar i AFD](https://docs.microsoft.com/azure/frontdoor/front-door-faq) och referera till frågan om att låsa upp Server delar för att få åtkomst till AFD.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver resurserna i den här självstudien använder du kommandot [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) för att ta bort resurs gruppen, frontend-dörren och WAF-principen.

```azurecli-interactive
  az group delete \
    --name <>
```
--Namnge resurs grupps namnet för alla resurser som distribueras i den här självstudien.

## <a name="next-steps"></a>Efterföljande moment

Om du vill veta mer om hur du felsöker din front dörr kan du fortsätta till instruktions guiderna.

> [!div class="nextstepaction"]
> [Felsöka vanliga problem med Routning](front-door-troubleshoot-routing.md)
