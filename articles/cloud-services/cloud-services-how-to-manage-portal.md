---
title: "常见的云服务管理任务 | Microsoft Docs"
description: "了解如何在 Azure 门户中管理云服务。 这些示例使用 Azure 门户。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 2501b6480e81b236995c37db7171a4ed1429dcbf
ms.openlocfilehash: 3400c137088800398e3757ecfcfb5007044ea66c


---
# <a name="how-to-manage-cloud-services"></a>如何管理云服务
> [!div class="op_single_selector"]
> * [Azure 门户](cloud-services-how-to-manage-portal.md)
> * [Azure 经典门户](cloud-services-how-to-manage.md)
> 
> 

可以在 Azure 门户的“云服务(经典)”区域中管理云服务。 本文介绍在管理云服务时可以执行的一些常见操作。 其中包括更新、删除、缩放过渡部署并将其升级为生产部署。

有关如何缩放云服务的详细信息，请参阅[此文](cloud-services-how-to-scale-portal.md)。

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>如何：更新云服务角色或部署
如果需要更新云服务的应用程序代码，请使用云服务边栏选项卡上的“更新”。 可以更新一个角色或所有角色。 若要进行更新，可以上载新服务包或服务配置文件。

1. 在 [Azure 门户][Azure 门户]中，选择要更新的云服务。 此步骤将打开云服务实例边栏选项卡。
2. 在边栏选项卡中，单击“更新”按钮。
   
    ![更新按钮](./media/cloud-services-how-to-manage-portal/update-button.png)

3. 使用新服务包文件 (.cspkg) 和服务配置文件 (.cscfg) 更新部署。
   
    ![更新部署](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **（可选）**更新部署标签和存储帐户。 
5. 如果任何角色只有一个角色实例，请选择“即使一个或多个角色包含单个实例也进行部署”，使升级能够继续进行。 
   
    如果每个角色至少具有两个角色实例（虚拟机），那么 Azure 在云服务更新期间只能保证 99.95% 的服务可用性。 因为有两个角色实例，其中一个虚拟机可以在另一个虚拟机更新时处理客户端请求。
    
6. 若要在上传包完成后应用更新，请选中“开始部署”。
7. 单击“确定”开始更新服务。

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>如何：交换部署以将暂留部署升级到生产环境
如果决定部署云服务的新版本，可以在云服务过渡环境中暂存和测试新版本。 使用“交换”将用于寻址这两个部署的 URL 地址互换，将新版本提升为生产。 

可以通过“云服务”页或仪表板来交换部署。

1. 在 [Azure 门户][Azure 门户]中，选择要更新的云服务。 此步骤将打开云服务实例边栏选项卡。
2. 在边栏选项卡中，单击“交换”按钮。
   
    ![云服务交换](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. 将打开以下确认提示。
   
    ![云服务交换](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. 验证部署信息后，单击“确定”交换部署。
   
    交换部署的速度很快，因为唯一发生更改的是部署所用的虚拟 IP 地址 (VIP)。
   
    为节省计算成本，可以在验证生产部署能够按预期运行后删除过渡部署。

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>如何：将资源链接到云服务
Azure 门户不会像当前 Azure 经典门户一样将资源链接在一起。 而是将其他资源部署到云服务正在使用的同一资源组。

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>如何：删除部署和云服务
必须先删除每个现有部署，然后才能删除云服务。

为节省计算成本，可以在验证生产部署能够按预期运行后删除过渡部署。 停止的已部署角色实例仍会产生计算成本。

可使用以下过程删除部署或云服务。 

1. 在 [Azure 门户][Azure 门户]中，选择要删除的云服务。 此步骤将打开云服务实例边栏选项卡。
2. 在边栏选项卡中，单击“删除”按钮。
   
    ![云服务交换](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. 可以通过勾选“云服务及其部署”，或通过选择“生产部署”或“暂存部署”来删除整个云服务。
   
    ![云服务交换](./media/cloud-services-how-to-manage-portal/delete-blade.png) 

4. 单击底部的“删除”按钮。
5. 要删除云服务，则单击“删除云服务”。 然后在出现确认提示时单击“是”。

> [!NOTE]
> 删除云服务时，如果已配置详细监视，则必须从存储帐户中手动删除数据。 有关在何处查找度量值表的信息，请参阅[此](cloud-services-how-to-monitor.md)文章。
> 
> 

[Azure 门户]: https://portal.azure.com

## <a name="next-steps"></a>后续步骤
* [云服务的常规配置](cloud-services-how-to-configure-portal.md)。
* 了解如何[部署云服务](cloud-services-how-to-create-deploy-portal.md)。
* 配置[自定义域名](cloud-services-custom-domain-name-portal.md)。
* 配置 [SSL 证书](cloud-services-configure-ssl-certificate-portal.md)。




<!--HONumber=Nov16_HO3-->


