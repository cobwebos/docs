---
title: 如何在 Azure 中部署 OPC 双云依赖项 |微软文档
description: 本文介绍如何部署执行本地开发和调试所需的 OPC 双子 Azure 依赖项。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824101"
---
# <a name="deploying-dependencies-for-local-development"></a>为本地开发部署依赖项

本文介绍如何仅部署执行本地开发和调试所需的 Azure 平台服务。   最后，您将部署一个资源组，其中包含本地开发和调试所需的一切。

## <a name="deploy-azure-platform-services"></a>部署 Azure 平台服务

1. 确保安装了 PowerShell 和[AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)扩展。  打开命令提示符或终端并运行：

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 按照提示为部署的资源组分配名称。  该脚本仅将依赖项部署到 Azure 订阅中的此资源组，而不将微型服务部署到此资源组。  该脚本还在 Azure 活动目录中注册应用程序。  这是支持基于 AuTH 的身份验证所必需的。  部署可能需要几分钟的时间。

3. 脚本完成后，您可以选择保存 .env 文件。  .env 环境文件是要在开发计算机上运行的所有服务和工具的配置文件。  

## <a name="troubleshooting-deployment-failures"></a>排除部署故障

### <a name="resource-group-name"></a>资源组名称

确保使用简短和简单的资源组名称。  该名称还用于命名资源，因为它必须符合资源命名要求。  

### <a name="azure-active-directory-aad-registration"></a>Azure 活动目录 （AAD） 注册

部署脚本尝试在 Azure 活动目录中注册 AAD 应用程序。  根据您对所选 AAD 租户的权限，这可能会失败。   有三个选项：

1. 如果从租户列表中选择了 AAD 租户，请重新启动脚本并从列表中选择其他脚本。
2. 或者，部署专用 AAD 租户，重新启动脚本并选择使用它。
3. 继续而不进行身份验证。  由于您在本地运行微服务，这是可以接受的，但不会模仿生产环境。  

## <a name="next-steps"></a>后续步骤

现在，您已成功将 OPC 孪生服务部署到现有项目，下面是建议的下一步：

> [!div class="nextstepaction"]
> [了解如何部署 OPC 双子模块](howto-opc-twin-deploy-modules.md)
