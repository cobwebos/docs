---
title: 如何部署在 Azure 中的 OPC 孪生云依赖项 |Microsoft Docs
description: 如何部署 OPC 孪生 Azure 依赖项。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae9f2b05bfc6ea6315022d04c8d267d916cf282e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61451593"
---
# <a name="deploying-dependencies-for-local-development"></a>部署依赖项以进行本地开发

本文介绍如何部署仅 Azure 平台服务需要进行本地开发和调试。   在结束时，将已部署的资源组，其中包含用于本地开发和调试所需的一切。

## <a name="deploy-azure-platform-services"></a>部署 Azure 平台服务

1. 请确保有 PowerShell 并[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0)安装扩展。  打开命令提示符或终端并运行：

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 按照提示将名称分配给你的部署的资源组。  该脚本将仅依赖项部署到 Azure 订阅，但不是微服务在此资源组。  该脚本还会在 Azure Active Directory 中注册应用程序。  这是支持基于 OAUTH 的身份验证所需的。  部署可能需要几分钟的时间。

3. 完成脚本后，你可以选择保存.env 文件。  .Env 环境文件是所有服务和你想要在开发计算机上运行的工具的配置文件。  

## <a name="troubleshooting-deployment-failures"></a>部署故障排除

### <a name="resource-group-name"></a>资源组名称

请确保使用简短和简单的资源组名称。  名称资源这种情况下它必须遵循与命名要求的资源也使用的名称。  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) 注册

部署脚本会尝试在 Azure Active Directory 中注册 AAD 应用程序。  具体取决于您对所选的 AAD 租户的权利，这可能会失败。   有 3 个选项：

1. 如果你的租户列表中选择 AAD 租户，重新启动该脚本并从列表中选择其他。
2. 或者，部署专用的 AAD 租户、 重新启动脚本，并选择此选项可以使用它。
3. 继续而无需验证。  由于本地运行微服务时，这是可以接受的但不是模拟生产环境。  

## <a name="next-steps"></a>后续步骤

现在，你已成功部署 OPC 孪生服务，向现有项目，下面是建议的下一步：

> [!div class="nextstepaction"]
> [了解如何将 OPC 孪生模块部署](howto-opc-twin-deploy-modules.md)
