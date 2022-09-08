# 如何用程式碼更改AEDT選項設定

AEDT當中所有的設定都存放在XML檔案當中：C:\Users\\\<UserName>\Documents\Ansoft\\\<AnsysProductNameversion>\config\\\<PC\_NAME>\_user.XML，比方HPC的設定等等。可以透過指令修改，比方：oDesktop.SetRegistryString('Desktop\Settings\ProjectOptions\HPCLicenseType' , 'Pool')。

處理機碼的指令包含如下：

* DeleteRegistryEntry
* DoesRegistryValueExist
* GetRegistryInt
* GetRegistryString
* SetRegistryFromFile
* SetRegistryInt
* SetRegistryString

其他像是AEDT Option當中的選項設定也可以透過上述的函式來讀取或修改。

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>
