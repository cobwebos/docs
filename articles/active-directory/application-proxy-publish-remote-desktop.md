---
title: "使用 Azure AD 应用代理发布远程桌面 | Microsoft Docs"
description: "介绍有关 Azure AD 应用程序代理连接器的基础知识。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 9724ad2e460837157c7677d2c91493cebc8f7012
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---

# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理发布远程桌面

本文介绍如何使用应用程序代理部署远程桌面服务 (RDS)，使远程用户仍可保持工作效率。 

本文的目标读者为：
- 想要通过远程桌面服务发布本地应用程序，为最终用户提供更多应用程序的当前 Azure AD 应用程序代理客户。 
- 想要使用 Azure AD 应用程序代理减小其部署的受攻击面的当前远程桌面服务客户。 此访问在 RDS 中提供有限的一组双重验证和条件访问控制机制。

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>应用程序代理如何适应标准 RDS 部署

标准 RDS 部署包括 Windows Server 上运行的各种远程桌面角色服务。 在[远程桌面服务体系结构](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)中了解多个部署选项。 [使用 Azure AD 应用程序代理的 RDS 部署](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)（如下图所示）与其他部署选项之间的最明显差别在于，应用程序代理方案可以从运行连接器服务的服务器提供永久性出站连接。 其他部署通过负载均衡器保留打开的入站连接。 

![应用程序代理位于 RDS VM 与公共 Internet 之间](./media/application-proxy-publish-remote-desktop/rds-with-app-proxy.png)

在 RDS 部署中，RD Web 角色和 RD 网关角色在面向 Internet 的计算机上运行。 由于以下原因，已公开这些终结点：
- RD Web 为用户提供一个公共终结点用于登录以及查看他们可以访问的各个本地应用程序和桌面。 选择资源后，将在 OS 上使用本机应用创建 RDP 连接。
- 用户启动 RDP 连接后，RD 网关开始发挥作用。 RD 网关处理通过 Internet 传入的加密 RDP 流量，并将其转换到用户要连接的本地服务器。 在此方案中，RD 网关接收的流量来自 Azure AD 应用程序代理。

>[!TIP]
>如果你以前尚未部署 RDS，或者在开始之前想要了解详细信息，请了解如何[使用 Azure Resource Manager 和 Azure 应用商店无缝部署 RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)。

## <a name="requirements"></a>要求

RD Web 和 RD 网关终结点必须位于同一台计算机上，并且有一个共用的根。 RD Web 和 RD 网关将作为单个应用程序发布，因此，你可以在两个应用程序之间体验单一登录。 

应该[已部署 RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure) 并[已启用应用程序代理](active-directory-application-proxy-enable.md)。 

最终用户只能在通过 RD Web 页连接的 Windows 7 和 Windows 10 桌面中访问此方案。 此方案不受其他操作系统的支持，即使是装有 Microsoft 远程桌面应用程序的系统。

最终用户在连接到其资源时，需要使用 Internet Explorer 并启用 RDS ActiveX 加载项。 

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>部署 RDS 和应用程序代理联合方案

为环境设置 RDS 和 Azure AD 应用程序代理后，请遵循合并两个解决方案的步骤。 这些步骤将会引导你以应用程序的形式发布两个面向 Web 的 RDS 终结点（RD Web 和 RD 网关），然后定向 RDS 上的流量，使其流经应用程序代理。

### <a name="publish-the-rd-host-endpoint"></a>发布 RD 主机终结点

1. 使用以下值[发布新的应用程序代理应用程序](application-proxy-publish-azure-portal.md)：
   - 内部 URL：https://\<rdhost\>.com/，其中，\<rdhost\> 是 RD Web 和 RD 网关共享的共用根。 
   - 外部 URL：系统会根据应用程序的名称自动填充此字段，但你可以修改它。 用户访问 RDS 时，将转到此 URL。 
   - 预身份验证方法：Azure Active Directory
   - 转换 URL 标头：否
2. 将用户分配到已发布的 RD 应用程序。 确保这些用户也都有权访问 RDS。
3. 将应用程序的单一登录方法保留为“已禁用 Azure AD 单一登录”。 用户必须在 Azure AD 和 RD Web 上各执行身份验证一次，但可以单一登录到 RD 网关。 
4. 转到“Azure Active Directory” > “应用注册” > *你的应用程序* > “设置”。 
5. 选择“属性”并将“主页 URL”字段更新为指向 RD Web 终结点（如 https://\<rdhost\>.com/RDWeb）。

### <a name="direct-rds-traffic-to-application-proxy"></a>将 RDS 流量定向到应用程序代理

以管理员身份连接到 RDS 部署，然后更改部署的 RD 网关服务器名称。 这可确保连接通过 Azure AD 应用程序代理。

1. 连接到运行 RD 连接代理角色的 RDS 服务器。
2. 启动“服务器管理器”。
3. 在左侧窗格中选择“远程桌面服务”。
4. 选择“概述”。
5. 在“部署概述”部分中，选择下拉菜单并选择“编辑部署属性”。
6. 在“RD 网关”选项卡中，将“服务器名称”字段更改为针对应用程序代理中的 RD 主机终结点设置的外部 URL。 
7. 将“登录方法”字段更改为“密码身份验证”。

  ![RDS 上的“部署属性”屏幕](./media/application-proxy-publish-remote-desktop/rds-deployment-properties.png)

8. 针对每个集合运行以下命令。 用自己的信息替换 \<yourcollectionname\> 和 \<proxyfrontendurl\>。 此命令在 RD Web 与 RD 网关之间启用单一登录并优化性能：

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s: <proxyfrontendurl> `n require pre-authentication:i:1"
   ```

配置远程桌面后，Azure AD 应用程序代理将会充当 RDS.的面向 Internet 的组件。 可以在 RD Web 和 RD 网关计算机上删除其他面向 Internet 的公共终结点。 

## <a name="test-the-scenario"></a>测试方案

在 Windows 7 或 10 计算机上使用 Internet Explorer 测试该方案。

1. 转到设置的外部 URL，或者在 [MyApps 面板](https://myapps.microsoft.com)中找到你的应用程序。
2. 系统将要求你在 Azure Active Directory 中进行身份验证。 请使用分配给应用程序的帐户。
3. 系统将要求你在 RD Web 中进行身份验证。 
4. RDS 身份验证成功后，可以选择所需的桌面或应用程序，然后开始工作。 

## <a name="next-steps"></a>后续步骤

[使用 Azure AD 应用程序代理启用对 SharePoint 的远程访问](application-proxy-enable-remote-access-sharepoint.md)  
[使用 Azure AD 应用程序代理远程访问应用时的安全注意事项](application-proxy-security-considerations.md)

