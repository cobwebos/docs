---
title: 快速入门：使用 Java 更改模型并训练 LUIS 应用 - Azure 认知服务 | Microsoft Docs
description: 在本 Java 快速入门中，你将向家庭自动化应用中添加示例话语并训练该应用。 示例话语是映射到意向的对话式用户文本。 通过提供意向的示例话语，可以教 LUIS 识别用户提供的文本类型属于哪种意向。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: 2554854507d127a7cf3ce016ed38310049b2c958
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43768815"
---
# <a name="quickstart-change-model-using-java"></a>快速入门：使用 Java 更改模型 

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>先决条件

[!include[Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](http://www.oracle.com/technetwork/java/javase/downloads/index.html)（Java 开发工具包，标准版）
* [Google 的 GSON JSON 库](https://github.com/google/gson)。

[!include[Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>示例话语 JSON 文件

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>创建快速入门代码

1. 将 Java 依赖项添加到名为 `AddUtterances.java` 的文件。

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. 创建 `AddUtterances` 类。 此类包含以下所有代码片段。

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. 将 LUIS 常量添加到该类。 复制以下代码，并将相应的占位符更改为自己的创作密钥、应用程序 ID 和版本 ID。

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. 向 AddUtterances 类添加用于调用 LUIS API 的方法。 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. 向 AddUtterances 类添加从 LUIS API 接收 HTTP 响应的方法。

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. 向 AddUtterances 类添加异常处理代码。 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. 向 AddUtterances 类添加主函数。

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>生成代码

使用依赖项编译 AddUtterance

```CMD
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>运行代码
调用不带参数的 `AddUtterance` 会将 LUIS 陈述添加到应用，但不训练应用。

```CMD
> java -classpath .;gson-2.8.2.jar AddUtterances
```

此命令行显示调用“添加陈述”API 的结果。 

[!include[Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>清理资源
完成本快速入门后，删除在本快速入门中创建的所有文件。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [以编程方式生成 LUIS 应用](luis-tutorial-node-import-utterances-csv.md) 