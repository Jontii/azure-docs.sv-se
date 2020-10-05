---
title: Aktivera Azure Machine Learning Studio i ett virtuellt nätverk
titleSuffix: Azure Machine Learning
description: Använd Azure Machine Learning Studio för att komma åt data som lagras i ett virtuellt nätverk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 58395463c494a95a8842cddbe4d51544ce03d212
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713364"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Använda Azure Machine Learning Studio i ett virtuellt Azure-nätverk

I den här artikeln får du lära dig hur du använder Azure Machine Learning Studio i ett virtuellt nätverk. Lär dig att:

> [!div class="checklist"]
> - Få åtkomst till Studio från en resurs i ett virtuellt nätverk.
> - Ge Studio åtkomst till data som lagras i ett virtuellt nätverk.
> - Förstå hur lagrings säkerhet påverkas av Studio.

Den här artikeln är del fem i en serie med fem delar som vägleder dig genom att skydda ett Azure Machine Learning-arbetsflöde. Vi rekommenderar starkt att du läser igenom [del ett: VNet-översikt](how-to-network-security-overview.md) för att förstå den övergripande arkitekturen först. 

Se de andra artiklarna i den här serien:

[1. VNet-översikt](how-to-network-security-overview.md)  >  [2. Skydda arbets ytan](how-to-secure-workspace-vnet.md)  >  [3. Skydda inlärnings miljö](how-to-secure-training-vnet.md)  >  [4. Skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)  >  [5. Aktivera Studio-funktioner](how-to-enable-studio-virtual-network.md)


> [!IMPORTANT]
> Även om de flesta Studio fungerar med data som lagras i ett virtuellt nätverk gör integrerade antecknings böcker __inte__det. Integrerade antecknings böcker stöder inte användning av lagring som finns i ett virtuellt nätverk. I stället kan du använda Jupyter-anteckningsböcker från en beräknings instans. Mer information finns i avsnittet [åtkomst data i en Compute instance-anteckningsbok]() .


## <a name="prerequisites"></a>Förutsättningar

+ Läs [översikten över nätverks säkerhet](how-to-network-security-overview.md) för att förstå vanliga scenarier för virtuella nätverk och övergripande arkitektur för virtuella nätverk.

+ Ett befintligt virtuellt nätverk och undernät som ska användas.

