---
title: Konfigurera data vetenskaps miljöer i Azure-team data science process
description: Konfigurera data vetenskaps miljöer på Azure för användning i team data science-processen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6b5571f24cc7acfd35cf2979318110ba2eecbb0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320548"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurera datavetenskapsmiljöer för användning i Team Data Science Process
Team data science-processen använder olika data vetenskaps miljöer för lagring, bearbetning och analys av data. De omfattar Azure Blob Storage, flera typer av virtuella Azure-datorer, HDInsight-kluster (Hadoop) och Azure Machine Learning arbets ytor. Beslutet om vilken miljö som ska användas beror på typen och mängden data som ska modelleras och mål målet för dessa data i molnet. 

* Råd om frågor att tänka på när du fattar det här beslutet finns i [Planera din Azure Machine Learning data vetenskaps miljö](plan-your-environment.md). 
* En förteckning över några av de scenarier som du kan stöta på när du utför avancerad analys finns i [scenarier för team data vetenskaps processen](plan-sample-scenarios.md)

I följande artiklar beskrivs hur du konfigurerar de olika data vetenskaps miljöerna som används av team data science-processen.

* [Azure Storage – konto](../../storage/common/storage-account-create.md)
* [HDInsight-kluster (Hadoop)](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klassisk) arbets yta](../classic/create-workspace.md)

**Microsoft data science Virtual Machine (DSVM)** är också tillgängligt som en virtuell Azure-dator (VM). Den här virtuella datorn är förinstallerad och konfigurerad med flera populära verktyg som ofta används för data analys och maskin inlärning. DSVM finns på både Windows och Linux. Mer information finns i [Introduktion till den molnbaserade data science Virtual Machine för Linux och Windows](../data-science-virtual-machine/overview.md).

Lär dig hur du skapar:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
