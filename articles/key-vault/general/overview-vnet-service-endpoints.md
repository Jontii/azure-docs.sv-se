---
title: Tjänst slut punkter för virtuella nätverk för Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Lär dig hur tjänst slut punkter i virtuella nätverk för Azure Key Vault ger dig möjlighet att begränsa åtkomsten till ett angivet virtuellt nätverk, inklusive användnings scenarier.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 3b1e71a5aadbe9e6a4fa89d4b3ec0fb6b1d9e6ce
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530462"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Tjänst slut punkter för virtuella nätverk för Azure Key Vault

Med tjänst slut punkter för virtuella nätverk för Azure Key Vault kan du begränsa åtkomsten till ett angivet virtuellt nätverk. Med slut punkterna kan du också begränsa åtkomsten till en lista över IPv4-adress intervall (Internet Protocol version 4). Alla användare som ansluter till nyckel valvet utanför dessa källor nekas åtkomst.

Det finns ett viktigt undantag för den här begränsningen. Om en användare har valt att tillåta betrodda Microsoft-tjänster, kan anslutningar från dessa tjänster göra genom brand väggen. Dessa tjänster omfattar till exempel Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager och Azure Backup. Sådana användare måste ändå presentera en giltig Azure Active Directory-token och måste ha behörigheter (konfigurerade som åtkomst principer) för att utföra den begärda åtgärden. Mer information finns i [tjänst slut punkter för virtuella nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Användningsscenarier

Du kan konfigurera [Key Vault brand väggar och virtuella nätverk](network-security.md) för att neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) som standard. Du kan bevilja åtkomst till trafik från specifika virtuella Azure-nätverk och offentliga Internet IP-adressintervall, så att du kan bygga en säker nätverks gränser för dina program.