+ En befintlig [Azure Machine Learning arbets yta med privat länk aktive rad](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Ett befintligt [Azure Storage-konto lade till det virtuella nätverket](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts).

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Få åtkomst till Studio från en resurs i VNet

Om du ansluter till Studio från en resurs i ett virtuellt nätverk (till exempel en beräknings instans eller virtuell dator) måste du tillåta utgående trafik från det virtuella nätverket till Studio. 

Om du till exempel använder nätverks säkerhets grupper (NSG) för att begränsa utgående trafik, lägger du till en regel till ett __service tag-__ mål för __AzureFrontDoor. frontend__.

## <a name="access-data-using-the-studio"></a>Få åtkomst till data med Studio

När du har [lagt till ett Azure Storage-konto i ditt virtuella nätverk](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)måste du konfigurera ditt lagrings konto så att det använder [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för att ge Studio åtkomst till dina data. Studio stöder lagrings konton som kon figurer ATS för att använda tjänst slut punkter eller privata slut punkter. Lagrings konton använder tjänstens slut punkter som standard. Om du vill aktivera privata slut punkter för lagring, se [Använd privata slut punkter för Azure Storage](../storage/common/storage-private-endpoints.md)

Om du inte aktiverar hanterad identitet kommer du att få det här felet, och `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` följande åtgärder kommer att inaktive ras:

* Förhandsgranska data i Studio.
* Visualisera data i designern.
* Skicka ett AutoML experiment.
* Starta ett etikettande projekt.

Studio har stöd för läsning av data från följande data lager typer i ett virtuellt nätverk:

* Azure-blobb
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="configure-datastores-to-use-managed-identity"></a>Konfigurera data lager för att använda hanterad identitet

Azure Machine Learning använder [data lager](concept-data.md#datastores) för att ansluta till lagrings konton. Använd följande steg för att konfigurera dina data lager för att använda hanterad identitet. 

1. I Studio väljer du __data lager__.

1. Om du vill skapa ett nytt data lager väljer du __+ nytt data lager__.

    Om du vill uppdatera ett befintligt data lager väljer du data lagret och väljer __uppdatera autentiseringsuppgifter__.

1. I data lager inställningarna väljer du __Ja__ för  __Tillåt Azure Machine Learning-tjänst för att komma åt lagringen med hjälp av arbets ytans hanterad identitet__.


De här stegen lägger till den arbetsytans hanterade identiteten som en __läsare__ till lagrings tjänsten med hjälp av Azure Resource based Access Control (RBAC). Med __läsar__ åtkomst kan arbets ytan Hämta brand Väggs inställningar och se till att data inte lämnar det virtuella nätverket.

> [!NOTE]
> Det kan ta upp till 10 minuter innan ändringarna börjar gälla.

## <a name="technical-notes-for-managed-identity"></a>Tekniska kommentarer för hanterad identitet

Att använda hanterad identitet för att komma åt lagrings tjänster påverkar vissa säkerhets aspekter. Dessa överväganden är unika för den typ av lagrings konto som du ansluter till. I det här avsnittet beskrivs ändringarna för varje typ av lagrings konto.

### <a name="azure-blob-storage"></a>Azure Blob Storage

För __Azure Blob Storage__läggs den arbets ytans hanterade identiteten också till som en [BLOB-datakälla](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) så att den kan läsa data från Blob Storage.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 åtkomst kontroll

Du kan använda både RBAC-och POSIX-typ åtkomst kontrol listor (ACL: er) för att styra data åtkomsten i ett virtuellt nätverk.

Om du vill använda RBAC lägger du till den arbets ytans hanterade identitet i [BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) -rollen. Mer information finns i [rollbaserad åtkomst kontroll i Azure](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control).

För att kunna använda ACL: er kan den arbets ytans hanterade identitet tilldelas åtkomst precis som andra säkerhets principer. Mer information finns i [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 åtkomst kontroll

Azure Data Lake Storage Gen1 stöder endast åtkomst kontrol listor med POSIX-typ. Du kan tilldela den arbets ytans hanterade identitets åtkomst till resurser precis som vilken annan säkerhets princip som helst. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database innesluten användare

Om du vill komma åt data som lagras i en Azure SQL Database med hjälp av hanterad identitet måste du skapa en SQL-innesluten användare som mappar till den hanterade identiteten Mer information om hur du skapar en användare från en extern provider finns i [skapa inneslutna användare som är mappade till Azure AD-identiteter](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

När du har skapat en SQL-innesluten användare beviljar du behörigheter till den med hjälp av [kommandot bevilja T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-default-datastore"></a>Standard data lager för Azure Machine Learning designer

Designern använder det lagrings konto som är kopplat till din arbets yta för att lagra utdata som standard. Du kan dock ange att den ska lagra utdata till alla data lager som du har åtkomst till. Om din miljö använder virtuella nätverk kan du använda dessa kontroller för att säkerställa att dina data är skyddade och tillgängliga.

Ange ett nytt standard lagrings utrymme för en pipeline:

1. I ett pipeline-utkast väljer du **kugg hjuls ikonen Inställningar** nära rubriken för din pipeline.
1. Välj **Välj standard data lager**.
1. Ange ett nytt data lager.

Du kan även åsidosätta standard data lagret per modul. Detta ger dig kontroll över lagrings platsen för varje enskild modul.

1. Välj den modul vars utdata du vill ange.
1. Expandera avsnittet **utdata-inställningar** .
1. Välj **Åsidosätt standardinställningar för utdata**.
1. Välj **Ange inställningar för utdata**.
1. Ange ett nytt data lager.

## <a name="next-steps"></a>Nästa steg

Den här artikeln är en valfri del av en serie virtuella nätverks serier i fyra delar. Se resten av artiklarna för att lära dig hur du skyddar ett virtuellt nätverk:

* [Del 1: översikt över virtuella nätverk](how-to-network-security-overview.md)
* [Del 2: skydda arbets ytans resurser](how-to-secure-workspace-vnet.md)
* [Del 3: skydda tränings miljön](how-to-secure-training-vnet.md)
* [Del 4: skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)