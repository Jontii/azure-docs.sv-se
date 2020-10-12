---
title: Servrar-Azure Database for MariaDB
description: Det här avsnittet innehåller överväganden och rikt linjer för att arbeta med Azure Database for MariaDB-servrar.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "79527800"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Server koncept i Azure Database for MariaDB
Den här artikeln innehåller överväganden och rikt linjer för att arbeta med Azure Database for MariaDB-servrar.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Vad är en Azure Database for MariaDB-Server?

En Azure Database for MariaDB-Server är en central administrativ punkt för flera databaser. Det är samma MariaDB Server-konstruktion som du kanske är van vid i den lokala världen. Mer specifikt är Azure Database for MariaDBs tjänsten hanterad, ger prestanda garantier och visar åtkomst och funktioner på server nivå.

En Azure Database for MariaDB Server:

- Skapas i en Azure-prenumeration.
- Är överordnad resurs för databaser.
- Innehåller ett namn område för databaser.
- Är en behållare med stark livs längd – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Tillhandahåller en anslutnings slut punkt för Server-och databas åtkomst.
- Tillhandahåller omfattning för hanterings principer som gäller för databaserna: inloggning, brand vägg, användare, roller, konfigurationer osv.
- Är tillgänglig i MariaDB-motor version 10,2. Mer information finns i [Azure Database for MariaDB databas versioner som stöds](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser på en Azure Database for MariaDB-server. Du kan välja att skapa en enskild databas per server för att använda alla resurser eller för att skapa flera databaser för att dela resurserna. Priserna är strukturerad per server, baserat på konfigurationen av pris nivån, virtuella kärnor och lagring (GB). Mer information finns i [pris nivåer](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hur gör jag för att säkra en Azure Database for MariaDB-Server?

Följande element hjälper till att säkerställa säker åtkomst till din databas.

|||
| :--| :--|
| **Autentisering och auktorisering** | Azure Database for MariaDB Server stöder inbyggd MySQL-autentisering. Du kan ansluta och autentisera till en server med serverns Administratörs inloggning. |
| **Protokoll** | Tjänsten har stöd för ett Message-baserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet stöds över TCP/IP och över UNIX-domänsuffix. |
| **Brandvägg** | För att skydda dina data förhindrar en brand Väggs regel all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Se [Azure Database for MariaDB Server brand Väggs regler](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten har stöd för att tvinga SSL-anslutningar mellan dina program och databas servern. Se [Konfigurera SSL-anslutning i ditt program för att ansluta till Azure Database for MariaDB på ett säkert sätt](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Hur gör jag för att hantera en server?
Du kan hantera Azure Database for MariaDB-servrar med hjälp av Azure Portal eller Azure CLI.

## <a name="next-steps"></a>Nästa steg
- En översikt över tjänsten finns i [Azure Database for MariaDB översikt](./overview.md)
- Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån**finns i [tjänst nivåer](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
