---
title: 有关订购 Azure Data Box 的教程 | Microsoft Docs
description: 了解部署先决条件以及如何订购 Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: cfb95f2fb02544197f9b2796a705844e33eca201
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392467"
---
# <a name="tutorial-order-azure-data-box"></a>教程：订购 Azure Data Box

Azure Data Box 是一个混合解决方案，可用于快速、方便、可靠地将本地数据导入 Azure 中。 请先将数据传输到 Microsoft 提供的 80 TB（可用容量）存储设备，然后将设备寄回。 然后，此数据将上传到 Azure。

本教程介绍如何订购 Azure Data Box。 本教程的介绍内容包括：

> [!div class="checklist"]
>
> * 部署 Data Box 的先决条件
> * 订购 Data Box
> * 跟踪订单
> * 取消订单

## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[门户](#tab/portal)

请先完成下述适用于 Data Box 服务和设备的配置先决条件，然后部署设备：

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

可以登录到 Azure，然后采用以下两种方式之一运行 Azure CLI 命令：

* 可以安装 CLI 并在本地运行 CLI 命令。
* 可以在 Azure 门户的 Azure Cloud Shell 中运行 CLI 命令。

在教程中，我们通过 Windows PowerShell 使用 Azure CLI，但你可以自由选择其中任意一种方法。

### <a name="install-the-cli-locally"></a>在本地安装 CLI

