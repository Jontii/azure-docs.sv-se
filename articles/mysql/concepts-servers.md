---
title: Server koncept – Azure Database for MySQL
description: Det här avsnittet innehåller överväganden och rikt linjer för att arbeta med Azure Database for MySQL-servrar.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5477aa0c4eb2a66af816b287a8b6d7da8a53952f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534899"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Server koncept i Azure Database for MySQL

Den här artikeln innehåller överväganden och rikt linjer för att arbeta med Azure Database for MySQL-servrar.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Vad är en Azure Database for MySQL-Server?

En Azure Database for MySQL-server är en central administrativ punkt för flera databaser. Det är samma server konstruktion för MySQL som du kanske är van vid i den lokala världen. Mer specifikt är Azure Database for MySQLs tjänsten hanterad, ger prestanda garantier och visar åtkomst och funktioner på server nivå.

En Azure Database for MySQL server:

- Skapas i en Azure-prenumeration.
- Är överordnad resurs för databaser.
- Innehåller ett namn område för databaser.
- Är en behållare med stark livs längd – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Tillhandahåller en anslutnings slut punkt för Server-och databas åtkomst.
- Tillhandahåller omfattning för hanterings principer som gäller för databaserna: inloggning, brand vägg, användare, roller, konfigurationer osv.
- Är tillgänglig i flera versioner. Mer information finns i [Azure Database for MySQL databas versioner som stöds](./concepts-supported-versions.md).

Du kan skapa en eller flera databaser på en Azure Database för MySQL-server. Du kan välja att skapa en enskild databas per server för att använda alla resurser eller för att skapa flera databaser för att dela resurserna. Priserna är strukturerad per server, baserat på konfigurationen av pris nivån, virtuella kärnor och lagring (GB). Mer information finns i [pris nivåer](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hur gör jag för att ansluta och autentisera till en Azure Database for MySQL-Server?

Följande element hjälper till att säkerställa säker åtkomst till din databas.

|     |     |
| :-- | :-- |
| **Autentisering och auktorisering** | Azure Database for MySQL Server stöder inbyggd MySQL-autentisering. Du kan ansluta och autentisera till en server med serverns Administratörs inloggning. |
| **Protokoll** | Tjänsten har stöd för ett Message-baserat protokoll som används av MySQL. |
| **TCP/IP** | Protokollet stöds över TCP/IP och över UNIX-domänsuffix. |
| **Brandvägg** | För att skydda dina data förhindrar en brand Väggs regel all åtkomst till din databas server tills du anger vilka datorer som har behörighet. Se [Azure Database for MySQL server brand Väggs regler](./concepts-firewall-rules.md). |
| **SSL** | Tjänsten har stöd för att tvinga SSL-anslutningar mellan dina program och databas servern.  Se [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL). |

## <a name="stopstart-an-azure-database-for-mysql-preview"></a>Stoppa/starta en Azure Database for MySQL (förhands granskning)

Azure Database for MySQL ger dig möjlighet att **stoppa** servern när den inte används och **Starta** servern när du återupptar aktiviteten. Detta görs i grunden för att spara kostnaderna på databas servrarna och bara betala för resursen när den används. Detta blir ännu mer viktigt för arbets belastningar för utveckling och testning och när du bara använder servern för en del av dagen. När du stoppar servern kommer alla aktiva anslutningar att tas bort. Senare kan du antingen använda [Azure Portal](how-to-stop-start-server.md) eller [CLI](how-to-stop-start-server.md)när du vill ta tillbaka servern online.

När servern är i **stoppat** läge debiteras inte serverns beräkning. Lagrings utrymmet fortsätter dock att faktureras eftersom serverns lagrings utrymme är kvar för att säkerställa att datafiler är tillgängliga när servern startas igen.

> [!IMPORTANT]
> När du **stoppar** servern förblir den kvar i det läget under de kommande 7 dagarna i en storlek. Om du inte **startar** den manuellt under den här tiden startas servern automatiskt i slutet av sju dagar. Du kan välja att **stoppa** det igen om du inte använder servern.

När tids servern stoppas kan inga hanterings åtgärder utföras på servern. För att kunna ändra konfigurations inställningar på-servern måste du [starta-servern](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Begränsningar för stoppa/starta-åtgärd
- Stöds inte med konfigurationer för Läs-replikering (både källa och repliker).

## <a name="how-do-i-manage-a-server"></a>Hur gör jag för att hantera en server?

Du kan hantera Azure Database for MySQL-servrar med hjälp av Azure Portal eller Azure CLI.

## <a name="next-steps"></a>Nästa steg

- En översikt över tjänsten finns i [Azure Database for MySQL översikt](./overview.md)
- Information om vilka resurs kvoter och begränsningar som baseras på din **pris nivå** finns i [pris nivåer](./concepts-pricing-tiers.md)
- Information om hur du ansluter till tjänsten finns i [anslutnings bibliotek för Azure Database for MySQL](./concepts-connection-libraries.md).
