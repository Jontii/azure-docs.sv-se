---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 06e4eea32aefcb400c144be98c274e3e4bb4b121
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93136228"
---
Den här guiden visar hur du installerar [talet SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systemkrav

Linux Se listan över [Linux-distributioner och mål arkitekturer som stöds](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten behöver du:

* gcc
* [Go Binary (1,13 eller senare)](https://golang.org/dl/)

* Linux-plattformar som stöds kräver vissa bibliotek installerade ( `libssl` för Secure Sockets Layer-stöd och `libasound2` för ljud support). Se distributionen nedan för de kommandon som behövs för att installera rätt versioner av dessa bibliotek.

   * På Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Om libssl 1.0.0 inte är tillgängligt installerar du libssl 1.0. x (där x är större än 0) eller libssl 1.1 i stället.

   * På RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - På RHEL/CentOS 7 följer du anvisningarna för [hur du konfigurerar RHEL/CentOS 7 för tal-SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Konfigurera go-miljö

1. Eftersom bindningarna förlitar sig på `cgo` måste du ange miljövariablerna så att de kan hitta SDK:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. För att köra program inklusive SDK måste vi dessutom meddela operativ systemet var du hittar libs:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list-go.md)]