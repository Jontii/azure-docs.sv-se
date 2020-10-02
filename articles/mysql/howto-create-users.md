---
title: Skapa databaser och användare – Azure Database for MySQL
description: I den här artikeln beskrivs hur du kan skapa nya användar konton för att interagera med en Azure Database for MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 10/1/2020
ms.openlocfilehash: ed653ffb6fc24a75170d51d345c0c64724ff90f1
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651029"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql-server"></a>Skapa databaser och användare i Azure Database for MySQL Server

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

I den här artikeln beskrivs hur du kan skapa användare i en Azure Database for MySQL-server.

> [!NOTE]
> Kompensations fri kommunikation
>
> Microsoft stöder en mängd olika och införlivande miljöer. Den här artikeln innehåller referenser till ordet _slav_. Microsofts [stil guide för en kostnads fri kommunikation](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) känner igen detta som ett undantags ord. Ordet används i den här artikeln för konsekvens eftersom det är det ord som visas i program varan. När program varan har uppdaterats för att ta bort ordet uppdateras den här artikeln som en justering.
>

När du först skapade din Azure Database for MySQL angav du ett användar namn och lösen ord för Server Administratörs inloggning. För mer information, kan du följa [snabb](quickstart-create-mysql-server-database-using-azure-portal.md)starten. Du kan hitta inloggnings användar namnet för Server administratören från Azure Portal.

Server administratörs användaren får vissa behörigheter för servern enligt listan: 

   VÄLJ, INFOGA, UPPDATERA, TA BORT, SKAPA, SLÄPPA, LÄSA IN, BEARBETA, REFERERA, INDEXERA, ÄNDRA, VISA DATABASER, SKAPA TEMPORÄRA TABELLER, LÅS TABELLER, KÖRA, REPLIKERING SLAVAR, REPLIKERINGSPARTNER, SKAPA, VISA, VISA, SKAPA RUTIN, ÄNDRA RUTIN, SKAPA ANVÄNDARE, HÄNDELSE, UTLÖSARE


När Azure Database for MySQL-servern har skapats kan du använda det första server administratörs användar kontot för att skapa ytterligare användare och ge administratörs åtkomst till dem. Server administratörs kontot kan också användas för att skapa mindre privilegierade användare som har åtkomst till enskilda databas scheman.

> [!NOTE]
> Superprivilegiumet och DBA-rollen stöds inte. Granska [privilegierna](concepts-limits.md#privileges--data-manipulation-support) i artikeln begränsningar för att förstå vad som inte stöds i tjänsten.<br><br>
> Lösen ords-plugin-program som "validate_password" och "caching_sha2_password" stöds inte av tjänsten.

## <a name="how-to-create-database-with-non-admin-user-in-azure-database-for-mysql"></a>Så här skapar du en databas med icke-administratörs användare i Azure Database for MySQL

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta server namn och inloggnings information från sidan Server **Översikt** eller sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra.
   Om du är osäker på hur du ansluter, se hur du använder MySQL Workbench för att [ansluta och fråga efter data för en enskild server](./connect-workbench.md) eller [ansluta och fråga efter data för flexibel Server](./flexible-server/connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt placeholder-värdet `db_user` med ditt avsedda nya användar namn och plats hållarens värde `testdb` med ditt eget databas namn.

   Den här SQL-koden skapar en ny databas med namnet testdb i exempel syfte. Sedan skapar den en ny användare i MySQL-tjänsten och beviljar alla behörigheter till det nya databasschemat (testdb \* ) för den användaren.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Verifiera bidragen i databasen.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Logga in på servern och ange den angivna databasen med hjälp av det nya användar namnet och lösen ordet. I det här exemplet visas mysql-kommandoraden. Med det här kommandot uppmanas du att ange lösen ordet för användar namnet. Ersätt ditt eget Server namn, databas namn och användar namn.

# <a name="single-server"></a>[Enskild server](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
# <a name="flexible-server"></a>[Flexibel server](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Så här skapar du ytterligare administratörs användare i Azure Database for MySQL

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta server namn och inloggnings information från sidan Server **Översikt** eller sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra.
   Om du är osäker på hur du ansluter, se [Använd MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt det nya användar namnet med plats hållarens värde `new_master_user` . Den här syntaxen beviljar de angivna behörigheterna för alla databas scheman (*.*) till användar namnet (new_master_user i det här exemplet).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Verifiera bidragen

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Alla Azure Database for MySQL-servrar skapas med en användare som kallas "azure_superuser". Detta är ett system konto som har skapats av Microsoft för att hantera-servern för att utföra övervakning, säkerhets kopiering och annat regelbundet underhåll. On-Call-tekniker kan också använda det här kontot för att få åtkomst till servern under en incident med certifikatautentisering och måste begära åtkomst med JIT-processer (just-in-Time).

## <a name="next-steps"></a>Nästa steg

Öppna brand väggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta:
- [Skapa och hantera brand Väggs regler på en enskild server](howto-manage-firewall-using-portal.md) 
- [ Skapa och hantera brand Väggs regler på en flexibel Server](flexible-server/how-to-connect-tls-ssl.md)

Mer information om hantering av användar konton finns i MySQL produkt dokumentation för [hantering av användar konton](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [beviljande av syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html)och [privilegier](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
