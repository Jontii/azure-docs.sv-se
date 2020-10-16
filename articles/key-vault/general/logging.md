---
title: Azure Key Vault loggning | Microsoft Docs
description: Lär dig hur du övervakar åtkomst till dina nyckel valv genom att aktivera loggning för Azure Key Vault, som sparar information i ett Azure Storage-konto som du anger.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 162e40555e11dff716b58eec4b1168728257693e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131181"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault-loggning

När du har skapat ett eller flera nyckel valv vill du förmodligen övervaka hur och när nyckel valven nås, och av vem. Du kan göra detta genom att aktivera loggning för Azure Key Vault, som sparar information i ett Azure Storage-konto som du anger. Steg för steg-anvisningar om hur du konfigurerar detta finns i [så här aktiverar du Key Vault loggning](howto-logging.md).

Du kan komma åt loggnings informationen 10 minuter (högst) efter nyckel valvs åtgärden. Oftast är informationen dock tillgänglig snabbare än så.  Det är upp till dig att hantera loggarna i ditt lagringskonto:

* Använd standardåtkomstmetoder i Azure för att skydda loggarna genom att begränsa vem som kan komma åt dem.
* Ta bort loggar som du inte vill behålla i ditt lagringskonto.

Översiktlig information om Key Vault finns i [Vad är Azure Key Vault?](overview.md). Information om var Key Vault finns på [sidan med priser](https://azure.microsoft.com/pricing/details/key-vault/). Information om hur du använder [Azure Monitor för Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview).

## <a name="interpret-your-key-vault-logs"></a>Tolka Key Vault-loggarna

När du aktiverar loggning skapas en ny behållare med namnet **Insights-logs-auditevent** automatiskt för det angivna lagrings kontot. Du kan använda samma lagrings konto för att samla in loggar för flera nyckel valv.

Enskilda blobbar lagras som text, formaterad som en JSON-blobb. Nu ska vi titta på en exempel logg post. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

I följande tabell visas fält namn och beskrivningar:

| Fältnamn | Beskrivning |
| --- | --- |
| **time** |Datum och tid i UTC. |
| **resourceId** |Azure Resource Manager resurs-ID. För Key Vault loggar är detta alltid Key Vault resurs-ID. |
| **operationName** |Namnet på åtgärden, som beskrivs i nästa tabell. |
| **operationVersion** |REST API version som begärs av klienten. |
| **kategori** |Typ av resultat. För Key Vault loggar `AuditEvent` är det enda, tillgängliga värdet. |
| **resultType** |Resultat av den REST API begäran. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |En ytterligare beskrivning av resultatet, om en sådan är tillgänglig. |
| **. Durationms** |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Detta omfattar inte nätverksfördröjningen, så den tid du mäter på klientsidan kanske inte stämmer med den här tiden. |
| **callerIpAddress** |IP-adressen för den klient som gjorde begäran. |
| **correlationId** |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| **Autentiseringsidentitet** |Identitet från den token som angavs i REST API begäran. Detta är vanligt vis en "användare", "tjänstens huvud namn" eller kombinationen "användare + appId", som i fallet med en begäran som resulterar från en Azure PowerShell-cmdlet. |
| **egenskaperna** |Information som varierar beroende på åtgärd (**operationName**). I de flesta fall innehåller det här fältet klient information (den användar agent sträng som skickas av klienten), exakt REST API begär ande-URI och HTTP-statuskod. När ett objekt returneras som ett resultat av en begäran (till exempel nyckel **skapa** eller **VaultGet**), innehåller det även nyckel-URI (as `id` ), valv-URI eller hemlig URI. |

Värdena för **operationName** -fältet är i *ObjectVerb* -format. Exempel:

* Alla Key Vault-åtgärder har `Vault<action>` formatet, till exempel `VaultGet` och `VaultCreate` .
* Alla viktiga åtgärder har `Key<action>` formatet, till exempel `KeySign` och `KeyList` .
* Alla hemliga åtgärder har `Secret<action>` formatet, till exempel `SecretGet` och `SecretListVersions` .

I följande tabell visas **operationName** -värdena och motsvarande REST API-kommandon:

### <a name="operation-names-table"></a>Åtgärds namn tabell

| operationName | REST API kommando |
| --- | --- |
| **Autentisering** |Autentisera via Azure Active Directory slut punkt |
| **VaultGet** |[Hämta information om ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Skapa eller uppdatera ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Ta bort ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Uppdatera ett nyckelvalv](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Visa en lista med alla nyckelvalv i en resursgrupp](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Skapa en nyckel](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Hämta information om en nyckel](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importera en nyckel till ett valv](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Säkerhetskopiera en nyckel](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Ta bort en nyckel](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Återställa en nyckel](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Signera med en nyckel](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verifiera med en nyckel](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Omsluta en nyckel](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Ta bort en nyckelomslutning](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Kryptera med en nyckel](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Dekryptera med en nyckel](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Uppdatera en nyckel](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Visa en lista med nycklarna i ett valv](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Visa en lista över versionerna av en nyckel](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Skapa en hemlighet](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Få en hemlighet](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Uppdatera en hemlighet](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Ta bort en hemlighet](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Visa en lista över hemligheterna i ett valv](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Visa en lista över versionerna av en hemlighet](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | Händelse publicering har ändrats för valv åtkomst princip |
| **SecretNearExpiryEventGridNotification** |Hemlig händelse som är nära utgångs händelse publicerad |
| **SecretExpiredEventGridNotification** |Hemligt utgånget händelse publicerat |
| **KeyNearExpiryEventGridNotification** |Nyckel för nära förfallo händelser publicerad |
| **KeyExpiredEventGridNotification** |Händelse publicerad för nyckel utgångna |
| **CertificateNearExpiryEventGridNotification** |Certifikat nära utgångs händelse publicerad |
| **CertificateExpiredEventGridNotification** |Utgånget certifikat-händelse publicerat |

## <a name="use-azure-monitor-logs"></a>Använda Azure Monitor-loggar

Du kan använda Key Vault-lösningen i Azure Monitor loggar för att granska Key Vault `AuditEvent` loggar. I Azure Monitor loggar använder du logg frågor för att analysera data och få den information du behöver. 

Mer information, inklusive hur du konfigurerar detta finns i [Azure Key Vault i Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Så här aktiverar du Key Vault loggning](howto-logging.md)
- En själv studie kurs som använder Azure Key Vault i ett .NET-webb program finns i [använda Azure Key Vault från ett webb program](tutorial-net-create-vault-azure-web-app.md).
- Programmeringsreferenser finns i [utvecklarguiden för Azure Key Vault](developers-guide.md).
- En lista över Azure PowerShell 1,0-cmdletar för Azure Key Vault finns i [Azure Key Vault-cmdletar](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
