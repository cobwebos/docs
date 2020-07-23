---
title: 快速入门：适用于 Java 的表单识别器客户端库
description: 在本快速入门中，开始使用适用于 Java 的表单识别器客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: 6ff56ca61304bdacb3512156babd637afd337c7e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242122"
---
[参考文档](https://docs.microsoft.com/java/api/overview/azure/formrecognizer) | [库源代码](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src) | [包 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer) | [示例](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 包含一组训练数据的 Azure 存储 Blob。 有关整理训练数据集的提示和选项，请参阅[为自定义模型生成训练数据集](../../build-training-data-set.md)。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Train** 文件夹下的文件。
* 最新版本的 [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。

## <a name="setting-up"></a>设置

### <a name="create-a-form-recognizer-azure-resource"></a>创建表单识别器 Azure 资源

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>创建环境变量

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目


在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

为示例应用创建一个文件夹。 在工作目录中运行以下命令：

```console
mkdir -p src/main/java
```

导航到新文件夹，创建名为 formrecognizer-quickstart.java 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

```java
import Azure.AI.FormRecognizer;
import Azure.AI.FormRecognizer.Models;

import java.util.concurrent.atomic.AtomicReference;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
```

在应用程序的 `main` 方法中，为该资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭编辑器、IDE 或 shell，然后再重新打开才能访问该变量。 稍后将定义方法。


```java
public static void Main(string[] args)
{
    String key = System.getenv("FORM_RECOGNIZER_KEY");
    String endpoint = System.getenv("FORM_RECOGNIZER_ENDPOINT");
}
```

### <a name="install-the-client-library"></a>安装客户端库

本快速入门使用 Gradle 依赖项管理器。 可以在 [Maven 中央存储库](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)中找到客户端库以及其他依赖项管理器的信息。

在项目的 *build.gradle.kts* 文件中，请务必以 `implementation` 语句的形式包含客户端库。 

```kotlin
dependencies {
    implementation group: 'com.azure', name: 'azure-ai-formrecognizer', version: '1.0.0-beta.3'
}
```

<!-- 
    Object model tbd
-->

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的表单识别器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [识别表单内容](#recognize-form-content)
* [识别回执](#recognize-receipts)
* [训练自定义模型](#train-a-custom-model)
* [使用自定义模型分析表单](#analyze-forms-with-a-custom-model)
* [管理自定义模型](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>验证客户端

在 `Main` 方法中添加以下代码。 此处，你将使用前面定义的订阅变量对两个客户端对象进行身份验证。 你将使用 AzureKeyCredential 对象，因此，如果需要，你可以更新 API 密钥而无需创建新的客户端对象。

```java
FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential("{key}"))
    .endpoint("{endpoint}")
    .buildClient();
    
FormTrainingClient trainingClient = recognizerClient.getFormTrainingClient();
```

### <a name="call-client-specific-methods"></a>调用特定于客户端的方法

下一个代码块使用客户端对象来调用表单识别器 SDK 中的每个主要任务的方法。 稍后将定义这些方法。

还需要为训练和测试数据添加对 URL 的引用。 
* 若要检索自定义模型训练数据的 SAS URL，请打开 Microsoft Azure 存储资源管理器，右键单击容器，然后选择“获取共享访问签名”。 确保选中“读取”和“列表”权限，然后单击“创建”。 然后复制 **URL** 部分中的值。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。
* 若要获取要测试的表单的 URL，可以使用上述步骤获取 blob 存储中单个文档的 SAS URL。 或者获取位于其他位置的文档的 URL。
* 使用上述方法还可获取回执图像的 URL。

> [!NOTE]
> 本指南中的代码片段使用通过 URL 访问的远程表单。 如果要改为处理本地表单文档，请参阅[参考文档](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)中的相关方法。

```java
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl) ;
```


## <a name="recognize-form-content"></a>识别表单内容

可以使用表单识别器识别文档中的表格、线条和单词，而无需训练模型。

若要识别位于给定 URI 的文件的内容，请使用 beginRecognizeContentFromUrl 方法。

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<OperationResult, List<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

返回的值是 FormPage 对象的集合：提交的文档中的每一页对应一个对象。 下面的代码循环访问这些对象，并输出提取的键/值对和表数据。

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %d and height: %d, measured with unit: %s.%n", formPage.getWidth(),
            formPage.getHeight(),
            formPage.getUnit());
        formPage.getTables().forEach(formTable -> {
            System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                formTable.getColumnCount());
            formTable.getCells().forEach(formTableCell -> {
                System.out.printf("Cell has text %s.%n", formTableCell.getText());
            });
            System.out.println();
        });
    });
}
```

## <a name="recognize-receipts"></a>识别回执

本部分演示如何使用预先训练的回执模型识别和提取美国回执中的常见字段。

若要从 URI 识别回执，请使用 beginRecognizeReceiptsFromUrl 方法。 返回的值是 RecognizedReceipt 对象的集合：提交的文档中的每一页对应一个对象。

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    SyncPoller<OperationResult, List<RecognizedReceipt>> syncPoller =
        formRecognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    List<RecognizedReceipt> receiptPageResults = syncPoller.getFinalResult();
```

接下来的代码块会循环访问回执，并将其详细信息输出到控制台。

```java
    for (int i = 0; i < receiptPageResults.size(); i++) {
        RecognizedReceipt recognizedReceipt = receiptPageResults.get(i);
        Map<String, FormField> recognizedFields = recognizedReceipt.getRecognizedForm().getFields();
        System.out.printf("----------- Recognized Receipt page %d -----------%n", i);
        FormField merchantNameField = recognizedFields.get("MerchantName");
        if (merchantNameField != null) {
            if (merchantNameField.getFieldValue().getType() == FieldValueType.STRING) {
                System.out.printf("Merchant Name: %s, confidence: %.2f%n",
                    merchantNameField.getFieldValue().asString(),
                    merchantNameField.getConfidence());
            }
        }
        FormField merchantAddressField = recognizedFields.get("MerchantAddress");
        if (merchantAddressField != null) {
            if (merchantAddressField.getFieldValue().getType() == FieldValueType.STRING) {
                System.out.printf("Merchant Address: %s, confidence: %.2f%n",
                    merchantAddressField.getFieldValue().asString(),
                    merchantAddressField.getConfidence());
            }
        }
        FormField transactionDateField = recognizedFields.get("TransactionDate");
        if (transactionDateField != null) {
            if (transactionDateField.getFieldValue().getType() == FieldValueType.DATE) {
                System.out.printf("Transaction Date: %s, confidence: %.2f%n",
                    transactionDateField.getFieldValue().asDate(),
                    transactionDateField.getConfidence());
            }
        }
```
接下来的代码块会循环访问在回执上检测到的各个项，并将其详细信息输出到控制台。

```java
        FormField receiptItemsField = recognizedFields.get("Items");
        if (receiptItemsField != null) {
            System.out.printf("Receipt Items: %n");
            if (receiptItemsField.getFieldValue().getType() == FieldValueType.LIST) {
                List<FormField> receiptItems = receiptItemsField.getFieldValue().asList();
                receiptItems.forEach(receiptItem -> {
                    if (receiptItem.getFieldValue().getType() == FieldValueType.MAP) {
                        receiptItem.getFieldValue().asMap().forEach((key, formField) -> {
                            if (key.equals("Name")) {
                                if (formField.getFieldValue().getType() == FieldValueType.STRING) {
                                    System.out.printf("Name: %s, confidence: %.2fs%n",
                                        formField.getFieldValue().asString(),
                                        formField.getConfidence());
                                }
                            }
                            if (key.equals("Quantity")) {
                                if (formField.getFieldValue().getType() == FieldValueType.INTEGER) {
                                    System.out.printf("Quantity: %d, confidence: %.2f%n",
                                        formField.getFieldValue().asInteger(), formField.getConfidence());
                                }
                            }
                            if (key.equals("Price")) {
                                if (formField.getFieldValue().getType() == FieldValueType.FLOAT) {
                                    System.out.printf("Price: %f, confidence: %.2f%n",
                                        formField.getFieldValue().asFloat(),
                                        formField.getConfidence());
                                }
                            }
                            if (key.equals("TotalPrice")) {
                                if (formField.getFieldValue().getType() == FieldValueType.FLOAT) {
                                    System.out.printf("Total Price: %f, confidence: %.2f%n",
                                        formField.getFieldValue().asFloat(),
                                        formField.getConfidence());
                                }
                            }
                        });
                    }
                });
            }
        }
    }
}
```

## <a name="train-a-custom-model"></a>训练自定义模型

本部分演示如何使用自己的数据训练模型。 训练的模型可以输出结构化数据，其中包含原始形式的文档中的键/值关系。 训练模型后，可对其进行测试和重新训练，并最终使用它来根据需求提取其他表单中的数据。

> [!NOTE]
> 你还可以使用图形用户界面（例如[表单识别器示例标记工具](../../quickstarts/label-tool.md)）来训练模型。

### <a name="train-a-model-without-labels"></a>不使用标签训练模型

训练自定义模型可以识别在自定义表单中找到的所有字段和值，而无需手动标记训练文档。

下面的方法使用给定文档集训练模型，并将该模型的状态输出到控制台。 

```java
private static String TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    String trainingSetSource = "{unlabeled_training_set_SAS_URL}";
    SyncPoller<OperationResult, CustomFormModel> trainingPoller =
        formTrainingClient.beginTraining(trainingSetSource, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getCreatedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getCompletedOn());
```
返回的 CustomFormModel 对象包含该模型可以识别的表单类型以及它可以从每种表单类型中提取的字段的相关信息。 下面的代码块将此信息输出到控制台。

```java 
    System.out.println("Recognized Fields:");
    // looping through the sub-models, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubmodels().forEach(customFormSubModel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        customFormSubModel.getFieldMap().forEach((field, customFormModelField) ->
            System.out.printf("Field: %s Field Label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

最后，此方法返回模型的唯一 ID。

```java
    return customFormModel.getModelId();
}
```

### <a name="train-a-model-with-labels"></a>使用标签训练模型

你还可以通过手动标记训练文档来训练自定义模型。 在某些情况下，使用标签训练可改善效果。 若要使用标签训练，你需要在 blob 存储容器中添加特殊标签信息文件 (\<filename\>.pdf.labels.json) 和训练文档。 [表单识别器示例标记工具](../../quickstarts/label-tool.md)提供了可帮助你创建这些标签文件的 UI。 获得它们后，可以调用 beginTraining 方法，并将 useTrainingLabels 参数设置为 `true`。

```java
private static String TrainModelWithLabels(
    FormRecognizerClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<OperationResult, CustomFormModel> trainingPoller = client.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getRequestedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getCompletedOn());
```

返回的 CustomFormModel 指示模型可以提取的字段，以及每个字段中的估计准确度。 下面的代码块将此信息输出到控制台。

```java
    // looping through the sub-models, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubmodels().forEach(customFormSubModel -> {
        System.out.printf("Sub-model accuracy: %.2f%n", customFormSubModel.getAccuracy());
        customFormSubModel.getFieldMap().forEach((label, customFormModelField) ->
            System.out.printf("Field: %s Field Name: %s Field Accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自定义模型分析表单

本部分演示如何通过你使用自己的表单训练的模型，从自定义表单类型中提取键/值信息和其他内容。

> [!IMPORTANT]
> 若要实现此方案，必须已训练好一个模型，以便可以将其 ID 传递到下面的方法中。 请参阅[训练模型](#train-a-model-without-labels)部分。

你将使用 beginRecognizeCustomFormsFromUrl 方法。 返回的值是 RecognizedForm 对象的集合：提交的文档中的每一页对应一个对象。

```java
// Analyze PDF form data
private static void AnalyzePdfForm(
    FormRecognizerClient formClient, String modelId, String pdfFormUrl)
{    
    String modelId = modelId;
    SyncPoller<OperationResult, List<RecognizedForm>> recognizeFormPoller =
        client.beginRecognizeCustomFormsFromUrl(pdfFormUrl, modelId);

    List<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

以下代码将分析结果输出到控制台。 它将输出每个识别的字段和相应的值以及置信度分数。

```java
    recognizedForms.forEach(form -> {
        System.out.println("----------- Recognized Form -----------");
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) -> {
            System.out.printf("Field %s has value %s with confidence score of %.2f.%n", label,
                formField.getFieldValue(),
                formField.getConfidence());
        });
        System.out.print("-----------------------------------");
    });
}
```

## <a name="manage-your-custom-models"></a>管理自定义模型

本部分演示如何管理帐户中存储的自定义模型。 例如，下面的代码在单个方法中执行所有模型管理任务。 首先，复制以下方法签名：

```java
private static void ManageModels(
    FormRecognizerClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>检查 FormRecognizer 资源帐户中的模型数

下面的代码块会检查表单识别器帐户中保存的模型数，并将其与帐户限制进行比较。

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = client.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCustomModelCount(), accountProperties.getCustomModelLimit());
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出资源帐户中当前存储的模型

下面的代码块会列出帐户中的当前模型，并将这些模型的详细信息输出到控制台。

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = client.getModelInfos();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = client.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Created on: %s%n", customModel.getRequestedOn());
        System.out.printf("Updated on: %s%n", customModel.getCompletedOn());
        customModel.getSubmodels().forEach(customFormSubModel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubModel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubModel.getAccuracy());
            if (customFormSubModel.getFieldMap() != null) {
                customFormSubModel.getFieldMap().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }

        });
    });
