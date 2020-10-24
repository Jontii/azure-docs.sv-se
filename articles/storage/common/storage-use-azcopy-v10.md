---
title: Kopiera eller flytta data till Azure Storage med AzCopy v10 | Microsoft Docs
description: AzCopy är ett kommando rads verktyg som du kan använda för att kopiera data till, från eller mellan lagrings konton. Den här artikeln hjälper dig att ladda ned AzCopy, ansluta till ditt lagringskonto och sedan överföra filer.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b1d25ae127d9a732225859a09622bb057c348e28
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488494"
---
# <a name="get-started-with-azcopy"></a>Kom igång med AzCopy

AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar eller filer till eller från ett lagringskonto. Den här artikeln hjälper dig att ladda ned AzCopy, ansluta till ditt lagringskonto och sedan överföra filer.

> [!NOTE]
> AzCopy **v10** är den version av AzCopy som stöds för tillfället.
>
> Om du behöver använda en tidigare version av AzCopy kan du läsa avsnittet [använda den tidigare versionen av AzCopy](#previous-version) i den här artikeln.

<a id="download-and-install-azcopy"></a>

## <a name="download-azcopy"></a>Ladda ned AzCopy

Börja med att ladda ned den körbara filen AzCopy v10 till valfri katalog på datorn. AzCopy v10 är bara en körbar fil, så det finns inget att installera.

- [Windows 64-bitars](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bitars](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux x86-64](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Filerna komprimeras som en zip-fil (Windows och Mac) eller en tar-fil (Linux). Information om hur du laddar ned och dekomprimerar filen tar i Linux finns i dokumentationen för din Linux-distribution.

> [!NOTE]
> Om du vill kopiera data till och från [Azure Table Storage](/azure/storage/tables/table-storage-overview) -tjänsten installerar du [AzCopy version 7,3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Köra AzCopy

Det blir enklare om du lägger till katalogplatsen för den körbara AzCopy-filen i din systemsökväg. På så sätt kan du skriva `azcopy` från valfri katalog i systemet.

Om du väljer att inte lägga till katalogen AzCopy i sökvägen måste du ändra katalogerna till platsen för din AzCopy-körbara fil och skriva `azcopy` eller `.\azcopy` i Windows PowerShell-Kommandotolken.

Om du vill se en lista över kommandon skriver du `azcopy -h` och trycker sedan på RETUR-tangenten.

Om du vill veta mer om ett speciellt kommando inkluderar du bara namnet på kommandot (till exempel: `azcopy list -h` ).

> [!div class="mx-imgBorder"]
> ![Infogad hjälp](media/storage-use-azcopy-v10/azcopy-inline-help.png)


För att hitta detaljerad referens dokumentation för varje kommando och kommando parameter, se [AzCopy](storage-ref-azcopy.md)

> [!NOTE] 
> Som ägare till ditt Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data. Innan du kan göra allt meningsfullt med AzCopy måste du bestämma hur du ska ange autentiseringsuppgifter för lagrings tjänsten. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Välj hur du tillhandahåller autentiseringsuppgifter

Du kan ange autentiseringsuppgifter för auktorisering genom att använda Azure Active Directory (AD) eller genom att använda en SAS-token (signatur för delad åtkomst).

Använd den här tabellen som en guide:

| Lagringstyp | För närvarande stöds metoden för auktorisering |
|--|--|
|**Blob Storage** | Azure AD och SAS |
|**Blob Storage (hierarkiskt namn område)** | Azure AD och SAS |
|**File Storage** | Endast SAS |

### <a name="option-1-use-azure-active-directory"></a>Alternativ 1: Använd Azure Active Directory

Med hjälp av Azure Active Directory kan du ange autentiseringsuppgifter en gång i stället för att behöva lägga till en SAS-token i varje kommando.  

> [!NOTE]
> Om du planerar att kopiera blobbar mellan lagrings konton i den aktuella versionen måste du lägga till en SAS-token till varje käll-URL. Du kan bara utelämna SAS-token från mål-URL: en. Exempel finns i [Kopiera blobbar mellan lagrings konton](storage-use-azcopy-blobs.md).

Den behörighets nivå som du behöver baseras på om du planerar att ladda upp filer eller bara hämta dem.

Om du bara vill hämta filer kontrollerar du att [lagrings-BLOB-dataläsaren](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) har tilldelats till din användar identitet, hanterad identitet eller tjänstens huvud namn.

> Användar identiteter, hanterade identiteter och tjänstens huvud namn är varje typ av *säkerhets objekt*, så vi använder termen *säkerhets objekt* för resten av den här artikeln.

Om du vill ladda upp filer kontrollerar du att någon av dessa roller har tilldelats ditt säkerhets objekt:

- [Storage Blob Data-deltagare](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)
- [Storage Blob Data-ägare](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

De här rollerna kan tilldelas till säkerhets objekt i alla dessa omfattningar:

- Behållare (fil system)
- Lagringskonto
- Resursgrupp
- Prenumeration

Information om hur du verifierar och tilldelar roller finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Tänk på att Azure Role-tilldelningar kan ta upp till fem minuter att sprida.

Du behöver inte ha någon av dessa roller tilldelade till ditt säkerhets objekt om ditt säkerhets objekt läggs till i åtkomst kontrol listan (ACL) för mål behållaren eller katalogen. I ACL: en måste ditt säkerhets objekt ha Skriv behörighet för mål katalogen och köra behörigheten för behållaren och varje överordnad katalog.

Läs mer i [åtkomst kontroll i Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autentisera en användar identitet

När du har kontrollerat att din användar identitet har fått den behörighet som krävs, öppnar du en kommando tolk, skriver följande kommando och trycker sedan på RETUR-tangenten.

```azcopy
azcopy login
```

Om du får ett fel meddelande kan du prova med klient-ID för den organisation som lagrings kontot tillhör.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Ersätt `<tenant-id>` plats hållaren med klient-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal.

Det här kommandot returnerar en autentiseringsnyckel och URL: en för en webbplats. Öppna webbplatsen, ange koden och välj sedan knappen **Nästa** .

![Skapa en container](media/storage-use-azcopy-v10/azcopy-login.png)

Ett inloggnings fönster visas. I det fönstret loggar du in på ditt Azure-konto med hjälp av dina autentiseringsuppgifter för Azure-kontot. När du har loggat in kan du stänga webbläsarfönstret och börja använda AzCopy.

<a id="service-principal"></a>

#### <a name="authenticate-a-service-principal"></a>Autentisera ett huvud namn för tjänsten

Det här är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användar åtgärder, särskilt när du kör lokalt. Om du planerar att köra AzCopy på virtuella datorer som körs i Azure är det enklare att administrera en hanterad tjänst identitet. Mer information finns i avsnittet [autentisera en hanterad identitet](#managed-identity) i den här artikeln.

Innan du kör ett skript måste du logga in interaktivt minst en tid så att du kan ange AzCopy med autentiseringsuppgifterna för tjänstens huvud namn.  Dessa autentiseringsuppgifter lagras i en säker och krypterad fil så att ditt skript inte behöver ange den känsliga informationen.

Du kan logga in på ditt konto med hjälp av en klient hemlighet eller genom att använda lösen ordet för ett certifikat som är kopplat till tjänstens huvud namn för appens registrering.

Mer information om hur du skapar tjänstens huvud namn finns i [så här gör du: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](/azure/active-directory/develop/howto-create-service-principal-portal).

Om du vill veta mer om tjänstens huvud namn i allmänhet, se [program-och tjänst huvud objekt i Azure Active Directory](/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Använda en klient hemlighet

Börja med att ställa in `AZCOPY_SPA_CLIENT_SECRET` miljövariabeln på klient hemligheten för tjänstens huvud namn för appens registrering.

> [!NOTE]
> Se till att ange det här värdet från kommando tolken och inte i miljö variabel inställningarna för ditt operativ system. På så sätt är värdet bara tillgängligt för den aktuella sessionen.

Det här exemplet visar hur du kan göra detta i PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Överväg att använda en prompt som du ser i det här exemplet. På så sätt visas inte lösen ordet i konsolens kommando historik.  

Skriv sedan följande kommando och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Ersätt `<application-id>` plats hållaren med program-ID: t för tjänstens huvud namn för appens registrering. Ersätt `<tenant-id>` plats hållaren med klient-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal. 

##### <a name="using-a-certificate"></a>Använda ett certifikat

Om du föredrar att använda dina egna autentiseringsuppgifter för auktorisering kan du ladda upp ett certifikat till appens registrering och sedan använda det certifikatet för att logga in.

Förutom att ladda upp ditt certifikat till din app-registrering måste du också ha en kopia av certifikatet som har sparats på datorn eller den virtuella dator där AzCopy ska köras. Den här kopian av certifikatet bör vara i. PFX eller. PEM-format och måste innehålla den privata nyckeln. Den privata nyckeln bör vara lösenordsskyddad. Om du använder Windows och ditt certifikat bara finns i ett certifikat Arkiv, måste du exportera certifikatet till en PFX-fil (inklusive den privata nyckeln). Vägledning finns i [export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)

Ställ sedan in `AZCOPY_SPA_CERT_PASSWORD` miljövariabeln på certifikatets lösen ord.

> [!NOTE]
> Se till att ange det här värdet från kommando tolken och inte i miljö variabel inställningarna för ditt operativ system. På så sätt är värdet bara tillgängligt för den aktuella sessionen.

Det här exemplet visar hur du kan utföra den här uppgiften i PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Skriv sedan följande kommando och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Ersätt `<path-to-certificate-file>` plats hållaren med den relativa eller fullständigt kvalificerade sökvägen till certifikat filen. AzCopy sparar sökvägen till det här certifikatet, men det sparar inte en kopia av certifikatet, så se till att hålla certifikatet på plats. Ersätt `<tenant-id>` plats hållaren med klient-ID: t för den organisation som lagrings kontot tillhör. Om du vill hitta klient-ID: t väljer du **Azure Active Directory > egenskaper > katalog-ID** i Azure Portal.

> [!NOTE]
> Överväg att använda en prompt som du ser i det här exemplet. På så sätt visas inte lösen ordet i konsolens kommando historik. 

<a id="managed-identity"></a>

#### <a name="authenticate-a-managed-identity"></a>Autentisera en hanterad identitet

Det här är ett bra alternativ om du planerar att använda AzCopy inuti ett skript som körs utan användar interaktion och skriptet körs från en virtuell Azure-dator (VM). När du använder det här alternativet behöver du inte lagra några autentiseringsuppgifter på den virtuella datorn.

Du kan logga in på ditt konto genom att använda en systemomfattande hanterad identitet som du har aktiverat på den virtuella datorn, eller genom att använda klient-ID, objekt-ID eller resurs-ID för en användardefinierad hanterad identitet som du har tilldelat till den virtuella datorn.

Mer information om hur du aktiverar en systemomfattande hanterad identitet eller skapar en användardefinierad hanterad identitet finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Använda en systemomfattande hanterad identitet

Kontrol lera först att du har aktiverat en systemomfattande hanterad identitet på den virtuella datorn. Se [systemtilldelad hanterad identitet](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Skriv sedan följande kommando i kommando konsolen och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Använda en användardefinierad hanterad identitet

Kontrol lera först att du har aktiverat en användardefinierad hanterad identitet på den virtuella datorn. Se [användarens tilldelade hanterade identitet](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Skriv sedan något av följande kommandon i kommando konsolen och tryck sedan på RETUR-tangenten.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Ersätt `<client-id>` plats hållaren med klient-ID: t för den användarspecifika hanterade identiteten.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Ersätt `<object-id>` plats hållaren med objekt-ID: t för den användarspecifika hanterade identiteten.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Ersätt `<resource-id>` plats hållaren med resurs-ID för den användare som tilldelats den hanterade identiteten.

### <a name="option-2-use-a-sas-token"></a>Alternativ 2: Använd en SAS-token

Du kan lägga till en SAS-token för varje käll-eller mål-URL som används i dina AzCopy-kommandon.

Det här exempel kommandot kopierar data rekursivt från en lokal katalog till en BLOB-behållare. En fiktiv SAS-token läggs till i slutet av för behållar-URL: en.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Mer information om SAS-token och hur du hämtar en finns i [använda signaturer för delad åtkomst (SAS)](/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Överföra filer

När du har autentiserat din identitet eller fått en SAS-token kan du börja överföra filer.

Du hittar exempel kommandon i någon av de här artiklarna.

- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)

- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)

- [Överföra data med AzCopy och Amazon S3-buckets](storage-use-azcopy-s3.md)

- [Överföra data med AzCopy och Azure Stack Storage](/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Använda AzCopy i ett skript

### <a name="obtain-a-static-download-link"></a>Hämta en statisk nedladdnings länk

Med tiden kommer [nedladdnings länken](#download-and-install-azcopy) för AzCopy att peka på nya versioner av AzCopy. Om skriptet laddar ned AzCopy kan skriptet sluta fungera om en nyare version av AzCopy ändrar de funktioner som skriptet är beroende av.

Undvik dessa problem genom att hämta en statisk (ändra) länk till den aktuella versionen av AzCopy. På så sätt laddar skriptet ned samma exakta version av AzCopy varje gång den körs.

Hämta länken genom att köra det här kommandot:

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `curl -s -D- https://aka.ms/downloadazcopy-v10-linux | grep ^Location` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).headers.location` |

> [!NOTE]
> För Linux `--strip-components=1` `tar` tar kommandot bort den översta mappen som innehåller versions namnet för Linux och extraherar i stället binärfilen direkt till den aktuella mappen. Detta gör att skriptet kan uppdateras med en ny version av `azcopy` genom att bara uppdatera `wget` URL: en.

URL: en visas i kommandots utdata. Skriptet kan sedan hämta AzCopy med hjälp av den URL: en.

| Operativsystem  | Kommando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Escape-specialtecken i SAS-token

I kommandofiler med `.cmd` tillägget måste du undanta de `%` tecken som visas i SAS-token. Du kan göra det genom att lägga till ytterligare ett `%` tecken bredvid befintliga `%` tecken i SAS token-strängen.

### <a name="run-scripts-by-using-jenkins"></a>Köra skript med Jenkins

Om du planerar att använda [Jenkins](https://jenkins.io/) för att köra skript, se till att placera följande kommando i början av skriptet.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Använd AzCopy i Azure Storage Explorer

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) använder AzCopy för att utföra alla dess data överförings åtgärder. Du kan använda [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om du vill dra nytta av prestanda fördelarna med AZCopy, men du föredrar att använda ett grafiskt användar gränssnitt i stället för kommando raden för att interagera med dina filer.

Storage Explorer använder din konto nyckel för att utföra åtgärder, så när du har loggat in på Storage Explorer behöver du inte ange ytterligare autentiseringsuppgifter för auktorisering.

<a id="previous-version"></a>

## <a name="use-the-previous-version-of-azcopy"></a>Använd den tidigare versionen av AzCopy

Om du behöver använda den tidigare versionen av AzCopy, se någon av följande länkar:

- [AzCopy i Windows (v8)](/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy på Linux (v7)](/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurera, optimera och felsöka AzCopy

Se [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Nästa steg

Om du har frågor, problem eller allmän feedback kan du skicka dem [till GitHub](https://github.com/Azure/azure-storage-azcopy) -sidan.
