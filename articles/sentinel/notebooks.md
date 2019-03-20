---
title: 在 Azure Sentinel 预览版中使用笔记本的搜索功能 |Microsoft Docs
description: 本文介绍如何使用 Azure Sentinel 搜索功能使用笔记本。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 63ce2be847017ed7e80fe5e573d5553311f6af2f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107672"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>使用笔记本来寻找异常

> [!IMPORTANT]
> Azure Sentinel 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 利用交互式的 Jupyter 笔记本的强大功能，提供见解和操作来调查或寻找您的环境中的安全异常。 Azure Sentinel 都预加载了由 Microsoft 的安全分析人员开发的笔记本。 每个 notebook 都是使用特定的用例的自包含工作流生成的用途。 用于更快的数据浏览和威胁搜索每个 notebook 中包含的可视化效果。 自定义内置的笔记本，以满足你的需求、 从头开始创建新的笔记本或从 Azure Sentinel 导入笔记本 GitHub 社区。 作为 Azure Notebooks 页中的项目导入的 Jupyter 笔记本-这可以使用户访问所有在一个位置及其 Azure Sentinel 笔记本。 笔记本可以运行与按钮的单击操作，也可以由用户根据其环境配置。

完全集成的体验使笔记本没有基础的维护开销与云计算和存储上运行。 利用现有的 Jupyter 笔记本生态系统的能力使您能够众包模型，而无需共享客户数据。 


每个 notebook 托管在 Azure Notebooks （当前处于预览状态），在 Azure 云中的交互式开发环境中。 笔记本始终是可访问性，始终可从任何浏览器中，在世界各地。  Azure Sentinel 适用于调查和搜索的内置笔记本克隆到属于你，并可以修改和定制您的环境的项目。 下面是一些最常用的内置笔记本：

- **警报调查和搜索**:此交互式笔记本启用快速会审警报的不同类的情况检索相关的活动和丰富警报相关联的活动和数据从其生成警报。

- **终结点主机引导式搜索**:可以通过向下钻取与终结点主机相关联的安全相关活动 hunt 安全漏洞的迹象。  

- **引导式查找 office 登录**:可以通过可视化的可疑日志的地理位置，以及显示从 Office 365 数据派生的异常登录模式搜寻可疑的登录名。

## <a name="run-a-notebook"></a>运行 notebook
在以下示例中，我们可以运行内置的笔记本，以搜索深度深入探讨以查看执行时，意外位置中的任何人在网络上的某些内容的位置异常。

1. 在 Azure Sentinel 门户中，单击**笔记本**然后选择任意内置笔记本。
  
   ![选择笔记本](./media/notebooks/select-notebook.png)

3. 单击**导入**GitHub 存储库克隆到你的 Azure Notebooks 项目。
   ![导入笔记本](./media/notebooks/import1.png)
4. 每个 notebook 将指导您完成执行 hunt 或调查的步骤。 模型、 库和其他依赖项和配置用于连接到 Azure Sentinel 自动导入若要启用单击一次执行。 预先加载的所有代码和库所需运行 notebook。 您可以立即开始而无需配置 Log Analytics 工作区针对运行 notebook。

   ![导入存储库](./media/notebooks/import2.png)

5. 浏览、 修改并运行提供的所有示例笔记本。 针对许多不同的方案中，这些过程用作构建基块。

   ![选择笔记本](./media/notebooks/import3.png)



## <a name="next-steps"></a>后续步骤
在本文中，您学习了如何运行在 Azure Sentinel 中使用笔记本搜索调查。 若要了解有关 Azure Sentinel 的详细信息，请参阅以下文章：

- [主动寻找威胁](hunting.md)
- [使用书签来保存搜索时的相关信息](bookmarks.md)