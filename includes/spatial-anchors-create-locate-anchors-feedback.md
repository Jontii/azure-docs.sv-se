---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "76694272"
---
## <a name="provide-feedback-to-the-user"></a>Ge feedback till användaren

Du kan skriva kod för att hantera sessionens uppdaterade händelse. Den här händelsen utlöses varje gång sessionen förbättrar förståelsen av din miljö. På så sätt kan du:

- Använd- `UserFeedback` klassen för att ge feedback till användaren när enheten flyttas och sessionen uppdaterar dess miljö förståelse. Gör
- Ta reda på i vilket ställe det finns tillräckligt med spårade spatialdata för att skapa avstånds ankare. Du fastställer detta med antingen `ReadyForCreateProgress` eller `RecommendedForCreateProgress` . En gång `ReadyForCreateProgress` är över 1 har vi tillräckligt med data för att spara moln rums ankare, men vi rekommenderar att du väntar tills `RecommendedForCreateProgress` är över 1 för att göra det.
