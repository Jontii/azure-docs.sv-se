---
title: Säkerhet för Azure Digital Twins-lösningar
titleSuffix: Azure Digital Twins
description: Lär dig mer om rekommenderade säkerhets metoder med Azure Digitals.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6784ca9dbc32811a02f4454be94d220c634318f5
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92503325"
---
# <a name="secure-azure-digital-twins"></a>Skydda digitala Azure-dubbla

För säkerhet ger Azure Digitals dubbla, exakt åtkomst kontroll över specifika data, resurser och åtgärder i distributionen. Det sker genom en detaljerad strategi för roll-och behörighets hantering som kallas **Azure rollbaserad åtkomst kontroll (Azure RBAC)**. Du kan läsa om de allmänna principerna för Azure RBAC [här](../role-based-access-control/overview.md).

Azure Digitals dubbla är också stöd för kryptering av data i vila.

## <a name="roles-and-permissions-with-azure-rbac"></a>Roller och behörigheter med Azure RBAC

Azure RBAC tillhandahålls till Azure Digitals sammanflätade via integrering med [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Du kan använda Azure RBAC för att bevilja behörighet till ett *säkerhets objekt*, som kan vara en användare, en grupp eller ett program tjänst objekt. Säkerhets objekt autentiseras av Azure AD och tar emot en OAuth 2,0-token i retur. Denna token kan användas för att auktorisera en åtkomstbegäran till en Azure Digitals-instans.

### <a name="authentication-and-authorization"></a>Autentisering och auktorisering

Med Azure AD är Access en två stegs process. När ett säkerhets objekt (en användare, grupp eller ett program) försöker få åtkomst till Azures digitala dubbla, måste begäran *autentiseras* och *auktoriseras*. 

1. Först *autentiseras*säkerhets objektets identitet och en OAuth 2,0-token returneras.
2. Därefter skickas token som en del av en begäran till tjänsten Azure Digitals dubblare för att *ge* åtkomst till den angivna resursen.

Steget autentisering kräver att en program förfrågan innehåller en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en [Azure Functions](../azure-functions/functions-overview.md) app, kan den använda en **hanterad identitet** för att få åtkomst till resurserna. Läs mer om hanterade identiteter i nästa avsnitt.

Auktoriserings steget kräver att en Azure-roll tilldelas till säkerhets objekt. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Azure Digitals dubbla ger Azure-roller som omfattar uppsättningar med behörigheter för Azure Digitals dubbla resurser. Dessa roller beskrivs längre fram i den här artikeln.

Mer information om roller och roll tilldelningar som stöds i Azure finns i [*förstå de olika rollerna*](../role-based-access-control/rbac-and-directory-admin-roles.md) i Azure RBAC-dokumentationen.

#### <a name="authentication-with-managed-identities"></a>Autentisering med hanterade identiteter

[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en funktion i Azure som gör att du kan skapa en säker identitet som är kopplad till distributionen där program koden körs. Du kan sedan associera identiteten med åtkomst kontroll roller för att bevilja anpassade behörigheter för åtkomst till specifika Azure-resurser som ditt program behöver.

Med hanterade identiteter hanterar Azure-plattformen den här körnings identiteten. Du behöver inte lagra och skydda åtkomst nycklar i din program kod eller konfiguration, antingen för själva identiteten eller för de resurser som du behöver komma åt. Ett digitalt Azure-klientprogram som körs inuti ett Azure App Service-program behöver inte hantera SAS-regler och nycklar eller andra åtkomsttoken. Klient programmet behöver bara slut punkts adressen för Azure Digitals-namnområdet. När appen ansluter binder Azure Digitals en hanterad entitets kontext till klienten. När den är kopplad till en hanterad identitet kan din Azure Digital-klient med dubbla Azure-klienter utföra alla behöriga åtgärder. Auktorisering beviljas sedan genom att associera en hanterad entitet med en Azure Digitals Azure-roll (beskrivs nedan).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Auktorisering: Azure-roller för Azure Digitals dubbla

Azure tillhandahåller **två inbyggda Azure-roller** för att auktorisera åtkomst till [API: er](how-to-use-apis-sdks.md#overview-data-plane-apis)för Azure Digitals dubbla data plan. Du kan referera till rollerna antingen efter namn eller efter ID:

| Inbyggd roll | Beskrivning | ID | 
| --- | --- | --- |
| Azure Digitals sammanflätade data ägare | Ger fullständig åtkomst till resurser med Azure Digitals dubbla resurser | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure Digitals sammanflätade data läsare | Ger skrivskyddad åtkomst till Azure Digitals dubbla resurser | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

>[!NOTE]
> De här rollerna har nyligen bytt namn från sina tidigare namn i för hands versionen:
> * Azure *Digitals dubbla data ägare* hade tidigare *Azure Digitals-ägare (för hands version)*.
> * *Azure Digitals data läsare* tidigare var *Azure Digitals-läsare (för hands version)*.

Du kan tilldela roller på två sätt:
* via fönstret åtkomst kontroll (IAM) för Azure Digitals dubbla i Azure Portal (se [*lägga till eller ta bort Azure roll tilldelningar med hjälp av Azure Portal*](../role-based-access-control/role-assignments-portal.md))
* via CLI-kommandon för att lägga till eller ta bort en roll

Mer detaljerad information om hur du gör detta finns i självstudien om Azure Digitals dubbla steg [*: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md).

Mer information om hur inbyggda roller definieras finns i [*förstå roll definitioner*](../role-based-access-control/role-definitions.md) i Azure RBAC-dokumentationen. Information om hur du skapar anpassade Azure-roller finns i [*Azure-anpassade roller*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automatisera roller

När du refererar till roller i automatiserade scenarier rekommenderar vi att du refererar till dem med deras **ID** i stället för deras namn. Namnen kan ändras mellan versioner, men ID: n kommer inte att göra dem till en mer stabil referens i Automation.

> [!TIP]
> Om du assiging roller med en cmdlet, t. ex. `New-AzRoleAssignment` ([referens](/powershell/module/az.resources/new-azroleassignment?view=azps-4.8.0)), kan du använda `-RoleDefinitionId` -parametern i stället för `-RoleDefinitionName` att skicka ett ID i stället för ett namn på rollen.

### <a name="permission-scopes"></a>Behörighetsomfattning

Innan du tilldelar en Azure-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det är bäst att endast bevilja det snävaste möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure Digitals resurser.
* Modeller: åtgärderna för den här resursen dikterar kontroll över [modeller](concepts-models.md) som laddats upp i Azure Digitals.
* Fråga Digitals grafer: åtgärderna för den här resursen bestämmer möjlighet att köra [frågor](concepts-query-language.md) på digitala inne i Azure Digitals grafer.
* Digital, dubbel: åtgärder för den här resursen ger kontroll över CRUD-åtgärder på [digitala](concepts-twins-graph.md) byggen i det dubbla diagrammet.
* Digital, dubbel relation: åtgärderna för den här resursen definierar kontroll över CRUD-åtgärder på relationer mellan digitala [band](concepts-twins-graph.md) i den dubbla grafen.
* Händelse väg: åtgärder för den här resursen bestämmer behörigheter för att [dirigera händelser](concepts-route-events.md) från Azure Digitals till en slut punkts tjänst som [händelsehubben](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)eller [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Fel söknings behörigheter

Om en användare försöker utföra en åtgärd som inte tillåts av deras roll, kan de få ett fel meddelande från begäran om läsning av tjänst `403 (Forbidden)` . Mer information och fel söknings steg finns i [*fel sökning: Azure Digitals-begäran misslyckades med status: 403 (förbjuden)*](troubleshoot-error-403.md).

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila

Azure Digitals-enheter tillhandahåller kryptering av data i vila och under överföring som de skrivs i våra data Center och dekrypterar dem åt dig när du har åtkomst till den. Krypteringen sker med hjälp av en Microsoft-hanterad krypterings nyckel.

## <a name="cross-origin-resource-sharing-cors"></a>Resursdelning för korsande ursprung (CORS)

Azure Digitals-korsningar stöder för närvarande inte **resurs delning mellan ursprung (CORS)**. Det innebär att om du anropar en REST API från en webbapp, ett [API Management (APIM)](../api-management/api-management-key-concepts.md) -gränssnitt eller en [Power Apps](https://docs.microsoft.com/powerapps/powerapps-overview) -anslutning kan du se ett princip fel.

För att lösa det här felet kan du göra något av följande:
* Remsans CORS-rubrik `Access-Control-Allow-Origin` från meddelandet. Den här rubriken anger om svaret kan delas. 
* Du kan också skapa en CORS-proxy och göra Azures digitala dubblare REST API begäran via den. 

## <a name="next-steps"></a>Nästa steg

* Se de här koncepten i instruktionen [*så här: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md).

* Se hur du interagerar med dessa begrepp från klient program kod i [*How-to: Write app authentication code*](how-to-authenticate-client.md).

* Läs mer om [Azure RBAC](../role-based-access-control/overview.md).
