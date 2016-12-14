---
title: "使用 Azure 机器学习 Web 服务门户管理 Web 服务 | Microsoft Docs"
description: "管理对 Azure 机器学习工作区的访问，部署并管理 ML API Web 服务"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: jhubbard
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3b04aa7406f44130deb4f12c856b459369301449


---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>使用 Azure 机器学习 Web 服务门户管理 Web 服务
可以使用 Microsoft Azure 机器学习 Web 服务门户来管理机器学习 Web 服务（新 Web 服务和经典 Web 服务）。 由于经典 Web 服务和新的 Web 服务基于不同的基础技术，针对它们的管理能力会稍有不同。

在机器学习 Web 服务门户中，你可以：

* 监视 Web 服务的使用方式。
* 配置说明、更新 Web 服务的密钥（仅限于新的 Web 服务）、更新存储帐户的密钥（仅限于新的 Web 服务）、启用日志记录以及启用或禁用示例数据。
* 删除 Web 服务。
* 创建、 删除或更新计费计划 （仅限于新的Web 服务）。
* 添加和删除终结点（仅限于经典 Web 服务）

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>管理新的 Web 服务
要管理新的 Web 服务：

1. 使用 Microsoft Azure 帐户登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/quickstart)门户 - 使用与 Azure 订阅相关联的帐户。
2. 在菜单上，单击“**Web 服务**”。

此时将显示你订阅的已部署 Web 服务列表。 

要管理 Web 服务，请单击“Web 服务”。 可以从“Web 服务”页上：

* 单击 Web 服务以对其进行管理。
* 单击 Web 服务的“计费计划”以进行更新。
* 删除 Web 服务。
* 复制并部署 Web 服务到另一个区域。

单击 Web 服务时，将打开 Web 服务快速入门页。 Web 服务快速入门页提供了两个允许管理 Web 服务的菜单选项：

* **仪表板** - 允许查看 Web 服务的使用情况。
* **配置** - 允许添加说明性文本、更新与 Web 服务相关联的存储帐户的密钥，以及启用或禁用示例数据。

### <a name="monitoring-how-the-web-service-is-being-used"></a>监视 Web 服务的使用方式
单击“**仪表板**”选项卡 。

从仪表板可以查看在一段时间内 Web 服务的总体使用情况。 可以从使用情况图表右上角的“期间”下拉菜单中选择要查看的期间。 仪表板显示以下信息：

* “**一段时间内的请求**”以步进图显示所选时间段内的请求数。 它可以帮助识别是否经历了使用峰值。
* “**请求 - 响应请求**”显示所选时间段内此服务收到的合计请求-响应调用数，以及有多少请求失败。
* “**平均请求 - 响应计算时间**”显示执行接收请求所需的平均时间。
* “**批请求**”显示所选时间段内此服务收到的合计批请求数，以及有多少批请求失败。
* “**平均作业滞后时间**”显示执行接收请求所需的平均时间。
* “**错误**”显示调用 Web 服务时合计发生的错误数。
* “**服务成本**”显示与服务关联的计费计划的费用。

### <a name="configuring-the-web-service"></a>配置 Web 服务
单击“**配置**”菜单选项。

可以更新以下属性：

* “**说明**”允许你输入 Web 服务的说明。
* “**标题**”允许你输入 Web 服务的标题
* “**密钥**”允许你修改主要和次要 API 密钥。
* “**存储帐户密钥**”允许更新与 Web 服务更改相关联的存储帐户密钥。 
* “**启用示例数据**”允许提供可用于测试请求 - 响应服务的示例数据。 如果在机器学习工作室中创建 Web 服务，示例数据将来自你用于训练模型的数据。 如果以编程方式创建服务，则从作为 JSON 包的一部分提供的示例数据中获取数据。

### <a name="managing-billing-plans"></a>管理计费计划
从 Web 服务快速入门页的菜单选项单击“**计划**”。 还可以单击与特定的 Web 服务关联的计划来管理该计划。

* “**新建**”允许创建一个新的计划。
* “**添加/删除计划实例**”允许“向外扩展”现有的计划来增加容量。
* “**升级/降级**”允许"向上扩展"现有的计划来增加容量。
* “**删除**”允许删除某一个计划。

单击一个计划以查看其仪表板。 仪表板为你提供所选时间段内的快照或计划使用情况。 若要选择要查看的时间段，单击仪表板右上方的“**期间**”下拉列表。 

计划仪表板提供了以下信息：

* “**计划描述**”显示有关与计划关联的成本和容量信息。
* “**计划使用情况**”显示已针对计划计费的事务数和计算小时数。
* “**Web 服务**”显示正在使用此计划的 Web 服务数。
* “**按调用列出的前几个 Web 服务**”显示产生最多调用并已按计划收费的前四个 Web 服务。
* “**按计算小时数列出的前几个 Web 服务**”显示正在使用计算资源并已按计划收费的前四个 Web 服务。

## <a name="manage-classic-web-services"></a>管理经典 Web 服务
> [!NOTE]
> 此部分中的过程与通过 Azure 机器学习 Web 服务门户管理经典 Web 服务相关。 有关通过机器学习工作区和 Azure 经典门户管理传统 Web 服务的信息，请参阅[管理 Azure 机器学习工作区](machine-learning-manage-workspace.md)。
> 
> 

