---
title: "排查已加入混合 Azure Active Directory 的下层设备问题 | Microsoft Docs"
description: "排查已加入混合 Azure Active Directory 的下层设备问题。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 8bc07c551b5554a3f5b8697ca77a00d9cd0f470a
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>排查已加入混合 Azure Active Directory 的下层设备问题 

本主题仅适用于以下设备： 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

对于 Windows 10 或 Windows Server 2016，请参阅[排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题](device-management-troubleshoot-hybrid-join-windows-current.md)。

本主题假设已[配置已加入混合 Azure Active Directory 的设备](device-management-hybrid-azuread-joined-devices-setup.md)以支持以下方案：

- 基于设备的条件访问

- [企业设置漫游](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md) 





本主题提供有关如何解决潜在问题的故障排除指导。  

**应了解的内容：** 

- 每个用户的最大设备数以设备为中心。 例如：如果 *jdoe* 和 *jharnett* 登录到某个设备，会在“用户信息”选项卡中为其中每个用户单独创建一个注册 (DeviceID)。  

- 初始注册/加入设备配置为在登录或锁定/解锁时执行尝试。 可能会有 5 分钟的延迟，由任务计划程序任务触发。 

- 重新安装操作系统或者手动取消注册再重新注册可能会在 Azure AD 上创建新的注册，并导致 Azure 门户中的“用户信息”选项卡下出现多个条目。 


## <a name="step-1-retrieve-the-registration-status"></a>步骤 1：检索注册状态 

**验证注册状态：**  

1. 以管理员身份打开命令提示符 

2. 键入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

此命令会显示一个对话框，其中提供了有关加入状态的更多详细信息。

![适用于 Windows 的工作区加入](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>步骤 2：评估混合 Azure AD 加入状态 

如果混合 Azure AD 加入不成功，对话框中会提供有关发生的问题的详细信息。

**最常见的问题包括：**

- AD FS 或 Azure AD 配置不当

    ![适用于 Windows 的工作区加入](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- 登录身份不是域用户

    ![适用于 Windows 的工作区加入](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)

- 已达到配额

    ![适用于 Windows 的工作区加入](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- 服务未响应 

    ![适用于 Windows 的工作区加入](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

也可以在“应用程序和服务日志\Microsoft 工作区加入”下面的事件日志中找到状态信息。
  
**混合 Azure AD 加入失败的最常见原因是：** 

- 计算机不在组织的内部网络上，或者在未与本地 AD 域控制器建立连接的 VPN 上。

- 使用本地计算机帐户登录到了计算机。 

- 服务配置问题： 

  - 联合服务器已配置为支持 **WIAORMULTIAUTHN**。 

  - 在计算机所属的 AD 林中的 Azure AD 内，不存在指向已验证域名的“服务连接点”对象。

  - 用户已达到设备限制。 

## <a name="next-steps"></a>后续步骤

如有问题，请参阅[设备管理常见问题解答](device-management-faq.md)  
