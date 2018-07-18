---
title: 在 Azure 中创建流量管理器配置文件 | Microsoft Docs
description: 本文介绍如何创建流量管理器配置文件
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: 1994c8374244b62e65b1a54234775d9a39f72bb3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29397564"
---
# <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

本文介绍如何创建“优先级”路由类型的配置文件以将用户路由到两个 Azure Web 应用终结点。 通过使用“优先级”路由类型，所有流量将路由到第一个终结点，而第二个终结点将保留为备份。 因此，如果第一个终结点处于不正常状态，则可以将用户路由到第二个终结点。

在本文中，两个以前创建的 Azure Web 应用终结点将关联到这个新创建的流量管理器配置文件。 若要详细了解如何创建 Azure Web 应用终结点，请访问[“Azure Web 应用文档”页](https://docs.microsoft.com/azure/app-service/)。 可以添加任何具有 DNS 名称且可通过公共 Internet 访问的终结点，例如，我们将使用 Azure Web 应用终结点。

### <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件
1. 在浏览器中，登录 [Azure 门户](http://portal.azure.com)。 如果还没有帐户，可以注册[免费一个月试用版](https://azure.microsoft.com/free/)。 
2. 单击“创建资源” > “网络” > “流量管理器配置文件” > “创建”。
4. 在“创建流量管理器配置文件”中，按如下所示完成操作：
    1. 在**名称**中，提供配置文件的名称。 此名称必须在 trafficmanager.net 区域中唯一，并将生成 DNS 名称（<name>,trafficmanager.net），该名称用于访问流量管理器配置文件。
    2. 在**路由方法**中，选择“优先级”路由方法。
    3. 在**订阅**中，选择要创建此配置文件的订阅
    4. 在**资源组**中，创建新的资源组，以在其下放置此配置文件。
    5. 在**资源组位置**中，选择资源组的位置。 此设置指的是资源组的位置，对将全局部署的流量管理器配置文件没有影响。
    6. 单击“创建”。
    7. 流量管理器配置文件的全局部署完成后，它会在相应的资源组中作为资源之一列出。

    ![创建流量管理器配置文件](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

1. 在门户的搜索栏中，搜索在前面部分中创建的**流量管理器配置文件**名称，并在显示的结果中单击该流量管理器配置文件。
2. 在“流量管理器配置文件”的“设置”部分中，单击“终结点”。
3. 单击 **“添加”**。
4. 按如下所示完成操作：
    1. 对于**类型**，单击“Azure 终结点”。
    2. 提供要用于识别此终结点的**名称**。
    3. 对于**目标资源类型**，单击“应用服务”。
    4. 对于**目标资源**，单击“选择应用服务”以显示同一订阅下的 Web 应用列表。 在“资源”中，选取要添加为第一个终结点的应用服务。
    5. 对于**优先级**，选为 **1**。 如果此终结点处于正常状态，这会导致所有流量转到此终结点。
    6. 使“添加为已禁用”保持未选中状态。
    7. 单击 **“确定”**
5.  对于下一个 Azure Web 应用终结点重复步骤 3 和步骤 4。 确保添加该终结点时将其**优先级**值设为 **2**。
6.  添加完这两个终结点后，这两个终结点会显示在“流量管理器配置文件”中，并且其监视状态为“联机”。

    ![添加流量管理器终结点](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>使用流量管理器配置文件
1.  在门户的搜索栏中，搜索在前面部分中创建的**流量管理器配置文件**名称。 在显示的结果中，单击流量管理器配置文件。
2. 单击“概览”。
3. “流量管理器配置文件”会显示新建的流量管理器配置文件的 DNS 名称。 任何客户端（例如，通过使用 Web 浏览器导航到客户端）均可使用此名称来路由到由路由类型确定的相应终结点。 在这种情况下，所有请求均会路由到第一个终结点，如果流量管理器检测到其处于不正常状态，则流量会自动故障转移到下一终结点。

## <a name="delete-the-traffic-manager-profile"></a>删除流量管理器配置文件
当不再需要时，删除资源组和已创建的流量管理器配置文件。 为此，请从“流量管理器配置文件”中选择该资源组，并单击“删除”。

## <a name="next-steps"></a>后续步骤

- 详细了解[路由类型](traffic-manager-routing-methods.md)。
- 详细了解[终结点类型](traffic-manager-endpoint-types.md)。
- 详细了解[终结点监视](traffic-manager-monitoring.md)。



