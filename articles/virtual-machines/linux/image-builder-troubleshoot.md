---
title: 排查 Azure 映像生成器服务问题
description: 排查使用 Azure VM 映像生成器服务时遇到的常见问题和错误
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 7c937353c645ee5d977a52ec0f8e935eba19a940
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969970"
---
# <a name="troubleshoot-azure-image-builder-service"></a>排查 Azure 映像生成器服务问题

本文将帮助你排查和解决使用 Azure 映像生成器服务时可能会遇到的常见问题。

AIB 故障可能发生在2个方面：
- 映像模板提交
- 映像生成

## <a name="troubleshoot-image-template-submission-errors"></a>排查图像模板提交错误

映像模板提交错误仅在提交时返回。 映像模板提交错误没有错误日志。 如果在提交期间出现错误，则可以通过检查模板的状态来返回错误，特别是查看 `ProvisioningState` 和 `ProvisioningErrorMessage` / `provisioningError` 。

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> 对于 PowerShell，你将需要安装 [Azure 映像生成器 PowerShell 模块](../windows/image-builder-powershell.md#prerequisites)。

以下部分包括常见映像模板提交错误的问题解决方法。

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>当前不支持更新/升级映像模板

#### <a name="error"></a>错误

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>原因

模板已存在。

#### <a name="solution"></a>解决方案

如果提交映像配置模板，但提交失败，模板项目仍然存在。 删除失败的模板。

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>资源操作已完成，但出现终端预配状态 "Failed"

#### <a name="error"></a>错误

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>原因

在大多数情况下，由于缺少权限，会发生资源部署失败错误。

#### <a name="solution"></a>解决方案

根据你的方案，Azure 映像生成器可能需要以下权限：
- 源映像或共享图像库资源组
- 分布图像或共享图像库资源
- 文件定制器正在访问的存储帐户、容器或 blob。 

有关配置权限的详细信息，请参阅 [使用 Azure CLI 配置 Azure 映像生成器服务权限](image-builder-permissions-cli.md) 或 [使用 PowerShell 配置 Azure 映像生成器服务权限](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>获取托管映像时出错

#### <a name="error"></a>错误

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>原因

缺少权限。

#### <a name="solution"></a>解决方案

根据你的方案，Azure 映像生成器可能需要以下权限：
* 源映像或共享图像库资源组
* 分布图像或共享图像库资源
* 文件定制器正在访问的存储帐户、容器或 blob。 

有关配置权限的详细信息，请参阅 [使用 Azure CLI 配置 Azure 映像生成器服务权限](image-builder-permissions-cli.md) 或 [使用 PowerShell 配置 Azure 映像生成器服务权限](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>映像版本的生成步骤失败

#### <a name="error"></a>错误
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>原因

Azure 映像生成器找不到源映像。

#### <a name="solution"></a>解决方案

请确保源图像正确且存在于 Azure 映像生成器服务的位置。

### <a name="downloading-external-file-to-local-file"></a>正在将外部文件下载到本地文件

#### <a name="error"></a>错误

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>原因

文件名或位置不正确，或位置不可访问。

#### <a name="solution"></a>解决方案

确保该文件可访问。 验证 "名称" 和 "位置" 是否正确。

## <a name="troubleshoot-build-failures"></a>排除生成故障

对于映像生成失败，可以从获取错误 `lastrunstatus` ，然后在自定义日志中查看详细信息。


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>自定义日志

当映像生成运行时，将创建日志并将其存储在存储帐户中。 创建映像模板项目时，Azure 映像生成器会在临时资源组中创建存储帐户。

存储帐户名称使用以下模式： **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\> **

例如， *IT_aibmdi_helloImageTemplateLinux01*。

可以通过选择**存储帐户**  >  **blob**来查看资源组中的自定义。  >  `packerlogs`  然后选择 " **目录 > 自定义日志**"。


### <a name="understanding-the-customization-log"></a>了解自定义日志

日志为详细日志。 其中介绍了映像生成，包括映像分发的任何问题，如共享映像库复制。 这些错误会出现在映像模板状态的错误消息中。

自定义日志包含以下阶段：

1. 使用 ARM 模板将生成 VM 和依赖项部署到 IT_ 过渡资源组阶段。 此阶段包括对 Azure 映像生成器资源提供程序的多个发布：
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. 部署阶段的状态。 此阶段包括每个资源部署的状态：
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. 连接到生成 VM 阶段。

    如果是 Windows，则 Azure 映像生成器服务将使用 WinRM 进行连接：
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    如果是 Linux，Azure 映像生成器服务将使用 SSH 进行连接：
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. 运行自定义阶段。 自定义运行时，可以通过查看自定义日志来识别它们。 搜索 * (遥测) *。
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. 取消预配阶段。 Azure 映像生成器添加隐藏的定制器。 此取消设置步骤负责为取消预配而准备 VM。 它使用 c:\DeprovisioningScript.ps1) 运行 Windows Sysprep (，或使用/tmp/DeprovisioningScript.sh) 在 Linux waagent 取消设置 (。 

    例如：
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. 清理阶段。 完成生成后，Azure 映像生成器资源会被删除。
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>脚本/内联自定义疑难解答提示
- 在将代码提供给 Image Builder 之前对其进行测试
- 确保 Azure 策略的和防火墙允许连接到远程资源。
- 将注释输出到控制台，如使用 `Write-Host` 或 `echo` ，这将允许你搜索自定义日志。

## <a name="troubleshoot-common-build-errors"></a>排查常见的生成错误

### <a name="packer-build-command-failure"></a>Packer 生成命令失败

#### <a name="error"></a>错误

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>原因

自定义失败。

#### <a name="solution"></a>解决方案

查看日志以查找定制员失败。 搜索 * (遥测) *。 

例如：
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>超过超时

#### <a name="error"></a>错误

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>原因

生成超出了生成超时。 在 "lastrunstatus" 中出现此错误。

#### <a name="solution"></a>解决方案

1. 查看自定义日志。 标识要运行的最后一个定制器。 搜索 `(telemetry)` 从日志底部开始。 

2. 检查脚本自定义。 自定义项可能不会阻止命令的用户交互，如 `quiet` 选项。 例如，会 `apt-get install -y` 导致脚本执行等待用户交互。

3. 如果使用 `File` 定制器下载大于 20 MB 的项目，请参阅 "解决方法" 一节。

4. 查看脚本中可能会导致脚本等待的错误和依赖项。

5. 如果预计自定义需要更多时间，请增加 [buildTimeoutInMinutes](image-builder-json.md)。 默认值为4小时。

6. 如果有大量占用资源的操作（例如下载千兆字节的文件），请考虑基础生成 VM 大小。 服务使用 Standard_D1_v2 VM。 VM 具有1个 vCPU 和 3.5 GB 的内存。 如果要下载 50 GB，则这可能会耗尽 VM 资源，并导致 Azure 映像生成器服务和生成 VM 之间发生通信故障。 通过设置 [VM_Size](image-builder-json.md#vmprofile)，使用较大的内存 VM 重试生成。

### <a name="long-file-download-time"></a>长时间文件下载时间

#### <a name="error"></a>错误
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>原因 

文件定制器正在下载大文件。

#### <a name="solution"></a>解决方案

文件定制器仅适用于少于 20 MB 的小文件下载。 对于较大的文件下载，请使用 script 或 inline 命令。 例如，在 Linux 上，你可以使用 `wget` 或 `curl` 。 在 Windows 上，可以使用 `Invoke-WebRequest` 。

### <a name="error-waiting-on-shared-image-gallery"></a>等待共享映像库时出错

#### <a name="error"></a>错误

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>原因

图像生成器等待添加图像并将其复制到共享映像库 (SIG) 超时。 如果将图像注入到 SIG 中，则可以假定映像生成成功。 但是，整个进程失败，因为映像生成器正在等待共享映像库完成复制。 即使生成失败，也会继续复制。 可以通过检查分发 *runOutput*来获取映像版本的属性。

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>解决方案

增加 **buildTimeoutInMinutes**。
 
### <a name="low-windows-resource-information-events"></a>低 Windows 资源信息事件

#### <a name="error"></a>错误

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>原因

资源耗尽。 如果 Windows 更新使用默认的生成 VM 大小 D1_V2 运行，则通常会出现此问题。

#### <a name="solution"></a>解决方案

增加生成 VM 大小。

### <a name="builds-finished-but-no-artifacts-were-created"></a>生成已完成，但未创建任何项目

#### <a name="error"></a>错误

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>原因

等待创建所需的 Azure 资源导致超时。

#### <a name="solution"></a>解决方案

请重新运行生成，然后重试。

### <a name="resource-not-found"></a>找不到资源

#### <a name="error"></a>错误

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>原因

缺少权限。

#### <a name="solution"></a>解决方案

重新检查 Azure 映像生成器是否具有所需的所有权限。 

有关配置权限的详细信息，请参阅 [使用 Azure CLI 配置 Azure 映像生成器服务权限](image-builder-permissions-cli.md) 或 [使用 PowerShell 配置 Azure 映像生成器服务权限](image-builder-permissions-powershell.md)

### <a name="sysprep-timing"></a>Sysprep 计时

#### <a name="error"></a>错误

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>原因

原因可能是由于 D1_V2 VM 大小导致的计时问题。 如果自定义项的限制和执行时间小于三秒，则由 Azure 映像生成器运行 sysprep 命令以取消预配。 当 Azure 映像生成器取消预配时，sysprep 命令将检查 *WindowsAzureGuestAgent*，这可能会导致计时问题。 

#### <a name="solution"></a>解决方案

增加 VM 大小。 或者，你可以添加一个60秒的 PowerShell 睡眠自定义，以避免出现计时问题。

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>取消上下文取消上下文后取消生成器

#### <a name="error"></a>错误
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>原因
映像生成器服务使用端口 22 (Linux) 或 5986 (Windows) 来连接到 build VM，这在映像生成过程中将服务从生成 VM 断开连接时出现。 断开连接的原因可能有所不同，但在脚本中启用或配置防火墙可能会阻止上述端口。

#### <a name="solution"></a>解决方案
查看脚本以了解防火墙更改/启用或更改 SSH 或 WinRM，并确保任何更改都允许在上述端口上的服务和生成 VM 之间进行持续连接。 有关映像生成器网络的详细信息，请查看 [要求](./image-builder-networking.md)。

## <a name="devops-task"></a>DevOps 任务 

### <a name="troubleshooting-the-task"></a>任务疑难解答
只有在自定义期间发生错误时，任务才会失败，这种情况下，任务将报告失败并将暂存资源组保留在日志中，以便你可以确定问题所在。 

若要查找日志，您需要知道模板名称，进入管道 > 失败的生成 > 钻取到 AIB DevOps 任务，然后您将看到日志和模板名称：
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

请参阅门户，在 "资源组" 中搜索模板名称，然后查找 IT_ * 的资源组。
> blob > 容器 > 日志中转到存储帐户。

### <a name="troubleshooting-successful-builds"></a>成功生成疑难解答
在某些情况下，你需要调查成功的生成，并想要查看日志。 如前所述，如果映像生成成功，将在清理过程中删除包含日志的暂存资源组。 但是，您可以执行的操作是在内联命令后面引入睡眠，然后在生成暂停时获取日志。 要执行此操作，请执行以下步骤：
 
1. 更新内联命令并添加： Write-Host/Echo "睡眠" –这将允许你在日志中搜索
2. 为至少10分钟入门添加睡眠，可以使用 " [开始-睡眠](/powershell/module/microsoft.powershell.utility/start-sleep)" 或 " `Sleep` Linux" 命令。
3. 使用上面的方法来确定日志位置，然后继续下载/检查日志，直到其进入睡眠状态。


### <a name="operation-was-canceled"></a>操作已取消

#### <a name="error"></a>错误

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>原因

如果用户未取消此生成，则 Azure DevOps 用户代理已将其取消。 由于 Azure DevOps 功能，最有可能的超时时间为1小时。 如果你使用的是专用项目和代理，则会收到60分钟的生成时间。 如果生成超过超时时间，DevOps 将取消正在运行的任务。

有关 Azure DevOps 功能和限制的详细信息，请参阅 [Microsoft 托管的代理](/azure/devops/pipelines/agents/hosted?view=azure-devops#capabilities-and-limitations)
 
#### <a name="solution"></a>解决方案

可以托管自己的 DevOps 代理，也可以考虑缩短生成时间。 例如，如果要分发到共享映像库，则复制到一个区域。 如果要异步复制，则为。 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Windows 登录缓慢： ' 请等待 Windows 模块安装程序 '

#### <a name="error"></a>错误
使用映像生成器创建 Windows 10 映像后，请从该映像创建一个 VM，然后从该映像创建一个 VM，并在第一次登录时需要等待几分钟，并显示以下消息：
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>解决方案
首先，在映像生成中，通过添加 Windows 重新启动定制器作为最后一个自定义项，并完成所有软件安装，以检查是否不需要任何未完成的重新启动。 最后，将 [/mode： vm](/windows-hardware/manufacture/desktop/sysprep-command-line-options) 选项添加到 AIB 使用的默认 sysprep，请参阅下面的 "从 AIB 映像创建的 vm 未成功创建" > 重写命令 "  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>无法成功创建从 AIB 映像创建的 Vm

默认情况下，Azure 映像生成器会在每个映像自定义阶段结束时运行 *预配* 代码，以便 *通用化* 映像。 通用化是一个进程，其中，映像已设置为可重复使用以创建多个 Vm，你可以传入 VM 设置，例如主机名、用户名等。对于 Windows，Azure 映像生成器会执行 *Sysprep*，Linux Azure 映像生成器将运行 `waagent -deprovision` 。 

对于 Windows，Azure 映像生成器使用通用 Sysprep 命令。 但是，这可能不适合每个成功的 Windows 通用化。 Azure 映像生成器允许自定义 Sysprep 命令。 请注意，Azure 映像生成器是一个映像自动化工具。 它负责成功运行 Sysprep 命令。 但是，你可能需要不同的 Sysprep 命令来使你的映像可重复使用。 对于 Linux，Azure 映像生成器使用一般 `waagent -deprovision+user` 命令。 有关详细信息，请参阅 [Microsoft Azure Linux 代理文档](https://github.com/Azure/WALinuxAgent#command-line-options)。

如果要迁移现有的自定义项，并且使用的是不同的 Sysprep/waagent 命令，则可以尝试使用映像生成器一般命令。 如果 VM 创建失败，请使用以前的 Sysprep/waagent 命令。

> [!NOTE]
> 如果 AIB 成功创建了一个 Windows 自定义映像，然后从该映像创建一个 VM，则查找 VM 将不会成功创建 (例如，VM 创建命令未完成/超时) ，你将需要查看 Windows Server Sysprep 文档。 或者，你可以向 Windows Server Sysprep 客户服务支持团队发出支持请求，用户可以对正确的 Sysprep 命令进行故障排除和建议。

### <a name="command-locations-and-filenames"></a>命令位置和文件名

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux：
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep 命令： Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>取消设置命令： Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>重写命令

若要重写命令，请使用 PowerShell 或 shell 脚本设置程序创建具有确切文件名的命令文件，并将其放入前面列出的目录中。 Azure 映像生成器将读取这些命令，并将输出写入 *自定义日志*。

## <a name="getting-support"></a>获取支持
如果已参考本指南，但仍无法解决问题，则可以提出支持案例。 执行此操作时，请选择 "正确的产品和支持" 主题，这样做会与 Azure VM 映像生成器支持团队联系。

选择案例产品：
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure 映像生成器概述](image-builder-overview.md)。