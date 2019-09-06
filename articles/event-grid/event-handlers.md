---
title: Azure 事件网格事件处理程序
description: 介绍 Azure 事件网格支持的事件处理程序
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 6093e1017af2fb8c54eaf1c3192f937172567982
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080560"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure 事件网格中的事件处理程序

事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 会自动将多个 Azure 服务配置为处理事件。 也可以使用任意 Webhook 来处理事件。 Webhook 不需要托管在 Azure 中便可处理事件。 事件网格仅支持 HTTPS WebHook 终结点。

本文提供每个事件处理程序的内容的链接。

## <a name="azure-automation"></a>Azure 自动化

使用 Azure 自动化通过自动化的 Runbook 来处理事件。

|标题  |描述  |
|---------|---------|
|[教程：Azure 自动化与事件网格和 Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |创建用于发送事件的虚拟机。 此活动触发一个用于标记虚拟机的自动化 Runbook，并触发一条发送到 Microsoft Teams 频道的消息。 |

## <a name="azure-functions"></a>Azure Functions

使用 Azure Functions 对事件进行无服务器响应。

使用 Azure Functions 作为处理程序时，请使用事件网格触发器而不是通用 HTTP 触发器。 事件网格会自动验证事件网格函数触发器。 使用泛型 HTTP 触发器时，必须实现[验证响应](security-authentication.md#webhook-event-delivery)。

|标题  |描述  |
|---------|---------|
| [Azure Functions 的事件网格触发器](../azure-functions/functions-bindings-event-grid.md) | 在 Functions 中使用事件网格触发器概述。 |
| [教程：使用事件网格自动调整上传图像的大小](resize-images-on-storage-blob-upload-event.md) | 用户通过 Web 应用将映像上传到存储帐户。 创建存储 Blob 后，事件网格会向用于重设已上传映像的大小的函数应用发送一个事件。 |
| [教程：将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md) | 当事件中心创建捕获文件时，事件网格会将一个事件发送到函数应用。 应用会检索捕获文件并将数据迁移到数据仓库。 |
| [教程：Azure 服务总线到 Azure 事件网格集成示例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格将消息从服务总线主题发送到函数应用和逻辑应用。 |

## <a name="event-hubs"></a>事件中心

如果解决方案获取事件的速度快于处理事件的速度，请使用事件中心。 应用程序按照自己的计划处理来自事件中心的事件。 可以通过缩放事件处理来处理传入的事件。

事件中心可以充当事件源或事件处理程序。 以下文章展示了如何将事件中心用作处理程序。

|标题  |描述  |
|---------|---------|
| [快速入门：使用 Azure CLI 和事件网格将自定义事件路由到 Azure 事件中心](custom-event-to-eventhub.md) | 将自定义事件发送到事件中心以供应用程序处理。 |
| [资源管理器模板：自定义主题和事件中心终结点](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 用于创建自定义主题的订阅的资源管理器模板。 它将事件发送到 Azure 事件中心。 |

有关事件中心用作源的示例，请参阅[事件中心源](event-sources.md#event-hubs)。

## <a name="hybrid-connections"></a>混合连接

使用 Azure 中继混合连接将事件发送到企业网络内没有可公开访问的终结点的应用程序。

|标题  |描述  |
|---------|---------|
| [教程：将事件发送到混合连接](custom-event-to-hybrid-connection.md) | 将自定义事件发送到现有混合连接以供侦听器应用程序处理。 |

## <a name="logic-apps"></a>逻辑应用

使用逻辑应用自动执行事件响应业务流程。

|标题  |描述  |
|---------|---------|
| [教程：通过 Azure 事件网格和逻辑应用监视虚拟机更改](monitor-virtual-machine-changes-event-grid-logic-app.md) | 逻辑应用可监视对虚拟机的更改并就这些更改发送电子邮件。 |
| [教程：使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件](publish-iot-hub-events-to-logic-apps.md) | 每次将设备添加到 IoT 中心时，逻辑应用就会发送一封通知电子邮件。 |
| [教程：Azure 服务总线到 Azure 事件网格集成示例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格将消息从服务总线主题发送到函数应用和逻辑应用。 |

## <a name="service-bus-queue-preview"></a>服务总线队列 （预览版）

使用 Service Bus 作为事件处理程序用于将您的事件中事件网格路由直接向服务总线队列用于企业应用程序中的缓冲或命令和控制方案。 在预览并不适用于服务总线主题和会话，但适用于所有层的服务总线队列。

请注意，服务总线时如处理程序是在公共预览版中，您必须安装 CLI 或 PowerShell 扩展时使用这些凭据创建事件订阅。

### <a name="install-extension-for-azure-cli"></a>安装适用于 Azure CLI 的扩展

对于 Azure CLI，需要[事件网格扩展](/cli/azure/azure-cli-extensions-list)。

在 [CloudShell](/azure/cloud-shell/quickstart) 中：

* 如果您以前安装扩展，将其与更新`az extension update -n eventgrid`。
* 如果你以前尚未安装该扩展，安装使用`az extension add -n eventgrid`。

对于本地安装：

1. [安装 Azure CLI](/cli/azure/install-azure-cli)。 请确保通过检查与具有最新版本， `az --version`。
1. 卸载早期版本的扩展的`az extension remove -n eventgrid`。
1. 安装`eventgrid`扩展名`az extension add -n eventgrid`。

### <a name="install-module-for-powershell"></a>安装适用于 PowerShell 的模块

对于 PowerShell，需要 [AzureRM.EventGrid 模块](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)。

在 [CloudShell](/azure/cloud-shell/quickstart-powershell) 中：

* 安装的模块`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`。

对于本地安装：

1. 以管理员身份打开 PowerShell 控制台。
1. 安装的模块`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`。

如果 `-AllowPrerelease` 参数不可用，请使用以下步骤：

1. 运行 `Install-Module PowerShellGet -Force`。
1. 运行 `Update-Module PowerShellGet`。
1. 关闭 PowerShell 控制台。
1. 以管理员身份重新启动 PowerShell。
1. 安装模块`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`。

### <a name="using-cli-to-add-a-service-bus-handler"></a>使用 CLI 添加服务总线处理程序

有关 Azure CLI，下面的示例订阅，并连接到服务总线队列的事件网格主题：

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>队列存储

使用队列存储接收需拉取的事件。 如果正在运行的进程需要很长时间才能响应，可能会使用队列存储。 通过向队列存储发送事件，应用程序可以按照自己的计划拉取和处理事件。

|标题  |描述  |
|---------|---------|
| [快速入门：使用 Azure CLI 和事件网格将自定义事件路由到 Azure 队列存储](custom-event-to-queue-storage.md) | 说明如何将自定义事件发送到队列存储。 |

## <a name="webhooks"></a>Webhook

将 Webhook 用于可响应事件的可自定义终结点。

|标题  |描述  |
|---------|---------|
| 快速入门：使用 [Azure CLI](custom-event-quickstart.md)、[PowerShell](custom-event-quickstart-powershell.md) 和[门户](custom-event-quickstart-portal.md)创建和路由自定义事件。 | 展示了如何将自定义事件发送到 WebHook。 |
| 快速入门：使用 [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)、[PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) 和[门户](blob-event-quickstart-portal.md)将 Blob 存储事件路由到自定义 Web 终结点。 | 展示了如何将 Blob 存储事件发送到 WebHook。 |
| [快速入门：发送容器注册表事件](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 展示了如何使用 Azure CLI 发送容器注册表事件。 |
| [概述：将事件接收到 HTTP 终结点](receive-events.md) | 介绍如何验证 HTTP 终结点以接收来自事件订阅的事件，然后接收和反序列化事件。 |

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