> [!NOTE]
> Key Vault brand väggar och regler för virtuella nätverk gäller endast för Key Vaultens [data plan](secure-your-key-vault.md#data-plane-access-control) . Key Vault kontroll Plans åtgärder (t. ex. skapa, ta bort och ändra åtgärder, inställning av åtkomst principer, inställning av brand väggar och virtuella nätverks regler) påverkas inte av brand väggar och virtuella nätverks regler.

Här följer några exempel på hur du kan använda tjänst slut punkter:

* Du använder Key Vault för att lagra krypterings nycklar, program hemligheter och certifikat, och du vill blockera åtkomst till ditt nyckel valv från det offentliga Internet.
* Du vill låsa åtkomsten till ditt nyckel valv så att endast ditt program eller en kort lista med angivna värdar kan ansluta till ditt nyckel valv.
* Du har ett program som körs i det virtuella Azure-nätverket och det här virtuella nätverket är låst för all inkommande och utgående trafik. Programmet måste fortfarande ansluta till Key Vault för att hämta hemligheter eller certifikat, eller använda kryptografiska nycklar.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Key Vault-brandväggar och virtuella nätverk

Här är de steg som krävs för att konfigurera brand väggar och virtuella nätverk. De här stegen gäller oavsett om du använder PowerShell, Azure CLI eller Azure Portal.

1. Aktivera [Key Vault loggning](logging.md) för att se detaljerade åtkomst loggar. Detta hjälper i diagnostik, när brand väggar och virtuella nätverks regler förhindrar åtkomst till ett nyckel valv. (Det här steget är valfritt, men rekommenderas.)
2. Aktivera **tjänst slut punkter för nyckel valv** för virtuella mål nätverk och undernät.
3. Ange brand väggar och virtuella nätverks regler för ett nyckel valv för att begränsa åtkomsten till nyckel valvet från vissa virtuella nätverk, undernät och IPv4-adress intervall.
4. Om det här nyckel valvet måste vara tillgängligt för alla betrodda Microsoft-tjänster aktiverar du alternativet för att tillåta att **betrodda Azure-tjänster** ansluter till Key Vault.

Mer information finns i [konfigurera Azure Key Vault brand väggar och virtuella nätverk](network-security.md).

> [!IMPORTANT]
> När brand Väggs reglerna är aktiva kan användarna bara utföra Key Vault [data Plans](secure-your-key-vault.md#data-plane-access-control) åtgärder när deras begär Anden härstammar från tillåtna virtuella nätverk eller IPv4-adress intervall. Detta gäller även för att komma åt Key Vault från Azure Portal. Även om användarna kan bläddra till ett nyckel valv från Azure Portal, kanske de inte kan lista nycklar, hemligheter eller certifikat om deras klient dator inte finns i listan över tillåtna. Detta påverkar också Key Vault väljare från andra Azure-tjänster. Användarna kanske kan se en lista över nyckel valv, men inte lista nycklar, om brand Väggs reglerna förhindrar sin klient dator.


> [!NOTE]
> Tänk på följande konfigurations begränsningar:
> * Högst 127 virtuella nätverks regler och 127 IPv4-regler är tillåtna. 
> * Små adress intervall som använder prefixlängden "/31" eller "/32" stöds inte. Konfigurera i stället dessa intervall med hjälp av enskilda IP-adressintervall.
> * IP-nätverksadresser tillåts endast för offentliga IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i RFC 1918) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med **10.**, **172.16-31**och **192,168.**.. 
> * Endast IPv4-adresser stöds för tillfället.

## <a name="trusted-services"></a>Betrodda tjänster

Här är en lista över betrodda tjänster som har behörighet att komma åt ett nyckel valv om alternativet **Tillåt betrodda tjänster** är aktiverat.

|Betrodd tjänst|Användnings scenarier som stöds|
| --- | --- |
|Azure Virtual Machines Deployment service|[Distribuera certifikat till virtuella datorer från kund hanterade Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Azure Resource Manager mall distributions tjänst|[Skicka säkra värden under distributionen](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Application Gateway v2 SKU|[TLS-avslutning med Key Vault certifikat](/azure/application-gateway/key-vault-certs)|
|Azure Disk Encryption volym krypterings tjänst|Tillåt åtkomst till BitLocker Key (Windows VM) eller DM-lösen fras (Linux VM) och nyckel krypterings nyckel under distributionen av virtuella datorer. Detta gör att [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|Tillåt säkerhets kopiering och återställning av relevanta nycklar och hemligheter under Azure Virtual Machines säkerhets kopiering med [Azure Backup](../../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online & SharePoint Online|Tillåt åtkomst till kund nyckel för Azure Storage tjänst kryptering med [kund nyckel](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Tillåt åtkomst till klient nyckeln för [Azure information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Distribuera Azure Web App-certifikat via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent datakryptering med Bring Your Own Key stöd för Azure SQL Database och Azure Synapse Analytics (tidigare SQL Data Warehouse)](../../azure-sql/database/transparent-data-encryption-byok-overview.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](../../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Kryptering av data i Azure Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) med en kundhanterad nyckel.|
|Azure Databricks|[Snabb, enkel och gemensam Apache Spark-baserad analys tjänst](../../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[Distribuera certifikat för anpassad domän från Key Vault med MSI](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Hämta autentiseringsuppgifter för data lager i Key Vault från Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Tillåt åtkomst till ett nyckel valv för kund hanterade nyckel scenarier](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure Service Bus|[Tillåt åtkomst till ett nyckel valv för kund hanterade nyckel scenarier](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Azure Import/Export| [Använda Kundhanterade nycklar i Azure Key Vault för import/export-tjänsten](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)
|Azure Container Registry|[Register kryptering med Kundhanterade nycklar](../../container-registry/container-registry-customer-managed-keys.md)<br><br/>[Överföra artefakter till ett annat register](../../container-registry/container-registry-transfer-images.md)

> [!NOTE]
> Du måste ställa in relevanta Key Vault åtkomst principer så att motsvarande tjänster får åtkomst till Key Vault.

## <a name="next-steps"></a>Nästa steg

* [Skydda ditt nyckel valv](secure-your-key-vault.md)
* [Konfigurera Azure Key Vault brand väggar och virtuella nätverk](network-security.md)
