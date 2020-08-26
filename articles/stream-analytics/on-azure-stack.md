---
title: '在 Azure Stack (预览版上运行 Azure 流分析) '
description: 创建 Azure 流分析 edge 作业，并通过 IoT Edge 运行时将其部署到 Azure Stack 集线器。
ms.service: stream-analytics
author: raan
ms.author: raan
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: 1fe035d99f8a5962406d5aae3f093d71d432b310
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88860742"
---
# <a name="run-azure-stream-analytics-on-azure-stack-preview"></a>在 Azure Stack (预览版上运行 Azure 流分析) 

> [!IMPORTANT]
> 此功能以预览版提供，建议不要在生产环境中使用。

可以将 Azure Stack 中心上的 Azure 流分析作为 IoT Edge 模块运行。 已将配置添加到 IoT Edge 模块，通过允许在每个 Azure Stack 中心雇用中找到自定义 Url，使其能够与 Azure Stack 中心订阅中运行的 blob 存储、事件中心和 IoT 中心交互。

通过 Azure Stack 上的流分析，可以在自己的专用、自治云（可通过本地使用一致的 Azure 服务与云本机应用连接或断开连接）中为流处理生成真正的混合体系结构。 

本文介绍如何将来自 IoT 中心或事件中心的数据流式传输到 Azure Stack 中心订阅中的另一个事件中心或 Blob 存储，并使用流分析进行处理。

## <a name="set-up-environments"></a>设置环境

Azure 流分析是 Azure Stack 集线器上的混合服务。 它是在 Azure 中配置的 IoT Edge 模块，但可以在 Azure Stack 中心运行。  

如果不熟悉 Azure Stack 集线器或 IoT Edge，请按照下面的说明来设置环境。

### <a name="prepare-the-azure-stack-hub-environment"></a>准备 Azure Stack 中心环境

创建 Azure Stack 中心订阅。 有关详细信息，请参阅 [创建 Azure Stack 中心订阅教程。](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services/)

