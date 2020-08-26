---
title: 教程：使用调试会话来诊断、修复和提交对技能组的更改
titleSuffix: Azure Cognitive Search
description: 调试会话（预览版）提供基于门户的界面，可用于评估和修复技能组中的问题/错误
author: tchristiani
ms.author: terrychr
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 2f25cab211d24facea7863c15b6d7671a9657ae9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290676"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>教程：诊断、修正和提交对技能组的更改

在本文中，你将使用 Azure 门户访问调试会话，以修复所提供的技能组存在的问题。 技能组存在一些需要解决的错误。 本教程将引导你通过调试会话确定并解决技能输入和输出存在的问题。

> [!Important]
> 调试会话是一项在提供时未附带服务级别协议的预览功能，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)或使用当前订阅
> * Azure 认知搜索服务实例
> * Azure 存储帐户
> * [Postman 桌面应用](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>创建服务并加载数据

本教程使用 Azure 认知搜索和 Azure 存储服务。

* [下载包含 19 个文件的示例数据](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19)。

* [创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)或[查找现有帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 

   选择 Azure 认知搜索所在的同一区域，以避免带宽费用。
   
   选择 StorageV2（常规用途 V2）帐户类型。

* 打开存储服务页并创建一个容器。 最佳做法是将访问级别指定为“专用”。 将容器命名为 `clinicaltrialdataset`。

* 在容器中，单击“上传”以上传在第一个步骤中下载并解压缩的示例文件。

* [创建 Azure 认知搜索服务](search-create-service-portal.md)或[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

REST 调用需要在每个请求中使用服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥 。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "获取 HTTP 终结点和访问密钥")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="create-data-source-skillset-index-and-indexer"></a>创建数据源、技能组、索引和索引器

在本部分中，将使用 Postman 和提供的集合来创建搜索服务的数据源、技能组、索引和索引器。

1. 如果没有 Postman，则可以[在此处下载 Postman 桌面应用](https://www.getpostman.com/)。
1. [下载调试会话 Postman 集合](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. 启动 Postman
1. 在“文件” > “新建”下，选择要导入的集合。
1. 导入集合后，展开操作列表 (...)。
1. 单击 **“编辑”** 。
1. 输入 searchService 的名称（例如，如果终结点为 `https://mydemo.search.windows.net`，则服务名称为“`mydemo`”）。
1. 输入包含搜索服务主要密钥或辅助密钥的 apiKey。
1. 输入 Azure 存储帐户的密钥页中的 storageConnectionString。
1. 输入在存储帐户中创建的容器的 containerName。

> [!div class="mx-imgBorder"]
> ![在 Postman 中编辑变量](media/cognitive-search-debug/postman-enter-variables.png)

该集合包含四个不同的 REST 调用，可用于完成本部分。

第一个调用创建数据源。 `clinical-trials-ds` 列中的一个值匹配。 第二个调用创建技能组 `clinical-trials-ss`。 第三个调用创建索引 `clinical-trials`。 第四个（也是最后一个）调用创建索引器 `clinical-trials-idxr`。 完成集合中的所有调用后，关闭 Postman 并返回 Azure 门户。

> [!div class="mx-imgBorder"]
> ![使用 Postman 创建数据源](media/cognitive-search-debug/postman-create-data-source.png)

## <a name="check-the-results"></a>检查结果

技能组包含几个常见错误。 在本部分中，运行空查询以返回所有文档时将显示多个错误。 在后续步骤中，将使用调试会话解决这些问题。

1. 在 Azure 门户中转到你的搜索服务。 
1. 选择“索引”选项卡。 
1. 选择 `clinical-trials` 索引
1. 单击“搜索”以运行空查询。 

搜索完成后，将列出两个没有数据的字段；窗口中列出“organizations”和“locations”。 按照以下步骤发现技能组生成的所有问题。

1. 返回搜索服务的“概述”页。
1. 选择“索引器”选项卡。 
1. 单击 `clinical-trials-idxr`，然后选择警告通知。 

预测输出字段映射存在很多问题，并且第三页上有警告，因为一个或多个技能输入无效。

返回搜索服务的“概述”屏幕。

## <a name="start-your-debug-session"></a>启动调试会话

> [!div class="mx-imgBorder"]
> ![启动新的调试会话](media/cognitive-search-debug/new-debug-session-screen-required.png)

1. 单击“调试会话(预览版)”选项卡。
1. 选择“+新建调试会话”
1. 为会话命名。 
1. 将会话连接到存储帐户。 
1. 提供索引器名称。 索引器具有对数据源、技能组和索引的引用。
1. 接受集合中第一个文档的默认文档选择。 
1. 保存会话。 保存会话将启动技能组定义的 AI 扩充管道。

> [!Important]
> 调试会话仅适用于单个文档。 可以选择数据集中的特定文档，否则会话将默认使用第一个文档。

> [!div class="mx-imgBorder"]
> ![新调试会话已启动](media/cognitive-search-debug/debug-execution-complete1.png)

调试会话执行完毕后，该会话默认显示“AI 扩充”选项卡，并突出显示“技能图”。

+ 技能图提供技能组的可视层次结构并从上到下显示其执行顺序。 图中并排的技能并行执行。 图中技能的颜色编码表示技能组中要执行的技能的类型。 在示例中，绿色技能为文本，红色技能为视觉。 单击图中的单个技能将在会话窗口的右窗格中显示该技能实例的详细信息。 技能设置、JSON 编辑器、执行详细信息和错误/警告均可供查看及编辑。
+ 扩充数据结构详细说明通过源文档内容中的技能生成的扩充树中的节点。

“错误/警告”选项卡将提供比之前显示的列表小得多的列表，因为此列表仅详细说明单个文档的错误。 和索引器显示的列表一样，你可以单击警告消息并查看此警告的详细信息。

## <a name="fix-missing-skill-input-value"></a>修复缺失的技能输入值

在“错误/警告”选项卡中，标记为 `Enrichment.NerSkillV2.#1` 的操作存在错误。 此错误的详细信息说明技能输入值“/document/languageCode”存在问题。 

1. 返回“AI 扩充”选项卡。
1. 单击“技能图”。
1. 单击标记为 #1 的技能，在右窗格中显示其详细信息。
1. 找到“languageCode”的输入。
1. 选择该行开头的 </> 符号，并打开表达式计算器。
1. 单击“求值”按钮以确认此表达式导致错误。 它将确认“languageCode”属性不是有效输入。

> [!div class="mx-imgBorder"]
> ![表达式计算器](media/cognitive-search-debug/expression-evaluator-language.png)

可通过两种方法在会话中研究此错误。 第一种方法是查看输入来自何处，层次结构中的哪个技能应该会生成此结果？ “技能详细信息”窗格中的“执行”选项卡应显示输入源。 如果没有源，则表示存在字段映射错误。

1. 单击“执行”选项卡。
1. 查看输入并找到“languageCode”。 未列出此输入的源。 
1. 切换左窗格以显示“扩充数据结构”。 没有与“languageCode”对应的映射路径。

> [!div class="mx-imgBorder"]
> ![扩充数据结构](media/cognitive-search-debug/enriched-data-structure-language.png)

存在与“language”对应的映射路径。 因此，技能设置中存在拼写错误。 若要修复此问题，需要使用“/document/language”表达式更新技能 #1 中的表达式。

1. 为“language”路径打开表达式计算器 </>。
1. 复制表达式。 关闭“服务” 窗口。
1. 转到技能 #1 的技能设置，然后为“languageCode”输入打开表达式计算器 </>。
1. 将新值“/document/language”粘贴到“表达式”框中，然后单击“求值”。
1. 它应该显示正确输入“en”。 单击“应用”以更新表达式。
1. 在右侧的“技能详细信息”窗格中单击“保存”。
1. 在会话的窗口菜单中单击“运行”。 这将启动对使用文档的技能组的另一次执行。 

调试会话执行完毕后，单击“错误/警告”选项卡，它将显示标记为“Enrichment.NerSkillV2.#1”的错误已消失。 但是，还有两个警告，提示服务无法将 organizations 和 locations 的输出字段映射到搜索索引。 缺失的值为“/document/merged_content/organizations”和“/document/merged_content/locations”。

## <a name="fix-missing-skill-output-values"></a>修复缺失的技能输出值

> [!div class="mx-imgBorder"]
> ![错误和警告](media/cognitive-search-debug/warnings-missing-value-locations-organizations.png)

某个技能缺失输出值。 若要确定出现错误的技能，请转到“扩充数据结构”，查找值名称并查看其“原始源”。 在缺失 organizations 和 locations 值的情况下，它们是技能 #1 的输出。 为每个路径打开表达式计算器 </> 时，列出的表达式将分别显示为“/document/content/organizations”和“/document/content/locations”。

> [!div class="mx-imgBorder"]
> ![表达式计算器 organizations 实体](media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png)

这些实体的输出为空，但其输出不应为空。 哪些输入生成此结果？

1. 转到“技能图”并选择技能 #1。
1. 在右侧的“技能详细信息”窗格中选择“执行”选项卡。
1. 为输入“text”打开表达式计算器 </>。

> [!div class="mx-imgBorder"]
> ![text 技能的输入](media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png)

此输入的显示结果看起来不像文本输入。 它看起来像是被新行包围的图像。 缺少文本意味着无法确定任何实体。 查看技能组的层次结构时，系统显示内容首先由技能 #6 (OCR) 处理，然后传递给技能 #5 (Merge)。 

1. 在“技能图”中选择技能 #5 (Merge)。
1. 在右侧的“技能详细信息”窗格中选择“执行”选项卡，然后为输出“mergedText”打开表达式计算器 </>。

> [!div class="mx-imgBorder"]
> ![Merge 技能的输出](media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png)

此处的文本与图像配对。 查看表达式“/document/merged_content”时，可以看到技能 #1 的“organizations”和“locations”路径中的错误。 它不应使用“/document/content”，而是应该使用“/document/merged_content”作为“text”输入。

1. 复制“mergedText”输出的表达式并关闭“表达式计算器”窗口。
1. 在“技能图”中选择技能 #1。
1. 在右侧的“技能详细信息”窗格中选择“技能设置”选项卡。
1. 为“text”输入打开表达式计算器 </>。
1. 将新表达式粘贴到框中。 单击“求值”。
1. 此时应显示包含所添加文本的正确输入。 单击“应用”以更新技能设置。
1. 在右侧的“技能详细信息”窗格中单击“保存”。
1. 在会话的窗口菜单中单击“运行”。 这将启动对使用文档的技能组的另一次执行。

索引器运行完毕后，错误仍然存在。 返回技能 #1 并进行调查。 该技能的输入已从“content”更正为“merged_content”。 这些实体在技能中的输出是什么？

1. 选择“AI 扩充”选项卡。
1. 选择“技能图”并单击技能 #1。
1. 浏览“技能设置”以找到“输出”。
1. 为“organizations”实体打开表达式计算器 </>。

> [!div class="mx-imgBorder"]
> ![organizations 实体的输出](media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png)

计算表达式的结果可给出正确的结果。 该技能正在为“organizations”实体确定正确的值。 但是，实体路径中的输出映射仍在引发错误。 在将技能中的输出路径与错误中的输出路径进行比较时，该技能将 /document/content 节点下的输出、组织和位置作为父级。 而输出字段映射期望结果在 /document/merged_content 节点下成为父级。 在上一步中，输入从“/document/content”更改为“/document/merged_content”。 需要更改技能设置中的上下文，以确保在正确的上下文中生成输出。

1. 选择“AI 扩充”选项卡。
1. 选择“技能图”并单击技能 #1。
1. 浏览“技能设置”以找到“上下文”。
1. 双击“上下文”设置，然后将其编辑为读取“/document/merged_content”。
1. 在右侧的“技能详细信息”窗格中单击“保存”。
1. 在会话的窗口菜单中单击“运行”。 这将启动对使用文档的技能组的另一次执行。

> [!div class="mx-imgBorder"]
> ![技能设置中的上下文更正](media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png)

所有错误均已解决。

## <a name="commit-changes-to-the-skillset"></a>提交对技能组的更改

启动调试会话后，搜索服务创建了技能组的副本。 这样做是为了让所做的更改不会影响生产系统。 现在，你已完成对技能组的调试，接下来可以将修复提交（覆盖原始技能组）到生产系统。 如果要继续更改技能组而不影响生产系统，则可以保存调试会话并在以后重新打开。

1. 在“调试会话”主菜单中，单击“提交更改”。
1. 单击“确定”以确认你希望更新技能组。
1. 关闭调试会话，然后选择“索引器”选项卡。
1. 打开“clinical-trials-idxr”。
1. 单击“重置”。
1. 单击 **“运行”** 。 单击“确定”以确认。

索引器运行完毕后，在“执行历史记录”选项卡中，最近一次运行的时间戳旁边应该有一个绿色复选标记和“成功”字样。若要确保已应用更改，请执行以下操作：

1. 退出“索引器”并选择“索引”选项卡。
1. 打开“clinical-trials”索引，然后在“搜索资源管理器”选项卡中，单击“搜索”。
1. 结果窗口应显示 organizations 和 locations 实体现在已填充预期值。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解技能组](https://docs.microsoft.com/azure/search/cognitive-search-working-with-skillsets)
> [详细了解增量扩充和缓存](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)