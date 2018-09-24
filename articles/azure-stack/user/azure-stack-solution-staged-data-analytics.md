---
title: 使用 Azure 和 Azure Stack 中创建临时的数据分析解决方案 |Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 中创建临时的数据分析解决方案。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: b704db0b79d056f5c7081d3fed117e1d1f22b336
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978822"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>教程： 使用 Azure 和 Azure Stack 中创建临时的数据分析解决方案 

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何使用在本地和公有云环境来满足多设施企业的需求。 Azure Stack 提供了一个快速、 安全且灵活的解决方案，用于收集、 处理、 存储和分配本地和远程数据，尤其是在安全、 保密性、 企业策略和法规要求可能不同位置之间时，用户。

在此模式下，你的客户收集，以便可以进行快速决定执行哪些将需要在集合时进行分析的数据。 通常此数据收集发生在没有 Internet 访问。 建立连接后，你可能需要执行占用大量资源的分析数据以获取更多见解。 公共云已经太晚了，或者不可用时，你仍可以分析数据。

在本教程中，我们将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 创建的原始数据存储 blob。
> - 创建一个新的 Azure Stack 函数，以便从 Azure Stack 的干净数据移到 Azure。
> - 创建 Blob 存储触发的函数。
> - 创建 Azure Stack 存储帐户包含 blob 和队列。
> - 创建队列触发的函数。
> - 测试队列触发的函数。

> [!Tip]  
> ![混合 pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 提供的敏捷性和创新的云计算到您的本地环境并启用唯一的混合云，可用于生成和部署混合应用任意位置。  
> 
> 本白皮书[混合应用程序的设计注意事项](https://aka.ms/hybrid-cloud-applications-pillars)评审 （放置、 可伸缩性、 可用性、 复原能力、 可管理性和安全性） 的软件质量的构成要素进行设计、 部署和操作混合应用程序。 设计注意事项，帮助您优化混合应用程序设计，最大程度减少在生产环境中的挑战。

## <a name="prerequisites"></a>必备项

生成此解决方案时需要一些准备工作：

-   安装并运行 Azure Stack (详细信息可在此处找到： [Azure Stack 概述](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-storage-overview))

-   Azure 订阅。 (创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   下载并安装 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)。

-   未提供这些函数处理的数据。 数据必须可生成可用于上传到 Azure Stack 存储 blob 容器。

## <a name="issues-and-considerations"></a>问题和注意事项

### <a name="scalability-considerations"></a>可伸缩性注意事项

Azure functions 和存储解决方案扩展以满足数据量和处理需求。 有关 Azure 的可伸缩性信息和目标，请参阅[Azure 的可扩展性文档](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。

### <a name="availability-considerations"></a>可用性注意事项

存储是此模式的主要可用性注意事项。 通过快速链接连接是必需的大型数据卷处理和分发。

### <a name="manageability-considerations"></a>可管理性注意事项

请考虑开发工具和源代码管理将使您能够管理你的解决方案。

## <a name="create-the-raw-data-storage-blob"></a>创建的原始数据存储 blob

存储帐户和 blob 容器将保存生成的本地活动，包括计算机和员工活动、 设施数据、 生产指标和其他报告的所有原始数据。

1.  登录到[ *Azure Stack 门户*](https://portal.local.azurestack.external/)。

2.  在 Azure Stack 门户中，展开左侧和右侧以打开服务菜单，然后选择菜单**所有服务**。 向下滚动到**存储**，然后选择**存储帐户**。 在存储帐户窗口中，选择**添加**。

3.  为帐户使用以下信息：

    a.  名称：**所选**

    b.  部署模型：**资源管理器**

    c.  帐户类型：**存储 (常规用途 V1)**

    d.  位置：**美国西部**

    e.  复制：**本地冗余存储 (LRS)**

    f.  性能：**标准**

    g.  需要安全传输：**已禁用**

    h.如果该值不存在，请单击“添加行”。  订阅： 选择其中一个

    i.  资源组： 指定新的资源组或选择现有资源组

    j.  配置虚拟网络：**已禁用**

4.  选择**创建以创建存储帐户**。

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image1.png)

5.  创建后，选择存储帐户的名称。

6.  在帐户边栏选项卡下, **BLOB 服务**标题下方，选择**容器**。

7.  在边栏选项卡的顶部，选择 **+ 容器。** 然后选择**容器**。

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image2.png)

8.  名称：**所选**

9.  公共访问级别：**容器**（容器和 blob 的匿名读取访问）

10.  选择“确定” 。

## <a name="create-an-azure-stack-function"></a>创建 Azure Stack 函数

创建一个新的 Azure Stack 函数来清理数据从 Azure Stack 移到 Azure。

1.  通过单击创建新的函数**函数**，然后 **+ 新建函数**按钮。

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image3.png)

2.  选择**计时器触发器**。

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image4.png)

