---
title: 使用新的会话主机扩展现有主机池 - Azure
description: 如何在 Windows 虚拟桌面中使用新会话主机扩展现有主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365213"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>使用新会话主机扩展现有主机池

在主机池内增加使用量时，可能需要使用新会话主机扩展现有主机池以处理新负载。

本文将介绍如何使用新的会话主机扩展现有主机池。

## <a name="what-you-need-to-expand-the-host-pool"></a>扩展主机池所需的内容

在开始之前，请确保使用以下方法之一创建了主机池和会话主机虚拟机 （VM）：

- [Azure 应用商店产品/服务](./create-host-pools-azure-marketplace.md)
- [GitHub Azure 资源管理器模板](./create-host-pools-arm-template.md)
- [使用 PowerShell 创建主机池](./create-host-pools-powershell.md)

首次创建主机池和会话主机 VM 时，还需要以下信息：

- VM 大小、映像和名称前缀
- 域联接和 Windows 虚拟桌面租户管理员凭据
- 虚拟网络名称和子网名称

接下来的三节是可用于扩展主机池的三种方法。 您可以使用您喜欢的任意部署工具执行任一操作。

>[!NOTE]
>在部署阶段，如果上一会话主机 VM 资源当前已关闭，您将看到错误消息。 出现这些错误是因为 Azure 无法运行 PowerShell DSC 扩展，以验证会话主机 VM 是否已正确注册到现有主机池。 您可以安全地忽略这些错误，也可以通过在启动部署过程之前启动现有主机池中的所有会话主机 VM 来避免错误。

## <a name="redeploy-from-azure"></a>从 Azure 重新部署

如果已使用[Azure 应用商店产品或](./create-host-pools-azure-marketplace.md) [GitHub Azure 资源管理器模板](./create-host-pools-arm-template.md)创建了主机池和会话主机 VM，则可以从 Azure 门户重新部署相同的模板。 重新部署模板会自动重新输入您输入到原始模板中的所有信息，但密码除外。

下面了解如何重新部署 Azure 资源管理器模板以展开主机池：

