---
title: Exportera eller ta bort data från arbets ytan
titleSuffix: Azure Machine Learning
description: 'Lär dig hur du exporterar eller tar bort din arbets yta med Azure Machine Learning Studio-, CLI-, SDK-och autentiserade REST-API: er.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 4aa0cecb380f75080abe471a4c414029436c57d6
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645949"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportera eller ta bort data för din Machine Learning service-arbetsyta

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I Azure Machine Learning kan du exportera eller ta bort data från din arbets yta med antingen portalens grafiska gränssnitt eller python SDK. I den här artikeln beskrivs båda alternativen.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Kontrol lera dina data för arbets ytan

In-Product data som lagras av Azure Machine Learning är tillgängliga för export och borttagning. Du kan exportera och ta bort med Azure Machine Learning Studio, CLI och SDK. Telemetri-data kan nås via Azures sekretess Portal. 

I Azure Machine Learning består personliga data av användar information i dokument för körnings historik. 

## <a name="delete-high-level-resources-using-the-portal"></a>Ta bort resurser på hög nivå med portalen

När du skapar en arbets yta skapar Azure ett antal resurser i resurs gruppen:

- Själva arbets ytan
- Ett lagringskonto
- Ett behållar register
- En program insikts instans
- Ett nyckel valv

De här resurserna kan tas bort genom att markera dem i listan och välja **ta bort** 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Skärm bild av portalen, med ikonen Ta bort markerad":::

Kör historik dokument, som kan innehålla personlig användar information, lagras i lagrings kontot i Blob Storage i undermappar i `/azureml` . Du kan ladda ned och ta bort data från portalen.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Skärm bild av azureml-katalogen i lagrings kontot i portalen":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Exportera och ta bort maskin inlärnings resurser med Azure Machine Learning Studio

Azure Machine Learning Studio ger en enhetlig vy över dina Machine Learning-resurser, till exempel bärbara datorer, data uppsättningar, modeller och experiment. Azure Machine Learning Studio betonar att bevara en post med dina data och experiment. Beräknings resurser som pipelines och beräknings resurser kan tas bort med hjälp av webbläsaren. För de här resurserna navigerar du till den aktuella resursen och väljer **ta bort**. 

Data uppsättningar kan avregistreras och experiment kan arkiveras, men de här åtgärderna tar inte bort data. För att helt ta bort data måste data uppsättningar och körnings data tas bort på lagrings nivå. Att ta bort på lagrings nivån görs via portalen, enligt beskrivningen ovan.

Du kan hämta utbildnings artefakter från experimentella körningar med Studio. Välj det **experiment** och den **körning** som du är intresse rad av. Välj **utdata + loggar** och navigera till de angivna artefakterna som du vill hämta. Välj **...** och **Ladda ned**.

Du kan ladda ned en registrerad modell genom att gå till önskad **modell** och välja **Ladda ned**. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="Skärm bild av sidan Studio-modell med alternativet för nedladdning markerat":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Exportera och ta bort resurser med python SDK

Du kan hämta utdata för en viss körning med: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Följande Machine Learning-resurser kan tas bort med python SDK: 

| Typ | Funktions anrop | Kommentarer | 
| --- | --- | --- |
| `Workspace` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | Använd `delete-dependent-resources` för att överlappa borttagningen |
| `Model` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) | | 