3.  选择**C\#** 作为语言，并将函数命名：`upload-to-azure`将计划设置为`0 0 * * * *`，这在 CRON 表示法是一次一小时。

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image5.png)

4.  创建函数应用使用图像下的表中指定的设置。

    | 设置 | 建议的值 | 描述 |
    | ---- | ---- | ---- |
    | 应用名称 | 全局唯一名称 | 用于标识新 Function App 的名称。 有效字符是`a` - `z`， `0``-9`，和`-`。 |
    | 订阅 | 你的订阅 | 要在其下创建此新函数应用的订阅。 |
    | **资源组** |  |  |
    | MyResourceGroup | 要在其中创建 Function App 的新资源组的名称。 |  |
    | OS | 窗口 | 仅当在 Windows 上运行时，才能使用无服务器托管。 |
    | **托管计划** |  |  |
    | 消耗量计划 | 定义如何将资源分配给 Function App 的托管计划。 在默认消耗计划中，根据函数需求动态添加资源。 在此无服务器托管，只需支付函数运行的时间。 |  |
    | 位置 | 离你最近的区域 | 选择靠近你或其他服务近的区域你函数的访问权限。 |
    | **存储帐户** |  |  |
    | \<在上面创建的存储帐户 > | Function App 使用的新存储帐户的名称。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可以使用现有帐户。 |  |

    **示例：**

    ![定义新的函数应用设置](media\azure-stack-solution-staged-data-analytics\image6.png)

5.  选择“创建”以预配和部署函数应用。

6.  选择门户右上角的“通知”图标，留意是否显示“部署成功”消息。

    ![定义新的函数应用设置](media\azure-stack-solution-staged-data-analytics\image7.png)

7.  选择**转到资源**若要查看新的 function app。

![已成功创建 Function App。](media\azure-stack-solution-staged-data-analytics\image8.png)

## <a name="create-a-blob-storage-triggered-function"></a>创建 Blob 存储触发的函数

1.  展开函数应用，然后选择**+** 按钮旁边**函数**。 如果函数应用中的第一个函数，请选择**自定义函数**。 此时将显示函数模板的完整集合。

  ![Azure 门户中的 Functions 快速入门页](media\azure-stack-solution-staged-data-analytics\image9.png)

2.  在搜索字段中，键入 blob，然后选择用于 Blob 存储触发器模板所需的语言。

  ![选择 Blob 存储触发器模板。](media\azure-stack-solution-staged-data-analytics\image10.png)

3.  使用下表中指定的设置：

    | 设置 | 建议的值 | 描述 |
    | ------- | ------- | ------- |
    | 姓名 | 在 Function App 中唯一 | 此 Blob 触发函数的名称。 |
    | 路径 | \<从上面的存储位置的路径 > | 所监视的 Blob 存储中的位置。 为名称参数绑定中传递的 blob 的文件名称。 |
    | 存储帐户连接 | 函数应用的连接 | 可以使用函数应用已使用的存储帐户连接，也可以创建一个新。 |

    **示例：**

    ![创建 Blob 存储触发的函数。](media\azure-stack-solution-staged-data-analytics\image11.png)

