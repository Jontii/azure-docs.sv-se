---
title: Installera Visual Studio 2019
description: Installera Visual Studio och SQL Server Development Tools (SSDT) för Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 073efd41c754b0d28e0bdab712f692f100d3316e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289522"
---
# <a name="getting-started-with-visual-studio-2019"></a>Komma igång med Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) är ett enda verktyg som gör att du kan göra följande:

- Ansluta, fråga och utveckla program
- Använd en objekt Utforskare för att visuellt utforska alla objekt i din data modell, inklusive tabeller, vyer, lagrade procedurer och så vidare.
- Generera T-SQL Data Definition Language-skript (DDL) för dina objekt
- Utveckla ditt informations lager med hjälp av en tillstånds-baserad metod med SSDT databas projekt
- Integrera ditt databas projekt med käll kontroll system som git med Azure databaser
- Konfigurera kontinuerliga integrerings-och distributions pipeliner med Automation-servrar som Azure DevOps

## <a name="install-visual-studio-2019"></a>Installera Visual Studio 2019

Se [Ladda ned Visual studio 2019](https://visualstudio.microsoft.com/downloads/) för att ladda ned och installera visual Studio **16,3 och senare**. Under installationen väljer du arbets belastningen lagring och bearbetning av data. Installation av fristående SSDT krävs inte längre i Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funktioner som inte stöds i SSDT

Det finns tillfällen när funktions versioner för Synapse SQL inte innehåller stöd för SSDT. Följande funktioner stöds för närvarande inte:


- [Arbets belastnings hantering](sql-data-warehouse-workload-management.md) -arbets belastnings grupper och klassificerare
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (inklusive tabell värdes funktioner)
  - Skicka in ett [support ärende eller rösta](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) för att få den funktion som stöds.
- [Dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - Skicka in ett [support ärende eller rösta](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) för att få den funktion som stöds.
- Tabeller med en [identitets kolumn](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)

## <a name="next-steps"></a>Nästa steg

Nu när du har den senaste versionen av SSDT är du redo att [ansluta](sql-data-warehouse-query-visual-studio.md) till din SQL-pool.
