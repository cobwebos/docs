---
title: 容器组的 YAML 引用
description: 引用 Azure 容器实例支持的 YAML 文件以配置容器组
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896566"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML 引用： Azure 容器实例

本文介绍 Azure 容器实例支持的 YAML 文件的语法和属性以配置[容器组](container-instances-container-groups.md)。 使用 YAML 文件将组配置输入到 Azure CLI 中的[az container create][az-container-create]命令。 

YAML 文件是为可重复部署配置容器组的一种简便方法。 这是使用[资源管理器模板](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups)或 Azure 容器实例 sdk 创建或更新容器组的一种简单方法。

> [!NOTE]
> 此引用适用于 REST API 版本 `2018-10-01`的 Azure 容器实例的 YAML 文件。

## <a name="schema"></a>架构 

下面是 YAML 文件的架构，其中包括突出显示键属性的注释。 有关此架构中的属性的说明，请参阅[属性值](#property-values)部分。

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

### <a name="microsoftcontainerinstancecontainergroups-object"></a>ContainerInstance/containerGroups 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  name | 字符串 | 是 | 容器组的名称。 |
|  apiVersion | 枚举 | 是 | 2018-10-01 |
|  位置 | 字符串 | No | 资源位置。 |
|  标记 | 对象 | No | 资源标记。 |
|  标识 | 对象 | No | 容器组的标识（如果已配置）。 - [ContainerGroupIdentity 对象](#ContainerGroupIdentity) |
|  属性 | 对象 | 是 | [ContainerGroupProperties 对象](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  type | 枚举 | No | 容器组所使用的标识的类型。 类型 "SystemAssigned，UserAssigned" 同时包含隐式创建的标识和一组用户分配的标识。 类型 "None" 将删除容器组中的任何标识。 -SystemAssigned、UserAssigned、SystemAssigned、UserAssigned、None |
|  userAssignedIdentities | 对象 | No | 与容器组关联的用户标识列表。 用户标识字典键引用将是以下形式的 Azure 资源管理器资源 Id： "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"。 |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  containers | 数组 | 是 | 容器组中的容器。 - [容器对象](#Container) |
|  imageRegistryCredentials | 数组 | No | 从中创建容器组的图像注册表凭据。 - [ImageRegistryCredential 对象](#ImageRegistryCredential) |
|  restartPolicy | 枚举 | No | 重启容器组中所有容器的策略。 - `Always` 始终重启-在失败时重新启动 `OnFailure`-`Never` 从不重新启动。 -Always、OnFailure、Never |
|  ipAddress | 对象 | No | 容器组的 IP 地址类型。 - [IpAddress 对象](#IpAddress) |
|  osType | 枚举 | 是 | 容器组中的容器所需的操作系统类型。 -Windows 或 Linux |
|  volumes | 数组 | No | 此容器组中的容器可以装载的卷的列表。 - [卷对象](#Volume) |
|  诊断 | 对象 | No | 容器组的诊断信息。 - [ContainerGroupDiagnostics 对象](#ContainerGroupDiagnostics) |
|  NetworkProfile | 对象 | No | 容器组的网络配置文件信息。 - [ContainerGroupNetworkProfile 对象](#ContainerGroupNetworkProfile) |
|  dnsConfig | 对象 | No | 容器组的 DNS 配置信息。 - [DnsConfiguration 对象](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>容器对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  name | 字符串 | 是 | 用户为容器实例提供的名称。 |
|  属性 | 对象 | 是 | 容器实例的属性。 - [ContainerProperties 对象](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  服务器 | 字符串 | 是 | 没有协议的 Docker 映像注册表服务器，如 "http" 和 "https"。 |
|  username | 字符串 | 是 | 专用注册表的用户名。 |
|  password | 字符串 | No | 专用注册表的密码。 |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  ports | 数组 | 是 | 容器组中公开的端口列表。 - [端口对象](#Port) |
|  type | 枚举 | 是 | 指定是否向公共 internet 或专用 VNET 公开 IP。 -Public 或 Private |
|  ip | 字符串 | No | 公开给公共 internet 的 IP。 |
|  dnsNameLabel | 字符串 | No | IP 的 Dns 名称标签。 |


<a id="Volume" />

### <a name="volume-object"></a>卷对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  name | 字符串 | 是 | 卷的名称。 |
|  azureFile | 对象 | No | Azure 文件卷。 - [AzureFileVolume 对象](#AzureFileVolume) |
|  emptyDir | 对象 | No | 空目录卷。 |
|  secret | 对象 | No | 机密卷。 |
|  gitRepo | 对象 | No | Git 存储库卷。 - [GitRepoVolume 对象](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | 对象 | No | 容器组日志分析信息。 - [LogAnalytics 对象](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  id | 字符串 | 是 | 网络配置文件的标识符。 |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  名称服务器 | 数组 | 是 | 容器组的 DNS 服务器。 -string |
|  searchDomains | 字符串 | No | 容器组中用于主机名查找的 DNS 搜索域。 |
|  options | 字符串 | No | 容器组的 DNS 选项。 |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  image | 字符串 | 是 | 用于创建容器实例的图像的名称。 |
|  command | 数组 | No | 要在容器实例中以 exec 形式执行的命令。 -string |
|  ports | 数组 | No | 容器实例上的已公开端口。 - [因此 containerport 对象](#ContainerPort) |
|  environmentVariables | 数组 | No | 要在容器实例中设置的环境变量。 - [value 对象](#EnvironmentVariable) |
|  资源 | 对象 | 是 | 容器实例的资源要求。 - [ResourceRequirements 对象](#ResourceRequirements) |
|  volumeMounts | 数组 | No | 容器实例可用的卷装入。 - [VolumeMount 对象](#VolumeMount) |
|  livenessProbe | 对象 | No | 活动探测。 - [ContainerProbe 对象](#ContainerProbe) |
|  readinessProbe | 对象 | No | 准备情况探测。 - [ContainerProbe 对象](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>端口对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  协议 | 枚举 | No | 与端口关联的协议。 -TCP 或 UDP |
|  port | integer | 是 | 端口号。 |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  shareName | 字符串 | 是 | 要作为卷装载的 Azure 文件共享的名称。 |
|  readOnly | boolean | No | 指示作为卷装载的 Azure 文件是否是只读的。 |
|  storageAccountName | 字符串 | 是 | 包含 Azure 文件共享的存储帐户的名称。 |
|  storageAccountKey | 字符串 | No | 用于访问 Azure 文件共享的存储帐户访问密钥。 |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  目录 | 字符串 | No | 目标目录名称。 不得包含或以 ".." 开头。  如果提供了 "."，则卷目录将是 git 存储库。  否则，如果指定了此项，则该卷将在子目录中包含具有给定名称的 git 存储库。 |
|  repository | 字符串 | 是 | 存储库 URL |
|  revision | 字符串 | No | 指定修订的提交哈希。 |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | 字符串 | 是 | Log analytics 的工作区 id |
|  workspaceKey | 字符串 | 是 | Log analytics 的工作区密钥 |
|  logType | 枚举 | No | 要使用的日志类型。 -ContainerInsights 或 ContainerInstanceLogs |
|  metadata | 对象 | No | Log analytics 的元数据。 |


<a id="ContainerPort" />

### <a name="containerport-object"></a>因此 containerport 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  协议 | 枚举 | No | 与端口关联的协议。 -TCP 或 UDP |
|  port | integer | 是 | 在容器组中公开的端口号。 |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Value 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  name | 字符串 | 是 | 环境变量的名称。 |
|  值 | 字符串 | No | 环境变量的值。 |
|  secureValue | 字符串 | No | 安全环境变量的值。 |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  请求 | 对象 | 是 | 此容器实例的资源请求。 - [ResourceRequests 对象](#ResourceRequests) |
|  限制 | 对象 | No | 此容器实例的资源限制。 - [ResourceLimits 对象](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  name | 字符串 | 是 | 卷装入的名称。 |
|  mountPath | 字符串 | 是 | 容器中应装入卷的路径。 不得包含冒号（:)。 |
|  readOnly | boolean | No | 指示卷装载是否为只读的标志。 |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  exec | 对象 | No | [ContainerExec 对象](#ContainerExec)的执行命令 |
|  httpGet | 对象 | No | [ContainerHttpGet 对象](#ContainerHttpGet)的 Http Get 设置 |
|  initialDelaySeconds | integer | No | 初始延迟秒数。 |
|  periodSeconds | integer | No | 时间段秒。 |
|  failureThreshold | integer | No | 失败阈值。 |
|  successThreshold | integer | No | 成功阈值。 |
|  timeoutSeconds | integer | No | 超时秒数。 |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | 数字 | 是 | 此容器实例的内存请求（GB）。 |
|  CPU | 数字 | 是 | 此容器实例的 CPU 请求。 |
|  GPU | 对象 | No | 此容器实例的 GPU 请求。 - [GpuResource 对象](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | 数字 | No | 此容器实例的内存限制（GB）。 |
|  CPU | 数字 | No | 此容器实例的 CPU 限制。 |
|  GPU | 对象 | No | 此容器实例的 GPU 限制。 - [GpuResource 对象](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  command | 数组 | No | 要在容器中执行的命令。 -string |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  路径 | 字符串 | No | 要探测的路径。 |
|  port | integer | 是 | 要探测的端口号。 |
|  scheme | 枚举 | No | 方案。 -http 或 https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource 对象

|  名称 | Type | 需要 | Value |
|  ---- | ---- | ---- | ---- |
|  count | integer | 是 | GPU 资源的计数。 |
|  sku | 枚举 | 是 | GPU 资源的 SKU。 -K80、P100、V100 |


## <a name="next-steps"></a>后续步骤

请参阅教程[使用 YAML 文件部署多容器组](container-instances-multi-container-yaml.md)。

请参阅使用 YAML 文件在[虚拟网络](container-instances-vnet.md)中部署容器组或[装载外部卷](container-instances-volume-azure-files.md)的示例。

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

