---
title: Koncept – säkerhets kontroller för Azure våren Cloud service
description: Använd säkerhets kontroller som är inbyggda i Azure våren Cloud service.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1472da4c87dc4579a30290460fb7811cf228be47
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892479"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Säkerhetskontroller för Azure Spring Cloud-tjänsten

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Säkerhets kontroller är inbyggda i moln tjänsten Azure våren.

En säkerhets kontroll är en kvalitet eller funktion i en Azure-tjänst som bidrar till tjänstens möjlighet att förhindra, identifiera och reagera på säkerhets risker.  För varje kontroll använder vi *Ja* eller *Nej* för att ange om den finns för närvarande för tjänsten.  Vi använder *saknas* för en kontroll som inte är tillämplig för tjänsten. 

**Säkerhets kontroller för data skydd**

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Kryptering på Server sidan i vila: Microsoft-hanterade nycklar | Yes | Användare som överfört källa och artefakter, konfigurations Server inställningar, appinställningar och data i beständig lagring lagras i Azure Storage, som automatiskt krypterar innehållet i vila.<br><br>Config server cache, binärfiler för körbara filer som skapats utifrån Uppladdad källa och program loggar under programmets livs längd sparas till Azure Managed disk, som automatiskt krypterar innehållet i vila.<br><br>Behållar avbildningar som skapats från användarens överförda källa sparas i Azure Container Registry som automatiskt krypterar avbildnings innehållet i vila. | [Azure Storage-kryptering av vilande data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Kryptering på Server sidan av Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Lagrings avbildnings lagring i Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Kryptering i tillfällig | Yes | Användar programmets offentliga slut punkter använder HTTPS för inkommande trafik som standard. |  |
| Krypterade API-anrop | Yes | Hanterings anrop för att konfigurera Azure våren Cloud service sker via Azure Resource Manager-anrop via HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**Säkerhets kontroller för nätverks åtkomst**

| Säkerhets kontroll | Ja/nej | Kommentarer | Dokumentation |
|:-------------|:-------|:-------------------------------|:----------------------|
| Service tag | Yes | Använd **AzureSpringCloud** service tag för att definiera utgående nätverks åtkomst kontroller i [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) eller [Azure-brandvägg](https://docs.microsoft.com/azure/firewall/service-tags)för att tillåta trafik till Azure våren Cloud-program.<br><br>*Obs:* För närvarande är det bara ny Azure våren Cloud Service-instans som skapats efter 2020/07/14 som stöder **AzureSpringCloud** service tag. | [Tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |

## <a name="next-steps"></a>Nästa steg

* [Snabb start: Distribuera ditt första Azure våren Cloud-program](spring-cloud-quickstart.md)
