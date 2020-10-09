---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: e969520e226999bc2b104fb4f757619832eaa165
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88944392"
---
## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 eller en senare 2. x-version.
::: zone-end  
::: zone pivot="programming-language-python"
+ Den Azure Functions Core Tools version som motsvarar din installerade python-version:

   | Python-version | Core tools-version |
   | -------------- | ------------------ |
   | Python 3,8     | [version 3. x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3,6<br/>Python 3.7 | [Version 2.7.1846 eller en senare version](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), aktiva LTS-och UNDERHÅLLs LTS-versioner (8.11.1 och 10.14.1 rekommenderas).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64-bitars)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), som stöds av Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core SDK 3,1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), version 8. 

    > [!IMPORTANT]
    > + Funktions stöd för Java 11 är för närvarande en för hands version och maven-archetype skapar en Java 8-distribution som standard. Om du i stället vill köra din Function-app i Java 11 måste du manuellt uppdatera pom.xml-filen med Java 11-värden. Mer information finns i [Java-versioner](../articles/azure-functions/functions-reference-java.md#java-versions). 
    > + `JAVA_HOME`Miljövariabeln måste anges till installations platsen för rätt version av JDK för att slutföra den här snabb starten.

+ [Apache maven](https://maven.apache.org), version 3,0 eller senare.

::: zone-end
