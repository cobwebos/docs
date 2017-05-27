1. 使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 有关登录的详细信息，请参阅 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli)。

  ```azurecli
  az login
  ```
2. 如果有多个 Azure 订阅，请列出该帐户的订阅。

  ```azurecli
  az account list --all
  ```
3. 指定要使用的订阅。

  ```azurecli
  az account set --subscription <replace_with_your_subscription_id>
  ```