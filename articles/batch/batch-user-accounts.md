---
title: 在用户帐户下运行任务 - Azure Batch
description: 如果能够配置要在其下运行任务的用户帐户，会很有帮助。 了解用户帐户的类型以及如何配置它们。
ms.topic: article
ms.date: 11/18/2019
ms.custom: seodec18
ms.openlocfilehash: 1aeb96075e95d7bc0d1e4527fb50b2d5238dbab5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980280"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>在批处理中的用户帐户下运行任务

> [!NOTE] 
> 出于安全原因，本文中所述的用户帐户与用于远程桌面协议 (RDP) 或安全外壳 (SSH) 的用户帐户不同。 
>
> 若要通过 SSH 连接到运行 Linux 虚拟机配置的节点，请参阅[使用远程桌面连接到 Azure 中的 Linux VM](../virtual-machines/virtual-machines-linux-use-remote-desktop.md)。 若要通过 RDP 连接到运行 Windows 的节点，请参阅[连接到 Windows Server VM](../virtual-machines/windows/connect-logon.md)。<br /><br />
> 若要通过 RDP 连接到运行云服务配置的节点，请参阅[为 Azure 云服务中的角色启用远程桌面连接](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md)。

Azure Batch 中的任务始终在用户帐户下运行。 默认情况下，任务在没有管理员权限的标准用户帐户下运行。 这些默认用户帐户设置通常足以满足操作需要。 但是，对于某些方案，如果能够配置用于运行任务的用户帐户，则会很有帮助。 本文介绍用户帐户的类型以及如何为方案配置这些帐户。

## <a name="types-of-user-accounts"></a>用户帐户的类型

Azure Batch 提供两种类型的用户帐户来运行任务：

- **自动用户帐户。** 自动用户帐户是 Batch 服务自动创建的内置用户帐户。 默认情况下，任务在自动用户帐户下运行。 可为任务配置自动用户规范，指明任务应在哪个自动用户帐户下运行。 使用自动用户规范可以指定将要运行任务的自动用户帐户的提升级别和范围。 

- **命名用户帐户。** 创建池时，可为该池指定一个或多个命名用户帐户。 每个用户帐户在该池的每个节点上创建。 除了帐户名以外，还可以指定用户帐户密码、提升级别，对于 Linux 池，还可以指定 SSH 私钥。 添加任务时，可以指定任务应在其下运行的命名用户帐户。

