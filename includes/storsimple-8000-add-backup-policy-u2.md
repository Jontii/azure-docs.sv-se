---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 02274bacb66a33ef54e07bc8113d7db46d4d5296
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187533"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Så här lägger du till en StorSimple-säkerhetskopieringsprincip

1. Gå till din StorSimple-enhet och klicka på **Säkerhetskopieringspolicy**.

2. Klicka på **+ Lägg till princip** i kommandofältet på bladet **Säkerhetskopieringspolicy**.
   
    ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. Utför följande steg på bladet **Skapa säkerhetskopieringspolicy**:
   
   1. **Välj enhet** fylls i automatiskt baserat på den enhet du valt.
   
   2. Ange ett **namn på** säkerhets kopierings principen som innehåller mellan 3 och 150 tecken. När principen har skapats kan du inte byta namn på den.
       
   3. Du kan tilldela volymer till säkerhetskopieringspolicyn genom att välja **Lägg till volymer** och sedan klicka på kryssrutan eller kryssrutorna i tabellistan över volymer för att koppla en eller flera volymer till säkerhetskopieringspolicyn.

       ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Du kan definiera ett schema för säkerhetskopieringspolicyn genom att klicka på **Första schemat** och sedan ändra följande parametrar:

       ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. För **Typ av ögonblicksbild** väljer du **Moln** eller **Lokal**.

       2. Ange säkerhets kopierings frekvensen (ange ett tal och välj sedan **dagar** eller **veckor** i list rutan.

       3. Ange ett kvarhållningsschema.

       4. Ange ett datum och en tid då säkerhetskopieringsprincipen ska börja.

       5. Klicka på **OK** för att definiera schemat.

   5. Klicka på **Skapa** för att skapa en säkerhetskopieringspolicy.

       ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Du meddelas när säkerhetskopieringspolicyn har skapats. Den nya principen visas i tabellvyn på bladet **Säkerhetskopieringspolicy**.

       ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

