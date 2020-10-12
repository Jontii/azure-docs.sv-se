---
title: Konfigurera registrering och inloggning med ett GitHub-konto
titleSuffix: Azure AD B2C
description: Tillhandahålla registrering och inloggning till kunder med GitHub-konton i dina program med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba2441ae48c99d63ae637d2b80069058a04c5ef9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388195"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med ett GitHub-konto med hjälp av Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Skapa ett GitHub OAuth-program

Om du vill använda ett GitHub-konto som [identitets leverantör](authorization-code-flow.md) i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett program i din klient som representerar det. Om du inte redan har ett GitHub-konto kan du registrera dig på [https://www.github.com/](https://www.github.com/) .

1. Logga in på webbplatsen för [GitHub Developer](https://github.com/settings/developers) med dina GitHub-autentiseringsuppgifter.
1. Välj **OAuth-appar** och välj sedan **ny OAuth-app**.
1. Ange ett **program namn** och din **Start sidas URL**.
1. Ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **återanrops-URL för auktorisering**. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Använd små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C.
1. Klicka på **Registrera program**.
1. Kopiera värdena för **klient-ID** och **klient hemlighet**. Du behöver båda för att lägga till identitets leverantören i din klient organisation.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurera ett GitHub-konto som en identitets leverantör

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **identitets leverantörer**och välj sedan **GitHub (för hands version)**.
1. Ange ett **namn**. Till exempel *GitHub*.
1. För **klient-ID**anger du klient-ID för det GitHub-program som du skapade tidigare.
1. Ange den klient hemlighet som du har spelat in för **klient hemligheten**.
1. Välj **Spara**.
