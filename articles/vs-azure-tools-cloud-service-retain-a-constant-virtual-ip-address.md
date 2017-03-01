---
title: "如何为云服务保留固定的虚拟 IP 地址 | Microsoft Docs"
description: "了解如何确保 Azure 云服务的虚拟 IP 地址 (VIP) 不更改。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fb9635bfe1111189d72b68b24ac0bfbee8e87203


---
# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>如何为云服务保留固定的虚拟 IP 地址
更新托管于 Azure 中的云服务时，可能需要确保该服务的虚拟 IP 地址 (VIP) 不发生更改。 许多域管理服务使用域名系统 (DNS) 注册域名。 仅当 VIP 保持不变时，DNS 才适用。 可使用 Azure Tools 中的**发布向导**来确保云服务的 VIP 在更新时不更改。 有关如何将 DNS 域管理用于云服务的详细信息，请参阅[为 Azure 云服务配置自定义域名](cloud-services/cloud-services-custom-domain-name.md)。

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>发布云服务而不更改其 VIP
当在特定环境（如生产环境）中第一次将云服务部署到 Azure 时，其 VIP 就已分配。 VIP 不会更改，除非显式删除部署或部署更新过程将其隐式删除。 若要保留 VIP，则切勿删除部署，且务必确保 Visual Studio 不自动删除部署。 可以通过在**发布向导**中指定部署设置来控制行为，该向导支持多个部署选项。 可以指定全新部署或更新部署，后者可以是增量更新或同时更新，这两种更新部署都将保留 VIP。 有关这些不同类型的部署的定义，请参阅 [Publish Azure application wizard](vs-azure-tools-publish-azure-application-wizard.md)（发布 Azure 应用程序向导）。  此外，还可以控制出错时是否删除云服务以前的部署。 如果未正确设置该选项，VIP 可能意外改变。

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>更新云服务，而不更改其 VIP
1. 部署云服务至少一次后，打开 Azure 项目节点的快捷菜单，然后选择“发布”。 此时将显示“发布 Azure 应用程序”向导。
2. 在订阅列表中，选择要部署到的订阅，然后选择“下一步”按钮。 将显示向导的“设置”页。
3. 在“常用设置”选项卡上，验证要部署到的云服务的名称、“环境”、“生成配置”和“服务配置”是否全部正确。
4. 在“高级设置”选项卡上，验证存储帐户和部署标签是否正确、是否清除了“失败时删除部署”复选框以及是否选中了“部署更新”复选框。 通过选中“部署更新”复选框，确保重新发布应用程序时不会删除部署且不会丢失 VIP。 通过清除“失败时删除部署”复选框，确保部署期间出错时不会丢失 VIP。
5. 若要进一步指定需要的角色更新方式，请选择“部署更新”框旁边的“设置”链接，然后选择“部署更新设置”对话框中的“增量更新”或“同时更新”选项。 如果选择增量更新，那么每个实例会一个接一个地进行更新，以使应用程序始终可用。 如果选择同时更新，那么所有实例将同时更新。 同时更新速度更快，但在更新过程中服务可能不可用。
6. 当对设置满意时，选择“下一步”按钮。
7. 在向导的“摘要”页上，验证设置，然后选择“发布”按钮。
   
   > [!WARNING]
   > 如果部署失败，应迅速查明失败原因并重新部署，以避免让云服务保持在中断状态。
   > 
   > 

## <a name="next-steps"></a>后续步骤
若要了解如何从 Visual Studio 发布到 Azure，请参阅 [Publish Azure application wizard](vs-azure-tools-publish-azure-application-wizard.md)（发布 Azure 应用程序向导）。




<!--HONumber=Nov16_HO3-->


