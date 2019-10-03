---
title: 计划 Azure 容器注册表任务
description: 设置计时器, 按定义的计划运行 Azure 容器注册表任务。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a4a1099d90b619be383d440067a692c51a2430ac
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509070"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>按定义的计划运行 ACR 任务

本文介绍如何按计划运行[ACR 任务](container-registry-tasks-overview.md)。 通过设置一个或多个*计时器触发器*来计划任务。

计划任务适用于如下方案:

* 运行容器工作负荷以执行计划的维护操作。 例如, 运行容器化应用程序从注册表中删除不需要的映像。
* 在 workday 期间在生产映像中运行一组测试, 作为实时站点监视的一部分。

您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装来运行本文中的示例。 如果要在本地使用, 则需要版本2.0.68 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。


## <a name="about-scheduling-a-task"></a>关于计划任务

* **使用 cron 表达式触发**-任务的计时器触发器使用*cron 表达式*。 表达式是一个字符串, 其中包含五个字段, 用于指定要触发任务的分钟、小时、日、月和日。 支持频率为每分钟一次。

  例如, 表达式`"0 12 * * Mon-Fri"`会在每个工作日的中午 UTC 触发任务。 请参阅本文稍后所述的[详细信息](#cron-expressions)。
* **多个计时器触发器**-允许将多个计时器添加到任务, 前提是计划不同。
    * 创建任务时指定多个计时器触发器, 或稍后添加它们。
    * 选择性地命名触发器以便于管理, 或者 ACR 任务将提供默认的触发器名称。
    * 如果计时器计划每次重叠, 则 ACR 任务会在计划的时间为每个计时器触发任务。
* **其他任务触发器**-在计时器触发的任务中, 还可以基于[源代码提交](container-registry-tutorial-build-task.md)或[基本映像更新](container-registry-tutorial-base-image-update.md)启用触发器。 与其他 ACR 任务一样, 你也可以[手动触发][az-acr-task-run]计划任务。

## <a name="create-a-task-with-a-timer-trigger"></a>使用计时器触发器创建任务

使用[az acr task create][az-acr-task-create]命令创建任务时, 可以选择添加计时器触发器。 `--schedule`添加参数并为计时器传递 cron 表达式。

作为一个简单的示例, 下面的命令将从`hello-world`每天 21:00 UTC 开始, 从 Docker 中心运行映像。 该任务在没有源代码上下文的情况下运行。

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

运行[az acr task show][az-acr-task-show]命令以查看计时器触发器是否已配置。 默认情况下, 基本映像更新触发器也处于启用状态。

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

用[az acr 任务运行][az-acr-task-run]手动触发任务, 以确保正确设置该任务:

```azurecli
az acr task run --name mytask --registry myregistry
```

如果容器成功运行, 则输出类似于以下内容:

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

在计划的时间之后, 运行[az acr task list-][az-acr-task-list-runs] run 命令, 以验证计时器是否按预期触发任务:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

当计时器成功时, 输出类似于以下内容:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>管理计时器触发器

使用[az acr task timer][az-acr-task-timer]命令可以管理 acr 任务的计时器触发器。

### <a name="add-or-update-a-timer-trigger"></a>添加或更新计时器触发器

创建任务后, 可以选择使用[az acr task timer add][az-acr-task-timer-add]命令添加计时器触发器。 下面的示例将计时器触发器名称*timer2*添加到之前创建的*mytask* 。 此计时器每日触发任务 10:30 UTC。

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

使用[az acr task timer update][az-acr-task-timer-update]命令更新现有触发器的计划或更改其状态。 例如, 更新名为*timer2*的触发器以触发 11:30 UTC 时间的任务:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>列出计时器触发器

[Az acr task timer list][az-acr-task-timer-list]命令显示为任务设置的计时器触发器:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

示例输出：

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>删除计时器触发器

使用[az acr task timer remove][az-acr-task-timer-remove]命令从任务中删除计时器触发器。 下面的示例从*mytask*中删除*timer2*触发器:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 表达式

ACR 任务使用[NCronTab](https://github.com/atifaziz/NCrontab)库来解释 cron 表达式。 ACR 任务中支持的表达式包含由空格分隔的五个必需字段:

`{minute} {hour} {day} {month} {day-of-week}`

用于 cron 表达式的时区是协调世界时 (UTC)。 小时数为24小时格式。

> [!NOTE]
> ACR 任务不支持 cron 表达式`{second}`中`{year}`的或字段。 如果复制在其他系统中使用的 cron 表达式, 请确保删除这些字段 (如果使用)。

每个字段可以具有下列类型之一的值：

|类型  |示例  |何时触发  |
|---------|---------|---------|
|一个具体值 |<nobr>`"5 * * * *"`</nobr>|每小时的时间晚5分钟|
|所有值 (`*`)|<nobr>`"* 5 * * *"`</nobr>|一小时中每分钟开始 5:00 UTC (每日60次)|
|一个范围（`-` 运算符）|<nobr>`"0 1-3 * * *"`</nobr>|每天3次, 1:00, 2:00, 3:00 UTC|
|一组值（`,` 运算符）|<nobr>`"20,30,40 * * * *"`</nobr>|每小时3次, 20 分钟, 30 分钟, 超过40分钟|
|一个间隔值（`/` 运算符）|<nobr>`"*/10 * * * *"`</nobr>|每小时6次, 在10分钟, 20 分钟后, 过去一小时

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron 示例

|示例|何时触发  |
|---------|---------|
|`"*/5 * * * *"`|每五分钟一次|
|`"0 * * * *"`|每小时一次（在每小时的开头）|
|`"0 */2 * * *"`|每两小时一次|
|`"0 9-17 * * *"`|每小时从9:00 到 17:00 UTC|
|`"30 9 * * *"`|每日 9:30 UTC|
|`"30 9 * * 1-5"`|每个工作日 9:30 UTC|
|`"30 9 * Jan Mon"`|1月每星期一 9:30 UTC|


## <a name="next-steps"></a>后续步骤

有关使用计划任务清理注册表中的存储库的示例, 请参阅[自动清除 Azure 容器注册表](container-registry-auto-purge.md)中的映像。

有关由源代码提交或基本映像更新触发的任务的示例, 请参阅[ACR 任务教程系列](container-registry-tutorial-quick-task.md)。



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
