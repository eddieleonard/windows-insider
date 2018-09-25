---
title: Manage Windows Insider Preview builds
description: how to use management solutions to install and monitor builds in your organization
services: WIP-at-work-pro
author: dawnwood
manager: elizapo
ms.assetid: 
ms.service: WIP-at-work-pro
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/28/2018
ms.author: dawn.wood
ms.localizationpriority: medium
---

# Manage Windows 10 Insider Preview Builds 
Administrators can manage installation of Insider Preview builds across multiple devices in their organization using the following steps: 

## Register your domain 
Organizations have the option of registering their Azure Active Directory domain in the Windows Insider Program. To register a domain, you must be registered in the Windows Insider Program with your work account in Azure AD (See [Register](wip-4-biz-register.md)) and you must be assigned a Global Administrator role on that Azure AD domain. Also requires Windows 10 Version 1703 or later on the machine used for registration. 

> [!div class="nextstepaction"]
> [Register your domain](https://insider.windows.com/en-us/for-business-organization-admin/)

__NOTE:__ 
* The Windows Insider Program only supports registration of domains in Azure Active Directory (and not Active Directory on premises) as a corporate authentication method.
* Once a domain is registered, administrators do not have to register each individual device or user with the Windows Insider Program in order to apply Insider Preview installation polices. 
* To get the most benefit out of the Windows Insider Program for Business, organizations should not use a test tenant of Azure AD. There will be no modifications to the Azure AD tenant to support the Windows Insider Program as it will only be used as an authentication method.

## Join devices to Azure Active Directory
In order to receive Insider Preview builds through Windows Update, devices must be joined to the same Azure AD domain that was registered with the Windows Insider Program. For devices on a local Active Directory not already joined to Azure AD, follow these steps: 

### To join individual devices 
1. Open __Settings__, and then select __Accounts__.
2. Select __Access work or school__, and then select __Connect__.
3. On the "Set up a work or school account" screen, select __Join this device to Azure Active Directory__.
4. On the "Let's get you signed in" screen, type your Azure AD email address (for example, alain@contoso.com), and then select __Next__.
5. On the "Enter password" screen, type your password, and then select __Sign in__.
6. On the "Make sure this is your organization" screen, review the information to make sure it's right, and then select __Join__.
 
See also [Join your work device to your organization's network](https://docs.microsoft.com/en-us/azure/active-directory/user-help/user-help-join-device-on-network)

### To join multiple devices 
To join multiple devices on your local Active Directory to your Azure AD domain, use Azure AD Connect. For more details, see: [Integrate your on-premises directories with Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect)

## Create and manage policies 
You can use Group Policy or MDM solutions such as Intune to configure the Windows Update for Business settings that control how and when Windows 10 Insider Preview Builds are installed on devices.  
>__NOTE:__ Insider Preview builds can only managed using Group Policy and cannot currently be managed using Windows Server Update Services (WSUS) or System Center Configuration Manager. To confirm that a device is connected to Windows Update and not WSUS, in Registry Editor go to: __HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate__.  

### Set using Group Policy
Use the Group Policy Management Console (GPMC) to manage the following Windows Update for Business settings on domain-joined devices. See [walkthrough guidance](https://docs.microsoft.com/en-us/windows/deployment/update/waas-wufb-group-policy). 

__Allow Telemetry__ sets the level of diagnostic data that can be sent to Microsoft. To enable installation of Insider Preview builds on a device, telemetry must be set to level 2 (enhanced) or higher. 
* Go to: __Computer Configuration/Administrative Templates/Windows Components/Windows Update/Data Collection and Preview builds/Allow Telemetry__ and click __Enabled__.
* In the drop-down box, select "2-Enhanced" or "3-Full". 

__Manage preview builds__ enables or prevents installation of Insider Preview builds on a device. You can also decide to stop Insider Preview builds once the Windows release is public. 
* Go to: __Computer Configuration/Administrative Templates/Windows Components/Windows Update/Windows Update for Business/Select when Preview Builds and Feature Updates are received__ and click __Enabled__.  
* For "Set the behavior for receiving preview builds, select "Enable preview builds". You can also select "Disable preview builds once next release is public" or "Disable preview builds". 

__Branch Readiness Level__ enables selection of flight rings and option to defer or pause the delivery of updates. 
* Go to: __Computer Configuration/Administrative Templates/Windows Components/Windows Update/Windows Update for Business/Select when Preview Builds and Feature Updates are received__ and click __Enabled__. 
* For "Select the Windows readiness level for the updates you want to receive", select your desired Ring. For more information, see [Windows readiness levels and flight rings](wip-4-biz-flight-levels-and-rings.md). 
* For "After a Preview Build or Feature Update is released, defer receiving it for this many days", enter a number up to 14 days. 
* For "Pause Preview Builds or Feature Updates starting" enter date. The pause will remain in effect for 35 days from the start date provided. 

### Set using MDM policies 
To set Allow Telemetry and Windows Insider for Business policies above using Intune or non-Microsoft MDM service providers, using the CSP settings below. For guidance on configuring CSPs, see [CSPs in MDM](https://docs.microsoft.com/en-us/windows/configuration/provisioning-packages/how-it-pros-can-use-configuration-service-providers#csps-in-mdm). 

[System/AllowTelemetry](https://docs.microsoft.com/en-us/windows/client-management/mdm/policy-csp-system#system-allowtelemetry)

[Update/ManagePreviewBuilds](https://docs.microsoft.com/en-us/windows/client-management/mdm/policy-csp-update#update-managepreviewbuilds) 

[Update/BranchReadinessLevel](https://docs.microsoft.com/en-us/windows/client-management/mdm/policy-csp-update#update-branchreadinesslevel)

### Set using using Intune 
1. Log into the [Azure portal](https://portal.azure.com) and select __Intune__ under __Resources__.
2. Navigate to __Device configuration > Profiles > Create profile__.
3. From the __Profile type__ drop-down list, choose __Device restrictions__.
4. In __Settings__ select __Reporting and Telemetry__ and set __Share usage data__ to "Enhanced" or "Full". (To enable installation of Insider Preview builds on a device, telemetry must be set to enhanced or higher.) 
5. Go to __Software Update>Windows 10 Update Rings__. Click “+” to create an Update Ring policy.
6. Under __Servicing Channel__, select "Fast" or "Slow" to assign Insider Preview builds from an Insider Preview Ring. See [Windows readiness levels and flight rings](wip-4-biz-flight-levels-and-rings.md) for more information about each choice. 
7. Adjust __Feature update deferral period__ if you want to defer deployment of Insider Preview builds for a certain number of days after release. 
8. Click __OK__ and __Create__ to set policy.
9. Go to __Assignments__ to assign the policy to users and devices. Note: you can create groups with one or more users or devices in Intune under __Groups__. 

![Intune Update Ring](images/wip-4-biz_manage_intune.png "ADD")

## Confirm and track installations  

### Confirm policy 
To confirm that your Group Policy or MDM policies have been set correctly, go to __Settings>Update & Security>Windows Update__ on the device and click on "View configured update policies". You can also check the following key in the Registry Editor on the device: __HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\windows\WindowsUpdate__. A device set to receive an Insider Preview build would show the following values: 
* BranchReadinessLevel = 2 (Fast), 4 (Slow) or 8 (Release Preview) 
* ManagePreviewBuilds = 1

__NOTE:__
* Once a policy has been set, the device must be restarted for the policy to be activated. 
* If a device is not receiving Insider Preview builds, see [Troubleshooting](wip-4-biz-troubleshooting.md). 

![Windows Update for Business values in Registry Editor](images/wip-4-biz-reg-xs.png "ADD")

### Track devices 
You can use Device Health in Windows Analytics to monitor the devices running Insider Preview builds. This can be useful for identifying device, device driver and application issues. See [Using Device Health to monitor Insider Preview builds](https://insider.windows.com/en-us/for-business-device-health/). 

## Related Topics
* [Deploy updates using Windows Update for Business](https://docs.microsoft.com/en-us/windows/deployment/update/waas-manage-updates-wufb) 
* [Manage software updates in Intune](https://docs.microsoft.com/en-us/intune/windows-update-for-business-configure)
* [How to use Insider Preview Builds to validate Applications and Infrastructure](https://insider.windows.com/en-us/for-business-getting-started/#validate)
* [Register for the Windows Insider Program for Business](wip-4-biz-register.md)
* [Share Feedback via the Feedback Hub](wip-4-biz-feedback-hub.md)

