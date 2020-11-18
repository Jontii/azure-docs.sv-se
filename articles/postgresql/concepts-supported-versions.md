---
title: Versioner som stöds – Azure Database for PostgreSQL-enskild server
description: Beskriver de postgres huvud-och del versioner som stöds i Azure Database for PostgreSQL-enskild server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 72d774b4ced6471ff7b355b2cb43c3c9127b5975
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658527"
---
# <a name="supported-postgresql-major-versions"></a>PostgreSQL huvud versioner som stöds

Se [Azure Database for PostgreSQL versions princip](concepts-version-policy.md) för information om support policy.

Azure Database for PostgreSQL stöder för närvarande följande huvud versioner:

## <a name="postgresql-version-11"></a>PostgreSQL-version 11
Den aktuella del versionen är 11,6. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-6.html) .

## <a name="postgresql-version-10"></a>PostgreSQL version 10
Den aktuella del versionen är 10,11. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/10/static/release-10-11.html) .

## <a name="postgresql-version-96"></a>PostgreSQL-version 9,6
Den aktuella del versionen är 9.6.16. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) .

## <a name="postgresql-version-95"></a>PostgreSQL-version 9,5
Den aktuella del versionen är 9.5.20. Läs postgresql- [dokumentationen](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) om du vill veta mer om förbättringar och korrigeringar i den här mindre versionen.

> [!NOTE]
> Om du anpassar med postgres policy för Community- [versioner](https://www.postgresql.org/support/versioning/)kommer Azure Database for PostgreSQL att tas ur bruk Postgres version 9,5 den 11 februari 2021. Mer information och begränsningar finns i [Azure Database for PostgreSQL versions policy](concepts-version-policy.md) .

## <a name="managing-upgrades"></a>Hantera uppgraderingar
PostgreSQL-projektet utfärdar regelbundet mindre versioner för att åtgärda rapporterade buggar. Azure Database for PostgreSQL automatiskt korrigering av servrar med mindre versioner under tjänstens månatliga distributioner. 

Automatiska uppgraderingar på plats för huvud versioner stöds inte. Om du vill uppgradera till nästa högre version kan du 
   * Se olika metoder för att utföra [större versions uppgraderingar med dump och återställning](./how-to-upgrade-using-dump-and-restore.md)
   * Använd [pg_dump och pg_restore](./howto-migrate-using-dump-and-restore.md) för att flytta en databas till en server som skapats med den nya motor versionen
   * Alternativt kan du uppgradera från PostgreSQL 10 till 11 med [Azure Database migration service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)

### <a name="version-syntax"></a>Versions syntax
Innan PostgreSQL version 10 anses den [postgresql versions principen](https://www.postgresql.org/support/versioning/) en _större versions_ uppgradering för att öka det första _eller_ andra numret. 9,5 till 9,6 ansågs till exempel vara en _huvud_ versions uppgradering. Från och med version 10 betraktas bara en ändring i det första talet som en huvud versions uppgradering. Till exempel är 10,0 till 10,1 en _mindre_ versions uppgradering. Version 10 till 11 är en _huvud_ versions uppgradering.

## <a name="next-steps"></a>Nästa steg
Information om PostgreSQL-tillägg som stöds finns i [tillägg-dokumentet](concepts-extensions.md).
