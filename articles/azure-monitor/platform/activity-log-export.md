---
title: 导出 Azure 活动日志
description: 将 Azure 活动日志导出到存储，以便存档或 Azure 事件中心导出到 Azure 之外。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: e46574ae7f8faa67c2cc0c1afef1917270f69175
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715896"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>将 Azure 活动日志导出到存储或 Azure 事件中心

> [!IMPORTANT]
> 用于将 Azure 活动日志发送到 Azure 存储和 Azure 事件中心的方法已更改为[诊断设置](diagnostic-settings.md)。 本文介绍不推荐使用的旧方法。 请参阅更新到[Azure 活动日志集合和导出](diagnostic-settings-legacy.md)进行比较。


[Azure 活动日志](platform-logs-overview.md)提供对 Azure 订阅中发生的订阅级别事件的见解。 除了查看 Azure 门户中的活动日志，或者将其复制到 Log Analytics 工作区（其中可以使用 Azure Monitor 收集的其他数据进行分析），可以创建日志配置文件，将活动日志存档到 Azure 存储帐户，或将其流式传输到 事件中心。

## <a name="archive-activity-log"></a>存档活动日志
如果要将日志数据保留超过90天（对保留策略具有完全控制），以进行审核、静态分析或备份，将活动日志存档到存储帐户非常有用。 如果只需将事件保留90天或更短的时间，则无需设置到存储帐户的存档，因为活动日志事件保留在 Azure 平台90天内。

## <a name="stream-activity-log-to-event-hub"></a>将活动日志流式传输到事件中心
[Azure 事件中心](/azure/event-hubs/)是一种数据流式处理平台和事件引入服务，每秒可接收和处理数百万事件。 이벤트 허브로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. 可以通过两种方式将流式传输功能用于活动日志：
* **타사 로깅 및 원격 분석 시스템으로 스트림**: 시간이 지나면서 Azure Event Hubs 스트리밍은 활동 로그를 타사 SIEM 및 로그 분석 솔루션으로 파이핑하기 위한 메커니즘이 되고 있습니다.
* **사용자 지정 원격 분석 및 로깅 플랫폼 빌드**: 사용자 지정 빌드 원격 분석 플랫폼이 이미 있거나 플랫폼 빌드에 대해 생각하고 있는 경우 Event Hubs의 확장성 높은 게시-구독 특성을 통해 활동 로그를 유연하게 수집할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

### <a name="storage-account"></a>Storage 계정
如果要存档活动日志，则需要[创建一个存储帐户](../../storage/common/storage-account-create.md)（如果尚未安装）。 不应使用存储在其中的其他非监视数据的现有存储帐户，以便您可以更好地控制对监视数据的访问。 不过，如果还将日志和指标存档到存储帐户，则可以选择使用同一个存储帐户将所有监视数据都保存在一个中央位置。

설정을 구성하는 사용자가 두 구독에 대한 적절한 RBAC 액세스를 가진 경우 스토리지 계정은 로그를 내보내는 구독과 동일한 구독을 가지고 있지 않아도 됩니다.
> [!NOTE]
>  현재는 보안 가상 네트워크 뒤에 있는 스토리지 계정에 데이터를 보관할 수 없습니다.

### <a name="event-hubs"></a>Event Hubs
如果要将活动日志发送到事件中心，则需要[创建一个事件中心](../../event-hubs/event-hubs-create.md)（如果还没有）。 如果以前已将活动日志事件流式传输到此事件中心命名空间，则会重复使用该事件中心。

공유 액세스 정책은 스트리밍 메커니즘에서 보유하는 권한을 정의합니다. 流式传输到事件中心需要“管理”、“发送”和“侦听”权限。 可以在事件中心命名空间的 "配置" 选项卡下的 Azure 门户中创建或修改事件中心命名空间的共享访问策略。

若要更新活动日志的日志配置文件以包含流式处理，必须对该事件中心授权规则具有 ListKey 权限。 설정을 구성하는 사용자에게 구독 모두에 액세스할 수 있는 적절한 RBAC 액세스 권한이 있다면 Event Hubs 네임스페이스가 로그를 내보내는 구독과 동일한 구독에 위치하지 않아도 됩니다. 구독은 모두 동일한 AAD 테넌트에 위치합니다.

