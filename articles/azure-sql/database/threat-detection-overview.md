---
title: Advanced Threat Protection
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot i Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 07a39edcb7a5605759ae70a014549863a038de1c
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89437062"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Avancerat skydd för Azure SQL Database, SQL-hanterad instans och Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Avancerat skydd för [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) och [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Avancerat skydd är en del av det [avancerade data säkerhets](advanced-data-security.md) erbjudandet, som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Advanced Threat Protection kan nås och hanteras via den centrala SQL ADS-portalen.

## <a name="overview"></a>Översikt

Avancerat skydd ger ett nytt säkerhets lager som gör det möjligt för kunder att upptäcka och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar om avvikande aktiviteter. Användare får en avisering om misstänkt databas aktiviteter, potentiella sårbarheter och SQL-injektering, samt avvikande databas åtkomst och frågor. Avancerat skydd integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minimera hotet. Avancerat skydd gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhets expert eller hantera avancerade säkerhets övervaknings system.

För en fullständig utrednings erfarenhet rekommenderar vi att du aktiverar granskning, som skriver databas händelser till en Gransknings logg i ditt Azure Storage-konto.  Information om hur du aktiverar granskning finns i [granskning för Azure SQL Database och Azure-Synapse](../../azure-sql/database/auditing-overview.md) eller [granskning för Azure SQL-hanterad instans](../managed-instance/auditing-configure.md).

## <a name="alerts"></a>Aviseringar

Avancerat skydd för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. En lista över aviseringar för Azure SQL Database finns i [aviseringarna för SQL Database och Azure Synapse Analytics (tidigare SQL Data Warehouse) i Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse).

## <a name="explore-detection-of-a-suspicious-event"></a>Utforska identifiering av misstänkt händelse

Du får ett e-postmeddelande när du har identifierat avvikande databas aktiviteter. E-postmeddelandet innehåller information om den misstänkta säkerhets händelsen, inklusive typen av avvikande aktiviteter, databasens namn, Server namn, program namn och händelse tiden. Dessutom innehåller e-postmeddelandet information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.

![Avvikande aktivitets rapport](./media/threat-detection-overview/anomalous_activity_report.png)

1. Klicka på länken **Visa senaste SQL-aviseringar** i e-postmeddelandet för att starta Azure Portal och visa sidan för Azure Security Center aviseringar, som ger en översikt över aktiva hot som har identifierats i databasen.

   ![Aktivitets hot](./media/threat-detection-overview/active_threats.png)

1. Klicka på en avisering om du vill ha mer information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.

   SQL-inmatning är till exempel ett av de vanligaste säkerhets problemen för webb program på Internet som används för att angripa data drivna program. Angripare drar nytta av program sårbarheter för att mata in skadliga SQL-uttryck i program post fält, bryta eller ändra data i databasen. För SQL-inmatnings aviseringar innehåller aviseringens information den sårbara SQL-instruktionen som utnyttjades.

   ![Speciell avisering](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Utforska aviseringar i Azure Portal

Avancerat skydd integrerar sina aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). Live SQL Advanced Threat Protection-paneler i bladet databas och SQL-annonser i Azure Portal spåra statusen för aktiva hot.

Klicka på **Avancerat skydds varning** för att starta sidan Azure Security Center aviseringar och få en översikt över aktiva SQL-hot som har identifierats i databasen.

   ![Avisering om Avancerat skydd](./media/threat-detection-overview/threat_detection_alert.png)

   ![Alert2 för avancerat skydd](./media/threat-detection-overview/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Avancerat skydd i Azure SQL Database & Azure-Synapse](threat-detection-configure.md).
- Läs mer om [Avancerat skydd i Azure SQL Managed instance](../managed-instance/threat-detection-configure.md).
- Läs mer om [Avancerad data säkerhet](advanced-data-security.md).
- Läs mer om [Azure SQL Database granskning](../../azure-sql/database/auditing-overview.md)
- Läs mer om [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Mer information om priser finns på sidan med [Azure SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/)  
