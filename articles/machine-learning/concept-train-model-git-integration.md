---
title: Git-integrering för Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur Azure Machine Learning integreras med en lokal git-lagringsplats. När du skickar en utbildning som körs från en lokal katalog som är git-lagringsplats, spåras information om lagrings platsen, gren och pågående genomförande som en del av körningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: bd77af133b88e1ba93054dbb7e0f896d8d418f89
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893555"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-integrering för Azure Machine Learning

[Git](https://git-scm.com/) är ett populärt versions kontroll system som gör att du kan dela och samar beta med dina projekt. 

Azure Machine Learning har fullt stöd för git-lagringsplatser för att spåra arbete – du kan klona lagrings platser direkt till fil systemet på den delade arbets ytan, använda git på din lokala arbets Station eller använda Git från en CI/CD-pipeline.

Om källfiler lagras i en lokal git-lagringsplats när ett jobb skickas till Azure Machine Learning spåras information om lagrings platsen som en del av inlärnings processen.

Eftersom Azure Machine Learning spårar information från en lokal git-lagrings platsen är den inte kopplad till någon speciell Central lagrings plats. Din lagrings plats kan klonas från GitHub, GitLab, BitBucket, Azure DevOps eller någon annan git-kompatibel tjänst.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klona Git-databaser till arbetsytans filsystem
Azure Machine Learning tillhandahåller ett delat fil system för alla användare på arbets ytan.
Om du vill klona en git-lagringsplats till den här fil resursen rekommenderar vi att du skapar en beräknings instans & öppnar en Terminal.
När terminalen har öppnats har du åtkomst till en fullständig git-klient och kan klona och arbeta med git via git CLI-upplevelsen.

Vi rekommenderar att du klonar lagrings platsen till din användar katalog så att andra inte kommer att göra kollisioner direkt på din arbets gren.

Du kan klona en git-lagringsplats som du kan autentisera till (GitHub, Azure databaser, BitBucket osv.)

En guide om hur du använder git CLI hittar du [här.](https://guides.github.com/introduction/git-handbook/)

## <a name="track-code-that-comes-from-git-repositories"></a>Spåra kod som kommer från git-databaser

När du skickar en utbildning som körs från python SDK eller Machine Learning CLI överförs filerna som behövs för att träna modellen till din arbets yta. Om `git` kommandot är tillgängligt i utvecklings miljön använder överförings processen för att kontrol lera om filerna är lagrade i en git-lagringsplats. I så fall, överförs information från git-lagringsplatsen också som en del av övnings körningen. Den här informationen lagras i följande egenskaper för övnings körningen:

| Egenskap | Git-kommando som används för att hämta värdet | Beskrivning |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Den URI som din lagrings plats har kopierats från. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Den URI som din lagrings plats har kopierats från. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Den aktiva grenen när körningen skickades. |
| `azureml.git.commit` | `git rev-parse HEAD` | Bekräfta hashen för den kod som skickades för körningen. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Bekräfta hashen för den kod som skickades för körningen. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, om grenen/genomförandet är smutsig; Annars, `false` . |

Den här informationen skickas för körningar som använder en uppskattnings-, maskin inlärnings-pipeline eller skript körning.

Om dina utbildningsbaserade filer inte finns i en git-lagringsplats i utvecklings miljön, eller om `git` kommandot inte är tillgängligt, spåras ingen git-relaterad information.

> [!TIP]
> Om du vill kontrol lera om git-kommandot är tillgängligt i utvecklings miljön öppnar du en Shell-session, kommando tolk, PowerShell eller andra kommando rads gränssnitt och skriver följande kommando:
>
> ```
> git --version
> ```
>
> Om det är installerat och i sökvägen får du ett svar som liknar `git version 2.4.1` . Mer information om hur du installerar git i utvecklings miljön finns på [git-webbplatsen](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Visa den loggade informationen

Git-informationen lagras i egenskaperna för en utbildnings körning. Du kan visa den här informationen med hjälp av Azure Portal, python SDK och CLI. 

### <a name="azure-portal"></a>Azure Portal

1. Från [Studio-portalen](https://ml.azure.com)väljer du din arbets yta.
1. Välj __experiment__och välj sedan ett av experimenten.
1. Välj en av körningarna från kolumnen __Kör nummer__ .
1. Välj __utdata + loggar__och expandera sedan __loggarna__ och __azureml__ -posterna. Välj den länk som börjar med __ ### \_ Azure__.

Den loggade informationen innehåller text som liknar följande JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

När du har skickat in en utbildnings körning returneras ett [körnings](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) objekt. `properties`Attribut för det här objektet innehåller den loggade git-informationen. Följande kod hämtar exempelvis commit hash:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run`CLI-kommandot kan användas för att hämta egenskaperna från en körning. Följande kommando returnerar till exempel egenskaperna för den senaste körningen i experimentet med namnet `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Mer information finns i referens dokumentationen för [AZ ml-körning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) .

## <a name="next-steps"></a>Nästa steg

* [Använda beräknings mål för modell träning](how-to-set-up-training-targets.md)