通过[创建日志配置文件](#create-a-log-profile)，将活动日志流式传输到事件中心。

## <a name="create-a-log-profile"></a>创建日志配置文件
定义如何使用**日志配置文件**导出 Azure 活动日志。 每个 Azure 订阅只能有一个日志配置文件。 可以通过门户中的 "活动日志" 边栏选项卡中的 "**导出**" 选项配置这些设置。 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell cmdlet 또는 CLI를 사용하여 프로그래밍 방식으로 구성할 수도 있습니다.

日志配置文件定义以下各项。

**应将活动日志发送到的位置。** 目前可用的选项为存储帐户或事件中心。

**应发送哪些事件类别。** 日志配置文件和活动日志事件中的*类别*含义不同。 在日志配置文件中，"*类别*" 表示操作类型（写入、删除、操作）。 在活动日志事件中，"*类别*" 属性表示事件的来源或类型（例如，"管理"、"ServiceHealth" 和 "警报"）。

**应导出哪些区域（位置）。** 你应包括所有位置，因为活动日志中的许多事件都是全局事件。

**应将活动日志保留在存储帐户中的时间长度。** 보존 기간이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 否则，该值可以是介于1到365之间的任意天数。

如果设置了保留策略，但禁止将日志存储在存储帐户中，则保留策略无效。 보존 정책은 매일 적용되므로 하루의 마지막에(UTC) 보존 정책이 지난 날의 로그가 삭제됩니다. 예를 들어, 하루의 보존 정책이 있는 경우 오늘 날짜가 시작될 때 하루 전의 로그가 삭제됩니다. 삭제 프로세스는 자정(UTC)에 시작되지만, 스토리지 계정에서 로그가 삭제될 때까지 최대 24시간이 걸릴 수 있습니다.


> [!IMPORTANT]
> 如果未注册 Microsoft Insights 资源提供程序，则在创建日志配置文件时可能会收到错误。 若要注册此提供程序，请参阅[Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。


### <a name="create-log-profile-using-the-azure-portal"></a>使用 Azure 门户创建日志配置文件

在 Azure 门户中使用 "**导出到事件中心**" 选项创建或编辑日志配置文件。

1. 从 Azure 门户的 " **Azure Monitor** " 菜单中，选择 "**活动日志**"。
3. **진단 설정**을 클릭합니다.

   ![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 单击紫色横幅了解旧体验。

    ![旧体验](media/diagnostic-settings-subscription/legacy-experience.png)

3. 在出现的边栏选项卡中，指定以下内容：
   * 包含要导出的事件的区域。 你应选择 "所有区域"，以确保不会错过关键事件，因为活动日志是一个全局（非区域）日志，因此大多数事件都没有关联的区域。
   * 如果要写入存储帐户，请执行以下操作：
       * 要将事件保存到的存储帐户。
       * 要在存储中保留这些事件的天数。 0일로 설정하면 로그를 계속 유지합니다.
   * 如果要写入事件中心，请执行以下操作：
       * 要在其中创建事件中心来流式传输这些事件的服务总线命名空间。

     ![활동 로그 내보내기 블레이드](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. **저장**을 클릭하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.


### <a name="configure-log-profile-using-powershell"></a>使用 PowerShell 配置日志配置文件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果日志配置文件已存在，首先需要删除现有的日志配置文件，然后再创建一个新的日志配置文件。

1. `Get-AzLogProfile`를 사용하여 로그 프로필이 있는지 확인합니다.  如果日志配置文件已存在，请记下*name*属性。

1. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `Remove-AzLogProfile`을 사용합니다.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. `Add-AzLogProfile`를 사용하여 새 로그 프로필을 만듭니다.

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 속성 | 필수 | Description |
    | --- | --- | --- |
    | 이름 |예 |로그 프로필의 이름입니다. |
    | StorageAccountId |아닙니다. |应将活动日志保存到其中的存储帐户的资源 ID。 |
    | serviceBusRuleId |아닙니다. |이벤트 허브를 만들 Service Bus 네임스페이스의 Service Bus 규칙 ID입니다. 这是一个字符串，格式为： `{service bus resource ID}/authorizationrules/{key name}`。 |
    | 위치 |예 |활동 로그 이벤트를 수집할 쉼표로 구분된 지역 목록입니다. |
    | RetentionInDays |예 |事件在存储帐户中的保留天数，介于1到365之间。 0 값은 로그를 무기한 저장합니다. |
    | 범주 |아닙니다. |수집할 쉼표로 구분된 이벤트 범주 목록입니다. 可能的值包括 "_写入_"、"_删除_" 和 "_操作_"。 |

### <a name="example-script"></a>예제 스크립트
下面是一个示例 PowerShell 脚本，用于创建将活动日志写入存储帐户和事件中心的日志配置文件。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>使用 Azure CLI 配置日志配置文件

로그 프로필이 이미 있으면 먼저 기존 로그 프로필을 제거한 다음, 새 로그 프로필을 생성해야 합니다.

1. `az monitor log-profiles list`를 사용하여 로그 프로필이 있는지 확인합니다.
2. *name* 속성의 값을 사용하여 로그 프로필을 제거하려면 `az monitor log-profiles delete --name "<log profile name>`을 사용합니다.
3. `az monitor log-profiles create`를 사용하여 새 로그 프로필을 만듭니다.

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 속성 | 필수 | Description |
    | --- | --- | --- |
    | name |예 |로그 프로필의 이름입니다. |
    | storage-account-id |예 |활동 로그를 저장할 Storage 계정의 리소스 ID입니다. |
    | 위치 |예 |활동 로그 이벤트를 수집할 공백으로 구분된 지역 목록입니다. `az account list-locations --query [].name`을 사용하여 구독에 대한 모든 지역 목록을 볼 수 있습니다. |
    | 일 |예 |事件应保留的天数，介于1到365之间。 0 값은 로그를 무기한(영원히) 저장합니다.  如果为零，则 enabled 参数应设置为 false。 |
    |사용 | 예 |True 또는 False입니다.  보존 정책을 사용하거나 비활성화하는 데 사용합니다.  True이면 일 매개 변수 0보다 큰 값이어야 합니다.
    | 범주 |예 |수집해야 할 공백으로 구분된 이벤트 범주 목록입니다. 가능한 값은 쓰기, 삭제 및 작업입니다. |



## <a name="next-steps"></a>다음 단계

* [활동 로그에 대한 자세한 내용](../../azure-resource-manager/management/view-activity-logs.md)
* [将活动日志收集到 Azure Monitor 日志中](activity-log-collect.md)