```

### <a name="delete-a-model-from-the-resource-account"></a>从资源帐户中删除模型

还可以通过引用模型的 ID 从帐户中删除该模型。

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s operation completed with status: %s%n", modelId.get(),
        client.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>运行应用程序

可使用以下命令生成应用：

```console
gradle build
```

使用 `run` 目标运行应用程序：

```console
gradle run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑难解答

从识别器客户端引发 `ErrorResponseException` 异常。 例如，如果尝试提供一个无效的文件源 URL，则会引发 `ErrorResponseException`，并会出现一个指出失败原因的错误。 在以下代码片段中，通过捕获异常并显示有关错误的其他信息来妥善处理该错误。

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>启用客户端日志记录
适用于 Java 的 Azure SDK 提供了一致的日志记录案例，有助于排查应用程序错误和加快解决速度。 生成的日志会在到达终端状态之前捕获应用程序的流，以帮助查找根本问题。 查看[日志记录 wiki](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK)，以获取有关启用日志记录的指南。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用表单识别器 Java 客户端库以不同的方式训练模型和分析表单。 接下来，请了解如何创建更好的训练数据集并生成更准确的模型。

> [!div class="nextstepaction"]
> [生成训练数据集](../../build-training-data-set.md)

* [什么是表单识别器？](../../overview.md)
* 在 [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples) 上可以找到本指南中使用的示例代码（以及更多代码）。