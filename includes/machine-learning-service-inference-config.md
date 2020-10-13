---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: f500df6a592769928470d22468ff2fdff18293a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88748844"
---
Posterna i `inferenceconfig.json` dokument översikten till parametrarna för klassen [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . I följande tabell beskrivs mappningen mellan entiteter i JSON-dokumentet och parametrarna för-metoden:

| JSON-entitet | Metod parameter | Beskrivning |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Sökväg till en lokal fil som innehåller koden som ska köras för avbildningen. |
| `sourceDirectory` | `source_directory` | Valfritt. Sökväg till mappar som innehåller alla filer för att skapa avbildningen, vilket gör det enkelt att komma åt filer i den här mappen eller undermappen. Du kan ladda upp en hel mapp från den lokala datorn som beroenden för WebService. Obs! dina entry_script, conda_file och extra_docker_file_steps sökvägar är relativa sökvägar till source_directory sökvägen. |
| `environment` | `environment` | Valfritt.  Azure Machine Learnings [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

Du kan inkludera fullständiga specifikationer för en Azure Machine Learning [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) i konfigurations filen för konfigurations filen. Om den här miljön inte finns i arbets ytan skapas den av Azure Machine Learning. Annars kommer Azure Machine Learning uppdatera miljön om det behövs. Följande JSON är ett exempel:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Du kan också använda en befintlig Azure Machine Learning [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) i separata CLI-parametrar och ta bort nyckeln "miljö" från konfigurations filen för konfigurations filen. Använd-e för miljö namnet och--ev för miljö versionen. Om du inte anger--EV kommer den senaste versionen att användas. Här är ett exempel på en konfigurations fil för uppdelning:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Följande kommando visar hur du distribuerar en modell med hjälp av den tidigare konfigurations filen för konfiguration (med namnet myInferenceConfig.jspå). 

Den använder också den senaste versionen av en befintlig Azure Machine Learnings [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) (med namnet azureml – minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
