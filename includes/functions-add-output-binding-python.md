---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "78191083"
---
Uppdatera *HttpExample \\ \_ \_ init \_ \_ . py* för att matcha följande kod, genom att lägga till `msg` parametern i funktions definitionen och `msg.set(name)` under `if name:` instruktionen.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg`Parametern är en instans av [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) . `set`Metoden skriver ett sträng meddelande till kön, i det här fallet skickas namnet till funktionen i URL-frågesträngen.
