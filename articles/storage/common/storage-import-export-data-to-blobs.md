---
title: Använda Azure import/export för att överföra data till Azure-blobbar | Microsoft Docs
description: Lär dig hur du skapar import-och export jobb i Azure Portal för att överföra data till och från Azure-blobbar.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 6d12c0ce0df44c37f4e7df49df2c11301513917c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514213"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Använd Azure import/export-tjänsten för att importera data till Azure Blob Storage

Den här artikeln innehåller stegvisa instruktioner för hur du använder Azure import/export-tjänsten för att importera stora mängder data på ett säkert sätt till Azure Blob Storage. För att kunna importera data till Azure-blobbar kräver tjänsten att du levererar krypterade disk enheter som innehåller dina data till ett Azure-datacenter.  

## <a name="prerequisites"></a>Krav

Innan du skapar ett import jobb för att överföra data till Azure Blob Storage bör du noggrant granska och slutföra följande lista över nödvändiga komponenter för den här tjänsten.
Du måste:

* Ha en aktiv Azure-prenumeration som kan användas för import/export-tjänsten.
* Ha minst ett Azure Storage konto med en lagrings behållare. Se listan över [lagrings konton och lagrings typer som stöds för import/export-tjänsten](storage-import-export-requirements.md).
  * Information om hur du skapar ett nytt lagrings konto finns i [så här skapar du ett lagrings konto](storage-account-create.md).
  * Information om lagrings behållare finns i [skapa en lagrings behållare](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Har tillräckligt många diskar av [typer som stöds](storage-import-export-requirements.md#supported-disks).
* Ha ett Windows-system som kör en [operativ system version som stöds](storage-import-export-requirements.md#supported-operating-systems).
* Aktivera BitLocker på Windows-systemet. Se [hur du aktiverar BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Ladda ned den senaste WAImportExport-versionen 1](https://www.microsoft.com/download/details.aspx?id=42659) på Windows-systemet. Den senaste versionen av verktyget har säkerhets uppdateringar för att tillåta en extern skydds funktion för BitLocker-nyckeln och den uppdaterade funktionen för upplåsnings läge.

  * Zippa upp till standardmappen `waimportexportv1` . Exempelvis `C:\WaImportExportV1`.
* Ha ett FedEx-/DHL-konto. Om du vill använda en annan operatör än FedEx/DHL kontaktar du Azure Data Box drifts team på `adbops@microsoft.com` .  
  * Kontot måste vara giltigt, måste ha ett saldo och måste ha funktioner för retur leverans.
  * Generera ett spårnings nummer för export jobbet.
  * Varje jobb ska ha ett separat spårningsnummer. Det finns inte stöd för flera jobb med samma spårningsnummer.
  * Om du inte har ett transport företags konto går du till:
    * [Skapa ett FedEX-konto](https://www.fedex.com/en-us/create-account.html)eller
    * [Skapa ett DHL-konto](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Steg 1: Förbered enheterna

I det här steget skapas en journal fil. Journal filen lagrar grundläggande information, till exempel enhets serie nummer, krypterings nyckel och lagrings konto information.

Utför följande steg för att förbereda enheterna.

1. Anslut dina disk enheter till Windows-systemet via SATA-anslutningar.
2. Skapa en enda NTFS-volym på varje enhet. Tilldela volymen en enhets beteckning. Använd inte mountpoints.
3. Aktivera BitLocker-kryptering på NTFS-volymen. Om du använder ett Windows Server-system kan du använda instruktionerna i [så här aktiverar du BitLocker på Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Kopiera data till krypterad volym. Använd dra och släpp-eller Robocopy eller ett sådant kopierings verktyg. En journal fil (*. jrn*) skapas i samma mapp som du kör verktyget.

   Om enheten är låst och du måste låsa upp enheten kan stegen för att låsa upp vara olika beroende på ditt användnings fall.

   * Om du har lagt till data till en förkrypterad enhet (WAImportExport-verktyget inte användes för kryptering) använder du BitLocker-nyckeln (ett numeriskt lösen ord som du anger) i popup-fönstret för att låsa upp enheten.

   * Om du har lagt till data till en enhet som har krypterats med WAImportExport-verktyget använder du följande kommando för att låsa upp enheten:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Öppna ett PowerShell-eller kommando rads fönster med administratörs behörighet. Kör följande kommando för att ändra katalogen till den zippade mappen:

    `cd C:\WaImportExportV1`
6. Kör följande kommando för att hämta BitLocker-nyckeln till enheten:

    `manage-bde -protectors -get <DriveLetter>:`
7. Kör följande kommando för att förbereda disken. **Beroende på data storleken kan detta ta flera timmar till dagar.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    En journal fil skapas i samma mapp som du körde verktyget. Två andra filer skapas också – en *. XML* -fil (mapp där du kör verktyget) och en *drive-manifest.xml* fil (mapp där data finns).

    De parametrar som används beskrivs i följande tabell:

    |Alternativ  |Beskrivning  |
    |---------|---------|
    |/j     |Namnet på Journal filen med fil namns tillägget. jrn. En journal fil skapas per enhet. Vi rekommenderar att du använder diskens serie nummer som Journal fil namn.         |
    |/ID     |Sessions-ID. Använd ett unikt sessions nummer för varje instans av kommandot.      |
    |/t:     |Enhets beteckningen för den disk som ska levereras. Till exempel enhet `D` .         |
    |/bk:     |Enhetens BitLocker-nyckel. Det numeriska lösen ordet från utdata från `manage-bde -protectors -get D:`      |
    |/srcdir:     |Enhets beteckningen för den disk som ska levereras följt av `:\` . Exempelvis `D:\`.         |
    |/dstdir:     |Namnet på mål behållaren i Azure Storage.         |
    |/blobtype:     |Det här alternativet anger vilken typ av blobbar som du vill importera data till. För block-blobbar är detta `BlockBlob` och för sid blobbar `PageBlob` .         |
    |/skipwrite:     |Alternativet som anger att det inte finns några nya data som behöver kopieras och befintliga data på disken måste förberedas.          |
    |/enablecontentmd5:     |Alternativet när det är aktiverat, säkerställer att MD5 beräknas och anges som `Content-md5` egenskap för varje blob. Använd bara det här alternativet om du vill använda `Content-md5` fältet när data har överförts till Azure. <br> Det här alternativet påverkar inte data integritets kontrollen (som inträffar som standard). Inställningen ökar den tid det tar att ladda upp data till molnet.          |
8. Upprepa föregående steg för varje disk som måste levereras. En journal fil med det angivna namnet skapas för varje körning av kommando raden.

    > [!IMPORTANT]
    > * Tillsammans med journal filen `<Journal file name>_DriveInfo_<Drive serial ID>.xml` skapas även en fil i samma mapp som verktyget finns i. XML-filen används i stället för journal filen när du skapar ett jobb om journal filen är för stor.

## <a name="step-2-create-an-import-job"></a>Steg 2: skapa ett import jobb

Utför följande steg för att skapa ett import jobb i Azure Portal.

1. Logga in på https://portal.azure.com/ .
2. Gå till **alla tjänster > lagring > import/export-jobb**.

    ![Gå till import/export-jobb](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klicka på **skapa import/export-jobb**.

    ![Klicka på Skapa import/export-jobb](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. I **grunderna**:

   * Välj **Importera till Azure**.
   * Ange ett beskrivande namn för import jobbet. Använd namnet för att följa förloppet för dina jobb.
       * Namnet får bara innehålla gemena bokstäver, siffror och bindestreck.
       * Namnet måste börja med en bokstav och får inte innehålla blank steg.
   * Välj en prenumeration.
   * Ange eller Välj en resurs grupp.  

     ![Skapa import jobb – steg 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. I **jobb information**:

   * Ladda upp filerna för enhets journaler som du fick under steget förberedelse av enhet. Om `waimportexport.exe version1` användes överför du en fil för varje enhet som du har för berett. Om journal filens storlek överskrider 2 MB kan du använda den som `<Journal file name>_DriveInfo_<Drive serial ID>.xml` skapats med journal filen.
   * Välj det mål lagrings konto där data ska finnas.
   * DropOff-platsen fylls i automatiskt baserat på den region där det valda lagrings kontot finns.

   ![Skapa import jobb – steg 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. I **information om retur leverans**:

   * Välj operatören i list rutan. Om du vill använda en annan operatör än FedEx/DHL väljer du ett befintligt alternativ i list rutan. Kontakta Azure Data Box drifts teamet på `adbops@microsoft.com`  med information om den operatör som du planerar att använda.
   * Ange ett giltigt transportföretags konto nummer som du har skapat med transport företaget. Microsoft använder det här kontot för att skicka tillbaka enheterna till dig när ditt import jobb har slutförts. Om du inte har ett konto nummer skapar du ett [FedEx](https://www.fedex.com/us/oadr/) -eller [DHL](https://www.dhl.com/) -konto.
   * Ange ett fullständigt och giltigt kontakt namn, telefon, e-postadress, gatuadress, ort, post, delstat/provins och land/region.

       > [!TIP]
       > Ange en grupp-e-postadress i stället för att ange en e-postadress för en enskild användare. Detta säkerställer att du får meddelanden även om en administratör lämnar.

     ![Skapa import jobb – steg 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. I **sammanfattningen**:

   * Granska jobb informationen som visas i sammanfattningen. Anteckna jobb namnet och leverans adressen till Azure-datacenter för att leverera diskar tillbaka till Azure. Den här informationen används senare på frakt etiketten.
   * Skapa import jobbet genom att klicka på **OK** .

     ![Skapa import jobb – steg 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Steg 3 (valfritt): Konfigurera kundens hanterad nyckel

Hoppa över det här steget och gå till nästa steg om du vill använda Microsofts hanterade nyckel för att skydda dina BitLocker-nycklar för enheterna. Om du vill konfigurera en egen nyckel för att skydda BitLocker-nyckeln följer du anvisningarna i [Konfigurera Kundhanterade nycklar med Azure Key Vault för Azure import/export i Azure Portal](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Steg 4: leverera enheterna

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Steg 5: uppdatera jobbet med spårnings information

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Steg 6: kontrol lera att data laddas upp till Azure

Spåra jobbet till slutfört. När jobbet har slutförts kontrollerar du att dina data har laddats upp till Azure. Ta bara bort lokala data när du har verifierat att överföringen har slutförts.

## <a name="next-steps"></a>Nästa steg

* [Visa jobb-och enhets status](storage-import-export-view-drive-status.md)
* [Granska import/export-krav](storage-import-export-requirements.md)
