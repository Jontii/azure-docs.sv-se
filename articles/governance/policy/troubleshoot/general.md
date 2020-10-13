---
title: Felsöka vanliga fel
description: 'Lär dig hur du felsöker problem med att skapa princip definitioner, de olika SDK: n och tillägget för Kubernetes.'
ms.date: 10/05/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6026dc75187c8a70203a2484380eed70d519599d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743445"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Felsöka fel med hjälp av Azure Policy

Du kan stöta på fel när du skapar princip definitioner, arbetar med SDK eller konfigurerar [Azure policy för Kubernetes](../concepts/policy-for-kubernetes.md) -tillägg. I den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta fel information

Fel informationens plats beror på vilken åtgärd som orsakar felet.

- När du arbetar med en anpassad princip kan du prova den i Azure Portal för att få en mer beskrivande feedback om schemat eller granska resulterande [efterlevnadsprinciper](../how-to/get-compliance-data.md) för att se hur resurserna utvärderades.
- När du arbetar med olika SDK ger SDK information om varför funktionen misslyckades.
- När du arbetar med tillägget för Kubernetes börjar du med [loggningen](../concepts/policy-for-kubernetes.md#logging) i klustret.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-alias-not-found"></a>Scenario: aliaset hittades inte

#### <a name="issue"></a>Problem

Azure Policy använder [alias](../concepts/definition-structure.md#aliases) för att mappa till Azure Resource Manager egenskaper.

#### <a name="cause"></a>Orsak

Ett felaktigt eller obefintligt alias används i en princip definition.

#### <a name="resolution"></a>Lösning

Kontrol lera först att Resource Manager-egenskapen har ett alias. Använd [Azure policy-tillägg för Visual Studio Code](../how-to/extension-for-vscode.md), [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)eller SDK för att slå upp tillgängliga alias. Om alias för en Resource Manager-egenskap inte finns skapar du ett support ärende.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scenario: utvärderings informationen är inte aktuell

#### <a name="issue"></a>Problem

En resurs har statusen "Ej startad" eller så är kompatibilitetsinformation inte aktuella.

#### <a name="cause"></a>Orsak

En ny princip eller initiativ tilldelning tar cirka 30 minuter att tillämpas. Nya eller uppdaterade resurser inom omfånget för en befintlig tilldelning blir tillgängliga cirka 15 minuter senare. En sökning efter standard kompatibilitet sker var 24: e timme. Mer information finns i [utvärderings utlösare](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Lösning

Börja med att vänta på rätt tids period för att en utvärdering ska slutföras och efterföljande resultat ska bli tillgängliga i Azure Portal eller SDK. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-not-as-expected"></a>Scenario: kompatibiliteten är inte som förväntat

#### <a name="issue"></a>Problem

En resurs är inte i utvärderings tillstånd, antingen _kompatibel_ eller _inte kompatibel_, som förväntas för resursen.

#### <a name="cause"></a>Orsak

Resursen är inte i rätt omfattning för princip tilldelningen eller så fungerar inte princip definitionen som avsett.

#### <a name="resolution"></a>Lösning

Följ de här stegen för att felsöka princip definitionen:

1. Börja med att vänta på rätt tids period för att en utvärdering ska slutföras och efterföljande resultat ska bli tillgängliga i Azure Portal eller SDK. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Kontrol lera att tilldelnings parametrarna och tilldelnings omfånget har angetts korrekt.
1. Kontrol lera [princip definitions läget](../concepts/definition-structure.md#mode):
   - Mode all för alla resurs typer.
   - Läget Indexerad om princip definitionen söker efter taggar eller plats.
1. Kontrol lera att resursens omfattning inte är [Exkluderad](../concepts/assignment-structure.md#excluded-scopes) eller [undantagen](../concepts/exemption-structure.md).
1. Om kompatibiliteten för en princip tilldelning visar `0/0` resurser, fastställdes inga resurser som är tillämpliga inom tilldelnings omfånget. Kontrol lera både princip definitionen och tilldelnings omfånget.
1. För en icke-kompatibel resurs som förväntades vara kompatibel kontrollerar du [varför det inte](../how-to/determine-non-compliance.md)är kompatibelt. Jämförelsen av definitionen för det utvärderade egenskap svärdet indikerar varför en resurs inte var kompatibel.
   - Om **målvärdet** är fel, ändra princip definitionen.
   - Om det **aktuella värdet** är fel kontrollerar du resurs nytto lasten via `resources.azure.com` .
1. Kontrol lera [fel sökning: tvång inte som förväntat](#scenario-enforcement-not-as-expected) för andra vanliga problem och lösningar.

Om du fortfarande har problem med en duplicerad och anpassad inbyggd princip definition eller anpassad definition, skapar du ett support ärende under redigering av **en princip** för att dirigera problemet korrekt.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: tvång inte som förväntat

#### <a name="issue"></a>Problem

En resurs som förväntas bli påverkad av Azure Policy är inte och det finns ingen post i [Azure aktivitets loggen](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Orsak

Princip tilldelningen har kon figurer ATS [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) för EnforcementMode _inaktive rad_. Även om tvingande läge är inaktiverat tillämpas inte princip påverkan och det finns ingen post i aktivitets loggen.

#### <a name="resolution"></a>Lösning

Följ de här stegen för att felsöka din princip tilldelnings genomförande:

1. Börja med att vänta på rätt tids period för att en utvärdering ska slutföras och efterföljande resultat ska bli tillgängliga i Azure Portal eller SDK. Om du vill starta en ny utvärderings sökning med Azure PowerShell eller REST API, se [utvärderings genomsökning på begäran](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Kontrol lera att tilldelnings parametrarna och tilldelnings omfånget är rätt inställda och att **enforcementMode** har _Aktiver ATS_. 
1. Kontrol lera [princip definitions läget](../concepts/definition-structure.md#mode):
   - Mode all för alla resurs typer.
   - Läget Indexerad om princip definitionen söker efter taggar eller plats.
1. Kontrol lera att resursens omfattning inte är [Exkluderad](../concepts/assignment-structure.md#excluded-scopes) eller [undantagen](../concepts/exemption-structure.md).
1. Kontrol lera att resurs nytto lasten matchar princip logiken. Detta kan göras genom att [fånga in en spårning](../../../azure-portal/capture-browser-trace.md) eller granska arm-mallens egenskaper.
1. Kontrol lera [fel sökning: kompatibiliteten är inte lika förväntat](#scenario-compliance-not-as-expected) för andra vanliga problem och lösningar.

Om du fortfarande har problem med en duplicerad och anpassad inbyggd princip definition eller anpassad definition, skapar du ett support ärende under redigering av **en princip** för att dirigera problemet korrekt.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: nekas av Azure Policy

#### <a name="issue"></a>Problem

Skapandet eller uppdateringen av en resurs nekas.

#### <a name="cause"></a>Orsak

En princip tilldelning till omfånget som din nya eller uppdaterade resurs uppfyller villkoren i en princip definition med en [neka](../concepts/effects.md#deny) -påverkan. Resurs möten dessa definitioner hindras från att skapas eller uppdateras.

#### <a name="resolution"></a>Lösning

Fel meddelandet från en princip tilldelning neka inkluderar princip definitionen och princip tilldelnings-ID: n. Om fel informationen i meddelandet saknas är det också tillgängligt i [aktivitets loggen](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Använd den här informationen om du vill ha mer information för att förstå resurs begränsningarna och justera resurs egenskaperna i din begäran om du vill matcha tillåtna värden.

## <a name="template-errors"></a>Fel i mall

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: funktioner som stöds av principer som bearbetas av mall

#### <a name="issue"></a>Problem

Azure Policy stöder ett antal mallar för Azure Resource Manager mallar (ARM-mallar) som bara är tillgängliga i en princip definition. Resource Manager bearbetar dessa funktioner som en del av en distribution i stället för som en del av en princip definition.

#### <a name="cause"></a>Orsak

Att använda funktioner som stöds, till exempel `parameter()` eller `resourceGroup()` , resulterar i det behandlade resultatet av funktionen vid distributions tiden i stället för att lämna funktionen för princip definitionen och Azure policy motorn att bearbeta.

#### <a name="resolution"></a>Lösning

Om du vill skicka en funktion genom att vara en del av en princip definition kan du undanta hela strängen med `[` så att egenskapen ser ut som `[[resourceGroup().tags.myTag]` . Escape-tecken gör att Resource Manager hanterar värdet som en sträng när mallen bearbetas. Azure Policy placerar sedan funktionen i princip definitionen så att den kan vara dynamisk som förväntat. Mer information finns [i syntax och uttryck i Azure Resource Manager mallar](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-installation-errors"></a>Installations fel för tillägg

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scenario: det går inte att installera med Helm-diagram på lösen ord

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Kommandot Miss lyckas med ett av följande meddelanden:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Orsak

Det genererade lösen ordet innehåller ett kommatecken ( `,` ) som Helm-diagrammet delar upp.

#### <a name="resolution"></a>Lösning

Undanta kommatecken ( `,` ) i lösen ordet när du kör `helm install azure-policy-addon` med ett omvänt snedstreck ( `\` ).

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scenario: det går inte att installera med Helm-diagram eftersom namnet redan finns

#### <a name="issue"></a>Problem

`helm install azure-policy-addon`Kommandot Miss lyckas med följande meddelande:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Orsak

Helm-diagrammet med namnet `azure-policy-addon` har redan installerats eller är delvis installerat.

#### <a name="resolution"></a>Lösning

Följ anvisningarna för att [ta bort Azure policy för Kubernetes-tillägget](../concepts/policy-for-kubernetes.md#remove-the-add-on)och kör sedan kommandot igen `helm install azure-policy-addon` .

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från experter via [Microsoft Q&A](/answers/topics/azure-policy.html).
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