> [!IMPORTANT] 
> Batch 服务版本 2017-01-01.4.0 引入了一项重大更改，你需要更新代码才能调用该版本。 如果你要从旧版 Batch 迁移代码，请注意，REST API 或 Batch 客户端库不再支持 **runElevated** 属性。 请使用任务的新 **userIdentity** 属性指定提升级别。 有关使用某个客户端库时如何更新 Batch 代码的快速指导，请参阅标题为[将代码更新到最新的 Batch 客户端库](#update-your-code-to-the-latest-batch-client-library)的部分。
>
>

## <a name="user-account-access-to-files-and-directories"></a>用户帐户对文件和目录的访问权限

自动用户帐户和命名用户帐户对任务的工作目录、共享目录和多实例任务目录拥有读/写访问权限。 这两种类型的帐户都对启动目录和作业准备目录拥有读取访问权限。

如果某个任务在用于运行启动任务的同一帐户下运行，则该任务对启动任务目录拥有读写访问权限。 同理，如果某个任务在用于运行作业准备任务的同一帐户下运行，则该任务对作业准备任务目录拥有读写访问权限。 如果用于运行某个任务的帐户不同于用于启动任务或作业准备任务的帐户，则该任务对相应目录拥有只读访问权限。

有关通过任务访问文件和目录的详细信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md#files-and-directories)。

## <a name="elevated-access-for-tasks"></a>任务的提升访问权限 

用户帐户的提升级别指示任务是否可以使用提升的访问权限运行。 自动用户帐户和命名用户帐户都可以使用提升的访问权限运行。 提升级别的两个选项为：

- **NonAdmin：** 任务以没有提升访问权限的标准用户身份运行。 Batch 用户帐户的默认提升级别始终为 **NonAdmin**。
- **Admin：** 任务以拥有提升访问权限的用户身份运行，以完全管理员权限操作。 

## <a name="auto-user-accounts"></a>自动用户帐户

默认情况下，任务在 Batch 中的自动用户帐户下，以没有提升访问权限但具有任务范围的标准用户身份运行。 如果为任务范围配置了自动用户规范，Batch 服务只为该任务创建自动用户帐户。

任务范围的替代设置为池范围。 如果为池范围配置了某个任务的自动用户规范，该任务将在可供池中任何任务使用的自动用户帐户下运行。 有关池范围的详细信息，请参阅标题为“以具有池范围的自动用户身份运行任务”部分。   

在 Windows 和 Linux 节点上，默认范围不同：

- 在 Windows 节点上，任务默认在任务范围下运行。
- Linux 节点始终在池范围下运行。

自动用户规范有四种可能的配置，其中每种配置对应于一个唯一的自动用户帐户：

- 具有任务范围的非管理员访问权限（默认的自动用户规范）
- 具有任务范围的管理员（提升的）访问权限
- 具有池范围的非管理员访问权限
- 具有池范围的管理员访问权限

> [!IMPORTANT] 
> 在任务范围下运行的任务对节点上的其他任务没有实际访问权限。 但是，有权访问帐户的恶意用户可能会通过提交一个使用管理员特权运行的任务来解除这种限制，从而能够访问其他任务目录。 恶意用户还可能会使用 RDP 或 SSH 连接到节点。 请务必保护对 Batch 帐户密钥的访问，防止出现这种情况。 如果你怀疑自己的帐户被泄露，请务必重新生成密钥。
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>以拥有提升访问权限的自动用户身份运行任务

如果需要使用提升的访问权限运行任务，可以针对管理员特权配置自动用户规范。 例如，启动任务可能需要使用提升的访问权限在节点上安装软件。

> [!NOTE] 
> 一般而言，最好是仅当有必要时才使用提升的访问权限。 最佳做法建议，只授予实现所需结果而要必须用到的最低特权。 例如，如果某个启动任务要为当前用户而不是所有用户安装软件，你可以避免向该任务授予提升的访问权限。 可以针对需要在同一帐户下运行的所有任务（包括启动任务）配置池范围和非管理员访问权限的自动用户规范。 
>
>

以下代码片段演示如何配置自动用户规范。 这些示例将提升级别设置为 `Admin`，将范围设置为 `Task`。 任务范围是默认设置，但此处出于示范目的包含了此设置。

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>以具有池范围的自动用户身份运行任务

预配节点后，将在池中的每个节点上创建两个池范围的自动用户帐户，其中一个帐户拥有提升的访问权限，另一个帐户没有提升的访问权限。 如果将给定任务的自动用户范围设置为池范围，将在这两个池范围自动用户帐户中的一个帐户下运行该任务。 

为自动用户指定池范围时，使用管理员访问权限运行的所有任务将在同一个池范围自动用户帐户下运行。 同样，不使用管理员权限运行的任务也在单个池范围自动用户帐户下运行。 

> [!NOTE] 
> 两个池范围自动用户帐户是独立的帐户。 在池范围管理帐户下运行的任务不能与标准帐户下运行的任务共享数据，反之亦然。 
>
>

在同一自动用户帐户下运行任务的优势在于，任务可与同一个节点上运行的其他任务共享数据。

在任务之间共享机密就是一种有效的方案，这样可以在两个池范围自动用户帐户中的一个帐户下运行任务。 例如，假设某个启动任务需要在其他任务可以使用的节点上预配机密。 可以使用 Windows 数据保护 API (DPAPI)，但这需要管理员特权。 不过，可以在用户级别保护机密。 在同一用户帐户下运行的任务无需提升的访问权限即可访问机密。

你可能想要在具有池范围的自动用户帐户下运行任务的另一种情景是实现消息传递接口 (MPI) 文件共享。 如果 MPI 任务中的节点需要处理相同的文件数据，MPI 文件共享将非常有用。 如果头节点和子节点在同一个自动用户帐户下运行，则头节点将创建可由子节点访问的文件共享。 

以下代码片段在 Batch .NET 中将自动用户的范围设置为任务的池范围。 已省略提升级别，因此，任务将在标准池范围自动用户帐户下运行。

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>命名用户帐户

创建池时，可以定义命名用户帐户。 命名用户帐户具有你提供的名称和密码。 可为命名用户帐户指定提升级别。 对于 Linux 节点，还可以提供 SSH 私钥。

命名用户帐户在池中的所有节点上存在，可供这些节点上运行的所有任务使用。 可为一个池定义任意数目的命名用户。 添加任务或任务集合时，可以指定任务需在池中定义的某个命名用户帐户下运行。

如果你要在同一个用户帐户下运行作业中的所有任务，但同时要将这些任务与其他作业中运行的任务区分开来，则命名用户帐户将非常有用。 例如，可为每个作业创建一个命名用户，并在该命名用户帐户下运行每个作业的任务。 然后，每个作业可与其自身的任务共享机密，但不能与其他作业中运行的任务共享机密。

还可以使用命名用户帐户来运行可在外部资源（例如文件共享）上设置权限的任务。 使用命名用户帐户可以控制用户标识，并使用该用户标识来设置权限。  

命名用户帐户可在 Linux 节点之间启用无密码 SSH。 可以在需要运行多实例任务的 Linux 节点中使用命名用户帐户。 池中的每个节点可以在整个池中定义的用户帐户下运行任务。 有关多实例任务的详细信息，请参阅[使用多实例任务运行 MPI 应用程序](batch-mpi.md)。

### <a name="create-named-user-accounts"></a>创建命名用户帐户

若要在 Batch 中创建命名用户帐户，请在池中添加一个用户帐户集合。 以下代码片段演示如何在 .NET、Java 和 Python 中创建命名用户帐户。 这些代码片段演示如何在池中创建管理员和非管理员命名帐户。 这些示例使用云服务配置创建池，但你在使用虚拟机配置创建 Windows 或 Linux 池时，可以使用相同的方法。

#### <a name="batch-net-example-windows"></a>Batch .NET 示例 (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET 示例 (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Batch Java 示例

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch Python 示例

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>在拥有提升的访问权限的命名用户帐户下运行任务

若要以提升权限的用户身份运行某个任务，请将该任务的 **UserIdentity** 属性设置为创建的命名用户帐户（该帐户的 **ElevationLevel** 属性已设置为 `Admin`）。

此代码片段指定任务应在命名用户帐户下运行。 此命名用户帐户是在创建池时定义的。 在本例中，创建的命名用户帐户拥有管理员权限：

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>将代码更新到最新的 Batch 客户端库

Batch 服务版本 2017-01-01.4.0 引入了一项重大更改，已将早期版本中的 **runElevated** 属性替换为 **userIdentity** 属性。 下表提供了从早期版本的客户端库更新代码时可以参考的简单更改对照。

### <a name="batch-net"></a>Batch .NET

| 如果代码使用...                  | 请更新为...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` 未指定 | 无需更新                                                                                               |

### <a name="batch-java"></a>Batch Java

| 如果代码使用...                      | 请更新为...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` 未指定 | 无需更新                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| 如果代码使用...                      | 请更新为...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`，其中 <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`，其中 <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` 未指定 | 无需更新                                                                                                                                  |


## <a name="next-steps"></a>后续步骤

* 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