4.  选择“创建”创建该函数。

### <a name="test-the-function"></a>测试函数

1.  在 Azure 门户中，浏览到该函数。 展开**日志**在页面底部，并确保日志流式处理未暂停。

2.  打开存储资源管理器并连接到在本部分的开始处创建的存储帐户。

3.  展开存储帐户中， **Blob 容器**，以及 blob 你之前创建。 选择**上传**，然后**将文件上传**。

    ![将文件上传到 blob 容器。](media\azure-stack-solution-staged-data-analytics\image12.png)

4.  在上传文件对话框中，选择文件字段。 浏览到本地计算机，如图像文件上的文件，选择它并选择**开放**，然后**上传**。

5.  返回到函数日志并验证已读取 blob。

    **示例：**

    ![在日志中查看消息。](media\azure-stack-solution-staged-data-analytics\image13.png)

## <a name="create-an-azure-stack-storage-account"></a>创建 Azure Stack 存储帐户

创建 Azure Stack 存储帐户包含 blob 和队列。

### <a name="storage-blob--data-archiving"></a>存储 Blob 数据存档

此存储帐户将存放两个容器。 这些容器是一个用于存放存档数据的 blob 和队列用于为总部分发分配的数据的处理。

使用步骤和设置上述体系结构来创建作为我们存档存储的另一个存储帐户和 blob 容器。

### <a name="storage-queue--filtered-data-holding"></a>存储队列筛选数据持有锁

1.  使用步骤和上述体系结构来访问新的存储帐户的设置。

2.  在存储帐户概述部分中，选择**队列。**

3.  选择 **+ 队列**和填充**名称**字段和填充到新队列的名称。

4.  选择**确定。**

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image14.png)

    ![Alt text](media\azure-stack-solution-staged-data-analytics\image15.png)

## <a name="create-a-queue-triggered-function"></a>创建队列触发的函数

1.  使用在上面的函数创建部分步骤以使用队列触发器而不是 blob 触发器创建另一个 Azure Stack 函数。

2.  使用下表中指定的设置：

    | 设置 | 建议的值 | 描述 |
    | ------- | ------- | ------- |
    | 姓名 | 在 Function App 中唯一 | 此队列触发函数的名称。 |
    | 路径 | \<从上面的存储位置的路径 > | 正在监视的存储中的位置。 为名称参数绑定中传递的队列的文件名称。 |
    | 存储帐户连接 | 函数应用的连接 | 可以使用函数应用已使用的存储帐户连接，也可以创建一个新。 |

3.  选择“创建”创建该函数。

## <a name="test-the-queue-triggered-function"></a>测试队列触发的函数

1.  在 Azure 门户中，浏览到该函数。 展开**日志**在页面底部，并确保日志流式处理未暂停。

2.  打开存储资源管理器并连接到在本部分的开始处创建的存储帐户。

3.  展开存储帐户中， **Blob 容器**，以及 blob 你之前创建。 选择**上传**，然后**将文件上传。**

    ![将文件上传到 blob 容器。](media\azure-stack-solution-staged-data-analytics\image12.png)

4.  在上传文件对话框中，选择文件字段。 浏览到本地计算机，如图像文件上的文件，选择它并选择**开放**，然后**上传**。

5.  返回到函数日志并验证已读取 blob。

  **示例：**

    ![在日志中查看消息。](media\azure-stack-solution-staged-data-analytics\image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>安全地存储和访问兼容的数据

安全地存储、 处理、 分布式和通过 Azure 和 Azure Stack 暂存数据分析和自定义终结点指令访问全球企业数据。 远程办公员工和机制活动、 设备数据和业务指标不断地编译、 存储、 测试的符合性，和根据公司策略和区域的规定。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。