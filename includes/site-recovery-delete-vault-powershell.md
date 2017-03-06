## <a name="delete-a-recovery-services-vault-powershell"></a>删除恢复服务保管库 (PowerShell)

1. 获取恢复服务保管库

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. 删除保管库

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> 请务必谨慎使用上述命令，因为如果误删任何保管库，将丢失所有数据。 这是永久性操作，无法逆转。  


