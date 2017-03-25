## <a name="set-up-azure-cli-for-azure-dns"></a>设置适用于 Azure DNS 的 Azure CLI

### <a name="before-you-begin"></a>开始之前

在开始配置之前，请确认你具有以下各项。

* Azure 订阅。 如果你还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 安装最新版本的 Azure CLI（在 Windows、Linux 或 MAC 中可用）。 有关详细信息，请参阅[安装 Azure CLI](../articles/cli-install-nodejs.md)。

### <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

打开控制台窗口并使用你的凭据进行身份验证。 有关详细信息，请阅读[从 Azure CLI 登录 Azure](../articles/xplat-cli-connect.md)

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>切换 CLI 模式

Azure DNS 使用 Azure Resource Manager。 确保切换 CLI 模式以使用 Azure Resource Manager 命令。

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>选择订阅

检查该帐户的订阅。

```azurecli
azure account list
```

选择要使用的 Azure 订阅。

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>创建资源组

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 但是，由于所有 DNS 资源都是全局性而非区域性的，因此资源组位置的选择不会影响 Azure DNS。

如果使用现有资源组，可跳过此步骤。

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>注册资源提供程序

Azure DNS 服务由 Microsoft.Network 资源提供程序管理。 使用 Azure DNS 前，必须将 Azure 订阅注册为使用此资源提供程序。 对每个订阅而言，这都是一次性操作。

```azurecli
azure provider register --namespace Microsoft.Network
```

