---
title: Hämta en signerings nyckel från en princip med hjälp av Azure Media Services v3 .NET
description: Det här avsnittet visar hur du hämtar en signerings nyckel från den befintliga principen med hjälp av Media Services v3 .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: d98f008e77fd56a369d298e26165ca29007c8128
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267354"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Hämta en signeringsnyckel från den befintliga principen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3-API: er returnerar inte hemligheter eller autentiseringsuppgifter för **Get** -eller **list** åtgärder. Se den detaljerade förklaringen här: Mer information finns i [RBAC-och Media Services-konton](rbac-overview.md)

Exemplet i den här artikeln visar hur du använder .NET för att hämta en signerings nyckel från den befintliga principen. 
 
## <a name="download"></a>Ladda ned 

Klona en GitHub-lagringsplats som innehåller det fullständiga .NET-exemplet på din dator med hjälp av följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Exemplet på ContentKeyPolicy med hemligheter finns i mappen [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .

## <a name="get-contentkeypolicy-with-secrets"></a>Hämta ContentKeyPolicy med hemligheter 

Använd **GetPolicyPropertiesWithSecretsAsync**, som du ser i exemplet nedan, för att komma till nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Nästa steg

[Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md) 
