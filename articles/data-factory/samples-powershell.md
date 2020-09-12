---
title: Azure PowerShell exempel för Azure Data Factory
description: Azure PowerShell exempel – skript som hjälper dig att skapa och hantera data fabriker.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 6df400e7ce4b66a1589a0c44089148b33ab88638
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439069"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Azure PowerShell exempel för Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Följande tabell innehåller länkar till exempel Azure PowerShell skript för Azure Data Factory.

| Skript | Beskrivning  |
|---|---|
|**Kopiera data**||
|[Kopiera blobbar från en mapp till en annan mapp i ett Azure-Blob Storage](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet kopierar blobbar från en mapp i Azure Blob Storage till en annan mapp i samma Blob Storage. |
|[Kopiera data från SQL Server till Azure Blob Storage](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet kopierar data från en SQL Server-databas till Azure Blob Storage. |
|[Mass kopiering](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här exemplet på PowerShell-skriptet kopierar data från flera tabeller i en databas i Azure SQL Database till Azure Synapse Analytics (tidigare SQL Data Warehouse). |
|[Stegvis kopia](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här exemplet på PowerShell-skriptet läser bara in nya eller uppdaterade poster från ett käll data lager till ett data lager för mottagare efter den första fullständiga kopian av data från källan till mottagaren. |
|**Omvandla data**||
|[Transformera data med ett Spark-kluster](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet transformerar data genom att köra ett program i ett Spark-kluster. |
|**Lyft och Shift SSIS-paket till Azure**||
|[Skapa Azure SSIS Integration Runtime](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Det här PowerShell-skriptet etablerar en Azure-SSIS integration runtime som kör SQL Server Integration Services-paket (SSIS) i Azure. |



