---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 618efc8d2c3784a487c302661f35d5a284c68178
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475695"
---
[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services- **gruppen som anropar ett hjälte exempel för iOS** visar hur kommunikations tjänsterna som anropar iOS-klientcertifikatet kan användas för att bygga en grupp anrops upplevelse som innehåller röst och video. I den här snabb starten får du lära dig hur du konfigurerar och kör exemplet. En översikt över exemplet finns för kontext.

## <a name="overview"></a>Översikt

Exemplet är ett inbyggt iOS-program som använder klient biblioteken för Azure Communication Services iOS för att bygga en anrops upplevelse som har funktioner för både röst-och video samtal. Programmet använder en komponent på Server sidan för att etablera åtkomsttoken som sedan används för att initiera klient biblioteket för Azure Communication Services. Om du vill konfigurera den här komponenten på Server sidan kan du följa den [Betrodda tjänsten med Azure Functions](../../tutorials/trusted-service-tutorial.md) själv studie kursen.

Exemplet ser ut så här:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Skärm bild som visar landnings sidan för exempel programmet.":::

När du trycker på knappen "Starta nytt samtal" skapar iOS-programmet ett nytt anrop och kopplar det. Programmet låter dig också ansluta till ett befintligt Azure Communication Services-samtal genom att ange det befintliga anropets ID.

När du har anslutit till ett samtal uppmanas du att ge programmet behörighet att få åtkomst till din kamera och mikrofon. Du uppmanas också att ange ett visnings namn.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Skärm bild som visar för anrops skärmen i exempel programmet.":::

När du har konfigurerat ditt visnings namn och dina enheter kan du ansluta till anropet. Du ser huvud anrops arbets ytan där den grundläggande anrops upplevelsen finns.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Skärm bild som visar exempel programmets huvud skärm.":::

Komponenter för den huvudsakliga uppringnings skärmen:

- **Medie Galleri**: huvud steget där deltagarna visas. Om en deltagare har sin kamera aktive rad visas deras video flöde här. Varje deltagare har en enskild panel som visar visnings namn och video ström (när det finns en). Galleriet har stöd för flera deltagare och uppdateras när deltagarna läggs till eller tas bort i anropet.
- **Åtgärds fält**: det är här som de primära anrops kontrollerna finns. Med de här kontrollerna kan du förvandla din video och mikrofon på/av, dela din skärm och lämna samtalet.

Nedan hittar du mer information om förutsättningar och steg för att konfigurera exemplet.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. Mer information finns i [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En Mac som kör [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), tillsammans med ett giltigt certifikat för utvecklare som installerats i din nyckel Ring.
- En Azure Communication Services-resurs. Mer information finns i [skapa en Azure Communication-resurs](../../quickstarts/create-communication-resource.md).
- En Azure-funktion som kör [betrodd tjänst logik](../../tutorials/trusted-service-tutorial.md) för att hämta åtkomsttoken.

## <a name="running-sample-locally"></a>Kör exempel lokalt

Den grupp som anropar samplet kan köras lokalt med XCode. Utvecklare kan antingen använda sin fysiska enhet eller en emulator för att testa programmet.

### <a name="before-running-the-sample-for-the-first-time"></a>Innan du kör exemplet för första gången

1. Installera beroenden genom att köra `pod install` .
2. Öppna `ACSCall.xcworkspace` i Xcode.
3. Uppdatera `AppSettings.plist` . Ange värdet för `acsTokenFetchUrl` nyckeln som URL för din autentiserings slut punkt.

### <a name="run-sample"></a>Kör exempel

Skapa och kör exemplet i XCode.

## <a name="optional-securing-an-authentication-endpoint"></a>Valfritt Skydda en autentiserings slut punkt

I demonstrations syfte använder det här exemplet en offentligt tillgänglig slut punkt som standard för att hämta en Azure Communication Services-token. För produktions scenarier rekommenderar vi att du använder din egen säkrade slut punkt för att tillhandahålla dina egna tokens.

Med ytterligare konfiguration har det här exemplet stöd för anslutning till en skyddad **Azure Active Directory** -slutpunkt (Azure AD) så att användaren måste logga in för att appen ska kunna hämta en Azure Communication Services-token. Se stegen nedan:

1. Aktivera Azure Active Directory autentisering i din app.  
   - [Registrera din app under Azure Active Directory (med iOS/macOS-plattforms inställningar)](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-ios) 
    - [Konfigurera din App Service-eller Azure Functions-app för att använda Azure AD-inloggning](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
2. Gå till sidan registrerad app-översikt under Azure Active Directory app-registreringar. Anteckna `Application (client) ID` , `Directory (tenant) ID` , `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure Active Directory konfiguration på Azure Portal.":::

3. Öppna `AppSettings.plist` i Xcode och Lägg till följande nyckel värden:
   - `acsTokenFetchUrl`: URL: en som begär Azure Communication Services-token 
   - `isAADAuthEnabled`: Ett booleskt värde som anger om Azure Communication Services-token-autentisering krävs eller inte
   - `aadClientId`: Ditt program (klient) ID
   - `aadTenantId`: Din katalog (klient) ID
   - `aadRedirectURI`: Omdirigerings-URI: n måste ha följande format: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: En matris med behörighets omfattningar som begärs av användare för auktorisering. Lägg till i `<Application ID URI>/user_impersonation` matrisen för att bevilja åtkomst till autentiserings slut punkt

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en kommunikations tjänst prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den. Läs mer om att [Rensa resurser](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- Bekanta dig med [att använda det anropande klient biblioteket](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Läs mer om [hur du anropar Works](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Mer att läsa

- [Azure Communication-GitHub](https://github.com/Azure/communication) – Hitta fler exempel och information på den officiella GitHub-Sidan
