>[!NOTE]
> 你可以在此页上的反馈或通过将注释[Azure 反馈](https://feedback.azure.com/forums/216843-virtual-machines)#azerrormessage 标记。

## <a name="error-response-format"></a>错误响应格式 
Azure Vm 使用以下 JSON 格式的错误响应：

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

错误响应始终包含状态代码和对象时出错。 每个错误对象始终包含错误代码和一条消息。 如果使用模板创建的 VM，错误对象还包含一个包含错误代码和消息的一个内部级别的详细信息节。 通常情况下，错误消息的最内部级别是根失败。


## <a name="common-virtual-machine-management-errors"></a>常见的虚拟机管理错误

本部分列出管理 Vm 时可能遇到的常见错误消息：

|  错误代码  |  错误消息  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  未能创建磁盘"{0}"与 URI {1} 使用 blob 时获取租约。 Blob 已在使用中。  |  
|  AllocationFailed  |  分配失败。 请尝试减少 VM 大小或 Vm 数量，稍后重试，或尝试部署到不同的可用性集或其他 Azure 位置。  |  
|  AllocationFailed  |  VM 分配因内部错误而失败。 请稍后重试或尝试部署到不同的位置。  |
|  ArtifactNotFound  |  在位置 '{2} 找不到发布服务器"{0}"和类型 {1} 的 VM 扩展。  |
|  ArtifactNotFound  |  扩展与发布服务器"{0}"，键入 {1}，并在扩展存储库中找不到类型处理程序版本 {2}。  |
|  ArtifactVersionNotFound  |  任何版本中不找到项目存储库，它满足请求的版本"{0}"。  |
|  ArtifactVersionNotFound  |  任何版本满足请求的版本"{0}"的项目存储库中找到 VM 扩展与发布服务器 {1} 并键入 {2}。  |
|  AttachDiskWhileBeingDetached  |  无法将数据磁盘"{0}"附加到 VM {1}，因为当前正在分离磁盘。 磁盘已完全分离，请等待，然后重试。  |
|  错误的请求  |  对齐在此区域中尚不支持可用性集。  |
|  错误的请求  |  不支持添加到非托管可用性集的被管理的磁盘的虚拟机或添加到托管可用性集的 blob 基于磁盘的虚拟机。 请创建一个可用性集管理的属性，才能向它添加了被管理的磁盘的 VM 设置。  |
|  错误的请求  |  在此区域中不支持托管的磁盘。  |
|  错误的请求  |  每个处理程序不支持的操作系统的多个 Vmextension 键入"{0}"。 VMExtension {1} 与处理程序 {2} 已添加，或在输入中指定。  |
|  错误的请求  |  在资源 {1} 与被管理的磁盘上不支持操作"{0}"。  |
|  CertificateImproperlyFormatted  |  从 {0} 检索的机密的 JSON 表示形式的数据字段的不是格式正确的 PFX 文件，或提供的密码不正确解码 PFX 文件。  |
|  CertificateImproperlyFormatted  |  从 {0} 检索的数据不可反序列化为 JSON。  |
|  冲突  |  仅在创建 VM 时或当 VM 被释放时，允许调整磁盘大小。  |
|  ConflictingUserInput  |  无法附加磁盘"{0}"，因为磁盘已拥有的 VM {1}。  |
|  ConflictingUserInput  |  源和目标资源组是相同的。  |
|  ConflictingUserInput  |  为磁盘 {0} 的源和目标存储帐户是不同的。  |
|  ContainerAlreadyOnLease  |  保存具有 URI {0} blob 的存储容器上已存在租约。  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  移动资源请求包含这由一个或多个 {0} s 请求中引用的 KeyVault 资源。 这不支持当前在跨订阅移动。 请查看 KeyVault 资源 Id 的错误详细信息。  |
|  DiagnosticsOperationInternalError  |  处理 VM {0} 的诊断配置文件时出现内部错误。  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Blob {0} 已由另一个磁盘属于 VM {1} 使用中。 你可以检查磁盘引用信息的 blob 元数据。  |
|  DiskBlobNotFound  |  找不到磁盘 {1} 与 URI {0} 的 VHD blob。  |
|  DiskBlobNotFound  |  找不到具有 URI {0} 的 VHD blob。  |
|  DiskEncryptionKeySecretMissingTags  |  {0} 密码没有 {1} 标记。 请更新密码版本，添加所需的标记，然后重试。  |
|  DiskEncryptionKeySecretUnwrapFailed  |  解包的使用失败的密钥 {1} 机密 {0} 值。  |
|  DiskImageNotReady  |  磁盘映像 {0} 处于 {1} 状态。 请在映像就绪后，重试。  |
|  DiskPreparationError  |  准备 VM 磁盘时出现一个或多个错误。 请参阅磁盘实例视图的详细信息。  |
|  DiskProcessingError  |  磁盘处理暂停如 VM 在发生故障的磁盘中具有其他磁盘。  |
|  ImageBlobNotFound  |  找不到磁盘 {1} 与 URI {0} 的 VHD blob。  |
|  ImageBlobNotFound  |  找不到具有 URI {0} 的 VHD blob。  |
|  IncorrectDiskBlobType  |  磁盘 blob 仅可以属于类型页 blob。 磁盘 {1} 的 blob {0} 是类型为块 blob。  |
|  IncorrectDiskBlobType  |  磁盘 blob 仅可以属于类型页 blob。 Blob {0} 属于类型 {1}。  |
|  IncorrectImageBlobType  |  磁盘 blob 仅可以属于类型页 blob。 磁盘 {1} 的 blob {0} 是类型为块 blob。  |
|  IncorrectImageBlobType  |  磁盘 blob 仅可以属于类型页 blob。 Blob {0} 属于类型 {1}。  |
|  InternalOperationError  |  无法解析存储帐户 {0}。 请确保它通过与计算资源位于同一位置中的存储资源提供程序中创建。  |
|  InternalOperationError  |  {0} 目标搜寻任务失败。  |
|  InternalOperationError  |  验证 VM"{0}"的网络配置文件时出错。  |
|  InvalidAccountType  |  AccountType {0} 无效。  |
|  InvalidParameter  |  参数 {0} 的值无效。  |
|  InvalidParameter  |  不允许指定的管理员密码。  |
|  InvalidParameter  |  "提供的密码必须介于 {0}-\ {1 \} 个字符长时间，并且必须至少满足 {2} 从以下密码复杂性要求： <ol><li> 包含一个大写字符</li><li>包含一个小写字符</li><li>包含一个数字</li><li>包含的特殊字符。</li></ol>  |
|  InvalidParameter  |  不允许指定的管理员用户名。  |
|  InvalidParameter  |  如果从平台或用户映像创建 VM 时，无法附加现有操作系统磁盘。  |
|  InvalidParameter  |  容器名称 {0} 无效。 容器名称必须是 3 到 63 个字符的长度，并且只能包含小写字母数字字符和连字符。 连字符前后必须为和后跟字母数字字符。  |
|  InvalidParameter  |  容器名称 {0} URL {1} 中的无效。 容器名称必须是 3 到 63 个字符的长度，并且只能包含小写字母数字字符和连字符。 连字符前后必须为和后跟字母数字字符。  |
|  InvalidParameter  |  URL {0} 中的 blob 名称包含一个斜杠。 这是目前不支持磁盘。  |
|  InvalidParameter  |  URI {0} 似乎是正确的 blob URI。  |
|  InvalidParameter  |  名为"{0}"已的磁盘使用相同的 LUN: {1}。  |
|  InvalidParameter  |  磁盘名为"{0}"已存在。  |
|  InvalidParameter  |  不能指定用户映像替代已在指定的映像引用中定义的磁盘。  |
|  InvalidParameter  |  名为"{0}"已的磁盘使用相同的 VHD URL {1}。  |
|  InvalidParameter  |  指定的容错域计数 {0} 必须介于范围 {1} 中至 {2}。  |
|  InvalidParameter  |  许可证类型 {0} 无效。 有效的许可证类型为： Windows_Client 或 Windows_Server，区分大小写。  |
|  InvalidParameter  |  Linux 主机名不能超过 {0} 个字符的长度或包含以下字符： {1}。  |
|  InvalidParameter  |  Ssh 公钥的目标路径是在 Linux 预配代理当前限制为其默认值 {0} 由于已知问题。  |
|  InvalidParameter  |  LUN {0} 处的磁盘已存在。  |
|  InvalidParameter  |  请求的订阅 {0} 必须与托管的磁盘 id 中包含订阅 {1} 匹配。  |
|  InvalidParameter  |  OSProfile 中的自定义数据必须采用 Base64 编码，{0} 个字符的最大长度。  |
|  InvalidParameter  |  URL {0} 中的 blob 名称必须以 {1} 扩展名结尾。  |
|  InvalidParameter  |  {0} 不是有效的捕获的 VHD blob 名称前缀。 有效的前缀匹配正则表达式 {1}。  |
|  InvalidParameter  |  如果未设置 VM 代理，则无法将证书添加到你的 VM。  |
|  InvalidParameter  |  LUN {0} 处的磁盘已存在。  |
|  InvalidParameter  |  无法创建 VM 因为请求的大小 {0} 不可用群集的可用性集的位置中当前分配。 提供的大小为： {1}。 读取多个在 VM 重设大小在 https://aka.ms/azure-resizevm 的策略。  |
|  InvalidParameter  |  请求的 VM 大小 {0} 当前区域中不可用。 当前的区域中可用的大小为： {1}。 了解更多的可用 VM 大小 https://aka.ms/azure-regions 在每个区域中。  |
|  InvalidParameter  |  请求的 VM 大小 {0} 当前区域中不可用。 了解更多的可用 VM 大小 https://aka.ms/azure-regions 在每个区域中。  |
|  InvalidParameter  |  Windows 管理员用户名不能超过 {0} 个字符长，以句点 （.） 结尾或包含以下字符： {1}。  |
|  InvalidParameter  |  Windows 计算机名不能超过 {0} 个字符长，能全为数字，或包含以下字符： {1}。  |
|  MissingMoveDependentResources  |  移动资源请求不包含所有从属资源。 请为缺少的资源 id 的错误详细信息。  |
|  MoveResourcesHaveInvalidState  |  将资源移动请求包含无效的存储帐户与关联的 Vm。 请检查这些资源 id 和引用的存储帐户名称的详细信息。  |
|  MoveResourcesHavePendingOperations  |  移动资源请求包含为其操作处于挂起状态的资源。 请检查这些资源 id 的详细信息。 挂起的操作完成后请重试操作。  |
|  MoveResourcesNotFound  |  移动资源请求包含找不到的资源。 请检查这些资源 id 的详细信息。  |
|  NetworkingInternalOperationError  |  未知的网络分配错误。  |
|  NetworkingInternalOperationError  |  未知的网络分配错误  |
|  NetworkingInternalOperationError  |  处理 VM 的网络配置文件中出现内部错误。  |
|  未找到  |  找不到可用性集 {0}。  |
|  未找到  |  源虚拟机"{0}"请求中指定不存在于此 Azure 位置。  |
|  未找到  |  找不到 id 为 {0} 租户。  |
|  未找到  |  找不到映像 {0}。  |
|  NotSupported  |  许可证类型是 {0}，但映像 blob {1} 不从本地。  |
|  OperationNotAllowed  |  无法删除可用性集 {0}。 在删除一个可用性集之前请确保它不包含任何 VM。  |
|  OperationNotAllowed  |  不允许更改可用性集对齐从 'Classic' 到的 SKU。  |
|  OperationNotAllowed  |  VM 未运行时，无法修改 VM 中的扩展。  |
|  OperationNotAllowed  |  具有 blob 基于磁盘的虚拟机仅支持捕获操作。 请使用图像资源 Api 从托管的虚拟机创建映像。  |
|  OperationNotAllowed  |  无法从映像 {1} 中创建资源 {0}，直到成功创建映像。  |
|  OperationNotAllowed  |  分配 VM，请重试后 VM 被释放时，不允许更新 encryptionsettings  |
|  OperationNotAllowed  |  不支持添加到 VM 具有 blob 基于磁盘的托管磁盘。  |
|  OperationNotAllowed  |  最大允许要附加到此大小的 VM 的数据磁盘数为 {0}。  |
|  OperationNotAllowed  |  不支持添加到 VM 与被管理的磁盘基于 blob 的磁盘。  |
|  OperationNotAllowed  |  在映像 {1} 上不允许执行操作"{0}"，因为映像标记为删除。 你可以仅重试删除操作 （或等待完成正在进行一个）。  |
|  OperationNotAllowed  |  VM {1} 上不允许执行操作"{0}"，因为 VM 已通用化。  |
|  OperationNotAllowed  |  不允许操作"{0}"，因为还原点集合 {1} 被标记为删除。  |
|  OperationNotAllowed  |  有关 VM 扩展 {1} 不允许执行操作"{0}"，因为它被标记为删除。 你可以仅重试删除操作 （或等待完成正在进行一个）。  |
|  OperationNotAllowed  |  不允许执行操作"{0}"，因为正在使用的映像 {2} 设置虚拟机 {1}。  |
|  OperationNotAllowed  |  由于虚拟机 ScaleSet {1} 当前正在使用的映像 {2} 不允许操作"{0}"。  |
|  OperationNotAllowed  |  VM {1} 上不允许执行操作"{0}"，因为 VM 标记为删除。 你可以仅重试删除操作 （或等待完成正在进行一个）。  |
|  OperationNotAllowed  |  VM {1} 上不允许执行操作"{0}"，因为取消分配或标记为已取消分配 VM。  |
|  OperationNotAllowed  |  VM {1} 上不允许执行操作"{0}"，因为 VM 正在运行。 请电源关闭显式以防你关闭 VM 从来宾操作系统中。  |
|  OperationNotAllowed  |  VM {1} 上不允许执行操作"{0}"，因为 VM 未释放。  |
|  OperationNotAllowed  |  VM {1} 上不允许执行操作"{0}"，因为 VM 具有扩展名 {2} 处于失败状态。  |
|  OperationNotAllowed  |  VM {1} 上不允许执行操作"{0}"，因为另一个操作正在进行。  |
|  OperationNotAllowed  |  操作"{0}"需要虚拟机 {1} 若要将映像一般化。  |
|  OperationNotAllowed  |  该操作需要 VM 运行 （或设置为运行）。  |
|  OperationNotAllowed  |  磁盘大小 {0} GB，这是小于的大小不允许 {1}GB 在图中，相应的磁盘。  |
|  OperationNotAllowed  |  只有在创建 VM 规模集时，可以添加处理程序"{0}"的 VM 规模集扩展。  |
|  OperationNotAllowed  |  只有在删除 VM 规模集时，可以删除处理程序"{0}"的 VM 规模集扩展。  |
|  OperationNotAllowed  |  VM"{0}"已在使用被管理的磁盘。  |
|  OperationNotAllowed  |  VM"{0}"属于经典可用性集 {1}。 请更新可用性集使用对齐 SKU，然后重试转换。  |
|  OperationNotAllowed  |  从映像创建虚拟机不能具有 blob 基于磁盘。 所有磁盘都具有要被管理的磁盘。  |
|  OperationNotAllowed  |  捕获无法完成操作，因为 VM 未通用化。  |
|  OperationNotAllowed  |  因为 VM 磁盘正在转换为被管理的磁盘，不允许在虚拟机"{0}"上的管理操作。  |
|  OperationNotAllowed  |  正在进行的操作将虚拟机 {0} 的电源状态更改为 {1}。 请一段时间后执行操作 {2}。  |
|  OperationNotAllowed  |  无法添加或更新 VM。 请求的 VM 大小 {0} 可能不可用现有的分配单元中。 读取多个在 VM 重设大小在 https://aka.ms/azure-resizevm 的策略。  |
|  OperationNotAllowed  |  无法调整大小 VM 因为请求的大小 {0} 不可用群集的可用性集的位置中当前分配。 提供的大小为： {1}。 读取多个在 VM 重设大小在 https://aka.ms/azure-resizevm 的策略。  |
|  OperationNotAllowed  |  无法调整 VM 的大小，因为请求的大小 {0} 不是当前所在 VM 群集中可用。 若要调整大小到 {1} VM 请释放 （这是在 Azure 门户的停止操作），然后重试调整大小操作。 读取多个在 VM 重设大小在 https://aka.ms/azure-resizevm 的策略。  |
|  OSProvisioningClientError  |  由于当前正在配置来宾操作系统，操作系统预配的虚拟机"{0}"失败。  |
|  OSProvisioningClientError  |  操作系统预配虚拟机"{0}"失败。 错误详细信息： {1} 请确保正确准备镜像 （通用化）。 <ul><li>适用于 Windows 的说明： https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH 主机密钥生成失败。 错误详细信息: {0}。 若要解决此问题，请验证 Linux 代理已在正确设置。 <ul><li>你可以检查的说明： https://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/ </li></ul> |
|  OSProvisioningClientError  |  为 VM 指定的用户名不适合此 Linux 分发。 错误详细信息: {0}。  |
|  OSProvisioningInternalError  |  操作系统预配虚拟机"{0}"由于内部错误失败。  |
|  OSProvisioningTimedOut  |  操作系统预配虚拟机"{0}"未完成在分配的时间。 VM 可能仍成功完成预配。 请稍后检查预配状态。  |
|  OSProvisioningTimedOut  |  操作系统预配虚拟机"{0}"未完成在分配的时间。 VM 可能仍成功完成预配。 请稍后检查预配状态。 此外，请确保正确准备镜像 （通用化）。   <ul><li>适用于 Windows 的说明： https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> 适用于 Linux 的说明： https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  操作系统预配虚拟机"{0}"未完成在分配的时间。 但是，检测到 VM 来宾代理中运行。 这表明尚未正确准备好使用 VM 映像作为来宾操作系统 （CreateOption = FromImage）。 若要解决此问题，请使用 VHD 一点与 CreateOption = 附加或准备将其正确作为映像：   <ul><li>适用于 Windows 的说明： https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> 适用于 Linux 的说明： https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  所需的 VM 大小当前不可用在选定位置。  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  无法更新资源，因为正在进行的平台更新。 请稍后重试。  |
|  StorageAccountLimitation  |  存储帐户"{0}"不支持创建磁盘所需的页 blob。  |
|  StorageAccountLimitation  |  存储帐户"{0}"已超过其已分配的配额。  |
|  StorageAccountLocationMismatch  |  无法解析存储帐户 {0}。 请确保它通过与计算资源位于同一位置中的存储资源提供程序中创建。  |
|  StorageAccountNotFound  |  找不到存储帐户 {0}。 确保存储帐户不会删除，并且属于与 VM 相同的 Azure 位置。  |
|  StorageAccountNotRecognized  |  请使用存储资源提供程序托管的存储帐户。 不支持使用 {0}。  |
|  StorageAccountOperationInternalError  |  访问存储帐户 {0} 时发生内部错误。  |
|  StorageAccountSubscriptionMismatch  |  存储帐户 {0} 不属于订阅 {1}。  |
|  StorageAccountTooBusy  |  存储帐户"{0}"是当前太忙。 请考虑使用另一个帐户。  |
|  StorageAccountTypeNotSupported  |  磁盘 {0} 使用 {1} 即 Blob 存储帐户。 请使用常规用途存储帐户重试。  |
|  StorageAccountTypeNotSupported  |  存储帐户 {0} 是 {1} 类型。 启动诊断支持 {2} 存储帐户类型。  |
|  SubscriptionNotAuthorizedForImage  |  未授权的订阅。  |
|  TargetDiskBlobAlreadyExists  |  Blob {0} 已存在。 请提供其他 blob URI 来创建新的空数据磁盘 {1}。  |
|  TargetDiskBlobAlreadyExists  |  捕获操作无法继续，因为目标映像 blob {0} 已存在并且未设置覆盖 VHD blob 的标志。 请删除 blob，或设置标志以覆盖 VHD blob，然后重试。  |
|  TargetDiskBlobAlreadyExists  |  捕获操作无法继续，因为目标映像 blob {0} 对其具有活动租约。   |
|  TargetDiskBlobAlreadyExists  |  Blob {0} 已存在。 请提供不同的 blob URI 作为目标磁盘 {1}。  |
|  TooManyVMRedeploymentRequests  |  VM"{0}"或同一可用性集中中与此 VM 的 Vm 收到过多重新部署请求。 请稍后重试。  |
|  VHDSizeInvalid  |  磁盘 {1} 与 blob {2} {0} 指定的磁盘大小值无效。 磁盘大小必须介于 {3} 和 \ {4 之间。  |
|  VMAgentStatusCommunicationError  |  VM"{0}"尚未报告 VM 代理或扩展的状态。 请验证 VM 正在运行的 VM 代理，并且还可以建立出站连接到 Azure 存储空间。  |
|  VMArtifactRepositoryInternalError  |  与要检索 VM 项目详细信息的项目存储库通信时出错。  |
|  VMArtifactRepositoryInternalError  |  从项目存储库检索 VM 项目数据时发生内部错误。  |
|  VMExtensionHandlerNonTransientError  |  处理程序"{0}"已失败的 VM 扩展 {1} 报告出现终端错误代码 {2} 和错误消息: {3}  |
|  VMExtensionManagementInternalError  |  处理 VM 扩展"{0}"时出现内部错误。  |
|  VMExtensionManagementInternalError  |  准备 VM 扩展时出现多个错误。 请参阅 VM 扩展实例视图的详细信息。  |
|  VMExtensionProvisioningError  |  处理扩展"{0}"时，VM 已报告了失败。 错误消息:"{1}"。  |
|  VMExtensionProvisioningError  |  未能在 VM 上设置多个 VM 扩展。 请参阅 VM 扩展实例视图的详细信息。  |
|  VMExtensionProvisioningTimeout  |  预配 VM 扩展"{0}"已超时。 扩展安装可能耗时太长，或无法获得扩展状态。  |
|  VMMarketplaceInvalidInput  |  从非应用商店映像创建虚拟机不需要计划信息，请在请求中删除的计划信息。 OS 磁盘名称为 {0}。  |
|  VMMarketplaceInvalidInput  |  购买信息不匹配。 无法从应用商店映像部署。 OS 磁盘名称为 {0}。  |
|  VMMarketplaceInvalidInput  |  从应用商店映像创建虚拟机需要在请求中的计划信息。 OS 磁盘名称为 {0}。  |
|  VMNotFound  |  找不到虚拟机"{0}"。  |
|  VMRedeploymentFailed  |  VM"{0}"重新部署失败，因为发生内部错误。 请稍后重试。  |
|  VMRedeploymentTimedOut  |  虚拟机"{0}"的重新部署未在分配的时间内完成。 它可能成功完成在一段时间。 否则，你可以重试请求。  |
|  VMStartTimedOut  |  VM"{0}"未在分配的时间启动。 VM 仍可能成功启动。 请稍后检查电源状态。  |


## <a name="next-steps"></a>后续步骤
如果需要更多帮助，你可以上联系的 Azure 专家[MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)。 或者，你可以申请提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)和选择**获取支持**。