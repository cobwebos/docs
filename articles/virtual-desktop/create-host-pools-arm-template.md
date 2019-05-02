---
title: 使用 Azure 资源管理器模板-Azure 中创建 Windows 虚拟桌面预览主机池
description: 如何使用 Azure 资源管理器模板在 Windows 虚拟桌面预览中创建主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: b1c7d1fe561ebc11f88644a5bf2fd8bda1c9a6c6
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924997"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建主机池

主机池是 Windows 虚拟桌面预览版租户环境中一个或多个相同虚拟机的集合。 每个主机池可以包含一个应用组，用户可以像在物理桌面上一样与该应用组交互。

按照本部分的说明为 Windows 虚拟桌面租户使用 Microsoft 提供的 Azure 资源管理器模板创建主机池。 本文将告诉您如何在 Windows 虚拟桌面中创建主机池、 Azure 订阅中的 Vm 创建资源组、 加入 AD 域中，这些 Vm 和 Vm 注册到 Windows 虚拟桌面。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>您需要运行 Azure 资源管理器模板

请确保运行 Azure 资源管理器模板之前了解以下操作：

- 其中，是图像的你想要使用的源。 是从 Azure 库或是自定义？
- 您的域加入凭据。
- 你的 Windows 虚拟桌面凭据。

使用 Azure 资源管理器模板创建 Windows 虚拟机主机池时，你可以从 Azure 库、 托管的映像或非托管的映像创建虚拟机。 若要了解有关如何创建 VM 映像的详细信息，请参阅[准备要上传到 Azure 的 Windows VHD 或 VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)并[在 Azure 中创建通用 VM 的托管的映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)。

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>运行预配新的主机池的 Azure 资源管理器模板

若要开始，请转到[此 GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)。

### <a name="deploy-the-template-to-azure"></a>将模板部署到 Azure

如果你在部署中使用的企业订阅，向下滚动并选择**部署到 Azure**，然后跳过预填充 out 参数在基于图像源。

如果你在部署云解决方案提供商订阅中，执行以下步骤将部署到 Azure:

1. 向下滚动并右键单击**部署到 Azure**，然后选择**复制链接位置**。
2. 打开记事本之类的文本编辑器并粘贴存在的链接。
3. 之后"https://portal.azure.com/"并输入之前井号标签 （#） at 符号 (@) 后面是租户域名。 下面是应使用的格式示例： https://portal.azure.com/@Contoso.onmicrosoft.com#create/。
4. 具有到云解决方案提供商订阅的管理员/参与者权限的用户登录到 Azure 门户。
5. 粘贴到文本编辑器复制到的地址栏的链接。

有关哪些参数应为输入你的方案的指南，请参阅 Windows 虚拟桌面[自述文件](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)。 自述文件始终使用最新更改进行更新。

## <a name="assign-users-to-the-desktop-application-group"></a>将用户分配到的桌面应用程序组

在开始测试虚拟机上的完整会话桌面前，GitHub Azure 资源管理器模板完成后，分配用户访问权限。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做），以便在 PowerShell 会话中使用。

若要将用户分配到桌面应用程序组中，打开 PowerShell 窗口并运行此 cmdlet 可登录到 Windows 虚拟桌面环境：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

接下来，将上下文设置为使用此 cmdlet 的 Azure 资源管理器模板中指定的租户组：

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

之后，将用户添加到此 cmdlet 的桌面应用程序组：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

用户的 UPN 应与该用户在 Azure Active Directory 中的标识（例如 user1@contoso.com）相匹配。 若要添加多个用户，必须针对每个用户运行此 cmdlet。

完成这些步骤后，添加到桌面应用程序组的用户可以使用支持的远程桌面客户端登录到 Windows 虚拟桌面，并且可以看到会话桌面的资源。

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。