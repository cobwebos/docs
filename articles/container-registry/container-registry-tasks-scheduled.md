---
title: 计划 Azure 容器注册表任务
description: 设置计时器，以便在定义的计划上运行的 Azure 容器注册表任务。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509698"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>在定义的计划上运行的 ACR 任务

本文介绍如何运行[ACR 任务](container-registry-tasks-overview.md)按计划。 通过设置一个或多个计划的任务*计时器触发器*。 

计划的任务非常有用的方案如下所示：

* 运行计划的维护操作的容器工作负荷。 例如，运行容器化的应用从注册表中删除不需要的映像。
* 在你实时站点监视 workday 期间生产映像上运行一组测试。

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装来运行本文中的示例。 如果想要使用它可在本地，版本 2.0.68 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。


## <a name="about-scheduling-a-task"></a>有关计划任务

* **触发器的 cron 表达式**-一项任务的计时器触发器使用*cron 表达式*。 表达式是时间的指定分钟、 小时、 天、 月和天的周来触发任务的五个字段的字符串。 支持最多每分钟一次的频率。 

  例如，表达式`"0 12 * * Mon-Fri"`在中午 UTC 上每个工作日触发任务。 请参阅本文稍后所述的[详细信息](#cron-expressions)。
* **多个计时器触发器**-将多个计时器添加到任务允许，只要计划存在差异。 
    * 在创建任务，或将其添加更高版本时，请指定多个计时器触发器。
    * （可选） 为了方便管理，命名为触发器或 ACR 任务将提供默认触发器名称。
    * 如果计时器计划重叠一次，ACR 任务触发的任务在计划的时间的每个计时器。 
* **其他任务触发器**-在计时器触发的任务中，您还可以启用基于触发事件[源代码提交](container-registry-tutorial-build-task.md)或[基本映像更新](container-registry-tutorial-base-image-update.md)。 其他 ACR 与任务一样，您还可以[手动触发][az-acr-task-run]计划的任务。

## <a name="create-a-task-with-a-timer-trigger"></a>使用计时器触发器创建任务

当创建的任务[az acr 任务创建][az-acr-task-create]命令时，你可以选择添加一个计时器触发器。 添加`--schedule`参数并传递计时器的 cron 表达式。 

作为一个简单的示例，以下命令运行的触发器`hello-world`每天在 21:00 UTC Docker 中心映像。 在任务运行而无需源代码上下文。

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

运行[az acr 任务显示][az-acr-task-show]命令，查看配置计时器触发器。 默认情况下，还会启用基本映像 update 触发器。

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

触发手动与任务[az acr 任务运行][az-acr-task-run]以确保它设置正确：

```azurecli
az acr task run --name mytask --registry myregistry
```

如果容器成功运行，是类似于以下输出：

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

计划的时间之后, 运行[az acr 任务列表运行][az-acr-task-list-runs]命令来验证计时器触发任务，按预期方式：

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

计时器操作成功，请将类似于以下输出：

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>管理计时器触发器

使用[az acr 任务计时器][az-acr-task-timer]用于管理 ACR 任务的计时器触发器的命令。

### <a name="add-or-update-a-timer-trigger"></a>添加或更新的一个计时器触发器

通过创建一个任务后，根据需要添加一个计时器触发器[az acr 任务计时器添加][az-acr-task-timer-add]命令。 下面的示例添加一个计时器触发器名称*计时器 2*到*mytask*之前创建。 此计时器触发的任务在 10:30 每天 UTC。

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

更新现有的触发器的计划或更改其状态，通过使用[az acr 任务计时器更新][az-acr-task-timer-update]命令。 例如，更新名为触发器*计时器 2*于 11:30 触发任务 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>列表计时器触发器

[Az acr 任务计时器列表][az-acr-task-timer-list]命令显示为任务设置的计时器触发器：

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

使用[az acr 任务计时器删除][az-acr-task-timer-remove]命令以从任务中删除一个计时器触发器。 下面的示例移除*计时器 2*从触发*mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 表达式

使用 ACR 任务[NCronTab](https://github.com/atifaziz/NCrontab)库来解释 cron 表达式。 ACR 任务中受支持的表达式具有五个空格分隔的必填的字段：

`{minute} {hour} {day} {month} {day-of-week}`

通过 cron 表达式使用的时区为协调世界时 (UTC)。 时间为 24 小时制的小时。

> [!NOTE]
> ACR 任务不支持`{second}`或`{year}`cron 表达式中的字段。 如果复制另一个系统中使用的 cron 表达式，请务必删除这些字段，如果使用它们。

每个字段可以具有下列类型之一的值：

|Type  |示例  |何时触发  |
|---------|---------|---------|
|一个具体值 |<nobr>"5 * * * *"</nobr>|每隔一小时在整点过后的 5 分钟|
|所有值 (`*`)|<nobr>"* 5 * * *"</nobr>|每分钟的小时开头 5:00 UTC （一天 60 次）|
|一个范围（`-` 运算符）|<nobr>"0 1-3 * * *"</nobr>|每天 3 次，在 1:00、 2:00 和 3:00 UTC|  
|一组值（`,` 运算符）|<nobr>"20,30,40 * * * *"</nobr>|每小时，在 20 分钟，30 分钟，并在整点过后的 40 分钟 3 次|
|一个间隔值（`/` 运算符）|<nobr>"*/10 * * * *"</nobr>|6 次 / 小时，在 10 分钟，20 分钟，诸如此类前一个小时

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron 示例

|示例|何时触发  |
|---------|---------|
|`"*/5 * * * *"`|每五分钟一次|
|`"0 * * * *"`|每小时一次（在每小时的开头）|
|`"0 */2 * * *"`|每两小时一次|
|`"0 9-17 * * *"`|每小时一次从 9:00 为 17:00 UTC|
|`"30 9 * * *"`|上午 9:30 UTC 每一天|
|`"30 9 * * 1-5"`|上午 9:30 UTC 每个工作日|
|`"30 9 * Jan Mon"`|上午 9:30 UTC 年 1 月中每个星期一|


## <a name="next-steps"></a>后续步骤

通过源代码提交或基本映像更新触发任务的示例，请查看[ACR 任务教程系列](container-registry-tutorial-quick-task.md)。



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