# <a name="scale-agent-nodes-in-a-container-service-cluster"></a>在容器服务群集中缩放代理节点
[部署 Azure 容器服务群集](../articles/container-service/dcos-swarm/container-service-deployment.md)后，可能需要更改代理节点数。 例如，可能需要更多代理，以便运行更多容器应用程序或实例。 

可以使用 Azure 门户或 Azure CLI 2.0（预览版）更改 DC/OS、Docker Swarm 或 Kubernetes 群集中的代理节点数。 

## <a name="scale-with-the-azure-portal"></a>使用 Azure 门户缩放

1. 在 [Azure 门户](https://portal.azure.com)中，浏览“容器服务”，并单击要修改的容器服务。
2. 在“容器服务”边栏选项卡中，单击“代理”。
3. 在“VM 计数”中，输入所需的代理节点数。

    ![在门户中缩放池](./media/container-service-scale/container-service-scale-portal.png)

4. 若要保存配置，请单击“保存”。

## <a name="scale-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 缩放

确保[已安装](/cli/azure/install-az-cli2)最新的 Azure CLI 2.0 并已登录到 Azure 帐户 (`az login`)。

### <a name="see-the-current-agent-count"></a>查看当前代理计数
若要查看群集中当前的代理数，请运行 `az acs show` 命令。 这会显示群集配置。 例如，以下命令显示资源组 `myResourceGroup` 中名为 `containerservice-myACSName` 的容器服务的配置：

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

该命令将返回 `AgentPoolProfiles` 下 `Count` 值中的代理数。

### <a name="use-the-az-acs-scale-command"></a>使用 az acs scale 命令
若要更改代理节点数，请运行 `az acs scale` 命令，并提供**资源组**、**容器服务名称**以及所需的**新代理计数**。 使用更小或更大的数字，可相应地减少或增加。

例如，要将前面的群集中的代理数更改为 10，请键入以下命令：

```azurecli
azure acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

Azure CLI 2.0 将返回一个表示容器服务新配置的 JSON 字符串，其中包括新的代理计数。

有关更多命令选项，请运行 `az acs scale --help`。

## <a name="scaling-considerations"></a>缩放注意事项

* 代理节点数必须介于 1 到 100（含）之间。 

* 核心配额可以限制群集中的代理节点数。

* 代理节点缩放操作将应用于包含代理池的 Azure 虚拟机规模集。 在 DC/OS 群集中，仅专用池的代理节点由本文中所示的操作进行缩放。

* 根据在群集中部署的 orchestrator，可以单独缩放群集上运行的容器的实例数。 例如，在 DC/OS 群集中，可使用 [Marathon UI](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) 更改容器应用程序的实例数。

* 目前，不支持自动缩放容器服务群集中的代理节点。

## <a name="next-steps"></a>后续步骤
* 请参阅[更多示例](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)，了解如何将 Azure CLI 2.0 命令用于 Azure 容器服务。
* 详细了解 Azure 容器服务中的 [DC/OS 代理池](../articles/container-service/dcos-swarm/container-service-dcos-agents.md)。

