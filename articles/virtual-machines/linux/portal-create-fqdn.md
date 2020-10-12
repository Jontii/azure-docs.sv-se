---
title: Skapa FQDN för en virtuell dator i Azure Portal
description: Lär dig hur du skapar ett fullständigt kvalificerat domän namn eller FQDN för en virtuell Linux-dator i Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deae22ad0c763e48df053d19beefba6054ed2767
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331479"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Skapa ett fullständigt kvalificerat domän namn i Azure Portal för en virtuell Linux-dator

När du skapar en virtuell dator (VM) i [Azure Portal](https://portal.azure.com)skapas automatiskt en offentlig IP-resurs för den virtuella datorn. Du använder den här IP-adressen för att fjärrans luta till den virtuella datorn. Även om portalen inte skapar ett [fullständigt kvalificerat domän namn eller fullständigt domän namn](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)kan du lägga till en när den virtuella datorn har skapats. I den här artikeln beskrivs stegen för att skapa ett DNS-namn eller fullständigt domän namn.

## <a name="create-a-fqdn"></a>Skapa ett fullständigt domän namn
Den här artikeln förutsätter att du redan har skapat en virtuell dator. Om det behövs kan du [skapa en virtuell dator i portalen](quick-create-portal.md) eller [med Azure CLI](quick-create-cli.md). Följ de här stegen när den virtuella datorn är igång:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Du kan nu fjärrans luta till den virtuella datorn med hjälp av det här DNS-namnet, till exempel med `ssh azureuser@mydns.westus.cloudapp.azure.com` .

## <a name="next-steps"></a>Nästa steg
Nu när den virtuella datorn har en offentlig IP-adress och ett DNS-namn kan du distribuera vanliga program ramverk eller tjänster, till exempel nginx, MongoDB, Docker osv.

Du kan också läsa mer om att [använda Resource Manager](../../azure-resource-manager/management/overview.md) för att få tips om hur du skapar dina Azure-distributioner.

