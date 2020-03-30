---
title: 如何将 OPC 孪生模块部署到现有 Azure 项目 |微软文档
description: 本文介绍如何将 OPC 孪生部署到现有项目。 您还可以了解如何排除部署故障。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824117"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>将 OPC 孪生部署到现有项目

OPC 双子模块在 IoT 边缘上运行，并为 OPC 孪生和注册表服务提供多项边缘服务。

OPC Twin 微服务通过 OPC 双 IoT 边缘模块，方便工厂操作员与工厂车间 OPC UA 服务器设备之间的通信。 微服务通过其 REST API 公开 OPC UA 服务（浏览、读取、写入和执行）。 

OPC UA 设备注册表微服务提供对已注册的 OPC UA 应用程序及其终结点的访问。 操作员和管理员可以注册和取消注册新的 OPC UA 应用程序，并浏览现有应用程序，包括其终结点。 除了应用程序和端点管理外，注册表服务还编目注册的 OPC 双 IoT 边缘模块。 服务 API 允许您控制边缘模块功能，例如启动或停止服务器发现（扫描服务），或激活可以使用 OPC Twin 微服务访问的新终结点孪生。

模块的核心是主管标识。 主管管理端点孪生，对应于使用相应的 OPC UA 注册表 API 激活的 OPC UA 服务器终结点。 此终结点孪生将从云中运行的 OPC Twin 微服务接收的 OPC UA JSON 转换为 OPC UA 二进制消息，这些消息通过有状态的安全通道发送到托管终结点。 主管还提供发现服务，将设备发现事件发送到 OPC UA 设备载入服务进行处理，其中这些事件会导致对 OPC UA 注册表的更新。  本文介绍如何将 OPC 孪生模块部署到现有项目。

> [!NOTE]
> 有关部署详细信息和说明的详细信息，请参阅 GitHub[存储库](https://github.com/Azure/azure-iiot-opc-twin-module)。

## <a name="prerequisites"></a>先决条件

确保安装了 PowerShell 和[AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)扩展。 如果尚未这样做，则克隆此 GitHub 存储库。 在 PowerShell 中运行以下命令：

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>将工业 IoT 服务部署到 Azure

1. 在 PowerShell 会话中，运行：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. 按照提示为部署的资源组和网站的名称分配名称。   该脚本将微服务及其 Azure 平台依赖项部署到 Azure 订阅中的资源组中。  该脚本还在 Azure 活动目录 （AAD） 租户中注册应用程序，以支持基于 AuTH 的身份验证。  部署需要几分钟时间。  成功部署解决方案后将看到的内容的示例：

   ![工业物联网 OPC 孪生部署到现有项目](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   输出包括公共终结点的 URL。 

3. 脚本成功完成后，选择是否要保存`.env`该文件。  如果要使用控制台`.env`等工具连接到云终结点，或部署用于开发和调试的模块，则需要环境文件。

## <a name="troubleshooting-deployment-failures"></a>排除部署故障

### <a name="resource-group-name"></a>资源组名称

确保使用简短和简单的资源组名称。  该名称还用于命名资源，因为它必须符合资源命名要求。  

### <a name="website-name-already-in-use"></a>网站名称已在使用中

网站的名称可能已在使用中。  如果遇到此错误，则需要使用不同的应用程序名称。

### <a name="azure-active-directory-aad-registration"></a>Azure 活动目录 （AAD） 注册

部署脚本尝试在 Azure 活动目录中注册两个 AAD 应用程序。  根据您对所选 AAD 租户的权限，部署可能会失败。 存在两个选项：

1. 如果从租户列表中选择了 AAD 租户，请重新启动脚本并从列表中选择其他脚本。
2. 或者，在另一个订阅中部署专用 AAD 租户，重新启动脚本，然后选择使用它。

> [!WARNING]
> 在没有身份验证的情况下，切勿继续。  如果您选择这样做，任何人都可以访问您的 OPC 双子终结点从互联网未经身份验证。   您可以随时选择["本地"部署选项](howto-opc-twin-deploy-dependencies.md)来踢轮胎。

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>部署一体式工业物联网服务演示

您可以部署一体式演示，而不仅仅是服务和依赖项。  全部在一个演示包含三个 OPC UA 服务器、OPC 双子模块、所有微服务以及一个示例 Web 应用程序。  它旨在用于演示目的。

1. 请确保您具有存储库的克隆（请参阅上文）。 在存储库的根目录中打开 PowerShell 提示符并运行：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. 按照提示为资源组分配新名称，为网站指定名称。  成功部署后，脚本将显示 Web 应用程序终结点的 URL。

## <a name="deployment-script-options"></a>部署脚本选项

该脚本采用以下参数：

```powershell
-type
```

部署类型（vm、本地、演示）

```powershell
-resourceGroupName
```

可以是现有资源组或新资源组的名称。

```powershell
-subscriptionId
```

可选，将部署资源的订阅 ID。

```powershell
-subscriptionName
```

或订阅名称。

```powershell
-resourceGroupLocation
```

可选，资源组位置。 如果指定，将尝试在此位置创建新的资源组。

```powershell
-aadApplicationName
```

AAD 应用程序要在下注册的名称。

```powershell
-tenantId
```

要使用的 AAD 租户。

```powershell
-credentials
```

## <a name="next-steps"></a>后续步骤

现在，您已经了解如何将 OPC Twin 部署到现有项目，下面是建议的下一步：

> [!div class="nextstepaction"]
> [OPC UA 客户端和 OPC UA PLC 的安全通信](howto-opc-vault-secure.md)