如果要在自己的服务器上评估 Azure Stack 中心，可以使用 Azure Stack 开发工具包 (ASDK) 。  有关 ASDK 的详细信息，请参阅 [ASDK 概述](https://docs.microsoft.com/azure-stack/asdk/)。

### <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

若要在 Azure Stack Hub 上运行 Azure 流分析，你的设备必须具有 IoT Edge 运行时，并且必须具有到 Azure Stack 集线器的网络连接，或者是在 Azure Stack 中心运行的 VM。 IoT Edge 运行时允许流分析边缘作业与在 Azure Stack 中心运行的 Azure 存储和 Azure 事件中心集成。 有关详细信息，请参阅 [Azure IoT Edge 流分析](stream-analytics-edge.md) 

除了对 Azure Stack 集线器资源的网络访问，IoT Edge 设备 (或 VM) 需要访问 Azure 公有云中的 Azure IoT 中心，才能配置流分析模块。 

以下指南介绍了如何在设备或 VM 上设置 IoT Edge 运行时：

* [在 Windows 上安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge-windows.md)
* [在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge-linux.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>创建 Azure 流分析 Edge 作业

ASA Edge 作业在部署到 Azure IoT Edge 设备的容器中运行。 它们由两个部分组成：
* 负责作业定义的云部分：用户在云中定义输入、输出、查询和其他设置（无序事件等）。
* 在 IoT 设备上运行的模块。 它包含 ASA 引擎，并从云接收作业定义。

### <a name="create-a-storage-account"></a>创建存储帐户

创建可以在 IoT Edge 设备上运行的 Azure 流分析作业时，需采用特定的存储方式，以便能够从设备进行调用。 你可以使用现有的 Azure 存储帐户，也可以创建一个新帐户。
1. 在 Azure 门户中，请参阅 **创建资源 > 存储 > 存储帐户-blob、文件、表、队列**。
2. 提供以下值来创建存储帐户：

   | 字段 | 值 |
   | --- | --- |
   | 名称 | 为存储帐户提供唯一的名称。 |
   | 位置 | 选择靠近你的位置。|
   | 订阅 | 选择与 IoT 中心相同的订阅。|
   | 资源组 | 建议为在 [IoT Edge 快速入门](https://docs.microsoft.com/azure/iot-edge/quickstart) 和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |

3. 将其他字段保留默认值，然后选择“创建”。****


### <a name="create-a-new-job"></a>创建新作业

1. 在 Azure 门户中，请参阅 **创建资源 > 物联网 > 流分析作业**。
2. 提供以下值来创建存储帐户：

   | 字段 | 值 |
   | --- | --- |
   | 作业名称 | 为作业提供一个名称。 例如，**IoTEdgeJob** |
   | 订阅 | 选择与 IoT 中心相同的订阅。|
   | 资源组 | 建议为在 [IoT Edge 快速入门](https://docs.microsoft.com/azure/iot-edge/quickstart) 和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |
   | 位置 | 选择靠近你的位置。 |
   | 宿主环境 | 选择“边缘”。 |

3. 选择“创建”。

### <a name="configure-your-job"></a>配置作业

在 Azure 门户中创建流分析作业以后，即可使用输入、输出以及要在流过的数据上运行的查询对其进行配置。 你可以在 Azure Stack 中心订阅中手动指定 IoT 中心或事件中心的输入。

1. 导航到 Azure 门户中的流分析作业。
2. 在 " **配置**" 下，选择 " **存储帐户设置** "，然后选择在上一步中创建的存储帐户。
   > [!div class="mx-imgBorder"]
   > [![作业存储帐户设置 ](media/on-azure-stack/storage-account-settings.png)](media/on-azure-stack/storage-account-settings.png#lightbox)
3. 在 " **作业拓扑**" 下选择 " **输入** "，然后选择 " **添加流输入"。**
4. 从下拉列表中选择 " **IoT 中心**"、" **事件中心**" 或 " **Edge 中心** "。 
5. 如果输入是 Azure Stack 中心订阅中的事件中心或 IoT 中心，请按如下所示手动提供信息。

   #### <a name="event-hub"></a>事件中心

   | 字段 | 值 |
   | --- | --- |
   | 输入别名 | 在作业查询中用于引用此输入的友好名称。 |
   | 服务总线命名空间 | 命名空间是一组消息传递实体的容器。 创建新的事件中心后，另请创建命名空间。  (示例： * <Event Hub Name> eventhub.shanghai.azurestack.corp.microsoft.com*)  |
   | 事件中心名称 | 要用作输入的事件中心的名称。 |
   | 事件中心策略名称 | 提供对事件中心的访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 此选项会自动进行填充，除非已选择手动提供事件中心设置的选项。 |
   | 事件中心策略密钥 | 用于授予对事件中心的访问权限的共享访问密钥。 此选项会自动进行填充，除非已选择手动提供事件中心设置的选项。 可以在事件中心设置中找到该文件。 |
   | 事件中心使用者组 (可选)  | 强烈建议对每个流分析作业使用不同的使用者组。 此字符串标识的使用者组用于从事件中心引入数据。 如果未指定任何使用者组，流分析作业将使用 $Default 使用者组。 |
   | 分区计数 | 分区计数是事件中心中的分区数。 |

   > [!div class="mx-imgBorder"]
   > [![事件中心输入 ](media/on-azure-stack/event-hub-input.png)](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT 中心

   | 字段 | 值 |
   | --- | --- |
   | 输入别名 | 在作业查询中用于引用此输入的友好名称。 |
   | IoT 中心 | 可用作输入的 IoT 中心的名称。  (示例：* <IoT Hub Name> . shanghai.azurestack.corp.microsoft.com*)  |
   | 共享访问策略名称 | 提供对 IoT 中心的访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 |
   | 共享访问策略密钥 | 用于授予对 IoT 中心的访问权限的共享访问密钥。 此选项会自动进行填充，除非已选择手动提供 IoT 中心设置的选项。 |
   | 使用者组 (可选)  | 强烈建议对每个流分析作业使用不同的使用者组。 用于从 IoT 中心引入数据的使用者组。 流分析使用 $Default 所有者组，除非指定了其他组。 |
   | 分区计数 | 分区计数是事件中心中的分区数。 |

   > [!div class="mx-imgBorder"]
   > [![IoT 中心输入 ](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

6. 将其他字段保留默认值，然后选择“保存”。
7. 在“作业拓扑”下打开“输出”，然后选择“添加”。
8. 从下拉列表中选择 "Blob 存储"、"事件中心" 或 "Edge 中心"。
9. 如果输出是 Azure Stack 中心订阅中的事件中心或 Blob 存储，请手动提供信息，如下所示。

   #### <a name="event-hub"></a>事件中心

   | 字段 | 值 |
   | --- | --- |
   | 输出别名 | 查询中使用的易记名称，用于将查询输出定向到此事件中心。 |
   | 服务总线命名空间 | 包含一组消息实体的容器。 创建新的事件中心后，还创建了服务总线命名空间。 (示例： *sb:// <Event Hub Name> . eventhub.shanghai.azurestack.corp.microsoft.com*)  |
   | 事件中心名称 | 事件中心输出的名称。 |
   | 事件中心策略名称 | 共享访问策略，可以在事件中心的“配置”选项卡上创建。每个共享访问策略具有名称、所设权限以及访问密钥。 |
   | 事件中心策略密钥 | 用于对事件中心命名空间的访问权限进行身份验证的共享访问密钥。 |

   > [!div class="mx-imgBorder"]
   > [![事件中心输出 ](media/on-azure-stack/event-hub-output.png)](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob 存储 

   | 字段 | 值 |
   | --- | --- |
   | 输出别名 | 查询中使用的友好名称，用于将查询输出定向到此 blob 存储。 |
   | 存储帐户 | 要将输出发送到的存储帐户的名称。 (示例： * <Storage Account Name> . blob.shanghai.azurestack.corp.microsoft.com*)  |
   | 存储帐户密钥 | 与存储帐户关联的密钥。 此选项会自动进行填充，除非已选择手动提供 Blob 存储设置的选项。 |

> [!NOTE]
> Azure Stack 集线器上的边缘作业不支持 Parquet 格式。 对于最小行和最大时间，请使用0或将其保留为空。


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>在连接到 Azure Stack 的 VM 或设备上部署流分析

1. 在 Azure 门户中，打开 IoT 中心。 导航到 **IoT Edge** 并单击要用于此部署的设备 (VM) 。
2. 选择“设置模块”。 然后，选择 " **+ 添加** " 并选择 " **Azure 流分析模块**"。 
3. 选择已创建的订阅和流分析边缘作业。 单击 " **保存** " 并选择 " **下一步：路由**"。

   > [!div class="mx-imgBorder"]
   > [![添加模块 ](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

4. 单击 "查看" 和 " **创建 >**"。
5. 在 " **查看** " 和 "创建" 步骤中，选择 " **创建**"。 
   > [!div class="mx-imgBorder"]
   > [![清单 ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. 确认模块已添加到列表。
   > [!div class="mx-imgBorder"]
   > [![部署页 ](media/on-azure-stack/edge-deployment.png)](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>后续步骤
- [Azure IoT Edge 流分析](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge)
- [开发流分析边缘作业](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
