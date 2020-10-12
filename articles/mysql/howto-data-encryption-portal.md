---
title: Data kryptering – Azure Portal – Azure Database for MySQL
description: Lär dig hur du konfigurerar och hanterar data kryptering för dina Azure Database for MySQL med hjälp av Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 201459f4a7d2d23b384435493d6272e569698933
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90887160"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Data kryptering för Azure Database for MySQL med hjälp av Azure Portal

Lär dig hur du använder Azure Portal för att konfigurera och hantera data kryptering för din Azure Database for MySQL.

## <a name="prerequisites-for-azure-cli"></a>Krav för Azure CLI

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
* I Azure Key Vault skapar du ett nyckel valv och en nyckel som ska användas för en kundhanterad nyckel.
* Nyckel valvet måste ha följande egenskaper för att kunna användas som kundhanterad nyckel:
  * [Mjuk borttagning](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Rensa skyddad](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Nyckeln måste ha följande attribut för att användas som en kundhanterad nyckel:
  * Inget förfallo datum
  * Inte inaktiverat
  * Kan utföra get-, wrap-och unwrap Key-åtgärder

## <a name="set-the-right-permissions-for-key-operations"></a>Ange rätt behörigheter för viktiga åtgärder

1. I Key Vault väljer du **åtkomst principer**  >  **Lägg till åtkomst princip**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

2. Välj **nyckel behörigheter**och välj **Hämta**, **Radbryt**, packa upp och **huvudobjektet**, vilket är namnet på **MySQL-servern**. Om ditt Server huvud namn inte finns i listan över befintliga huvud konton måste du registrera det. Du uppmanas att registrera ditt Server huvud namn när du försöker konfigurera data kryptering för första gången, och det Miss lyckas.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

3. Välj **Spara**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ange data kryptering för Azure Database for MySQL

1. I Azure Database for MySQL väljer du **data kryptering** för att ställa in den Kundhanterade nyckeln.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

2. Du kan antingen välja ett nyckel valv och nyckel par eller ange en nyckel identifierare.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

3. Välj **Spara**.

4. Starta om servern för att se till att alla filer (inklusive temporära filer) är helt krypterade.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Använda data kryptering för återställning eller replik servrar

När Azure Database for MySQL har krypterats med en kunds hanterade nyckel som lagras i Key Vault krypteras även alla nyligen skapade kopior av servern. Du kan göra den här nya kopian antingen via en lokal åtgärd eller geo-återställning eller via en replikering (lokal/över region) åtgärd. För en krypterad MySQL-server kan du använda följande steg för att skapa en krypterad återställd Server.

1. Välj **översikts**  >  **återställning**på servern.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

   Om du använder en aktive rad server väljer du **replikering**under rubriken **Inställningar** .

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

2. När återställningen är klar är den nya servern som skapas krypterad med den primära serverns nyckel. Funktionerna och alternativen på servern är dock inaktiverade och servern är inte tillgänglig. Detta förhindrar all data manipulering eftersom den nya serverns identitet ännu inte har fått behörighet att komma åt nyckel valvet.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

3. Om du vill göra servern tillgänglig igen, verifierar du nyckeln på den återställda servern. Välj nyckel för att verifiera **data kryptering**  >  **Revalidate key**.

   > [!NOTE]
   > Det första försöket att validera kommer att Miss lyckas eftersom den nya serverns tjänst huvud namn måste ges åtkomst till nyckel valvet. Om du vill generera tjänstens huvud namn väljer du **revalidate Key**, som visar ett fel, men som genererar tjänstens huvud namn. Därefter kan du se [de här stegen](#set-the-right-permissions-for-key-operations) tidigare i den här artikeln.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

   Du måste ge nyckel valvet åtkomst till den nya servern.

4. När du har registrerat tjänstens huvud namn, verifierar nyckeln igen och servern fortsätter med sin normala funktion.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Skärm bild av Key Vault med åtkomst principer och Lägg till åtkomst princip markerad":::

## <a name="next-steps"></a>Nästa steg

 Mer information om data kryptering finns i [Azure Database for MySQL data kryptering med kundhanterad nyckel](concepts-data-encryption-mysql.md).
