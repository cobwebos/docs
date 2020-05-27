---
title: 快速入门：使用成批的问题测试知识库
description: 使用 QnA Maker 批量测试工具在 QnA Maker 资源中测试知识库，以获取预期的解答、置信度分数和多轮提示。
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 9845b7c7cc19550c450a1eb00ec02731ef2d9d44
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873675"
---
# <a name="quickstart-test-knowledge-base-with-batch-questions-and-expected-answers"></a>快速入门：使用成批的问题和预期的解答测试知识库

使用 QnA Maker 批量测试工具在 QnA Maker 资源中测试知识库，以获取预期的解答、置信度分数和多轮提示。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [创建 QnA Maker 服务](create-publish-knowledge-base.md)或使用现有服务（使用英语）。
* 下载[多轮示例 `.docx` 文件](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* 下载[批量测试工具](https://aka.ms/qnamakerbatchtestingtool)，从 `.zip` 文件中提取可执行文件。

## <a name="sign-into-qna-maker-portal"></a>登录 QnA Maker 门户

[登录](https://www.qnamaker.ai/) QnA Maker 门户。

## <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>根据多轮示例 .docx 文件创建新知识库

1. 从工具栏中选择“创建知识库”。
1. 跳过“步骤 1”（因为你应该已经有了一项 QnA Maker 资源），转到“步骤 2”来选择现有的资源信息： 
    * Azure Active Directory ID
    * Azure 订阅名称
    * Azure QnA 服务名称
    * 语言 - 英语
1. 输入名称 `Multi-turn batch test quickstart` 作为知识库的名称。

1. 在**步骤 4** 中，按照下表配置设置：

    |设置|值|
    |--|--|
    |**允许从 URL、.pdf 或 .docx 文件进行多轮提取。**|已选中|
    |**默认答案文本**| `Batch test - default answer not found.`|
    |**+ 添加文件**|选择先决条件中列出的已下载 `.docx` 文件。|
    |**聊天内容**|选择“专业”|

1. 在“步骤 5”中，选择“创建 KB” 。

    创建过程完成后，门户会显示可编辑的知识库。

## <a name="save-train-and-publish-knowledge-base"></a>保存、训练和发布知识库

1. 从工具栏选择“保存并训练”，以便保存知识库。
1. 从工具栏选择“发布”，然后再次选择“发布”，以便发布知识库。 发布以后，即可从公共 URL 终结点对知识库进行查询。 发布完成后，保存在“发布”页上显示的主机 URL 和终结点密钥信息。

    |所需数据| 示例|
    |--|--|
    |发布的主机|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |发布的密钥|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX`（在 `Endpoint` 后显示的 32 个字符的字符串）|
    |应用 ID|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`（在 `POST` 中显示的 36 个字符的字符串） |

## <a name="create-batch-test-file-with-question-ids"></a>使用问题 ID 创建批量测试文件

若要使用批量测试工具，请通过文本编辑器创建名为 `batch-test-data-1.tsv` 的文件。 此文件需通过制表符隔离以下列。

|TSV 输入文件字段|说明|示例|
|--|--|--|
|知识库 ID|在“发布”页上找到的知识库 ID。 通过在单个文件中使用不同的知识库 ID，对单个文件中同一服务的多个知识库同时进行测试。|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`（在 `POST` 中显示的 36 个字符的字符串） |
|问题|用户会输入的问题文本。 最多 1,000 个字符。|`How do I sign out?`|
|元数据标记|可选|`topic:power` 使用“键:值”格式|
|Top 参数|可选|`25`|
|预期的答案 ID|可选|`13`|

对于此知识库，请只将 2 个必需列的 3 行添加到文件。 第一个列是知识库 ID，第二个列应该是以下问题列表：

|第 2 列 - 问题|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

这些问题是知识库中原封不动的措辞，应该返回 100 作为置信度分数。

接下来，请使用同一知识库 ID 添加一些与这些问题类似的问题（但并不是与随后的 3 行问题完全相同）：

|第 2 列 - 问题|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> 确保仅使用制表符分隔符来分隔每个列。 前导空格或尾随空格会添加到列数据，当类型或大小不正确时会导致程序引发异常。

在 Excel 中打开时，批量测试文件如下图所示。 为了安全起见，已将知识库 ID 替换为 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 进行你自己的批量测试时，请确保列显示你的知识库 ID。

> [!div class="mx-imgBorder"]
> ![从批量测试输入第一版 .tsv 文件](../media/batch-test/batch-test-1-input.png)

## <a name="test-the-batch-file"></a>测试批处理文件

在命令行处使用以下 CLI 格式运行批量测试程序。

将 `YOUR-RESOURCE-NAME` 和 `ENDPOINT-KEY` 替换为你自己的服务名称值和终结点密钥值。 这些值可以在 QnA Maker 门户的“设置”页上找到。

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
测试完成后，会生成 `out.tsv` 文件：

> [!div class="mx-imgBorder"]
> ![从批量测试输出第一版 .tsv 文件](../media/batch-test/batch-test-1-output.png)

为了安全起见，已将知识库 ID 替换为 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 进行你自己的批量测试时，此列会显示你的知识库 ID。

第 4 列中置信度分数的测试输出显示，头 3 个问题按预期返回了分数 100，因为每个问题与其在知识库中显示的完全相同。 最后 3 个问题包含新的有关问题的措辞，不返回 100 作为置信度分数。 若要提高测试和用户的分数，需向知识库添加更多备用问题。

## <a name="testing-with-the-optional-fields"></a>使用可选字段进行测试

了解格式和流程以后，即可生成测试文件，以便通过数据源（例如聊天日志）针对知识库运行测试。

由于数据源和流程已自动化，因此可以使用不同的设置多次运行测试文件，以便确定正确的值。

例如，如果你有一个聊天日志且希望确定哪个聊天日志文本适用于哪些元数据字段，则请创建一个测试文件并设置每个行的元数据字段。 运行测试，然后查看与元数据匹配的行。 通常情况下，匹配结果应该为正，但应查看假正结果。 假正是指与元数据匹配但基于不应匹配的文本的行。

## <a name="using-optional-fields-in-the-input-batch-test-file"></a>使用输入批量测试文件中的可选字段

通过以下图表了解如何查找可选数据的字段值。

|列号|可选列|数据位置|
|--|--|--|
|3|metadata|针对现有的键:值对导出现有的知识库。|
|4|top|建议使用默认值 `25`。|
|5|问答集 ID|针对 ID 值导出现有的知识库。 另请注意，ID 已在输出文件中返回。|

## <a name="add-metadata-to-the-knowledge-base"></a>向知识库添加元数据

1. 在 QnA 门户中的“编辑”页上，向以下问题添加元数据 `topic:power`：

    |问题|
    |--|
    |Charge your Surface Pro 4|
    |Check the battery level|

    两个 QnA 对已设置元数据。

    > [!TIP]
    > 若要查看每个集的元数据和 QnA ID，请导出知识库。 选择“设置”页，然后选择以 `.xls` 文件形式“导出”。  找到此下载的文件，使用 Excel 将其打开，查看其中的元数据和 ID。

1. 依次选择“保存并训练”、“发布”页、“发布”按钮。   这些操作使更改适用于批量测试。 从“设置”页下载知识库。

    下载的文件有适用于元数据的正确格式，以及正确的问答集 ID。 在下一部分使用这些字段

    > [!div class="mx-imgBorder"]
    > ![导出的包含元数据的知识库](../media/batch-test/exported-knowledge-base-with-metadata.png)

## <a name="create-a-second-batch-test"></a>创建另一个批量测试

有两个用于批量测试的主要方案：
* **处理聊天日志文件** - 确定上一个未见问题的排名最靠前的答案 - 最常见的情况是你需要处理查询的日志文件，例如某个聊天机器人的用户问题。 只使用所需列创建批处理文件测试。 测试会返回每个问题的排名最靠前的答案。 这并不意味着排名最靠前的答案是正确答案。 完成此测试以后，请转到验证测试。
* **验证测试** - 验证预期的答案。 此测试要求批量测试中的所有问题和匹配的预期答案均已验证。 这可能需要进行一些手动处理。

以下过程假设方案是处理聊天日志

1. 创建新的批量测试文件，使之包括可选数据 `batch-test-data-2.tsv`。 添加来自原始批量测试输入文件中的 6 行，然后为每行添加元数据、top 值和 QnA 对 ID。

    若要模拟按照知识库检查聊天日志中的新文本这一自动化过程，请将每个列的元数据设置为同一值：`topic:power`。

    > [!div class="mx-imgBorder"]
    > ![从批量测试输入第二版 .tsv 文件](../media/batch-test/batch-test-2-input.png)

1. 再次运行测试，更改输入和输出文件的名称，表明这是第二次测试。

    > [!div class="mx-imgBorder"]
    > ![从批量测试输出第二版 .tsv 文件](../media/batch-test/batch-test-2-output.png)

## <a name="test-results-and-an-automated-test-system"></a>测试结果和自动测试系统

此测试输出文件可以作为自动持续测试管道的一部分进行分析。

此特定测试输出应这样解读：每一行都使用元数据进行了筛选，由于每一行都与知识库中的元数据不匹配，因此会返回这些不匹配行的默认答案（“no good match found in kb”）。 那些匹配的行则返回 QnA ID 和分数。

所有行都返回了标签“incorrect”，因为没有任何行与预期的答案 ID 匹配。

你应该可以通过这些结果了解到，你可以采用聊天日志并使用文本作为每行的查询。 在不了解数据的情况下，可以通过结果了解数据的大量相关信息，方便以后使用：

* 元数据
* QnA ID
* score

使用元数据进行筛选是否有利于测试？ 是/否。 测试系统应该为每个元数据对创建测试文件，并创建没有元数据对的测试。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续测试知识库，请删除批处理文件工具和测试文件。

如果不打算继续使用此知识库，请按以下步骤删除知识库：

1. 在 QnA Maker 门户中，从顶部菜单选择“我的知识库”。
1. 在知识库列表中，选择本快速入门的知识库所在行的“删除”图标。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)
