---
title: 'Använd Azure CLI för filer & ACL: er i Azure Data Lake Storage Gen2'
description: Använd Azure CLI för att hantera kataloger och åtkomst kontrol listor för kataloger och kataloger (ACL) i lagrings konton som har ett hierarkiskt namn område.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: ee461193be81297c6577ce4c264cabbf08e72417
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359450"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Använd Azure CLI för att hantera kataloger, filer och ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder [CLI (Azure Command-Line Interface)](https://docs.microsoft.com/cli/azure/) för att skapa och hantera kataloger, filer och behörigheter i lagrings konton som har ett hierarkiskt namn område. 

[Exempel](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Ge feedback](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](data-lake-storage-quickstart-create-account.md) anvisningarna för att skapa en.
> * Azure CLI-version `2.6.0` eller högre.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Kontrol lera att du har rätt version av Azure CLI installerad

1. Öppna [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), eller om du har [installerat](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Kontrol lera att den version av Azure CLI som har installerats är `2.6.0` eller högre genom att använda följande kommando.

   ```azurecli
    az --version
   ```
   Om din version av Azure CLI är lägre än `2.6.0` kan du installera en senare version. Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Anslut till kontot

1. Om du använder Azure CLI lokalt kan du köra inloggnings kommandot.

   ```azurecli
   az login
   ```

   Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

   Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal. Logga sedan in med dina konto uppgifter i webbläsaren.

   Mer information om olika autentiseringsmetoder finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](../common/authorize-data-operations-cli.md).

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

> [!NOTE]
> Exemplet som presenteras i den här artikeln visar Azure Active Directory (AD) auktorisering. Mer information om autentiseringsmetoder finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](../common/authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Skapa en container

En behållare fungerar som ett fil system för dina filer. Du kan skapa en med hjälp av `az storage fs create` kommandot. 

I det här exemplet skapas en behållare med namnet `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Visa behållar egenskaper

Du kan skriva ut egenskaperna för en behållare till-konsolen med hjälp av `az storage fs show` kommandot.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Lista behållarens innehåll

Lista innehållet i en katalog med hjälp av `az storage fs file list` kommandot.

I det här exemplet visas innehållet i en behållare med namnet `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Ta bort en container

Ta bort en behållare med hjälp av `az storage fs delete` kommandot.

I det här exemplet tas en behållare med namnet `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog referens med hjälp av `az storage fs directory create` kommandot. 

I det här exemplet läggs en katalog `my-directory` till som heter i en behållare med namnet `my-file-system` som finns i ett konto med namnet `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Visa katalog egenskaper

Du kan skriva ut egenskaperna för en katalog till-konsolen med hjälp av `az storage fs directory show` kommandot.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Byta namn på eller flytta en katalog

Byt namn på eller flytta en katalog med hjälp av `az storage fs directory move` kommandot.

I det här exemplet byter namn på en katalog från namnet `my-directory` till namnet `my-new-directory` i samma behållare.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

I det här exemplet flyttas en katalog till en behållare med namnet `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog med hjälp av `az storage fs directory delete` kommandot.

Det här exemplet tar bort en katalog med namnet `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Kontrol lera om en katalog finns

Ta reda på om en angiven katalog finns i behållaren med hjälp av `az storage fs directory exists` kommandot.

Det här exemplet visar om en katalog med namnet `my-directory` finns i `my-file-system` behållaren. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Ladda ned från en katalog

Hämta en fil från en katalog med hjälp av `az storage fs file download` kommandot.

I det här exemplet hämtas en fil med namnet `upload.txt` från en katalog med namnet `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Lista innehållet i en katalog med hjälp av `az storage fs file list` kommandot.

I det här exemplet visas innehållet i en katalog med namnet `my-directory` som finns i `my-file-system` behållaren för ett lagrings konto med namnet `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Ladda upp en fil till en katalog

Ladda upp en fil till en katalog med hjälp av `az storage fs directory upload` kommandot.

I det här exemplet överförs en fil med namnet `upload.txt` till en katalog med namnet `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Visa fil egenskaper

Du kan skriva ut egenskaperna för en fil till-konsolen med hjälp av `az storage fs file show` kommandot.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Byta namn på eller flytta en fil

Byt namn på eller flytta en fil med hjälp av `az storage fs file move` kommandot.

I det här exemplet byter namn på en fil från namnet `my-file.txt` till namnet `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Ta bort en fil

Ta bort en fil med hjälp av `az storage fs file delete` kommandot.

Det här exemplet tar bort en fil med namnet `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="manage-access-control-lists-acls"></a>Hantera åtkomst kontrol listor (ACL: er)

Du kan hämta, ange och uppdatera åtkomst behörigheter för kataloger och filer.

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera kommandon kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll i Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Hämta en ACL

Hämta ACL för en **katalog** med hjälp av `az storage fs access show` kommandot.

Det här exemplet hämtar ACL: en för en katalog och skriver sedan ut ACL: en till-konsolen.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Hämta åtkomst behörigheterna för en **fil** med hjälp av `az storage fs access show` kommandot. 

Det här exemplet hämtar ACL: en för en fil och skriver sedan ut ACL: en till-konsolen.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Följande bild visar utdata när du har hämtat ACL för en katalog.

![Hämta ACL-utdata](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

I det här exemplet har ägande användaren Läs-, skriv-och körnings behörighet. Den ägande gruppen har bara Läs-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Ange en ACL

Använd `az storage fs access set` kommandot för att ange ACL för en **katalog**. 

I det här exemplet anges ACL: en för en katalog för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

I det här exemplet anges *standard* -ACL: en för en katalog för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Använd `az storage fs access set` kommandot för att ange ACL för en **fil**. 

I det här exemplet anges ACL: en för en fil för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Följande bild visar utdata när du har angett ACL för en fil.

![Hämta ACL-utdata 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

I det här exemplet har ägande användare och ägande grupp bara Läs-och Skriv behörighet. Alla andra användare har Skriv-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Uppdatera en ACL

Ett annat sätt att ange den här behörigheten är att använda `az storage fs access set` kommandot. 

Uppdatera ACL: en för en katalog eller fil genom `-permissions` att ange parametern till kort form för en ACL.

I det här exemplet uppdateras ACL: en för en **katalog**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

I det här exemplet uppdateras ACL: en för en **fil**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Du kan också uppdatera den ägande användaren och gruppen av en katalog eller fil genom att ange `--owner` parametrarna eller i `group` enhets-ID: t eller användarens huvud namn (UPN) för en användare. 

I det här exemplet ändras ägaren till en katalog. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Det här exemplet ändrar ägaren till en fil. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="set-an-acl-recursively"></a>Ange en ACL rekursivt

Du kan lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt. Mer information finns i [ange åtkomst kontrol listor (ACL) rekursivt för Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Se även

* [Exempel](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
* [Lämna feedback](https://github.com/Azure/azure-cli-extensions/issues)
* [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)


