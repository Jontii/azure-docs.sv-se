---
title: Nyheter i Azure Backup
description: Lär dig mer om nya funktioner i Azure Backup.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ba29ddea5d5f096640f2bfc012c44ab06bb3e131
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309672"
---
# <a name="whats-new-in-azure-backup"></a>Nyheter i Azure Backup

Azure Backup kan ständigt förbättra och lansera nya funktioner som förbättrar skyddet av dina data i Azure. De här nya funktionerna utökar ditt data skydd till nya arbets belastnings typer, förbättrar säkerheten och förbättrar tillgängligheten för dina säkerhets kopierings data. De lägger också till nya funktioner för hantering, övervakning och automatisering.

Du kan lära dig mer om de nya versionerna genom att skriva bok märken på den här sidan eller genom [att prenumerera på uppdateringar här](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Uppdaterings Sammanfattning

- November 2020
  - [Azure Resource Manager mall för säkerhets kopiering av Azure-filresursen (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Stegvisa säkerhets kopieringar för SAP HANA databaser på virtuella Azure-datorer](#incremental-backups-for-sap-hana-databases)
- September 2020
  - [Backup Center](#backup-center)
  - [Säkerhetskopiera Azure Database for PostgreSQL](#backup-azure-database-for-postgresql)
  - [Säkerhets kopiering och återställning av selektiv disk](#selective-disk-backup-and-restore)
  - [Återställning mellan regioner för SQL Server och SAP HANA databaser på virtuella Azure-datorer](#cross-region-restore-for-sql-server-and-sap-hana)
  - [Stöd för säkerhets kopiering av virtuella datorer med upp till 32 diskar](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Förenklad konfigurations upplevelse för säkerhets kopiering för SQL i virtuella Azure-datorer](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Säkerhetskopiera SAP HANA i RHEL Azure Virtual Machines](#backup-sap-hana-in-rhel-azure-virtual-machines)
  - [ZRS (Zone redundant Storage) för säkerhets kopierings data](#zone-redundant-storage-zrs-for-backup-data)
  - [Mjuk borttagning för SQL Server och SAP HANA arbets belastningar i virtuella Azure-datorer](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-resource-manager-template-for-afs-backup"></a>Azure Resource Manager mall för AFS-säkerhetskopiering

Azure Backup har nu stöd för att konfigurera säkerhets kopiering för befintliga Azure-filresurser med en Azure Resource Manager ARM-mall (ARM). Mallen konfigurerar skydd för en befintlig Azure-filresurs genom att ange lämplig information för Recovery Services valvet och säkerhets kopierings principen. Du kan också skapa ett nytt Recovery Services valv och en säkerhets kopierings princip och registrera lagrings kontot som innehåller fil resursen i Recovery Services-valvet.

Mer information finns i [Azure Resource Manager mallar för Azure Backup](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases"></a>Stegvisa säkerhets kopieringar för SAP HANA databaser

Azure Backup stöder nu stegvisa säkerhets kopieringar för SAP HANA databaser som finns på virtuella Azure-datorer. På så sätt kan du snabbare och mer kostnads effektivt säkerhetskopiera dina SAP HANA data.

Mer information finns i [olika alternativ som är tillgängliga när du skapar en säkerhets kopierings princip](sap-hana-faq-backup-azure-vm.md#policy) och [hur du skapar en säkerhets kopierings princip för SAP HANA databaser](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center"></a>Backup Center

Azure Backup har aktiverat en ny inbyggd hanterings funktion för att hantera hela reserv fastigheten från en central konsol. Backup Center ger dig möjlighet att övervaka, hantera och optimera data skydd i stor skala på ett enhetligt sätt som överensstämmer med Azures inbyggda hanterings upplevelser.

Med Backup Center får du en sammanställd vy över dina lager över prenumerationer, platser, resurs grupper, valv och till och med-klienter med Azure Lighthouse. Backup Center är också ett åtgärds Center där du kan utlösa säkerhets kopierings aktiviteter, till exempel konfigurera säkerhets kopiering, återställning, skapande av principer eller valv, allt från en enda plats. Dessutom kan du, med sömlös integrering till Azure Policy, nu styra din miljö och spåra kompatibiliteten från ett säkerhets kopierings perspektiv. Inbyggda Azure-principer som är särskilt utformade för Azure Backup också att du kan konfigurera säkerhets kopior i stor skala.

Mer information finns i [Översikt över säkerhets kopierings Center](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql"></a>Säkerhetskopiera Azure Database for PostgreSQL

Azure Backup och Azure Database-tjänster har tillsammans skapat en lösning för säkerhets kopiering i företags klass för Azure PostgreSQL (nu i för hands version). Nu kan du uppfylla dina krav på data skydd och efterlevnad med en kundkontrollerad säkerhets kopierings princip som möjliggör kvarhållning av säkerhets kopior i upp till 10 år. Med detta har du detaljerad kontroll över hur du hanterar säkerhets kopierings-och återställnings åtgärderna på den enskilda databas nivån. På samma sätt kan du återställa över PostgreSQL-versioner eller till Blob Storage utan problem.

Mer information finns i [Azure Database for PostgreSQL säkerhets kopiering](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Säkerhets kopiering och återställning av selektiv disk

Azure Backup stöder säkerhets kopiering av alla diskar (operativ system och data) i en virtuell dator tillsammans med säkerhets kopierings lösningen för virtuella datorer. Nu kan du använda säkerhets kopierings-och återställnings funktionen selektiva diskar för att säkerhetskopiera en delmängd av data diskarna i en virtuell dator. Detta ger en effektiv och kostnads effektiv lösning för dina säkerhets kopierings-och återställnings behov. Varje återställnings punkt innehåller bara de diskar som ingår i säkerhets kopierings åtgärden.

Mer information finns i [selektiv säkerhets kopiering av diskar och återställning för virtuella Azure-datorer](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana"></a>Återställning mellan regioner för SQL Server och SAP HANA

Med introduktionen av återställning mellan regioner kan du nu initiera återställningar i en sekundär region på för att undvika problem med verkliga stillestånd i en primär region för din miljö. Detta gör att den sekundära regionen återställer helt kund kontrollerad. Azure Backup använder säkerhetskopierade data som replikeras till den sekundära regionen för sådana återställningar.

Förutom stöd för återställning mellan regioner för virtuella Azure-datorer har funktionen nu utökats för att återställa SQL och SAP HANA databaser på virtuella Azure-datorer.

Mer information finns i [återställning mellan regioner för SQL-databaser](restore-sql-database-azure-vm.md#cross-region-restore) och [återställning mellan regioner för SAP HANA databaser](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Stöd för säkerhets kopiering av virtuella datorer med upp till 32 diskar

Fram till nu har Azure Backup stöd för 16 hanterade diskar per virtuell dator. Azure Backup har nu stöd för säkerhets kopiering av upp till 32 hanterade diskar per virtuell dator.

Mer information finns i [support mat ris för VM-lagring](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Enklare säkerhets kopierings konfiguration för SQL i virtuella Azure-datorer

Det är nu ännu enklare att konfigurera säkerhets kopieringar för dina SQL Server i virtuella Azure-datorer med inbyggd säkerhets kopierings konfiguration i den virtuella dator rutan i Azure Portal. Med bara några få steg kan du aktivera säkerhets kopiering av SQL Server för att skydda alla befintliga databaser samt de som läggs till i framtiden.

Mer information finns i [säkerhetskopiera en SQL Server från fönstret VM](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines"></a>Säkerhetskopiera SAP HANA RHEL Azure Virtual Machines

Azure Backup är en inbyggd lösning för säkerhets kopiering för Azure och är BackInt certifierad av SAP. Azure Backup har nu lagt till stöd för Red Hat Enterprise Linux (RHEL), ett av de vanligaste Linux-operativsystemen som kör SAP HANA.

Mer information finns i [support mat ris för säkerhets kopiering av SAP HANA databas](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data"></a>ZRS (Zone redundant Storage) för säkerhets kopierings data

Azure Storage ger en bra balans mellan hög prestanda, hög tillgänglighet och hög data återhämtning med de varierande alternativen för redundans. Med Azure Backup kan du utöka de här fördelarna med att säkerhetskopiera data även med alternativ för att lagra säkerhets kopior i lokalt redundant lagring (LRS) och Geo-redundant lagring (GRS). Nu finns det ytterligare hållbarhets alternativ med stöd för ZRS (Zone redundant Storage).

Mer information finns i [set Storage redundans for the Recovery Services Vault](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Mjuk borttagning för SQL Server och SAP HANA arbets belastningar

Problem med säkerhets problem, t. ex. skadlig kod, utpressnings program vara och intrång, ökar. De här säkerhets problemen kan vara dyra, i termer av både Money och data. För att skydda mot sådana angrepp tillhandahåller Azure Backup säkerhetsfunktioner som hjälper dig att skydda säkerhets kopierings data även efter borttagning.

En sådan funktion är mjuk borttagning. Med mjuk borttagning, även om en obehörig aktör tar bort en säkerhets kopia (eller säkerhetskopierade data tas bort av misstag), bevaras säkerhets kopierings data i ytterligare 14 dagar, vilket gör att det inte går att återställa säkerhets kopierings objekt utan data förlust. De ytterligare 14 dagarna för kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar inga kostnader för dig.

Förutom stöd för mjuk borttagning för virtuella Azure-datorer, är SQL Server och SAP HANA arbets belastningar i virtuella Azure-datorer också skyddade med mjuk borttagning.

Mer information finns i [mjuk borttagning för SQL Server i Azure VM och SAP HANA i Azure VM-arbetsbelastningar](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Nästa steg

- [Azure Backup vägledning och bästa praxis](guidance-best-practices.md)
