---
title: "使用 Windows VM MSI 通过 SAS 凭据访问 Azure 存储"
description: "本教程演示了如何使用 Windows VM 托管服务标识 (MSI) 通过 SAS 凭据（而非存储帐户访问密钥）访问 Azure 存储。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/11/2017
ms.author: bryanla
ms.openlocfilehash: a2138d0b99ed57cb78b99e3785f7192c1a323ba5
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-a-sas-credential"></a>使用 Windows VM 托管服务标识通过 SAS 凭据访问 Azure 存储

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教程演示了如何为 Windows 虚拟机启用托管服务标识 (MSI)，然后使用 MSI 来获取存储共享访问签名 (SAS) 凭据。 具体而言，是[服务 SAS 凭据](/azure/storage/common/storage-dotnet-shared-access-signature-part-1?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures)。 

服务 SAS 提供了在不公开帐户访问密钥的情况下授权特定的服务（在我们的示例中为 blob 服务）在有限时间内访问存储帐户中对象的权限。 可以像平常在执行存储操作时一样使用 SAS 凭据，例如使用存储 SDK 时。 对于本教程，我们将演示使用 Azure 存储 PowerShell 上传和下载 blob。 将了解如何执行以下操作：


> [!div class="checklist"]
> * 在 Windows 虚拟机上启用 MSI 
> * 向 VM 授予对资源管理器中的存储帐户 SAS 的访问权限 
> * 使用 VM 的标识获取一个访问令牌，并使用它从资源管理器检索 SAS 

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Windows 虚拟机

本教程将新建 Windows VM。 另外，还可以在现有 VM 上启用 MSI。

