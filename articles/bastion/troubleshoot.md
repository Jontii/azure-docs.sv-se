---
title: Felsöka Azure-skydds | Microsoft Docs
description: I den här artikeln får du lära dig hur du felsöker Azure-skydds.
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: f3c142491363f30513877ae4368f291430aa3675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85831938"
---
# <a name="troubleshoot-azure-bastion"></a>Felsöka Azure Bastion

Den här artikeln visar hur du felsöker Azure-skydds.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Det gick inte att skapa en NSG på AzureBastionSubnet

**F:** När jag försöker skapa en NSG i Azure skydds-undernätet visas följande fel meddelande: *"nätverks säkerhets gruppen <NSG name> har inte de regler som krävs för Azure skydds Subnet AzureBastionSubnet"*.

**A:** Om du skapar och använder en NSG för *AzureBastionSubnet*kontrollerar du att du har lagt till följande regler i din NSG. Om du inte lägger till de här reglerna går det inte att skapa eller uppdatera NSG.

1. Kontroll Plans anslutning – inkommande den 443 från GatewayManager
2. Diagnostikloggning och andra – utgående på 443 till AzureCloud (regionala Taggar i denna service tag stöds inte ännu.)
3. Mål-VM – utgående för 3389 och 22 till VirtualNetwork

Det finns ett exempel på NSG-reglerna för referens i [snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).
Mer information finns i [NSG-vägledning för Azure skydds](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Det går inte att använda min SSH-nyckel med Azure skydds

**F:** När jag försöker bläddra i min SSH-nyckelfil får du följande fel meddelande: *"den privata SSH-nyckeln måste börja med-----starta RSA Private-nyckel-----och avslutas med-----End RSA New key-----"*.

**A:** Azure skydds stöder bara RSA SSH-nycklar, vid den här tidpunkten. Se till att du bläddrar i en nyckel fil som är en privat RSA-nyckel för SSH, med offentlig nyckel etablerad på den virtuella mål datorn. 

Du kan till exempel använda följande kommando för att skapa en ny RSA SSH-nyckel:

**ssh-keygen-t RSA-b 4096-C " email@domain.com "**

Utdata:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Det går inte att logga in på en Windows-domänansluten virtuell dator

**F:** Jag kan inte ansluta till den virtuella Windows-datorn som är domänansluten.

**A:** Azure skydds stöder domänanslutna VM-inloggning för användar namn – lösen ords baserad domän inloggning. När du anger domänautentiseringsuppgifter i Azure Portal använder du formatet UPN () i username@domain stället för formatet *domän \ användar namn* för att logga in. Detta stöds för domänanslutna eller hybrid-anslutna (både domänanslutna och Azure AD-anslutna) virtuella datorer. Det finns inte stöd för virtuella Azure AD-anslutna datorer.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Fil överförings problem

**F:** Stöds fil överföring med Azure-skydds?

**A:** Fil överföring stöds inte för tillfället. Vi arbetar med att lägga till stöd.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Svart skärm i Azure Portal

**F:** När jag försöker ansluta med Azure skydds får jag en svart skärm i Azure Portal.

**A:** Detta inträffar när det finns ett problem med nätverks anslutningen mellan webbläsaren och Azure-skydds (din klients Internet brand vägg kan blockera WebSockets-trafik eller liknande), eller mellan Azure-skydds och den virtuella mål datorn. De flesta fall innehåller en NSG som tillämpas antingen på AzureBastionSubnet eller på ditt virtuella mål-undernät som blockerar RDP/SSH-trafiken i det virtuella nätverket. Tillåt WebSocket-trafik på klientens Internet brand vägg och kontrol lera NSG: er på ditt mål-VM-undernät.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [vanliga frågor och svar om skydds](bastion-faq.md).
