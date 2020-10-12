---
title: inkludera fil
description: inkludera fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "68229270"
---
Använda Azure-portalen för att skapa en IoT-hubb:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **skapa en resurs**  >  **Sakernas Internet**  >  **IoT Hub**.

    ![Välj för att installera IoT Hub](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. När du skapar din IoT-hubb på fri nivå använder du värdena i tabellen nedan:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | Skapa ny. I den här självstudiekursen används namnet **tutorial-iot-hub-rg**. |
    | Region | I den här självstudien används **USA, västra**. Du väljer normalt den region som är närmast dig. |
    | Namn | Följande skärmbild använder namnet **tutorials-iot-hub**. Du måste välja ett eget unikt namn när du skapar din hubb. |

    ![Inställningar för hubb 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Inställning | Värde |
    | ------- | ----- |
    | Pris- och skalnivå | F1 Kostnadsfri. Du kan bara ha en hubb på den kostnadsfria nivån för en prenumeration. |
    | IoT Hub-enheter | 1 |

    ![Inställningar för hubb 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Klicka på **Skapa**. Det kan ta några minuter för hubben att skapas.

    ![Inställningar för hubb 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Anteckna IoT-hubbnamnet som du har valt. Du använder det här värdet senare i kursen.
