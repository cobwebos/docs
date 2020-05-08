---
title: Windows 虚拟桌面管理工具-Azure
description: 如何排查 Windows 虚拟桌面管理工具的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb9206d2c2c15c036b0cf1184b79ed126966eb3b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614793"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>排查 Windows 虚拟桌面管理工具问题

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。

本文介绍部署 Windows 虚拟桌面管理工具时可能出现的问题，以及如何修复这些问题。

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>错误：已配置管理工具服务，但自动安装失败

如果成功设置了管理工具的服务，但自动安装失败，则会看到以下错误消息：

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

这通常意味着以下两种情况之一：

- 用户拥有其订阅和租户级别全局管理员的所有者权限，但他们无法登录到 Azure。
- 用户的帐户设置已启用多重身份验证。

修复此问题的方法：

1. 确保为 Azure Active Directory 用户主体名称创建的用户具有 "参与者" 订阅级别。
2. 用 UPN 帐户登录到 <portal.azure.com> 以检查帐户设置，并确保未启用多重身份验证。 如果已打开，请将其关闭。
3. 请访问 Windows 虚拟桌面许可页面，确保服务器和客户端应用程序已同意。
4. 如果问题仍然存在，请查看[部署管理工具](manage-resources-using-ui.md)教程，并重新部署工具。

## <a name="error-job-with-specified-id-already-exists"></a>错误：已存在具有指定 ID 的作业

如果用户看到错误消息 "已存在具有指定 ID 的作业"，则这是因为在部署模板时，它们没有在 "Application name" 参数中提供唯一名称。

若要解决此问题，请重新部署 "应用程序名称" 参数已填充的管理工具。

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>打开管理工具时延迟的许可提示

部署管理工具时，可能无法立即打开许可提示。 这意味着，加载 Azure Web 应用服务所用的时间比平时长。 加载 Azure Web 完成后，应会出现提示符。

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>用户无法在 "美国东部" 区域部署管理工具

如果客户将区域设置为 "美国东部"，则他们无法部署管理工具。

若要解决此问题，请将管理工具部署在不同的区域中。 在不同的区域重新部署该工具不会影响用户体验。

## <a name="next-steps"></a>后续步骤

- 了解[故障排除概述、反馈和支持](troubleshoot-set-up-overview-2019.md)中的升级跟踪。
- 了解如何在[远程桌面服务的 ARM 模板](https://github.com/Azure/RDS-Templates/blob/master/README.md)上报告 Windows 虚拟桌面工具的问题。
- 若要了解如何部署管理工具，请参阅[部署管理工具](manage-resources-using-ui.md)。