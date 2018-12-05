---
title: 迁移 Microsoft Translator Hub 工作区和项目 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 将 Hub 工作区和项目迁移到自定义翻译。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 611cd0878e88d2e1c0a988f73b57e391c5a8551d
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975901"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>将 Hub 工作区和项目迁移到自定义翻译

可将 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 工作区和项目迁移到自定义翻译。 迁移从 Hub 开始。


下面是迁移过程中要迁移的项：

1.  项目定义。

2.  训练定义将用于在自定义翻译中创建新的模型定义。

3.  训练中使用的并行和单语文件将全部在自定义翻译中作为新文档迁移。

4.  自动生成的系统测试和优化数据将在自定义翻译中作为新文档导出和创建。

对于所有已部署的训练，自定义翻译将会训练模型且不产生任何成本。 可以选择手动部署这些训练。

>[!Note]
>要使训练成功，自定义翻译至少需要 10,000 个提取的句子。 如果提取的句子数少于[建议的最少数目](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences)，自定义翻译将无法执行训练。

所有已成功但尚未部署的训练将在自定义翻译中作为草稿迁移。

## <a name="find-custom-translator-workspace-id"></a>查找自定义翻译工作区 ID

若要迁移 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 工作区，需要获取自定义翻译中的目标工作区 ID。 自定义翻译中的目标工作区是所有 Hub 工作区和项目要迁移到的位置。

可以在自定义翻译的“设置”页上找到目标工作区 ID： 

1.  在自定义翻译门户中转到“设置”页。

2.  在“基本信息”部分可以找到工作区 ID。

    ![如何查找目标工作区 ID](media/how-to/how-to-find-destination-ws-id.png)

3. 请保留目标工作区 ID，以便在迁移过程中参考。

## <a name="migrate-workspace"></a>迁移工作区

将整个 Hub 工作区迁移到自定义翻译时，项目、文档和训练将迁移到自定义翻译。 在迁移之前，必须选择是只要迁移已部署的训练，还是要迁移所有已成功的训练。

迁移工作区：

1.  登录到 Microsoft Translator Hub。

2.  转到“设置”页。

3.  在“设置”页上单击“将工作区数据迁移到自定义翻译”。

    ![如何从 Hub 迁移](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  在下一页上，选择以下两个选项之一：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。  仅部署的训练：如果选择此选项，则只会已部署的系统和相关文档。

    b.  所有成功的训练：如果选择此选项，则会迁移所有已成功的训练和相关文档。

    c.  在自定义翻译中输入目标工作区 ID。

    ![如何从 Hub 迁移](media/how-to/how-to-migrate-from-hub-screen.png)

5.  单击“提交请求”。

## <a name="migrate-project"></a>迁移项目

Microsoft Translator Hub 允许有选择性地迁移项目。

迁移项目：

1.  登录到 Microsoft Translator Hub。

2.  转到“项目”页。

3.  单击相应项目的“迁移”链接。

    ![如何从 Hub 迁移](media/how-to/how-to-migrate-from-hub.png)

4.  在下一页上，选择以下两个选项之一：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。  仅部署的训练：如果选择此选项，则只会已部署的系统和相关文档。 

    b.  所有成功的训练：如果选择此选项，则会迁移所有已成功的训练和相关文档。

    c.  在自定义翻译中输入目标工作区 ID。

    ![如何从 Hub 迁移](media/how-to/how-to-migrate-from-hub-screen.png)

5.  单击“提交请求”。

## <a name="migration-history"></a>迁移历史记录

请求从 Hub 迁移工作区/项目后，在自定义翻译的“设置”页中可以看到迁移历史记录。 

若要查看迁移历史记录，请执行以下步骤：

1.  在自定义翻译门户中转到“设置”页。

2.  在“设置”页的“迁移历史记录”部分，单击“迁移历史记录”。

    ![迁移历史记录](media/how-to/how-to-migration-history.png)

“迁移历史记录”页显示所请求的每次迁移的以下摘要信息。

1.  迁移者：提交此迁移请求的用户的姓名和电子邮件

2.  迁移时间：迁移日期和时间戳

3.  项目数：请求迁移的项目数，以及成功迁移的项目数。

4.  训练数：请求迁移的训练数，以及成功迁移的训练数。

5.  文档数：请求迁移的文档数，以及成功迁移的文档数。

    ![迁移历史记录详细信息](media/how-to/how-to-migration-history-details.png)

如需有关项目、训练和文档的更详细迁移报告，可以选择以 CSV 格式导出详细信息。

>[!Note]
>仅支持迁移到存在 NMT 语言的语言对。 请查看当前[支持的 NMT 语言](https://www.microsoft.com/translator/business/languages/)列表。 对于不存在 NMT 语言的语言对，数据将从 Hub 移到自定义翻译，但无法针对这些语言对执行训练。

## <a name="custom-translator-versus-hub"></a>自定义翻译与 Hub

此表比较了 Microsoft Translator Hub 和自定义翻译器之间的功能。

|   | Hub | 自定义翻译 |
|:-----|:----:|:----:|
|自定义功能状态   | 正式版  | 预览 |
| 文本 API 版本  | V2    | V3  |
| SMT 自定义 | 是   | 否 |
| NMT 自定义 | 否    | 是 |
| 新的统一语音服务自定义 | 否    | 是 |
| 无跟踪 | 是 | 是 |

## <a name="next-steps"></a>后续步骤

- [训练模型](how-to-train-model.md)。
- 通过 [Microsoft 文本翻译 API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 开始使用已部署的自定义翻译模型。
