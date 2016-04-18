<!-- not suitable for Mooncake -->

<properties
	pageTitle="自动缩放虚拟机缩放集 | Azure"
	description="开始使用 Azure PowerShell 创建和管理你的第一个 Azure 虚拟机缩放集"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="01/05/2016"
	wacn.date=""/>

# 自动缩放虚拟机缩放集中的虚拟机

> [AZURE.SELECTOR]
- [Azure CLI](/documentation/articles/virtual-machines-vmss-walkthrough-cli)
- [Azure PowerShell](/documentation/articles/virtual-machines-vmss-walkthrough)

<br>

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-rm-include.md)]经典部署模型。

使用虚拟机缩放集可以轻松地将相同的虚拟机作为集来进行部署和管理。缩放集为超大规模应用程序提供高度可缩放且可自定义的计算层，并且它们支持 Windows 平台映像、Linux 平台映像、自定义映像和扩展。有关缩放集的详细信息，请参阅[虚拟机缩放集](/documentation/articles/virtual-machines-vmss-overview)。

本教程演示如何创建 Windows 虚拟机的虚拟机缩放集，并自动缩放集中的虚拟机。为此，将使用 Azure PowerShell 创建 Azure Resource Manager 模板并部署它。有关模板的详细信息，请参阅[创作 Azure 资源管理器模板](/documentation/articles/resource-group-authoring-templates)。

在本教程中生成的模板非常类似于可在模板库中找到的模板。若要了解详细信息，请参阅 [Deploy a simple VM Scale Set with Windows VMs and a Jumpbox（使用 Windows VM 和 Jumpbox 部署简单的 VM 缩放集）](https://azure.microsoft.com/documentation/templates/201-vmss-windows-jumpbox/)。

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

[AZURE.INCLUDE [virtual-machines-vmss-preview-ps](../includes/virtual-machines-vmss-preview-ps-include.md)]

## 步骤 1：创建资源组和存储帐户

1. “登录到 Azure”。打开 Azure PowerShell 窗口并运行 “Login-AzureRmAccount”。

2. “创建资源组” - 所有资源都必须部署到资源组。对于本教程，将资源组命名为 “vmsstestrg1”。请参阅 [New-AzureRmResourceGroup](https://msdn.microsoft.com/zh-cn/library/mt603739.aspx)。

3. “将存储帐户部署到新的资源组” - 本教程使用多个存储帐户来实现虚拟机缩放集。使用 [New-AzureRmStorageAccount](https://msdn.microsoft.com/zh-cn/library/mt607148.aspx) 创建名为 “vmsstestsa” 的存储帐户。让 Azure PowerShell 窗口保持打开状态以便完成本教程中的后续步骤。

## 步骤 2：创建模板
借助 Azure Resource Manager 模板，你可以使用资源和关联部署参数的 JSON 描述来统一部署和管理 Azure 资源。

1. 在你常用的文本编辑器中，创建文件 C:\\VMSSTemplate.json 并添加初始 JSON 结构，以支持模板。

	```
	{
		"$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
		}
		"variables": {
		}
		"resources": [
		]
	}
	```

2. 参数并不总是必需的，但它们有助于简化模板管理。它们提供了为模板指定值的方法，描述了值的类型、所需的默认值，有时还描述参数的允许值。

	将这些参数添加到已添加到模板中的参数父元素下：

	- 用于访问缩放集中虚拟机的 jumpbox 虚拟机的名称。
	- 存储模板的存储帐户名称。
	- 最初在缩放集中创建的虚拟机实例数。
	- 虚拟机上的管理员帐户的名称和密码。
	- 在资源组中创建的资源的前缀。


	```
	"vmName": {
		"type": "string"
	},
	"vmSSName": {
		"type": "string"
	},
	"instanceCount": {
		"type": "string"
	},
	"adminUsername": {
		"type": "string"
	},
	"adminPassword": {
		"type": "securestring"
	},
	"resourcePrefix": {
		"type": "string"
	}
	```

3. 可以在模板中使用变量指定可能经常发生变化的值或需要通过组合参数值创建的值。

	将这些变量添加到已添加到模板中的变量父元素下：

	- 网络接口所用的 DNS 名称。
	- 在缩放集中使用的虚拟机大小。有关虚拟机大小的详细信息，请参阅[虚拟机的大小](/documentation/articles/virtual-machines-size-specs)。
	- 用于定义将在缩放集中虚拟机上运行的操作系统的平台映像信息。有关选择映像的详细信息，请参阅[使用 Windows PowerShell 和 Azure CLI 来浏览和选择 Azure 虚拟机映像](/documentation/articles/resource-groups-vm-searching)。
	- 虚拟网络和子网的 IP 地址名称和前缀。
	- 虚拟网络、负载平衡器和网络接口的名称和标识符。
	- 与缩放集中虚拟机关联的帐户的存储帐户名称。
	- 已安装在虚拟机上的诊断扩展的设置。有关诊断扩展的详细信息，请参阅[使用 Azure Resource Manager 模板创建具有监视和诊断功能的 Windows 虚拟机](/documentation/articles/virtual-machines-extensions-diagnostics-windows-template)。

	```
	"apiVersion": "2015-06-15"
	"dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
	"dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
	"vmSize": "Standard_A0",
	"imagePublisher": "MicrosoftWindowsServer",
	"imageOffer": "WindowsServer",
	"imageVersion": "2012-R2-Datacenter",
	"addressPrefix": "10.0.0.0/16",
	"subnetName": "Subnet",
	"subnetPrefix": "10.0.0.0/24",
	"publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
	"publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
	"loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
	"virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
	"nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
	"nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	"publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
	"publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
	"lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	"nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
	"frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	"storageAccountType": "Standard_LRS",
	"storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Processor Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU utilization" locale="en-us"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
	```

4. 在本教程中，你将部署以下资源和扩展：

 - Microsoft.Storage/storageAccounts
 - Microsoft.Network/virtualNetworks
 - Microsoft.Network/publicIPAddresses
 - Microsoft.Network/loadBalancers
 - Microsoft.Network/networkInterfaces
 - Microsoft.Compute/virtualMachines
 - Microsoft.Compute/virtualMachineScaleSets
 - Microsoft.Insights.VMDiagnosticsSettings
 - Microsoft.Insights/autoscaleSettings

	有关资源管理器资源的详细信息，请参阅 [Azure Resource Manager 中的 Azure 计算、网络和存储提供程序](/documentation/articles/virtual-machines-azurerm-versus-azuresm)。

	将存储帐户资源添加到已添加到模板中的资源父元素下。此模板使用一个循环来创建建议的 5 个存储帐户，其中将存储操作系统磁盘和诊断数据。这组帐户可在一个缩放集中最多支持 100 个虚拟机，这是当前的最大值。每个存储帐户通过将变量中定义的字母指示符与模板的参数中提供的后缀组合来命名。

	```
	{
		"type": "Microsoft.Storage/storageAccounts",
		"name": "[concat(variables('resourcePrefix'), parameters('storageAccountSuffix')[copyIndex()])]",
		"apiVersion": "2015-05-01-preview",
		"copy": {
			"name": "storageLoop",
			"count": 5
		},
		"location": "[resourceGroup().location]",
		"properties": {
			"accountType": "[variables('storageAccountType')]"
		}
	},
	```

5. 添加虚拟网络资源。有关详细信息，请参阅[网络资源提供程序](/documentation/articles/resource-groups-networking)。

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/virtualNetworks",
		"name": "[variables('virtualNetworkName')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"addressSpace": {
				"addressPrefixes": [
					"[variables('addressPrefix')]"
				]
			},
			"subnets": [
				{
					"name": "[variables('subnetName')]",
					"properties": {
						"addressPrefix": "[variables('subnetPrefix')]"
					}
				}
			]
		}
	},
	```

6. 添加负载平衡器和网络接口所使用的公共 IP 地址资源。

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP1')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName1')]"
			}
		}
	},
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP2')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName2')]"
			}
		}
	},
	```

