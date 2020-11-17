---
author: baanders
description: ta med fil för Azure Digitals dubbla komponenter – krav för att konfigurera en registrering av appen
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124850"
---
För att autentisera alla resurser som används i den här artikeln måste du ställa in en [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) - **app-registrering**. Följ instruktionerna i [*instruktion: skapa en app-registrering*](../articles/digital-twins/how-to-create-app-registration.md) för att konfigurera den här. 

När du har registrerat en app behöver du registreringens **_program_** -ID och **_katalog (klient)-ID_** ([hitta i Azure Portal](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id)). Anteckna dessa värden om du vill använda dem senare för att ge åtkomst till Azures digitala dubbla API: er.