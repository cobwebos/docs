---
title: 如何将 OPC 克隆模块部署到现有的 Azure 项目 |Microsoft Docs
description: 本文介绍如何将 OPC 克隆部署到现有项目。 你还可以了解如何排查部署故障。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824117"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>将 OPC 克隆部署到现有项目

OPC 克隆模块在 IoT Edge 上运行，并向 OPC 克隆和注册表服务提供多个边缘服务。

OPC 克隆微服务通过 OPC 双子 IoT Edge 模块，促进工厂操作员与工厂中的 OPC UA 服务器设备之间的通信。 微服务通过其 REST API 公开 OPC UA 服务（浏览、读取、写入和执行）。 

OPC UA 设备注册表微服务提供对已注册的 OPC UA 应用程序及其终结点的访问权限。 操作员和管理员可以注册和取消注册新的 OPC UA 应用程序，并浏览现有的 OPC UA 应用程序，包括其终结点。 除应用程序和终结点管理外，注册表服务还将注册的 OPC 克隆 IoT Edge 模块。 服务 API 使你能够控制边缘模块功能，例如，启动或停止服务器发现（扫描服务），或者激活可使用 OPC 克隆微服务访问的新终结点孪生。

模块的核心是监察员标识。 监控器管理终结点克隆，这对应于使用相应 OPC UA 注册表 API 激活的 OPC UA 服务器终结点。 此终结点孪生将从云中运行的 OPC 克隆微服务接收的 OPC UA JSON 转换为 OPC UA 二进制消息，这些消息通过有状态安全通道发送到托管终结点。 此监察员还提供了发现服务，用于将设备发现事件发送到 OPC UA 设备载入服务进行处理，在此类事件中，这些事件会导致更新 OPC UA 注册表。  本文介绍如何将 OPC 克隆模块部署到现有项目。

> [!NOTE]
> 有关部署详细信息和说明的详细信息，请参阅 GitHub[存储库](https://github.com/Azure/azure-iiot-opc-twin-module)。

## <a name="prerequisites"></a>先决条件

请确保已安装 PowerShell 和[AzureRM powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)扩展。 如果尚未这样做，请克隆此 GitHub 存储库。 在 PowerShell 中运行以下命令：

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

2. 按照提示为部署的资源组和网站的名称分配一个名称。   该脚本将微服务及其 Azure 平台依赖项部署到 Azure 订阅中的资源组。  该脚本还会在 Azure Active Directory （AAD）租户中注册一个应用程序，以支持基于 OAUTH 的身份验证。  部署需要几分钟时间。  成功部署解决方案后会看到的示例：

   ![工业 IoT OPC 将部署到现有项目](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   输出包括公共终结点的 URL。 

3. 脚本成功完成后，选择是否要保存 `.env` 文件。  如果要使用诸如控制台之类的工具连接到云终结点或部署和调试模块，则需要 `.env` 环境文件。

## <a name="troubleshooting-deployment-failures"></a>部署故障排除

### <a name="resource-group-name"></a>资源组名称

请确保使用简短且简单的资源组名称。  该名称还用于命名资源，因为它必须符合资源命名要求。  

### <a name="website-name-already-in-use"></a>网站名称已被使用

网站的名称可能已在使用中。  如果遇到此错误，则需要使用其他应用程序名称。

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory （AAD）注册

部署脚本尝试在 Azure Active Directory 中注册两个 AAD 应用程序。  部署可能失败，具体取决于你对所选 AAD 租户的权限。 有两个选项：

1. 如果从租户列表中选择了 AAD 租户，请重新启动脚本，然后从列表中选择另一个。
2. 另外，还可以在另一个订阅中部署私有 AAD 租户，重新启动脚本，并选择使用它。

> [!WARNING]
> 从不进行身份验证时不继续。  如果你选择这样做，任何人都可以从未经身份验证的 Internet 访问你的 OPC 克隆终结点。   始终可以选择["本地" 部署选项](howto-opc-twin-deploy-dependencies.md)来启动橡胶轮。

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>部署多个工业 IoT 服务演示

你还可以部署一个一体演示，而不只是服务和依赖项。  其中的所有演示都包含三个 OPC UA 服务器、OPC 克隆模块、所有微服务和示例 Web 应用程序。  它用于演示目的。

1. 请确保具有存储库的克隆（请参阅上文）。 在存储库的根目录中打开 PowerShell 提示符，并运行以下命令：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. 按照提示为资源组和网站的名称分配新名称。  成功部署后，该脚本会显示 web 应用程序终结点的 URL。

## <a name="deployment-script-options"></a>部署脚本选项

此脚本采用以下参数：

```powershell
-type
```

部署类型（vm、本地、演示）

```powershell
-resourceGroupName
```

可以是现有或新资源组的名称。

```powershell
-subscriptionId
```

可选，将在其中部署资源的订阅 ID。

```powershell
-subscriptionName
```

或订阅名称。

```powershell
-resourceGroupLocation
```

可选，资源组位置。 如果已指定，则将尝试在此位置创建新的资源组。

```powershell
-aadApplicationName
```

要在其下注册的 AAD 应用程序的名称。

```powershell
-tenantId
```

要使用的 AAD 租户。

```powershell
-credentials
```

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 OPC 克隆部署到现有项目，下面是建议的后续步骤：

> [!div class="nextstepaction"]
> [OPC UA 客户端和 OPC UA PLC 的安全通信](howto-opc-vault-secure.md)
