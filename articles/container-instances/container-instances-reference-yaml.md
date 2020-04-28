---
title: 容器组的 YAML 参考
description: Azure 容器实例支持的用于配置容器组的 YAML 文件参考
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896566"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML 参考：Azure 容器实例

本文介绍 Azure 容器实例支持的、用于配置[容器组](container-instances-container-groups.md)的 YAML 文件的语法和属性。 使用 YAML 文件可将组配置输入到 Azure CLI 中的 [az container create][az-container-create] 命令。 

YAML 文件是为可再现的部署配置容器组的便捷方式。 这是使用[资源管理器模板](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups)或 Azure 容器实例 sdk 创建或更新容器组的一种简单方法。

> [!NOTE]
> 本参考文章适用于 Azure 容器实例 REST API 版本 `2018-10-01` 的 YAML 文件。

## <a name="schema"></a>架构 

下面是 YAML 文件的架构，其中的注释突出显示了关键属性。 有关此架构中的属性的说明，请参阅[属性值](#property-values)部分。

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>属性值

下表描述了需要在架构中设置的值。

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Microsoft.ContainerInstance/containerGroups 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | 是 | 容器组的名称。 |
|  apiVersion | 枚举 | 是 | 2018-10-01 |
|  location | string | 否 | 资源位置。 |
|  tags | object | 否 | 资源标记。 |
|  identity | object | 否 | 容器组的标识（如果已配置）。 - [ContainerGroupIdentity 对象](#ContainerGroupIdentity) |
|  properties | object | 是 | [ContainerGroupProperties 对象](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  type | 枚举 | 否 | 容器组使用的标识类型。 类型“SystemAssigned ,UserAssigned”包含隐式创建的标识和一组用户分配的标识。 类型“None”删除容器组中的所有标识。 - SystemAssigned、UserAssigned、SystemAssigned、UserAssigned、None |
|  userAssignedIdentities | object | 否 | 与容器组关联的用户标识列表。 用户标识字典键引用是采用以下格式的 Azure 资源管理器资源 ID：“/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}”。 |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | 是 | 容器组中的容器。 - [Container 对象](#Container) |
|  imageRegistryCredentials | array | 否 | 创建容器组所依据的映像注册表凭据。 - [ImageRegistryCredential 对象](#ImageRegistryCredential) |
|  restartPolicy | 枚举 | 否 | 容器组中所有容器的重启策略。 - `Always` 始终重启-`OnFailure` 失败时重启- `Never` 永不重启。 - Always、OnFailure、Never |
|  ipAddress | object | 否 | 容器组的 IP 地址类型。 - [IpAddress 对象](#IpAddress) |
|  osType | 枚举 | 是 | 容器组中容器所需的操作系统类型。 - Windows 或 Linux |
|  volumes | array | 否 | 此容器组中的容器可以装载的卷列表。 - [Volume 对象](#Volume) |
|  诊断 | object | 否 | 容器组的诊断信息。 - [ContainerGroupDiagnostics 对象](#ContainerGroupDiagnostics) |
|  networkProfile | object | 否 | 容器组的网络配置文件信息。 - [ContainerGroupNetworkProfile 对象](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | 否 | 容器组的 DNS 配置信息。 - [DnsConfiguration 对象](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Container 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | 是 | 用户提供的容器实例名称。 |
|  properties | object | 是 | 容器实例的属性。 - [ContainerProperties 对象](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  server | string | 是 | 没有协议（例如“http”或“https”）的 Docker 映像注册表服务器。 |
|  username | string | 是 | 专用注册表的用户名。 |
|  password | string | 否 | 专用注册表的密码。 |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | 是 | 容器组上公开的端口列表。 - [Port 对象](#Port) |
|  type | 枚举 | 是 | 指定 IP 是否向公共 Internet 或专用 VNET 公开。 - Public 或 Private |
|  ip | string | 否 | 向公共 Internet 公开的 IP。 |
|  dnsNameLabel | string | 否 | IP 的 DNS 名称标签。 |


<a id="Volume" />

### <a name="volume-object"></a>Volume 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | 是 | 卷的名称。 |
|  azureFile | object | 否 | Azure 文件卷。 - [AzureFileVolume 对象](#AzureFileVolume) |
|  emptyDir | object | 否 | 空目录卷。 |
|  secret | object | 否 | 机密卷。 |
|  gitRepo | object | 否 | Git 存储库卷。 - [GitRepoVolume 对象](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | 否 | 容器组日志分析信息。 - [LogAnalytics 对象](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  id | string | 是 | 网络配置文件的标识符。 |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  nameServers | array | 是 | 容器组的 DNS 服务器。 - string |
|  searchDomains | string | 否 | 容器组中主机名查找的 DNS 搜索域。 |
|  options | string | 否 | 容器组的 DNS 选项。 |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  图像 | string | 是 | 用于创建容器实例的映像的名称。 |
|  command | array | 否 | 要在容器实例中执行的 exec 形式的命令。 - string |
|  ports | array | 否 | 容器实例上的已公开端口。 - [ContainerPort 对象](#ContainerPort) |
|  environmentVariables | array | 否 | 要在容器实例中设置的环境变量。 - [EnvironmentVariable 对象](#EnvironmentVariable) |
|  resources | object | 是 | 容器实例的资源要求。 - [ResourceRequirements 对象](#ResourceRequirements) |
|  volumeMounts | array | 否 | 容器实例可用的卷装入点数。 - [VolumeMount 对象](#VolumeMount) |
|  livenessProbe | object | 否 | 运行情况探测。 - [ContainerProbe 对象](#ContainerProbe) |
|  readinessProbe | object | 否 | 就绪情况探测。 - [ContainerProbe 对象](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Port 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  协议 | 枚举 | 否 | 与端口关联的协议。 - TCP 或 UDP |
|  port | integer | 是 | 端口号。 |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | string | 是 | 要以卷形式装载的 Azure 文件共享的名称。 |
|  readOnly | boolean | 否 | 指示以卷形式装载的 Azure 文件共享是否为只读的标志。 |
|  storageAccountName | string | 是 | 包含 Azure 文件共享的存储帐户的名称。 |
|  storageAccountKey | string | 否 | 用于访问 Azure 文件共享的存储帐户访问密钥。 |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  目录 | string | 否 | 目标目录名称。 不得包含“..”，也不能以其开头。  如果提供了“.”，则卷目录将是 Git 存储库。  否则，卷将包含子目录中具有给定名称的 Git 存储库（如果已指定）。 |
|  repository | string | 是 | 存储库 URL |
|  revision | string | 否 | 指定的修订版的提交哈希。 |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | 是 | Log Analytics 的工作区 ID |
|  workspaceKey | string | 是 | Log Analytics 的工作区键 |
|  logType | 枚举 | 否 | 要使用的日志类型。 - ContainerInsights 或 ContainerInstanceLogs |
|  metadata | object | 否 | Log Analytics 的元数据。 |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  协议 | 枚举 | 否 | 与端口关联的协议。 - TCP 或 UDP |
|  port | integer | 是 | 容器组中公开的端口号。 |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>EnvironmentVariable 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | 是 | 环境变量的名称。 |
|  value | string | 否 | 环境变量的值。 |
|  secureValue | string | 否 | 安全环境变量的值。 |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  请求 | object | 是 | 此容器实例的资源请求。 - [ResourceRequests 对象](#ResourceRequests) |
|  限制 | object | 否 | 此容器实例的资源限制。 - [ResourceLimits 对象](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  name | string | 是 | 卷装入点的名称。 |
|  mountPath | string | 是 | 容器中要将该卷装载到的路径。 不得包含冒号 (:)。 |
|  readOnly | boolean | 否 | 指示卷装入点是否为只读的标志。 |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  exec | object | 否 | 用于探测的执行命令 - [ContainerExec 对象](#ContainerExec) |
|  httpGet | object | 否 | 用于探测的 Http Get 设置 - [ContainerHttpGet 对象](#ContainerHttpGet) |
|  initialDelaySeconds | integer | 否 | 初始延迟秒数。 |
|  periodSeconds | integer | 否 | 时间段秒数。 |
|  failureThreshold | integer | 否 | 失败阈值。 |
|  successThreshold | integer | 否 | 成功阈值。 |
|  timeoutSeconds | integer | 否 | 超时秒数。 |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 是 | 此容器实例的内存请求 (GB)。 |
|  cpu | number | 是 | 此容器实例的 CPU 请求。 |
|  gpu | object | 否 | 此容器实例的 GPU 请求。 - [GpuResource 对象](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 否 | 此容器实例的内存限制 (GB)。 |
|  cpu | number | 否 | 此容器实例的 CPU 限制。 |
|  gpu | object | 否 | 此容器实例的 GPU 限制。 - [GpuResource 对象](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  command | array | 否 | 要在容器中执行的命令。 - string |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  path | string | 否 | 要探测的路径。 |
|  port | integer | 是 | 要探测的端口号。 |
|  scheme | 枚举 | 否 | 方案。 - http 或 https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource 对象

|  名称 | 类型 | 必须 | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | 是 | GPU 资源计数。 |
|  sku | 枚举 | 是 | GPU 资源的 SKU。 - K80、P100、V100 |


## <a name="next-steps"></a>后续步骤

参阅教程[使用 YAML 文件部署多容器组](container-instances-multi-container-yaml.md)。

参阅使用 YAML 文件在[虚拟网络](container-instances-vnet.md)中部署容器组或[装载外部卷](container-instances-volume-azure-files.md)的示例。

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

