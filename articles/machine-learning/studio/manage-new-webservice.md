---
title: 使用 Azure 机器学习 Web 服务门户 | Microsoft 文档
description: 管理对 Azure 机器学习工作区的访问，部署并管理 ML API Web 服务
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.openlocfilehash: c02ded6605b6374e0e37462205f8390f54b24b58
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>使用 Azure 机器学习 Web 服务门户管理 Web 服务
可以使用 Microsoft Azure 机器学习 Web 服务门户来管理机器学习 Web 服务（新 Web 服务和经典 Web 服务）。 由于经典 Web 服务和新的 Web 服务基于不同的基础技术，针对它们的管理能力会稍有不同。

在机器学习 Web 服务门户中，可以：

* 监视 Web 服务的使用方式。
* 配置说明、更新 Web 服务的密钥（仅限于新的 Web 服务）、更新存储帐户的密钥（仅限于新的 Web 服务）、启用日志记录以及启用或禁用示例数据。
* 删除 Web 服务。
* 创建、 删除或更新计费计划 （仅限于新的Web 服务）。
* 添加和删除终结点（仅限于经典 Web 服务）

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>用于管理基于资源管理器的新 Web 服务的权限

新 Web 服务以 Azure 资源的形式部署。 因此，必须拥有正确的权限才能部署和管理新 Web 服务。  若要部署或管理新 Web 服务，必须分配有该 Web 服务部署到的订阅上的参与者或管理员角色。 如果邀请其他用户加入机器学习工作区，必须向其分配订阅上的参与者或管理员角色，他们才能部署或管理 Web 服务。 

如果用户没有访问 Azure 机器学习 Web 服务门户中的资源的正确权限，那么在尝试部署 Web 服务时会收到以下错误：

*Web Service deployment failed.This account does not have sufficient access to the Azure subscription that contains the Workspace.In order to deploy a Web Service to Azure, the same account must be invited to the Workspace and be given access to the Azure subscription that contains the Workspace.*（Web 服务部署失败。此帐户没有足够的权限访问工作区所属的 Azure 订阅。若要将 Web 服务部署到 Azure，必须邀请同一帐户加入工作区并为其授予对工作区所属的 Azure 订阅的访问权限。）

有关创建工作区的详细信息，请参阅[创建并共享 Azure 机器学习工作区](create-workspace.md)。

有关设置访问权限的详细信息，请参阅[在 Azure 门户中查看用户和组的访问权限分配 — 公开预览版](../../role-based-access-control/role-assignments-users.md)。


## <a name="manage-new-web-services"></a>管理新的 Web 服务
要管理新的 Web 服务：

1. 使用 Microsoft Azure 帐户登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/quickstart)门户 - 使用与 Azure 订阅相关联的帐户。
2. 在菜单上，单击“**Web 服务**”。

此时会显示你订阅的已部署 Web 服务列表。 

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

* “**说明**”允许输入 Web 服务的说明。
* “**标题**”允许输入 Web 服务的标题
* “**密钥**”允许修改主要和次要 API 密钥。
* “**存储帐户密钥**”允许更新与 Web 服务更改相关联的存储帐户密钥。 
* “**启用示例数据**”允许提供可用于测试请求 - 响应服务的示例数据。 如果在机器学习工作室中创建 Web 服务，示例数据将来自你用于训练模型的数据。 如果以编程方式创建服务，则从作为 JSON 包的一部分提供的示例数据中获取数据。

### <a name="managing-billing-plans"></a>管理计费计划
从 Web 服务快速入门页的菜单选项单击“**计划**”。 还可以单击与特定的 Web 服务关联的计划来管理该计划。

* “**新建**”允许创建一个新的计划。
* “**添加/删除计划实例**”允许“向外扩展”现有的计划来增加容量。
* “**升级/降级**”允许“纵向扩展”现有的计划来增加容量。
* “**删除**”允许删除某一个计划。

单击一个计划以查看其仪表板。 仪表板提供所选时间段内的快照或计划使用情况。 若要选择要查看的时间段，单击仪表板右上方的“**期间**”下拉列表。 

计划仪表板提供了以下信息：

* “**计划描述**”显示有关与计划关联的成本和容量信息。
* “**计划使用情况**”显示已针对计划计费的事务数和计算小时数。
* “**Web 服务**”显示正在使用此计划的 Web 服务数。
* “**按调用列出的前几个 Web 服务**”显示产生最多调用并已按计划收费的前四个 Web 服务。
* “**按计算小时数列出的前几个 Web 服务**”显示正在使用计算资源并已按计划收费的前四个 Web 服务。

## <a name="manage-classic-web-services"></a>管理经典 Web 服务
> [!NOTE]
> 此部分中的过程与通过 Azure 机器学习 Web 服务门户管理经典 Web 服务相关。 有关通过机器学习工作室和 Azure 门户管理经典 Web 服务的信息，请参阅[管理 Azure 机器学习工作区](manage-workspace.md)。
> 
> 

要管理经典 Web 服务：

1. 使用 Microsoft Azure 帐户登录到 [Microsoft Azure 机器学习 Web 服务](https://services.azureml.net/quickstart)门户 - 使用与 Azure 订阅相关联的帐户。
2. 在菜单上，单击“**经典 Web 服务**”。

要管理经典 Web 服务，单击“**经典 Web 服务**”。 可以从“经典 Web 服务”页上：

* 单击 Web 服务以查看关联的终结点。
* 删除 Web 服务。

在管理经典 Web 服务时，每个终结点单独管理。 单击“Web 服务”页中的 Web 服务时会打开与服务关联的终结点列表。 

在“经典 Web 服务”终结点页，可以添加和删除服务上的终结点。 有关添加终结点的详细信息，请参阅[创建终结点](create-endpoint.md)。

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

* “**说明**”允许输入 Web 服务的说明。 “说明”是必填字段。
* “**日志记录**”允许启用或禁用终结点上的错误记录。 有关日志记录的详细信息，请参阅[为机器学习 Web 服务启用日志记录](web-services-logging.md)。
* “**启用示例数据**”允许提供可用于测试请求 - 响应服务的示例数据。 如果在机器学习工作室中创建 Web 服务，示例数据将来自你用于训练模型的数据。 如果以编程方式创建服务，则从作为 JSON 包的一部分提供的示例数据中获取数据。


