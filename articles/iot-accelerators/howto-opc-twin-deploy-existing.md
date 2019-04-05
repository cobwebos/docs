---
title: 如何将 Azure IoT OPC UA 设备管理模块部署到现有项目 |Microsoft Docs
description: 如何将 OPC 孪生部署到现有项目。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9b1dee8ea43d01825449cb6010fbfa62e4715cff
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047256"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>将 OPC 孪生部署到现有项目

OPC 设备孪生模块在 IoT Edge 上运行，并提供多个边缘服务到 OPC 设备孪生和注册表服务。 

OPC 设备孪生微服务促进工厂运算符和 OPC 孪生 IoT Edge 模块通过在工厂车间的 OPC UA 服务器设备之间通信。 微服务公开通过其 REST API 的 OPC UA 服务 （浏览、 读取、 写入和执行）。 

OPC UA 设备注册表微服务提供了对已注册的 OPC UA 应用程序和其终结点的访问。 操作员和管理员可以注册和取消注册新的 OPC UA 应用程序和浏览现有的包括其终结点。 除了应用程序和终结点管理，注册表服务还编录已注册的 OPC 设备孪生 IoT Edge 模块。 服务 API 将帮助您控制的边缘模块功能，例如，启动或停止服务器发现 （扫描服务），或激活可使用 OPC 孪生微服务访问的新终结点孪生。

模块的核心是监督程序标识。 监督程序管理终结点孪生，对应于使用相应的 OPC UA 注册表 API 激活的 OPC UA 服务器终结点。 此终结点孪生转换来自 OPC 孪生微服务在云中运行到 OPC UA 二进制消息，通过有状态安全通道发送到托管终结点的 OPC UA JSON。 监督程序还提供了设备发现事件发送到 OPC UA 设备载入服务进行处理，在更新到 OPC UA 注册表导致这些事件的发现服务。  本文介绍如何将 OPC 孪生模块部署到现有项目。 

> [!NOTE]
> 部署详细信息和说明的详细信息，请参阅 GitHub[存储库](https://github.com/Azure/azure-iiot-opc-twin-module)。

## <a name="prerequisites"></a>必备组件

请确保有 PowerShell 并[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)安装扩展。   如果你不执行此操作，克隆此 GitHub 存储库。  打开命令提示符或终端并运行：

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>将工业 IoT 服务部署到 Azure

1. 打开命令提示符或终端中运行：

   ```bash
   deploy
   ```

2. 按照提示将对部署的资源组的名称和一个名称分配给网站。   该脚本部署微服务和到 Azure 订阅中的资源组及其 Azure 平台依赖项。  该脚本还会以支持基于 OAUTH 的身份验证在 Azure Active Directory (AAD) 租户中注册应用程序。  部署将需要几分钟的时间。  已成功部署解决方案后将看到的示例：

   ![工业 IoT OPC 孪生将部署到现有项目](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   输出包括公共终结点的 URL。 

3. 该脚本成功完成后，选择是否要保存.env 文件。  如果你想要连接到云终结点使用控制台等工具或部署的模块，用于开发和调试，需要.env 环境文件。

## <a name="troubleshooting-deployment-failures"></a>部署故障排除

### <a name="resource-group-name"></a>资源组名称

请确保使用简短和简单的资源组名称。  名称资源这种情况下它必须遵循与命名要求的资源也使用的名称。  

### <a name="website-name-already-in-use"></a>已在使用网站名称

可能的网站名称已在使用它。  如果遇到此错误，您需要使用不同的应用程序名称。

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) 注册

部署脚本会尝试在 Azure Active Directory 中注册两个 AAD 应用程序。  根据您为所选的 AAD 租户的权限，则部署可能失败。 有两个选项：

1. 如果你的租户列表中选择 AAD 租户，重新启动该脚本并从列表中选择其他。
2. 或者，部署另一个订阅中的专用 AAD 租户，重新启动该脚本，然后选择此选项可以使用它。

> [!WARNING]
> 永远不会继续而无需验证。  如果你选择这样做，任何人都可以从未经身份验证的 Internet 访问 OPC 设备管理终结点。   你可以始终选择["本地"部署选项](howto-opc-twin-deploy-dependencies.md)进行检验。

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>部署-一体工业 IoT 服务演示

而不是只是服务和依赖项也可以部署-一体演示。  一个演示中的所有包含三个 OPC UA 服务器、 OPC 孪生模块、 所有微服务和 Web 应用程序的示例。  它被用于演示目的。

1. 请确保你拥有的存储库 （见上文） 克隆。 打开命令提示符或终端运行的存储库的根目录中：

   ```bash
   deploy -type demo
   ```

2. 按照提示操作以将新的名称分配给资源组和将名称传递给该网站。  一旦成功部署，脚本将显示 web 应用程序终结点的 URL。

## <a name="deployment-script-options"></a>部署脚本选项

该脚本采用以下参数：

```bash
-type
```

类型的部署 （虚拟机，本地，演示）

```bash
-resourceGroupName
```

可以是一个现有或新的资源组的名称。

```bash
-subscriptionId
```

也可选择将在其中部署资源的订阅 ID。

```bash
-subscriptionName
```

或订阅名称。

```bash
-resourceGroupLocation
```

选择资源组位置。 如果指定，将尝试在此位置创建新的资源组。

```bash
-aadApplicationName
```

若要注册 AAD 应用程序的名称。 

```bash
-tenantId
```

若要使用的 AAD 租户。

```bash
-credentials
```

## <a name="next-steps"></a>后续步骤

现在，已了解如何将 OPC 孪生部署到现有项目，下面是建议的下一步：

> [!div class="nextstepaction"]
> [OPC 客户端和 OPC PLC 的安全通信 ](howto-opc-vault-deploy-existing-client-plc-communication.md)
