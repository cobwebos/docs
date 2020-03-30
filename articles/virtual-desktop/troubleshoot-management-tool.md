---
title: Windows 虚拟桌面管理工具 - Azure
description: 如何解决 Windows 虚拟桌面管理工具的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127489"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>排查 Windows 虚拟桌面管理工具问题

本文介绍了在部署 Windows 虚拟桌面管理工具时可能出现的问题以及如何修复这些问题。

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>错误：管理工具服务已配置但自动设置失败

当您成功为管理工具设置服务但自动设置失败时，您将看到以下错误消息：

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

这通常意味着以下两件事之一：

- 用户对其订阅和租户级别的全局管理员具有所有者权限，但他们无法登录到 Azure。
- 用户帐户设置已启用多重身份验证。

修复此问题的方法：

1. 确保为 Azure 活动目录用户主体名称创建的用户具有"参与者"订阅级别。
2. 使用 UPN 帐户登录以<portal.azure.com>以检查帐户设置并确保未启动多重身份验证。 如果打开，请将其关闭。
3. 访问 Windows 虚拟桌面同意页，并确保服务器和客户端应用已获得同意。
4. 如果问题仍然存在并重新部署该工具，请查看["部署管理工具](manage-resources-using-ui.md)教程"。

## <a name="error-job-with-specified-id-already-exists"></a>错误：具有指定 ID 的作业已存在

如果用户看到错误消息"具有指定 ID 的作业已存在"，那是因为在部署模板时，他们在"应用程序名称"参数中未提供唯一名称。

要解决此问题，请重新部署已填充"应用程序名称"参数的管理工具。

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>打开管理工具时延迟同意提示

部署管理工具时，同意提示可能不会立即打开。 这意味着 Azure Web 应用服务加载的时间比平常长。 加载完 Azure Web 后，应显示提示。

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>用户无法在美国东部区域部署管理工具

如果客户将区域设置到美国东部，则他们无法部署管理工具。

要解决此问题，请将管理工具部署到其他区域。 在不同区域重新部署该工具不应影响用户体验。

## <a name="next-steps"></a>后续步骤

- 在[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)中了解有关升级跟踪。
- 了解如何在[用于远程桌面服务的 ARM 模板](https://github.com/Azure/RDS-Templates/blob/master/README.md)中报告 Windows 虚拟桌面工具的问题。
- 有关 Windows 虚拟桌面和升级跟踪的故障排除概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 要了解如何部署管理工具，请参阅[部署管理工具](manage-resources-using-ui.md)。