1. 登录到 Azure[门户](https://portal.azure.com/)。
2. 从 Azure 门户顶部的搜索栏中，搜索**资源组**并选择**服务**下的项。
3. 查找并选择创建主机池时创建的资源组。
4. 在浏览器左侧的面板中，选择 **"部署**"。
5. 为主机池创建过程选择适当的部署：
     - 如果使用 Azure 应用商店产品创建原始主机池，请选择从**rds.wvd 预配-主机池**开始的部署。
     - 如果使用 GitHub Azure 资源管理器模板创建原始主机池，请选择名为**Microsoft.template**的部署。
6. 选择**重新部署**。
     
     >[!NOTE]
     >如果在选择 **"重新部署"** 时模板未自动重新部署，请在浏览器左侧的面板中选择 **"模板"，** 然后选择 **"部署**"。

7. 选择包含现有主机池中的当前会话主机 VM 的资源组。
     
     >[!NOTE]
     >如果看到一个错误，指示您选择其他资源组，即使您输入的资源组正确，请选择另一个资源组，然后选择原始资源组。

8. 输入以下_artifactsLocation *URL*：`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. 在*Rdsh 实例数*中输入所需的新会话主机总数。 例如，如果要将主机池从 5 个会话主机扩展到 8 个，请输入**8**。
10. 输入用于现有域 UPN 的相同现有域密码。 不要更改用户名，因为当您运行模板时，这将导致错误。
11. 输入您为*租户管理员 Upn 或应用程序 ID*输入的用户或应用程序 ID 所使用的相同租户管理员密码。再次，不要更改用户名。
12. 完成提交以展开主机池。

## <a name="run-the-azure-marketplace-offering"></a>运行 Azure 应用商店产品/服务

[使用 Azure 应用商店按照创建主机池](./create-host-pools-azure-marketplace.md)中的说明进行操作，直到到达["运行 Azure 应用商店"产品以预配新的主机池](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)。 达到该点时，需要为每个选项卡输入以下信息：

### <a name="basics"></a>基础

除*默认桌面用户*外，本节中的所有值都应与首次创建主机池和会话主机 VM 时提供的内容匹配：

1.    对于*订阅*，选择首次创建主机池的订阅。
2.    对于*资源组*，选择现有主机池会话主机 VM 所在的同一资源组。
3.    对于*区域*，选择现有主机池会话主机 VM 所在的同一区域。
4.    对于*主机池名称*，输入现有主机池的名称。
5.    对于*桌面类型*，选择与现有主机池匹配的桌面类型。
6.    对于*默认桌面用户*，输入要登录到 Windows 虚拟桌面客户端并在 Azure 应用商店产品完成后访问桌面的任何其他用户的逗号分隔列表。 例如，如果要user3@contoso.com分配和user4@contoso.com访问，请输入user3@contoso.com。user4@contoso.com
7.    选择**下一个 ：配置虚拟机**。

>[!NOTE]
>除*默认桌面用户*外，所有字段必须与现有主机池中配置的内容完全匹配。 如果存在导致新主机池的不匹配。

### <a name="configure-virtual-machines"></a>配置虚拟机

除 VM 总数外，本节中的所有参数值都应与首次创建主机池和会话主机 VM 时提供的内容相匹配。 您输入的 VM 数将是扩展主机池中的 VM 数：

1. 选择与现有会话主机 VM 匹配的 VM 大小。
    
    >[!NOTE]
    >如果要查找的特定 VM 大小未出现在 VM 大小选择器中，那是因为我们尚未将其加入到 Azure 市场工具中。 若要请求 VM 大小，请在 [Windows 虚拟桌面 UserVoice 论坛](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)中创建请求或投票赞成现有请求。

2. 自定义*使用情况配置文件*、*用户总数*和*虚拟机参数数*，以选择要在主机池中具有的会话主机总数。 例如，如果要将主机池从 5 个会话主机扩展到 8 个，请将这些选项配置为访问 8 个虚拟机。
3. 输入虚拟机名称的前缀。 例如，如果输入“prefix”作为名称，则虚拟机的名称将是“prefix-0”、“prefix-1”，依此类推。
4. 选择 **"下一步 ：虚拟机设置**"。

### <a name="virtual-machine-settings"></a>虚拟机设置

本节中的所有参数值都应与首次创建主机池和会话主机 VM 时提供的内容相匹配：

1. 对于*映像源*和*图像操作系统版本*，输入首次创建主机池时提供的信息。
2. 对于*AD 域加入 UPN*和相关密码，请输入您首次创建主机池以将 VM 加入活动目录域时提供的信息。 这些凭据将用于在虚拟机上创建本地帐户。 您可以重置这些本地帐户，以便以后更改其凭据。
3. 对于虚拟网络信息，为现有主机池会话主机 VM 所在的位置选择相同的虚拟网络和子网。
4. 选择**下一个 ：配置 Windows 虚拟桌面信息**。

### <a name="windows-virtual-desktop-information"></a>Windows 虚拟桌面信息

本节中的所有参数值都应与首次创建主机池和会话主机 VM 时提供的内容相匹配：

1. 对于“Windows 虚拟桌面租户组名称”**，请输入租户所在的租户组的名称。 将此字段保留默认值，除非为你提供了具体的租户组名称。
2. 对于“Windows 虚拟桌面租户名称”**，请输入要在其中创建此主机池的租户的名称。
3. 指定首次创建主机池和会话主机 VM 时使用的相同凭据。 如果使用服务主体，请输入服务主体所在的 Azure 活动目录实例的 ID。
4. 选择**下一个 ： 查看 + 创建**。

## <a name="run-the-github-azure-resource-manager-template"></a>运行 GitHub Azure 资源管理器模板

按照运行 Azure[资源管理器模板中的说明来预配新的主机池](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)，并提供除*Rdsh 实例数*之外的所有相同的参数值。 在运行模板后，输入主机池中所需的会话主机 VM 数。 例如，如果要将主机池从 5 个会话主机扩展到 8 个，请输入**8**。

## <a name="next-steps"></a>后续步骤

现在，您已经扩展了现有的主机池，您可以登录到 Windows 虚拟桌面客户端以作为用户会话的一部分对其进行测试。 您可以使用以下任一客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](./connect-windows-7-and-10.md)
- [使用 Web 客户端进行连接](./connect-web.md)
- [使用 Android 客户端进行连接](./connect-android.md)
- [使用 macOS 客户端进行连接](./connect-macos.md)
- [使用 iOS 客户端进行连接](./connect-ios.md)