1.  单击 Azure 门户左上角的“+/创建新服务”按钮。
2.  选择“计算”，然后选择“Windows Server 2016 Datacenter”。 
3.  输入虚拟机信息。 此处创建的用户名和密码是用于登录虚拟机的凭据。
4.  在下拉列表中为虚拟机选择正确的订阅。
5.  若要在新资源组中创建虚拟机，请选择“资源组”中的“新建”。 完成后，单击“确定”。
6.  选择 VM 大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在设置边栏选项卡中保留默认值，然后单击“确定”。

    ![Alt 图像文本](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>在 VM 上启用 MSI

通过虚拟机 MSI，可以从 Azure AD 获取访问令牌，而无需在代码中插入凭据。 事实上，启用 MSI 会执行两项操作：在 VM 上安装 MSI VM 扩展，以及为虚拟机启用 MSI。  

1. 导航到新虚拟机的资源组，并选择已在上一步中创建的虚拟机。
2. 在左侧面板上，在 VM“设置”下，单击“配置”。
3. 若要注册并启用 MSI，请选择“是”，若要禁用，请选择“否”。
4. 务必单击“保存”，以保存配置。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 若要检查 VM 上安装了哪些扩展，请单击“扩展”。 如果 MSI 已启用，列表中会显示“ManagedIdentityExtensionforWindows”。

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>创建存储帐户 

如果还没有存储帐户，现在将创建存储帐户。 也可以跳过此步骤，并向 VM MSI 授予对现有存储帐户的 SAS 凭据的访问权限。 

1. 单击 Azure 门户左上角的“+/创建新服务”按钮。
2. 依次单击“存储”、“存储帐户”，并将显示新的“创建存储帐户”面板。
3. 输入存储帐户的名称，稍后将使用该名称。  
4. **部署模型**和**帐户类型**应分别设置为“资源管理器”和“通用”。 
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。
6. 单击“创建” 。

    ![新建存储帐户](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在存储帐户中创建 Blob 容器

稍后我们会将文件上传并下载到新存储帐户。 由于文件需要 blob 存储，我们需要创建用于存储文件的 blob 容器。

1. 导航回新创建的存储帐户。
2. 在左侧面板上，单击“Blob 服务”下的“容器”链接。
3. 单击页面顶部的“+ 容器”，将滑出“新建容器”面板。
4. 为容器指定名称，选择访问级别，单击“确定”。 在本教程中的后面部分将使用所指定的名称。 

    ![创建存储容器](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-a-storage-sas"></a>向 VM 的 MSI 授予访问权限以使用存储 SAS 

Azure 存储原本不支持 Azure AD 身份验证。  但是，可以使用 MSI 从资源管理器检索存储 SAS，然后使用 SAS 来访问存储。  在此步骤中，将向 VM MSI 授予对存储帐户 SAS 的访问权限。   

1. 导航回新创建的存储帐户。   
2. 单击左侧面板中的“访问控制 (IAM)”。  
3. 单击页面顶部的“+ 添加”，为 VM 添加新的角色分配
4. 在页面左侧，将“角色”设置为“存储帐户参与者”。  
5. 在下一个下拉列表中，把“将访问权限分配给”设置为资源“虚拟机”。  
6. 接下来，确保“订阅”下拉列表中列出了正确的订阅，然后将“资源组”设置为“所有资源组”。  
7. 最后，在“选择”下，从下拉列表中选择你的 Windows 虚拟机，然后单击“保存”。 

    ![Alt 图像文本](media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 标识获取访问令牌，并使用它调用 Azure 资源管理器 

在本教程的剩余部分中，我们将从前面创建的 VM 开始工作。

在此部分中，将需要使用 Azure 资源管理器 PowerShell cmdlet。  如果尚未安装，请[下载最新版本](https://docs.microsoft.com/powershell/azure/overview)，然后再继续。

1. 在 Azure 门户中，导航到“虚拟机”，转到你的 Windows 虚拟机，然后在“概述”页面中单击顶部的“连接”。
2. 输入创建 Windows VM 时添加的用户名和密码。 
3. 现在，已经创建了与虚拟机的远程桌面连接，请在远程会话中打开 PowerShell。 
4. 使用 Powershell 的 Invoke-WebRequest，向本地 MSI 终结点发出请求以获取 Azure 资源管理器的访问令牌。

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > “资源”参数的值必须完全匹配 Azure AD 预期的值。 如果使用 Azure 资源管理器资源 ID，必须在 URI 的结尾添加斜线。
    
    接下来，提取“内容”元素，该元素以 JavaScript 对象表示法 (JSON) 格式字符串的形式存储在 $response 对象中。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    接下来，从响应中提取访问令牌。
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>从 Azure 资源管理器中获取 SAS 凭据，以便调用存储 

现在，使用在上一部分中检索到的访问令牌通过 PowerShell 调用资源管理器，来创建存储 SAS 凭据。 具有 SAS 凭据后，我们便可以调用存储操作。

对于此请求，我们将使用以下 HTTP 请求参数来创建 SAS 凭据：

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

这些参数包括在针对 SAS 凭据的请求的 POST 正文中。 有关用于创建 SAS 凭据的参数的详细信息，请参阅 [List Service SAS REST reference](/rest/api/storagerp/storageaccounts/listservicesas)（列出服务 SAS REST 参考）。

首先，将参数转换为 JSON，然后调用存储 `listServiceSas` 终结点来创建 SAS 凭据：

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> URL 区分大小写。因此，请确保使用的大小写与之前在命名资源组时使用的大小写完全相同，包括“resourceGroups”使用的是大写“G”。 

现在，我们可以从响应中提取 SAS 凭据：

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

如果检查 SAS 凭据，将会看到类似以下内容的内容：

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

接下来，我们将创建一个名为“test.txt”的文件。 然后，通过 `New-AzureStorageContent` cmdlet 使用 SAS 凭据进行身份验证，将该文件上传到 blob 容器，然后下载该文件。

```bash
echo "This is a test text file." > test.txt
```

请务必首先使用 `Install-Module Azure.Storage` 安装 Azure 存储 cmdlet。 然后，可以使用 `Set-AzureStorageBlobContent` PowerShell cmdlet 上传刚创建的 blob：

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

响应：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

也可以使用 `Get-AzureStorageBlobContent` PowerShell cmdlet 下载刚上传的 blob：

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

响应：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```


## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](../active-directory/msi-overview.md)。
- 若要了解如何使用存储帐户密钥完成此同一教程，请参阅[使用 Windows VM 托管服务标识访问 Azure 存储](msi-tutorial-windows-vm-access-storage.md)
- 有关 Azure 存储帐户 SAS 功能的详细信息，请参阅：
  - [使用共享访问签名 (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Constructing a Service SAS](/rest/api/storageservices/Constructing-a-Service-SAS.md)（构造服务 SAS）

使用以下评论部分提供反馈，帮助我们改进内容。