* 安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 2.0.67 或更高版本。 或者，可以[使用 MSI 安装](https://aka.ms/installazurecliwindows)。

#### <a name="sign-in-to-azure"></a>登录 Azure

打开 Windows PowerShell 命令窗口，并使用 [az login](/cli/azure/reference-index#az-login) 命令登录到 Azure：

```azurecli
PS C:\Windows> az login
```

下面是成功登录时的输出：

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

#### <a name="install-the-azure-data-box-cli-extension"></a>安装 Azure Data Box CLI 扩展

在使用 Azure Data Box CLI 命令之前，需要安装扩展。 借助 Azure CLI 扩展，可访问尚未在核心 CLI 中提供的试验性和预发布的命令。 有关扩展详细信息，请参阅[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview)。

若要安装 Azure Data Box 的扩展，请运行以下命令：`az extension add --name databox`：

```azurecli

    PS C:\Windows> az extension add --name databox
```

如果扩展安装成功，你将看到以下输出：

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

可以通过浏览器使用 [Azure Cloud Shell](https://shell.azure.com/)（一个 Azure 托管的交互式 Shell 环境）来运行 CLI 命令。 Azure Cloud Shell 通过 Azure 服务支持 Bash 或 Windows PowerShell。 已预安装 Azure CLI 并将其配置为与帐户一起使用。 单击 Azure 门户右上角部分菜单上的“Cloud Shell”按钮：

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

该按钮会启动交互式 shell，可以使用它来运行本文中概述的步骤。

<!-- To start Azure Cloud Shell:

| Option | Example/Link |
|-----------------------------------------------|---|
| Select **Try It** in the upper-right corner of a code block. Selecting **Try It** doesn't automatically copy the code to Cloud Shell. | ![Example of Try It for Azure Cloud Shell](../../includes/media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Go to [https://shell.azure.com](https://shell.azure.com), or select the **Launch Cloud Shell** button to open Cloud Shell in your browser. | [![Launch Cloud Shell in a new window](../../includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Select the **Cloud Shell** button on the menu bar at the upper right in the [Azure portal](https://portal.azure.com). | ![Cloud Shell button in the Azure portal](../../includes/media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

To run the code in this article in Azure Cloud Shell:

1. Start Cloud Shell.

2. Select the **Copy** button on a code block to copy the code.

3. Paste the code into the Cloud Shell session by selecting **Ctrl**+**Shift**+**V** on Windows and Linux or by selecting **Cmd**+**Shift**+**V** on macOS.

4. Select **Enter** to run the code.

For this tutorial, we use Windows PowerShell command prompt to run Azure CLI commands. -->

<!-- This goes away, we'll show this later when we show how to order a Data Box. -->
<!-- ## Change the output format type

All Azure CLI commands will use json as the output format by default unless you change it. You can change the output format by using the global parameter `--output <output-format>`. -->

<!-- ```azurecli

az databox job <command> --output <output-format>

```

Azure Data Box CLI commands support the following output formats:

* json (default setting)
* jsonc
* table
* tsv
* yaml
* yamlc
* none

You can use the parameter `--output` with all Azure Data Box CLI commands. -->

<!-- To set the output format to yaml: -->

<!-- ```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "yaml"

``` -->
<!-- 
To set the out format to tabular form (easier to read):

```azurecli
PS C:\Windows>az databox job show --resource-group "myresourcegroup" --name "mydataboxorder" --output "table"

``` -->

<!-- Here's the example output of `az databox job show` after changing the output format to table:

```azurecli
PS C:\WINDOWS\system32> az databox job show --resource-group "GDPTest" --name "mydataboxtest3" --output "table"
Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.

DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
--------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
NonScheduled    True             True                       False          True                         westus      mydataboxorder  myresourcegroup          2020-06-11T22:05:49.436622+00:00  DeviceOrdered

``` -->

---

## <a name="order-data-box"></a>订购 Data Box

# <a name="portal"></a>[门户](#tab/portal)

在 Azure 门户中执行以下步骤以订购设备。

1. 使用 Microsoft Azure 凭据在以下 URL 登录：[https://portal.azure.com](https://portal.azure.com)。
2. 单击“+ 创建资源”并搜索 *Azure Data Box*。 单击“Azure Data Box”。

   [![搜索“Azure Data Box 1”](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. 单击“创建”。

4. 检查 Data Box 服务是否在你的区域可用。 输入或选择以下信息，然后选择“应用”。

    |设置  |值  |
    |---------|---------|
    |订阅     | 对于 Data Box 服务，选择一个 EA、CSP 或 Azure 赞助订阅。 <br> 订阅链接到计费帐户。       |
    |传输类型     | 选择“导入到 Azure”。        |
    |源国家/地区    |    选择数据当前所在的国家/地区。         |
    |目标 Azure 区域     |     选择要在其中传输数据的 Azure 区域。        |

5. 选择“Data Box”。 单次订购的可用最大容量为 80 TB。 可以创建多个订单，以增加数据大小。

      [![选择 Data Box 选项 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. 在“订单”中指定“订单详细信息”。  输入或选择以下信息，然后选择“下一步”。

    |设置  |值  |
    |---------|---------|
    |名称     |  提供友好名称用于跟踪订单。 <br> 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 <br> 名称必须以字母或数字开头和结尾。      |
    |资源组     |    使用现有资源组，或创建新的资源组。 <br> 资源组是可以统一管理或部署的资源的逻辑容器。         |
    |目标 Azure 区域     | 选择存储帐户的区域。 <br> 有关详细信息，请转到[适用区域](data-box-overview.md#region-availability)。        |
    |存储目标     | 从存储帐户和/或托管磁盘中进行选择。 <br> 根据指定的 Azure 区域，从现有存储帐户的筛选列表中选择一个或多个存储帐户。 Data Box 可以与最多 10 个存储帐户链接。 <br> 也可以创建新的**常规用途 v1**、**常规用途 v2** 或 **Blob 存储帐户**。 <br>支持使用虚拟网络的存储帐户。 若要允许 Data Box 服务使用受保护的存储帐户，请在存储帐户网络防火墙设置中启用受信任的服务。 有关详细信息，请了解如何[将 Azure Data Box 添加为受信任的服务](../storage/common/storage-network-security.md#exceptions)。|

    如果使用存储帐户作为存储目标，则会看到以下屏幕截图：

    ![存储帐户的 Data Box 订单](media/data-box-deploy-ordered/order-storage-account.png)

    如果使用 Data Box 从本地虚拟硬盘 (VHD) 创建托管磁盘，则还需提供以下信息：

    |设置  |值  |
    |---------|---------|
    |资源组     | 若要从本地 VHD 创建托管磁盘，请创建新的资源组。 使用现有资源组的前提是，资源组是在以前由 Data Box 服务为托管磁盘创建 Data Box 订单时创建的。 <br> 指定多个用分号分隔的资源组。 最多支持 10 个资源组。|

    ![托管磁盘的 Data Box 订单](media/data-box-deploy-ordered/order-managed-disks.png)

    为托管磁盘指定的存储帐户用作临时存储帐户。 Data Box 服务将 VHD 作为页 Blob 上传到临时存储帐户，然后将其转换为托管磁盘并移到资源组。 有关详细信息，请参阅[验证 Azure 中的数据上传](data-box-deploy-picked-up.md#verify-data-upload-to-azure)。

7. 在“寄送地址”中，提供你的姓名、公司的名称和邮政地址，以及有效的电话号码。 单击“验证地址”。 服务将验证寄送地址，以确定服务是否在该区域可用。 如果服务在指定的寄送地址可用，则会将结果通知给你。

   如果你选择了自我托管交付，则在成功下单后，你将收到一封电子邮件通知。 有关自托管寄送的详细信息，请参阅[使用自托管寄送](data-box-portal-customer-managed-shipping.md)。

8. 成功验证寄送详细信息后，单击“下一步”。

9. 在“通知详细信息”中，指定电子邮件地址。 服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

10. 查看与订单、联系人、通知和隐私条款相关的信息**摘要**。 选中对应于同意隐私条款的复选框。

11. 单击“订购”。 创建订单需要几分钟时间。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 执行以下步骤以订购设备：

1. 记下 Data Box 订单的设置。 这些设置包括你的个人/企业信息、订阅名称、设备信息和寄送信息。 在运行 CLI 命令以创建 Data Box 订单时，需要将这些设置用作参数。 下表显示了用于 `az databox job create` 的参数设置：

   | 设置（参数） | 说明 |  示例值 |
   |---|---|---|
   |resource-group| 使用现有资源组，或创建新的资源组。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |name| 要创建的订单的名称。 | "mydataboxorder"|
   |contact-name| 与寄送地址关联的名称。 | "Gus Poland"|
   |phone| 接收订单的个人或企业的电话号码。| "14255551234"
   |location| 要向你寄送设备的、距你最近的 Azure 区域。| "US West"|
   |sku| 要订购的特定 Data Box 设备。 有效值是："DataBox", "DataBoxDisk", and "DataBoxHeavy"| "DataBox" |
   |email-list| 与订单关联的电子邮件地址。| "gusp@contoso.com" |
   |street-address1| 订单将寄送到的街道地址。 | "15700 NE 39th St" |
   |street-address2| 辅助性地址信息，如公寓号或楼号。 | "Bld 123" |
   |city| 设备将寄送到的城市。 | "Redmond" |
   |state-or-province| 设备将寄送到的省/市/自治区。| "WA" |
   |country| 设备将寄送到的国家/地区。 | "United States" |
   |postal-code| 与寄送地址相关联的邮政编码。| "98052"|
   |company-name| 你工作的公司的名称。| "Contoso, LTD" |
   |存储帐户| 要从中导入数据的 Azure 存储帐户。| "mystorageaccount"|
   |debug| 在详细日志记录中包含调试信息  | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

2. 在所选的命令提示符或终端中，使用 [az databox job create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) 创建 Azure Data Box 订单。

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   以下是命令使用情况的示例：

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   以下是运行命令后得到的输出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. 所有 Azure CLI 命令默认使用 json 作为输出格式，除非进行更改。 可以使用全局参数 `--output <output-format>` 更改输出格式。 将格式更改为“table”可提高输出的可读性。

   下面是刚运行的那个命令，只是稍微更改了格式：

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   以下是运行命令后得到的输出：

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

---

## <a name="track-the-order"></a>跟踪订单

# <a name="portal"></a>[门户](#tab/portal)

下单后，可以从 Azure 门户跟踪订单状态。 转到你的 Data Box 订单，然后转到“概述”来查看状态。 门户中会显示订单处于“已订购”状态。

如果设备缺货，你会收到通知。 如果设备有货，Microsoft 会确定要发货的设备，并准备发货。 在设备准备期间，会执行以下操作：

* 将为与设备关联的每个存储帐户创建 SMB 共享。
* 将为每个共享生成访问凭据（例如用户名和密码）。
* 也会生成用于解锁设备的设备密码。
* 锁定 Data Box 的目的是在任何时候防止对设备进行未经授权的访问。

设备准备完成后，门户会显示订单处于“已处理”状态。

![处理的 Data Box 订单](media/data-box-overview/data-box-order-status-processed.png)

然后，Microsoft 会安排区域承运人发运设备。 设备发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。

![派遣的 Data Box 订单](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>跟踪单个订单

若要获取单个现有 Azure Data Box 订单的跟踪信息，请运行 [az databox job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show)。 该命令显示有关订单的信息，例如（但不限于）：名称、资源组、跟踪信息、订阅 ID、联系人信息、寄送类型和设备 sku。

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   下表显示 `az databox job show` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |resource-group [必填]| 与订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |名称[必填]| 要显示的订单的名称。 | "mydataboxorder"|
   |debug| 在详细日志记录中包含调试信息 | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

   以下是输出格式设置为“table”的命令示例：

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   以下是运行命令后得到的输出：

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> 可以在订阅级别支持列出订单，这使资源组成为可选参数（而不是必需参数）。

### <a name="list-all-orders"></a>列出所有订单

如果订购了多个设备，可以运行 [az databox job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) 查看所有的 Azure Data Box 订单。 该命令列出属于特定资源组的所有订单。 输出中还显示：订单名称、寄送状态、Azure 区域、交付类型、订单状态。 已取消的订单也包含在列表中。
此命令还显示每个订单的时间戳。

```azurecli
az databox job list --resource-group <resource-group>
```

下表显示 `az databox job list` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |resource-group [必填]| 包含该订单的资源组名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |debug| 在详细日志记录中包含调试信息 | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

   以下是输出格式设置为“table”的命令示例：

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   以下是运行命令后得到的输出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        GDPTest          2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  GDPTest          2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       GDPTest          2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       GDPTest          2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       GDPTest          2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

---

## <a name="cancel-the-order"></a>取消订单

# <a name="portal"></a>[门户](#tab/portal)

若要取消此订单，请在 Azure 门户中转到“概览”，然后在命令栏中选择“取消” 。

下单后，只要订单状态尚未标记为“已处理”，就可以随时取消订单。

若要删除已取消的订单，请转到“概况”，然后在命令栏中选择“删除” 。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>取消订单

若要取消 Azure Data Box 订单，请运行 [az databox job cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel)。 你需要指定取消订单的原因。

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   下表显示 `az databox job cancel` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |resource-group [必填]| 与要删除的订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |名称[必填]| 要删除的订单的名称。 | "mydataboxorder"|
   |原因[必填]| 取消订单的原因。 | “我输入了错误信息，需要取消订单。” |
   |是| 不提示确认。 | --yes (-y)| --yes -y |
   |debug| 在详细日志记录中包含调试信息 | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

   下面是包含输出的命令示例：

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   以下是运行命令后得到的输出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>删除订单

如果取消了 Azure Data Box 订单，可以运行 [az databox job delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) 删除订单。

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   下表显示 `az databox job delete` 的参数信息：

   | 参数 | 说明 |  示例值 |
   |---|---|---|
   |resource-group [必填]| 与要删除的订单关联的资源组的名称。 资源组是可以统一管理或部署的资源的逻辑容器。 | "myresourcegroup"|
   |名称[必填]| 要删除的订单的名称。 | "mydataboxorder"|
   |订阅| Azure 订阅的名称或 ID (GUID)。 | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |是| 不提示确认。 | --yes (-y)| --yes -y |
   |debug| 在详细日志记录中包含调试信息 | --debug |
   |help| 显示此命令的帮助信息。 | --help -h |
   |only-show-errors| 只显示错误，取消显示警告。 | --only-show-errors |
   |output -o| 设置输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。 默认值是 json。 | --output "json" |
   |query| JMESPath 查询字符串。 有关详细信息，请参阅 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括详细日志记录。 | --verbose |

下面是包含输出的命令示例：

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   以下是运行命令后得到的输出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

---

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的文章，例如：

> [!div class="checklist"]
>
> * 部署 Data Box 的先决条件
> * 订购 Data Box
> * 跟踪订单
> * 取消订单

请继续学习下一篇教程，了解如何设置 Data Box。

> [!div class="nextstepaction"]
> [设置 Azure Data Box](./data-box-deploy-set-up.md)
