---
title: How SSO to on-premises resources works on Azure AD joined devices | Microsoft Docs
description: Learn how to configure hybrid Azure Active Directory joined devices.

services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019

ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn

ms.collection: M365-identity-device-management
---
# How SSO to on-premises resources works on Azure AD joined devices

It is probably not a surprise that an Azure Active Directory (Azure AD) joined device gives you a single sign-on (SSO) experience to your tenant's cloud apps. If your environment has an on-premises Active Directory (AD), you can extend the SSO experience on these devices to resources and applications that rely on on-premises AD as well. 

This article explains how this works.

## Prerequisites

 If Azure AD joined machines are not connected to your organization's network, a VPN or other network infrastructure is required. On-premises SSO requires line-of-sight communication with your on-premises AD DS domain controllers.

## How it works 

With an Azure AD joined device, your users already have an SSO experience to the cloud apps in your environment. If your environment has an Azure AD and an on-premises AD, you may want to expand the scope of your SSO experience to your on-premises Line Of Business (LOB) apps, file shares, and printers.

Azure AD joined devices have no knowledge about your on-premises AD environment because they aren't joined to it. However, you can provide additional information about your on-premises AD to these devices with Azure AD Connect.

An environment that has both, an Azure AD and an on-premises AD, is also known has hybrid environment. If you have a hybrid environment, it is likely that you already have Azure AD Connect deployed to synchronize your on-premises identity information to the cloud. As part of the synchronization process, Azure AD Connect synchronizes on-premises user information to Azure AD. When a user signs in to an Azure AD joined device in a hybrid environment:

1. Azure AD sends the details of the user's on-premises domain back to the device, along with the [Primary Refresh Token] (concept-primary-refresh-token.md)
1. The local security authority (LSA) service enables Kerberos and NTLM authentication on the device.

During an access attempt to a resource requesting Kerberos or NTLM in the user's on-premises environment, the device:

1. Sends the on-premises domain information and user credentials to the located DC to get the user authenticated.
1. Receives a Kerberos [Ticket-Granting Ticket (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) or NTLM token based on the protocol the on-premises resource or application supports. If the attempt to get the Kerberos TGT or NTLM token for the domain fails (related DCLocator timeout can cause a delay), Credential Manager entries are attempted, or the user may receive an authentication popup requesting credentials for the target resource.

All apps that are configured for **Windows-Integrated authentication** seamlessly get SSO when a user tries to access them.

Windows Hello for Business requires additional configuration to enable on-premises SSO from an Azure AD joined device. For more information, see [Configure Azure AD joined devices for On-premises Single-Sign On using Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## What you get

With SSO, on an Azure AD joined device you can: 

- Access a UNC path on an AD member server
- Access an AD member web server configured for Windows-integrated security 

If you want to manage your on-premises AD from a Windows device, install the [Remote Server Administration Tools for Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

You can use:

- The Active Directory Users and Computers (ADUC) snap-in to administer all AD objects. However, you have to  specify the domain that you want to connect to manually.
- The DHCP snap-in to administer an AD-joined DHCP server. However, you may need to specify the DHCP server name or address.
 
## What you should know

You may have to adjust your [domain-based filtering](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) in Azure AD Connect to ensure that the data about the required domains is synchronized.

Apps and resources that depend on Active Directory machine authentication don't work because Azure AD joined devices don't have a computer object in AD. 

You can't share files with other users on an Azure AD-joined device.

## Next steps

For more information, see [What is device management in Azure Active Directory?](overview.md) 