要管理经典 Web 服务：

1. 使用 Microsoft Azure 帐户登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/quickstart)门户 - 使用与 Azure 订阅相关联的帐户。
2. 在菜单上，单击“**经典 Web 服务**”。

要管理经典 Web 服务，单击“**经典 Web 服务**”。 可以从“经典 Web 服务”页上：

* 单击 Web 服务以查看关联的终结点。
* 删除 Web 服务。

在管理经典 Web 服务时，每个终结点单独管理。 单击“Web 服务”页中的 Web 服务时会打开与服务关联的终结点列表。 

在“经典 Web 服务”终结点页，可以添加和删除服务上的终结点。 有关添加终结点的详细信息，请参阅[创建终结点](machine-learning-create-endpoint.md)。

单击其中的一个终结点，以打开 Web 服务快速入门页。 在“快速入门”页上，有两个菜单选项可用于管理 Web 服务：

* **仪表板** - 允许查看 Web 服务的使用情况。
* **配置** - 允许添加说明性文本、打开或关闭错误日志记录、更新与 Web 服务相关联的存储帐户的密钥，以及启用和禁用示例数据。

### <a name="monitoring-how-the-web-service-is-being-used"></a>监视 Web 服务的使用方式
单击“**仪表板**”选项卡 。

从仪表板可以查看在一段时间内 Web 服务的总体使用情况。 可以从使用情况图表右上角的“期间”下拉菜单中选择要查看的期间。 仪表板显示以下信息：

* “**一段时间内的请求**”以步进图显示所选时间段内的请求数。 它可以帮助识别是否经历了使用峰值。
* “**请求 - 响应请求**”显示所选时间段内此服务收到的合计请求-响应调用数，以及有多少请求失败。
* “**平均请求 - 响应计算时间**”显示执行接收请求所需的平均时间。
* “**批请求**”显示所选时间段内此服务收到的合计批请求数，以及有多少批请求失败。
* “**平均作业滞后时间**”显示执行接收请求所需的平均时间。
* “**错误**”显示调用 Web 服务时合计发生的错误数。
* “**服务成本**”显示与服务关联的计费计划的费用。

### <a name="configuring-the-web-service"></a>配置 Web 服务
单击“**配置**”菜单选项。

可以更新以下属性：

* “**说明**”允许你输入 Web 服务的说明。 “说明”是必填字段。
* “**日志记录**”允许启用或禁用终结点上的错误记录。 有关日志记录的详细信息，请参阅[为机器学习 Web 服务启用日志记录](machine-learning-web-services-logging.md)。
* “**启用示例数据**”允许提供可用于测试请求 - 响应服务的示例数据。 如果在机器学习工作室中创建 Web 服务，示例数据将来自你用于训练模型的数据。 如果以编程方式创建服务，则从作为 JSON 包的一部分提供的示例数据中获取数据。

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>授予或暂停门户中的用户对 Web 服务的访问权
通过使用 Azure 经典门户，你可以允许或拒绝对特定用户的访问。

### <a name="access-for-users-of-new-web-services"></a>新的 Web 服务的用户访问权限
若要允许其他用户使用 Azure 机器学习 Web 服务门户中的 Web 服务，必须将他们添加为 Azure 订阅的共同管理员。

使用 Microsoft Azure 帐户登录到 [Azure 经典门户](https://manage.windowsazure.com/) - 使用与 Azure 订阅相关联的帐户。

1. 在导航窗格中，单击“**设置**”，然后单击“**管理员**”。
2. 在窗口底部，单击“**添加**”。 
3. 在“添加共同管理员”对话框中，键入要添加为共同管理员的人员的电子邮件地址，然后选择允许共同管理员访问的订阅。
4. 单击“保存” 。

### <a name="access-for-users-of-classic-web-services"></a>经典 Web 服务的用户访问权限
要管理工作区：

使用 Microsoft Azure 帐户登录到 [Azure 经典门户](https://manage.windowsazure.com/) - 使用与 Azure 订阅相关联的帐户。

1. 在 Microsoft Azure 服务面板中，单击“**机器学习**”。
2. 单击想要管理的工作区。
3. 单击“**配置**” 选项卡。

从配置选项卡中，可以通过单击“**拒绝**”暂停对机器学习工作区的访问。 用户将不再能够在机器学习工作室中打开工作区。 若要恢复访问，单击“**允许**”。

对于特定用户：

若要管理其他有权访问机器学习工作室中的工作区的帐户，单击”**仪表板**“选项卡中的”**登录到机器学习工作室**“。 这将打开机器学习工作室中的工作区。 从此处单击”**设置**“选项卡，然后单击”**用户**“。 你可以单击”**邀请多个用户**“，以允许用户访问工作区，或选择一个用户并单击”**删除**“。

> [!NOTE]
> ”**登录到机器学习工作室**“链接会使用当前登录的 Microsoft 帐户打开机器学习工作室。 用于登录 Azure 经典门户以创建工作区的 Microsoft 帐户不会自动具有打开工作区的权限。 若要打开工作区，则必须登录到已定义为工作区所有者的 Microsoft 帐户，或者需要收到所有者发出的邀请以加入工作区。
> 
> 




<!--HONumber=Nov16_HO3-->


