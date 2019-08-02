---
title: 部署 IoT Edge 模块的 Azure 安全中心 (预览版) |Microsoft Docs
description: 了解如何在 IoT Edge 上部署适用于 IoT security agent 的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7171923e4badb3355a64b63515d40e73fadca6b0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596362"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>在 IoT Edge 设备上部署安全模块

> [!IMPORTANT]
> 用于 IoT IoT Edge 设备支持的 Azure 安全中心目前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

用于 IoT 模块的**Azure 安全中心**为 IoT Edge 设备提供了一个全面的安全解决方案。
安全模块将收集、聚合和分析来自你的操作系统和容器系统的原始安全数据到可操作的安全建议和警报。
若要了解详细信息, 请参阅[IoT Edge 的安全模块](security-edge-architecture.md)。

本文介绍如何在 IoT Edge 设备上部署安全模块。

## <a name="deploy-security-module"></a>部署安全模块

使用以下步骤为 IoT Edge 部署用于 IoT 安全模块的 Azure 安全中心。

### <a name="prerequisites"></a>先决条件

- 在 IoT 中心中, 确保你的设备已[注册为 IoT Edge 设备](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)。

- IoT Edge 模块的 Azure 安全中心要求在 IoT Edge 设备上安装[审核 framework](https://linux.die.net/man/8/auditd) 。

    - 在 IoT Edge 设备上运行以下命令, 以安装框架:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - 通过运行以下命令验证审核是否处于活动状态:
   
      `sudo systemctl status auditd`
      
        预期的响应为`active (running)`。 

### <a name="deployment-using-azure-portal"></a>使用 Azure 门户部署

1. 在 Azure 门户中, 打开**Marketplace**。

1. 选择 "**物联网**", 然后搜索 "适用于**IoT 的 Azure 安全中心**" 并选择它。

   ![为 IoT 选择 Azure 安全中心](media/howto/edge-onboarding-8.png)

1. 单击 "**创建**" 以配置部署。 

1. 选择 IoT 中心的 Azure**订阅**, 并选择**iot 中心**。<br>选择 "**部署到设备**" 以面向单个设备, 或选择 "**大规模部署**" 以面向多个设备, 并单击 "**创建**"。 有关大规模部署的详细信息, 请参阅[如何部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)。 

    >[!Note] 
    >如果选择了 "**大规模部署**", 请添加设备名称和详细信息, 然后继续执行以下说明中的 "**添加模块**" 选项卡。     

为 IoT 使用 Azure 安全中心创建 IoT Edge 部署有三个步骤。 下列各节将引导完成每个步骤。 

#### <a name="step-1-add-modules"></a>步骤 1：添加模块

1. 从 "**添加模块**" 选项卡的 "**部署模块**" 区域, 单击 " **AzureSecurityCenterforIoT**"。 
   
1. 将**名称**更改为**azureiotsecurity**。
1. 将**映像 URI**更改为**mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**。
1. 验证 "**创建容器" 选项**值是否设置为:      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. 验证是否选择了 "**设置模块克隆的所需属性**", 并将配置对象更改为:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. 单击“保存”。
1. 滚动到选项卡的底部, 选择 "**配置高级边缘运行时设置**"。
   
   
1. 将**Edge 中心**下的**图像**更改为**mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**。

   >[!Note]
   > 用于 IoT 模块的 Azure 安全中心需要基于 SDK 版本1.20 的 IoT Edge 集线器的分叉版本。
   > 通过更改 IoT Edge 集线器映像, 你将指导 IoT Edge 设备将最新稳定版本替换为 IoT Edge 中心的分叉版本, 但 IoT Edge 服务并不正式支持该版本。

1. 验证 "**创建选项**" 设置为: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. 单击“保存”。
   
1. 单击“下一步”。

#### <a name="step-2-specify-routes"></a>步骤 2：指定路由 

1. 在 "**指定路由**" 选项卡中, 确保您具有将消息从**azureiotsecurity**模块转发到 **$upstream**的路由 (显式或隐式)。 
1. 单击“下一步”。

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream
    ~~~

#### <a name="step-3-review-deployment"></a>步骤 3：查看部署

- 在 "**查看部署**" 选项卡中, 查看部署信息, 然后选择 "**提交**" 以完成部署。

## <a name="diagnostic-steps"></a>诊断步骤

如果遇到问题, 则可以使用容器日志来了解 IoT Edge 安全模块设备的状态。 可以使用本部分中的命令和工具来收集信息。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>验证是否已安装所需的容器并按预期方式工作

1. 在 IoT Edge 设备上运行以下命令:
    
     `sudo docker ps`
   
1. 验证以下容器是否正在运行:
   
   | 姓名 | 影像 |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   如果所需的容器不存在, 请检查你的 IoT Edge 部署清单是否与推荐的设置一致。 有关详细信息, 请参阅[部署 IoT Edge 模块](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>检查模块日志中是否存在错误
   
1. 在 IoT Edge 设备上运行以下命令:

   `sudo docker logs azureiotsecurity`
   
1. 对于更详细的日志, 请将以下环境变量添加到**azureiotsecurity**模块部署`logLevel=Debug`:。

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息, 请转到模块配置的操作方法指南。 
> [!div class="nextstepaction"]
> [模块配置操作方法指南](./how-to-agent-configuration.md)
