---
title: Skydd mot hot för Azure Key Vault
description: I den här artikeln förklaras hur du konfigurerar Avancerat skydd för Azure Key Vault i Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 7025b091b83a56b7b3df18c3f42cb84b163560bb
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89277773"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Skydd mot Azure Key Vault (för hands version)

Avancerat skydd för Azure Key Vault ger ytterligare ett lager med säkerhets information. Det här verktyget identifierar potentiellt skadliga försök att komma åt eller utnyttja Key Vault-konton. Med det inbyggda avancerade skydds skyddet i Azure Security Center kan du åtgärda hot utan att vara säkerhets expert, och utan att lära dig fler säkerhets övervaknings system.

När Security Center identifierar avvikande aktivitet visas aviseringar. Den skickar också prenumerations administratören med information om den misstänkta aktiviteten och rekommendationerna för att undersöka och åtgärda de identifierade hoten.

## <a name="configure-threat-protection-from-security-center"></a>Konfigurera skydd mot hot från Security Center

Som standard är Avancerat skydd aktiverat för alla dina Key Vault-konton när du prenumererar på Security Center standard pris nivån. Mer information finns i [prissättning](security-center-pricing.md).

Så här aktiverar eller inaktiverar du skyddet för en speciell prenumeration:

1. I det vänstra fönstret i Security Center väljer du **pris & inställningar**.

1. Välj prenumerationen med de lagrings konton som du vill aktivera eller inaktivera hot skydd för.

1. Välj **pris nivå**.

1. Från gruppen **Välj pris nivå efter resurs typ** letar du upp raden med **nyckel valv** och väljer **aktive** rad eller **inaktive**rad.

    [![Aktivera eller inaktivera Avancerat skydd för Key Vault i Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Välj **Spara**.


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och inaktiverar Avancerat skydd mot Azure Key Vault. 

Information om relaterade material finns i följande artiklar:

- [Skydd mot hot i Azure Security Center](threat-protection.md)– i den här artikeln beskrivs säkerhets aviserings källorna i Azure Security Center.
- [Key Vault säkerhets aviseringar](alerts-reference.md#alerts-azurekv)– avsnittet Key Vault i referens tabellen för alla Azure Security Center aviseringar