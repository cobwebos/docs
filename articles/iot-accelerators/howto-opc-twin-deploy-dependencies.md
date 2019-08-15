---
title: 如何在 Azure 中部署 OPC 克隆云依赖项 |Microsoft Docs
description: 如何部署 OPC 克隆 Azure 依赖关系。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: cb07899b51280cff8613d637640c0da23debbc8e
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016517"
---
# <a name="deploying-dependencies-for-local-development"></a>部署本地开发的依赖项

本文介绍如何仅部署进行本地开发和调试所需的 Azure 平台服务。   最后, 你将部署一个资源组, 其中包含本地开发和调试所需的一切。

## <a name="deploy-azure-platform-services"></a>部署 Azure 平台服务

1. 请确保已安装 PowerShell 和[AzureRM powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)扩展。  打开命令提示符或终端并运行:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 按照提示为部署分配资源组的名称。  此脚本仅在 Azure 订阅中部署此资源组的依赖项, 但不会部署微服务。  该脚本还会在 Azure Active Directory 中注册应用程序。  这是支持基于 OAUTH 的身份验证所必需的。  部署可能需要几分钟的时间。

3. 脚本完成后, 您可以选择保存 env 文件。  Env 环境文件是要在开发计算机上运行的所有服务和工具的配置文件。  

## <a name="troubleshooting-deployment-failures"></a>部署故障排除

### <a name="resource-group-name"></a>资源组名

请确保使用简短且简单的资源组名称。  该名称还用于命名资源, 因为它必须符合资源命名要求。  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) 注册

部署脚本尝试在 Azure Active Directory 中注册 AAD 应用程序。  根据你对所选 AAD 租户的权限, 这可能会失败。   有三个选项：

1. 如果从租户列表中选择了 AAD 租户, 请重新启动脚本, 然后从列表中选择另一个。
2. 或者, 部署私有 AAD 租户, 重新启动脚本并选择使用它。
3. 继续而不进行身份验证。  由于你在本地运行微服务, 因此可以接受, 但不会模拟生产环境。  

## <a name="next-steps"></a>后续步骤

现在已成功将 OPC 克隆服务部署到现有项目中, 接下来是建议的下一个步骤:

> [!div class="nextstepaction"]
> [了解如何部署 OPC 克隆模块](howto-opc-twin-deploy-modules.md)
