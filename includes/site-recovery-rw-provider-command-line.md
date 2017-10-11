UnifiedSetup.exe [/ ServerMode < CS/PS >] [/ 安装驱动器<DriveLetter>] [/ MySQLCredsFilePath <MySQL credentials file path>] [/ VaultCredsFilePath <Vault credentials file path>] [/ EnvType < VMWare/NonVMWare >] [/ PSIP < IP 地址，以便用于数据传输] [/ CSIP <IP address of CS to be registered with>] [/ PassphraseFilePath <Passphrase file path>]

参数：

* / ServerMode： 必需。 指定是否应安装的配置和进程服务器，或仅在进程服务器。 输入值： CS，ps。
* InstallLocation： 必需。 在其中安装组件的文件夹。
* / MySQLCredsFilePath。 必需。 中的 MySQL server 凭据的存储文件路径。 此格式应为该文件：
* [MySQLCredentials]
* MySQLRootPassword ="<Password>"
* MySQLUserPassword ="<Password>"
* / VaultCredsFilePath。 必需。 保管库凭据文件的位置
* / EnvType。 必需。 安装类型。 值： VMware NonVMware
* / PSIP 和 /CSIP。 必需。 进程服务器和配置服务器 IP 地址。
* / PassphraseFilePath。 必需。 密码文件的位置。
* / BypassProxy。 可选。 指定连接到 Azure 的配置服务器不使用代理。
* / ProxySettingsFilePath。 可选。 （默认代理需要身份验证或自定义代理） 的代理设置。 此格式应为该文件：
* [ProxySettings]
* ProxyAuthentication ="是/否"
* 代理 IP ="IP 地址 >"
* 友好 ="<Port>"
* ProxyUserName ="<User Name>"
* 代理 ="<Password>"
* DataTransferSecurePort。 可选。 复制数据端口号。
* SkipSpaceCheck。 可选。 跳过检查缓存的空间。
* AcceptThirdpartyEULA。 必需。 接受第三方 EULA。
* ShowThirdpartyEULA。 必需。 显示第三方 EULA。 如果作为输入提供，则忽略所有其他参数。
