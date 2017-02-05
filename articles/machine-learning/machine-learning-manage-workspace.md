---
title: "管理机器学习工作区 | Microsoft Docs"
description: "管理对 Azure 机器学习工作区的访问，部署并管理 ML API Web 服务"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: c5479dd817e8929ff5e9129d4643d49758e7ab16
ms.openlocfilehash: 32e74821309507fed4a20b005dbfbba12ffb0703


---
# <a name="manage-an-azure-machine-learning-workspace"></a>管理 Azure 机器学习工作区
> [!NOTE]
> 本文中的过程与 Azure 机器学习经典 Web 服务相关。 有关在机器学习 Web 服务门户中管理 Web 服务的信息，请参阅[使用 Azure 机器学习 Web 服务门户管理 Web 服务](machine-learning-manage-new-webservice.md)。
> 
> 

使用 Azure 经典门户，可管理机器学习工作区以：

* 监视工作区的使用情况
* 将工作区配置为允许或拒绝访问
* 管理在工作区中创建的 Web 服务
* 删除工作区

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

此外，仪表板选项卡提供工作区使用情况的概述和工作区信息的速览。  

> [!TIP]
> 在 Azure 机器学习工作室中，在“WEB 服务”选项卡上可添加、更新或删除机器学习 Web 服务。
> 
> 

要管理工作区：

1. 使用 Microsoft Azure 帐户登录到 [Azure 经典门户](https://manage.windowsazure.com/) - 使用与 Azure 订阅相关联的帐户。
2. 在 Microsoft Azure 服务面板中，单击“**机器学习**”。
3. 单击想要管理的工作区。

工作区页面有三个选项卡：

* **仪表板** - 允许查看工作区使用情况和信息
* **配置** - 允许管理对工作区的访问
* **Web 服务** - 允许管理已从此工作区发布的 Web 服务

## <a name="to-monitor-how-the-workspace-is-being-used"></a>监视工作区的使用情况
单击“**仪表板**”选项卡 。

从仪表板中，可查看工作区的总体使用情况，并获取工作区信息的速览。

* “计算”图显示正在由工作区使用的计算资源。 可更改视图以显示相对或绝对值，并且可更改图中显示的时间范围。
* **使用情况概述**显示正在由工作区使用的 Azure 存储。
* **速览**提供工作区信息和有用的链接的摘要。

> [!NOTE]
> ”**登录到机器学习工作室**“链接会使用当前登录的 Microsoft 帐户打开机器学习工作室。 用于登录 Azure 经典门户以创建工作区的 Microsoft 帐户不会自动具有打开工作区的权限。 若要打开工作区，则必须登录到已定义为工作区所有者的 Microsoft 帐户，或者需要收到所有者发出的邀请以加入工作区。
> 
> 

## <a name="to-grant-or-suspend-access-for-users"></a>为用户授予或暂停访问权限
单击“**配置**” 选项卡。

从配置选项卡，可执行以下操作：

* 通过单击“拒绝”暂停机器学习工作区的访问权限。 用户将不再能够在机器学习工作室中打开工作区。 若要恢复访问，请单击“允许”。

若要管理其他有权访问机器学习工作室中的工作区的帐户，请单击”仪表板“选项卡中的“登录到机器学习工作室”（请参阅关于**登录到机器学习工作室**的以前说明）。 这将打开机器学习工作室中的工作区。 从此处单击”**设置**“选项卡，然后单击”**用户**“。 你可以单击”**邀请多个用户**“，以允许用户访问工作区，或选择一个用户并单击”**删除**“。

## <a name="to-manage-web-services-in-this-workspace"></a>管理此工作区中的 Web 服务
单击“Web 服务”选项卡。

这显示从此工作区发布的 Web 服务的列表。
若要管理 Web 服务，请单击列表中的名称以打开 Web 服务页。

Web 服务可能已定义一个或多个终结点。

* 除“默认”终结点外，还可定义更多终结点。 若要添加终结点，请单击仪表板底部的“管理终结点”以打开 Azure 机器学习 Web 服务门户。
* 若要删除终结点（无法删除“默认”终结点），请单击终结点行开头的复选框，然后单击“删除”。 这将从 Web 服务中删除终结点。
  
  > [!NOTE]
  > 如果当删除 Web 服务终结点时，应用程序正在使用该终结点，应用程序将在下次尝试访问该服务时收到错误。
  > 
  > 

单击 Web 服务的名称以打开它。 

从仪表板可以查看在一段时间内 Web 服务的总体使用情况。 可以从使用情况图表右上角的“期间”下拉菜单中选择要查看的期间。 仪表板显示以下信息：

* “**一段时间内的请求**”以步进图显示所选时间段内的请求数。 它可以帮助识别是否经历了使用峰值。
* “**请求 - 响应请求**”显示所选时间段内此服务收到的合计请求-响应调用数，以及有多少请求失败。
* “**平均请求 - 响应计算时间**”显示执行接收请求所需的平均时间。
* “**批请求**”显示所选时间段内此服务收到的合计批请求数，以及有多少批请求失败。
* “**平均作业滞后时间**”显示执行接收请求所需的平均时间。
* “**错误**”显示调用 Web 服务时合计发生的错误数。
* “**服务成本**”显示与服务关联的计费计划的费用。

从“配置”页面，可更新以下属性：

* “**说明**”允许你输入 Web 服务的说明。 “说明”是必填字段。
* “**日志记录**”允许启用或禁用终结点上的错误记录。 有关日志记录的详细信息，请参阅[为机器学习 Web 服务启用日志记录](machine-learning-web-services-logging.md)。
* “**启用示例数据**”允许提供可用于测试请求 - 响应服务的示例数据。 如果在机器学习工作室中创建 Web 服务，示例数据将来自你用于训练模型的数据。 如果以编程方式创建服务，则从作为 JSON 包的一部分提供的示例数据中获取数据。




<!--HONumber=Jan17_HO1-->