7. 添加缩放集使用的负载平衡器资源。有关详细信息，请参阅 [Azure Resource Manager 对负载平衡器的支持](/documentation/articles/load-balancer-arm)。

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"name": "[variables('loadBalancerName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "loadBalancerFrontEnd",
					"properties": {
						"publicIPAddress": {
							"id": "[variables('publicIPAddressID1')]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "bepool1"
				}
			],
			"inboundNatPools": [
				{
					"name": "natpool1",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('frontEndIPConfigID')]"
						},
						"protocol": "tcp",
						"frontendPortRangeStart": 50000,
						"frontendPortRangeEnd": 50500,
						"backendPort": 3389
					}
				}
			]
		}
	},
	```

8. 添加 jumpbox 虚拟机使用的网络接口资源。由于虚拟机缩放集中的虚拟机不可使用公共 IP 地址直接访问，因此将在缩放集所在的同一虚拟网络中创建 jumpbox 虚拟机，并使用它来远程访问集中的虚拟机。


	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/networkInterfaces",
		"name": "[variables('nicName1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Dynamic",
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
						},
						"subnet": {
							"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
						}
					}
				}
			]
		}
	},
	```


9. 在缩放集所在的同一网络中添加虚拟机。

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[parameters('vmName')]",
		"location": "[resourceGroup().location]",
      "dependsOn": [
			"[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
		],
		"properties": {
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[parameters('vmName')]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]"
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[variables('imagePublisher')]",
					"offer": "[variables('imageOffer')]",
					"sku": "[variables('imageVersion')]",
					"version": "latest"
				},
				"osDisk": {
					"name": "osdisk1",
					"vhd": {
						"uri":  "[concat('https://',parameters('resourcePrefix'),'saa.blob.core.chinacloudapi.cn/vhds/',parameters('resourcePrefix'),'osdisk1.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
					{
						"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
					}
				]
			}
		}
	},
	```

10.	添加虚拟机缩放集，并指定将在缩放集中的所有虚拟机上安装的诊断扩展。此资源的许多设置都与虚拟机资源相似。他们的主要区别是：

	- **capacity** - 指定应在缩放集中初始化多少个虚拟机。通过指定 instanceCount 参数的值来设置此值。

	- **upgradePolicy** - 指定如何对缩放集中的虚拟机进行更新。手动指定在重新部署模板时，模板中的更新仅影响新虚拟机。自动指定将更新并重新启动缩放集中的所有虚拟机。

	在所有存储帐户都根据 dependsOn 元素的指定创建之前，将不会创建虚拟机缩放集。

	```
	{
		"type": "Microsoft.Compute/virtualMachineScaleSets",
		"apiVersion": "[variables('apiVersion')]",
		"name": "[parameters('vmSSName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"storageLoop",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
			"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
		],
		"sku": {
			"name": "[variables('vmSize')]",
			"tier": "Standard",
			"capacity": "[parameters('instanceCount')]"
		},
		"properties": {
			"upgradePolicy": {
				"mode": "Manual"
			},
			"virtualMachineProfile": {
				"storageProfile": {
					"osDisk": {
						"vhdContainers": [
							"[concat('https://', parameters('resourcePrefix'), 'saa.blob.core.chinacloudapi.cn/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sag.blob.core.chinacloudapi.cn/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sam.blob.core.chinacloudapi.cn/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sas.blob.core.chinacloudapi.cn/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'say.blob.core.chinacloudapi.cn/vmss')]"
						],
						"name": "vmssosdisk",
						"caching": "ReadOnly",
						"createOption": "FromImage"
					},
					"imageReference": {
						"publisher": "[variables('imagePublisher')]",
						"offer": "[variables('imageOffer')]",
						"sku": "[variables('imageVersion')]",
						"version": "latest"
					}
				},
				"osProfile": {
					"computerNamePrefix": "[parameters('vmSSName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"networkProfile": {
					"networkInterfaceConfigurations": [
						{
							"name": "[variables('nicName2')]",
							"properties": {
								"primary": "true",
								"ipConfigurations": [
									{
										"name": "ip1",
										"properties": {
											"subnet": {
												"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
											},
											"loadBalancerBackendAddressPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
												}
											],
											"loadBalancerInboundNatPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
												}
											]
										}
									}
								]
							}
						}
					]
				},
				"extensionProfile": {
					"extensions": [
						{
							"name": "Microsoft.Insights.VMDiagnosticsSettings",
							"properties": {
								"publisher": "Microsoft.Azure.Diagnostics",
								"type": "IaaSDiagnostics",
								"typeHandlerVersion": "1.5",
								"autoUpgradeMinorVersion": true,
								"settings": {
									"xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
									"storageAccount": "[variables('diagnosticsStorageAccountName')]"
								},
								"protectedSettings": {
									"storageAccountName": "[variables('diagnosticsStorageAccountName')]",
									"storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
									"storageAccountEndPoint": "https://core.chinacloudapi.cn"
								}
							}
						}
					]
				}
			}
		}
	},
	```

11.	添加 autoscaleSettings 资源以定义缩放集如何根据集中虚拟机上的处理器使用率进行调整。对于本教程，以下是重要值：

 - **metricName** - 这与我们在 wadperfcounter 变量中定义的性能计数器相同。使用该变量，诊断扩展可收集 **Processor(\_Total)\\% Processor Time** 计数器。
 - **metricResourceUri** - 这是虚拟机缩放集的资源标识符。
 - **timeGrain** - 这是收集的指标的粒度。在此模板中，它设置为 1 分钟。
 - **statistic** - 这可确定如何组合指标以调节自动缩放操作。可能的值包括：Average、Min、Max。在此模板中，我们要查找缩放集中虚拟机的平均总 CPU 使用率。
 - **timeWindow** - 这是收集实例数据的时间范围。它必须介于 5 分钟和 12 个小时之间。
 - **timeAggregation** - 这可确定应如何随着时间的推移组合收集的数据。默认值为 Average。可能的值包括：Average、Minimum、Maximum、Last、Total、Count。
 - **operator** - 这是用于比较指标数据和阈值的运算符。可能的值包括：Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual。
 - **threshold** - 这是将触发缩放操作的值。在此模板中，当缩放集中的虚拟机的平均 CPU 使用率超过 50% 时，会将虚拟机添加到缩放集。
 - **direction** - 这可确定在达到阈值时执行的操作。可能的值为 Increase 或 Decrease。在此模板中，如果在定义的时间窗口中阈值超过 50%，则将增加缩放集中的虚拟机数。
 - **type** - 这是应发生的操作类型，此属性必须设置为 ChangeCount。
 - **value** - 这是已在缩放集中添加或删除的虚拟机数。此值必须大于或等于 1。默认值为 1。在此模板中，当达到该阈值时，缩放集中的虚拟机数将递增 1。
 - **cooldown** - 这是在上次缩放操作后，在下次操作发生之前要等待的时间量。此值必须介于 1 分钟和 1 周之间。

	```
	{
		"type": "Microsoft.Insights/autoscaleSettings",
		"apiVersion": "2015-04-01",
		"name": "[concat(parameters('resourcePrefix'),'as1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		],
		"properties": {
			"enabled": true,
			"name": "[concat(parameters('resourcePrefix'),'as1')]",
			"profiles": [
				{
					"name": "Profile1",
					"capacity": {
						"minimum": "1",
						"maximum": "10",
						"default": "1"
					},
					"rules": [
						{
							"metricTrigger": {
								"metricName": "\\Processor(_Total)\\% Processor Time",
								"metricNamespace": "",
								"metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
								"timeGrain": "PT1M",
								"statistic": "Average",
								"timeWindow": "PT5M",
								"timeAggregation": "Average",
								"operator": "GreaterThan",
								"threshold": 50.0
							},
							"scaleAction": {
								"direction": "Increase",
								"type": "ChangeCount",
								"value": "1",
								"cooldown": "PT5M"
							}
						}
					]
				}
			],
			"targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		}
	}
	```

12.	保存模板文件。

## 步骤 3：将模板上载到存储空间

只要你知道在步骤 1 中创建的存储帐户的帐户名称和主密钥，就可以从 Azure PowerShell 窗口上载模板。

1.	在 Azure PowerShell 窗口中，设置变量以指定在步骤 1 中部署的存储帐户的名称。

		$StorageAccountName = "vmstestsa"

2.	设置用于指定存储帐户的主密钥的变量。

		$StorageAccountKey = "<primary-account-key>"

	可以通过在 Azure 门户中查看存储帐户资源时单击密钥图标来获取此密钥。

3.	创建用于使用存储帐户验证操作的存储帐户上下文对象。

		$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

4.	创建新的模板容器，可以在其中存储你创建的模板。

		$ContainerName = "templates"
		New-AzureStorageContainer -Name $ContainerName -Context $ctx  -Permission Blob

5.	将模板文件上载到新容器。

		$BlobName = "VMSSTemplate.json"
		$fileName = "C:" + $BlobName
		Set-AzureStorageBlobContent -File $fileName -Container $ContainerName -Blob  $BlobName -Context $ctx

## 步骤 4：部署模板

创建模板后，便可以开始部署资源了。使用以下命令启动相关进程：

		New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.chinacloudapi.cn/templates/VMSSTemplate.json"

当你按 Enter 时，系统将提示你为所指定的变量提供值。提供以下值：

	vmName: vmsstestvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: VMpass1
	resourcePrefix: vmsstest

成功部署所有资源应大约需要 15 分钟。

>[AZURE.NOTE]你还可以利用门户的功能来部署资源。为此，请使用以下链接：https://portal.azure.cn/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## 步骤 5：监视资源

你可以使用以下方法获取有关虚拟机缩放集的一些信息：

 - Azure 门户 - 当前使用门户可以获取有限数量的信息。
 - [Azure 资源浏览器](https://resources.azure.com/) - 要浏览缩放集的当前状态，这是最好的工具。遵循此路径，你应该看到所创建的缩放集的实例视图：

		subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell - 使用此命令可获取一些信息：

		Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

 - 就像连接任何其他虚拟机一样连接到 jumpbox 虚拟机，然后可以远程访问缩放集中的虚拟机，以监视单个进程。

>[AZURE.NOTE]用于获取有关缩放集的信息的完整 REST API 可在[虚拟机扩展集](https://msdn.microsoft.com/zh-cn/library/mt589023.aspx)中找到

## 步骤 6：删除资源

由于你需要为 Azure 中使用的资源付费，因此，删除不再需要的资源总是一种良好的做法。你不需要从资源组中分别删除每个资源，而是可以删除资源组，这样就会自动删除它包含的所有资源。

	Remove-AzureRmResourceGroup -Name vmsstestrg1

如果要保留资源组，可以仅删除缩放集。

	Remove-AzureRmResource -Name vmsstest1 -ResourceGroupName vmsstestrg1 -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

<!---HONumber=Mooncake_0411_2016-->