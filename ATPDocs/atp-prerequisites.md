---
# required metadata

title: Azure Advanced Threat Protection prerequisites | Microsoft Docs
description: Describes the requirements for a successful deployment of Azure ATP in your environment
keywords:
author: rkarlin
ms.author: rkarlin
manager: mbaldwin
ms.date: 2/21/2018
ms.topic: get-started-article
ms.prod:
ms.service: azure-advanced-threat-protection
ms.technology:
ms.assetid: 62c99622-2fe9-4035-9839-38fec0a353da

# optional metadata

#ROBOTS:
#audience:
#ms.devlang:
ms.reviewer: itargoet
ms.suite: ems
#ms.tgt_pltfrm:
#ms.custom:

---

*Applies to: Azure Advanced Threat Protection*



# Azure ATP Prerequisites
This article describes the requirements for a successful deployment of Azure ATP in your environment.

>[!NOTE]
> For information on how to plan resources and capacity, see [Azure ATP capacity planning](atp-capacity-planning.md).


Azure ATP is composed of the Azure ATP cloud service, which consists of the workspace management portal and a workspace portal, the Azure ATP standalone sensor and/or the Azure ATP sensor. For more information about the Azure ATP components, see [Azure ATP architecture](atp-architecture.md).

Each Azure ATP workspace supports an Active Directory forest boundary and supports Forest Functional Level (FFL) of Windows 2003 and above. For deployments with multiple forests, a separate Azure ATP workspace is required for each forest.


[Before you start](#before-you-start): This section lists information you should gather and accounts and network entities you should have before starting Azure ATP installation.

[Azure ATP workspace management portal](#azure-atp-workspace-management-portal-and-workspace-portal-requirements): This section describes the workspace management portal browser requirements.

[Azure ATP workspace portal](#azure-atp-workspace-management-portal-and-workspace-portal-requirements): This section describes browser requirements for running the Azure ATP workspace portal.

[Azure ATP standalone sensor](#azure-atp-sensor-requirements): This section lists Azure ATP standalone sensor hardware, software requirements as well as settings  you need to configure on your Azure ATP standalone sensor servers.

[Azure ATP sensor](#azure-atp-lightweight-sensor-requirements): This section lists Azure ATP sensor hardware, and software requirements.

![Azure ATP architecture diagram](media/ATP-architecture-topology.png)

## Before you start
This section lists information you should gather and accounts and network entities you should have before starting Azure ATP installation.


-   An **on-premises** Azure AD user account and password with read access to all objects in the monitored domains.

    > [!NOTE]
    > If you have set custom ACLs on various Organizational Units (OU) in your domain, make sure that the selected user has read permissions to those OUs.

-   If you run Wireshark on Azure ATP standalone sensor, you will need to restart the Azure Advanced Threat Protection sensor Service after you have stopped the Wireshark capture. If not, the sensor stops capturing traffic.

- If you attempt to install the ATP sensor on a machine configured with a NIC Teaming adapter, you receive an installation error. If you want to install the ATP sensor on a machine configured with NIC teaming, contact your Azure ATP support representative.

-    Recommended: User should have read-only permissions on the Deleted Objects container. This allows Azure ATP to detect bulk deletion of objects in the domain. For information about configuring read-only permissions on the Deleted Objects container, see the **Changing permissions on a deleted object container** section in the [View or Set Permissions on a Directory Object](https://technet.microsoft.com/library/cc816824%28v=ws.10%29.aspx) article.

-   Optional: A user account of a user who has no network activities. This account is configured as the Azure ATP Honeytoken user. For more information, see [Configure exclusions and Honeytoken user](install-atp-step7.md).

-   Optional: When deploying the standalone sensor, it is necessary to forward  Windows events 4776, 4732, 4733, 4728, 4729, 4756, 4757, and 7045 to ATP to further enhance Azure ATP Pass-the-Hash, Brute Force, Modification to sensitive groups, Honey Tokens detections, and malicious service creation. In the Azure ATP sensor, these events are received automatically. In the Azure ATP standalone sensor, these events can be received from your SIEM or by setting Windows Event Forwarding from your domain controller. Events collected provide Azure ATP with additional information that is not available via the domain controller network traffic.


## Azure ATP workspace management portal and workspace portal requirements
Access to the Azure ATP workspace portal and the Azure ATP workspace management portal is via a browser, supporting the following browsers and settings:
-	Microsoft Edge
-	Internet Explorer version 10 and above
-	Google Chrome 4.0 and above
-	Minimum screen width resolution of 1700 pixels
-	Firewall/proxy open - To communicate with the Azure ATP cloud service, you must have open: *.atp.azure.com port 443 in your firewall/proxy. 

## Azure ATP standalone sensor requirements
This section lists the requirements for the Azure ATP standalone sensor.
### General
The Azure ATP standalone sensor supports installation on a server running Windows Server 2012 R2 or Windows Server 2016 (Include server core).
The Azure ATP standalone sensor can be installed on a server that is a member of a domain or workgroup.
The Azure ATP standalone sensor can be used to monitor Domain Controllers with Domain Functional Level of Windows 2003 and above.

For your domain controllers to communicate with the cloud service, you must open port 443 in your firewalls and proxies to *.atp.azure.com.


For information on using virtual machines with the Azure ATP standalone sensor, see [Configure port mirroring](configure-port-mirroring.md).

> [!NOTE]
> A minimum of 5 GB of space is required and 10 GB is recommended. This includes space needed for the Azure ATP binaries, Azure ATP logs, and performance logs.

### Server specifications
For optimal performance, set the **Power Option** of the Azure ATP standalone sensor to **High Performance**.<br>
An Azure ATP standalone sensor can support monitoring multiple domain controllers, depending on the amount of network traffic to and from the domain controllers.

>[!NOTE] 
> When running as a virtual machine, dynamic memory or any other memory ballooning feature is not supported.

For more information about the Azure ATP standalone sensor hardware requirements, see [Azure ATP capacity planning](atp-capacity-planning.md).

### Time synchronization

The servers and domain controllers onto which the sensor is installed must have time synchronized to within five minutes of each other.


### Network adapters
The Azure ATP standalone sensor requires at least one Management adapter and at least one Capture adapter:

-   **Management adapter** - used for communications on your corporate network. This adapter should be configured with the following settings:

    -   Static IP address including default sensor

    -   Preferred and alternate DNS servers

    -   The **DNS suffix for this connection** should be the DNS name of the domain for each domain being monitored.

        ![Configure DNS suffix in advanced TCP/IP settings](media/ATP-DNS-Suffix.png)

        > [!NOTE]
        > If the Azure ATP standalone sensor is a member of the domain, this may be configured automatically.

-   **Capture adapter** - used to capture traffic to and from the domain controllers.

    > [!IMPORTANT]
    > -   Configure port mirroring for the capture adapter as the destination of the domain controller network traffic. For more information, see [Configure port mirroring](configure-port-mirroring.md). Typically, you need to work with the networking or virtualization team to configure port mirroring.
    > -   Configure a static non-routable IP address for your environment with no default sensor and no DNS server addresses. For example, 1.1.1.1/32. This ensures that the capture network adapter can capture the maximum amount of traffic and that the management network adapter is used to send and receive the required network traffic.

### Ports
The following table lists the minimum ports that the Azure ATP standalone sensor requires configured on the management adapter:

|Protocol|Transport|Port|To/From|Direction|
|------------|-------------|--------|-----------|-------------|
|LDAP|TCP and UDP|389|Domain controllers|Outbound|
|Secure LDAP (LDAPS)|TCP|636|Domain controllers|Outbound|
|LDAP to Global Catalog|TCP|3268|Domain controllers|Outbound|
|LDAPS to Global Catalog|TCP|3269|Domain controllers|Outbound|
|SSL (*.atp.azure.com)|TCP|443|Azure ATP cloud service|Outbound|
|Kerberos|TCP and UDP|88|Domain controllers|Outbound|
|Netlogon (SMB, CIFS, SAM-R)|TCP and UDP|445|Domain controllers|Outbound|
|Windows Time|UDP|123|Domain controllers|Outbound|
|DNS|TCP and UDP|53|DNS Servers|Outbound|
|NTLM over RPC|TCP|135|All devices on the network|Outbound|
|NetBIOS|UDP|137|All devices on the network|Outbound|
|Syslog (optional)|TCP/UDP|514, depending on configuration|SIEM Server|Inbound|
|RADIUS|UDDP|1813|RADIUS|Inbound|

> [!NOTE]
> - Using the Directory service user account, the sensor queries endpoints in your organization for local admins using SAM-R (network logon) in order to build the [lateral movement path graph](use-case-lateral-movement-path.md). For more information, see [Configure SAM-R required permissions](install-atp-step8-samr.md).
> - The following ports need to be open inbound on devices on the network from the Azure ATP standalone sensors:
>   -   NTLM over RPC (TCP Port 135) for resolution purposes
>   -   NetBIOS (UDP port 137) for resolution purposes
>   -   SAM-R queries (TCP/UDP port 445) for detection purposes


## Azure ATP sensor requirements
This section lists the requirements for the Azure ATP sensor.
### General
The Azure ATP sensor supports installation on a domain controller running Windows Server 2008 R2 SP1 (not including Server Core), Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 (including Core but not Nano).

The domain controller can be a read-only domain controller (RODC).

For your domain controllers to communicate with the cloud service, you must open port 443 in your firewalls and proxies to *.atp.azure.com.

During installation, the .Net Framework 4.7 is installed and might cause a reboot of the domain controller.


> [!NOTE]
> A minimum of 5 GB of space is required and 10 GB is recommended. This includes space needed for the Azure ATP binaries, Azure ATP logs, and performance logs.

### Server specifications

The Azure ATP sensor requires a minimum of two cores and 6 GB of RAM installed on the domain controller.
For optimal performance, set the **Power Option** of the Azure ATP sensor to **High Performance**.
The Azure ATP sensor can be deployed on domain controllers of various loads and sizes, depending on the amount of network traffic to and from the domain controllers and the amount of resources installed on that domain controller.

>[!NOTE] 
> When running as a virtual machine, dynamic memory or any other memory ballooning feature is not supported.

For more information about the Azure ATP sensor hardware requirements, see [Azure ATP capacity planning](atp-capacity-planning.md).

### Time synchronization

The servers and domain controllers onto which the sensor is installed must have time synchronized to within five minutes of each other.

### Network adapters

The Azure ATP sensor monitors the local traffic on all of the domain controller's network adapters. <br>
After deployment, you can use the Azure ATP workspace portal if you ever want to modify which network adapters are monitored.

The sensor is not supported on domain controllers running Windows 2008 R2 with Broadcom Network Adapter Teaming enabled.

### Ports
The following table lists the minimum ports that the Azure ATP sensor requires:

|Protocol|Transport|Port|To/From|Direction|
|------------|-------------|--------|-----------|-------------|
|SSL (*.atp.azure.com)|TCP|443|Azure ATP cloud service|Outbound|
|DNS|TCP and UDP|53|DNS Servers|Outbound|
|NTLM over RPC|TCP|135|All devices on the network|Outbound|
|Netlogon (SMB, CIFS, SAM-R)|TCP/UDP|445|Domain controllers|Outbound|
|NetBIOS|UDP|137|All devices on the network|Outbound|
|Syslog (optional)|TCP/UDP|514, depending on configuration|SIEM Server|Inbound|
|RADIUS|UDDP|1813|RADIUS|Inbound|

> [!NOTE]
> - Using the Directory service user account, the sensor queries endpoints in your organization for local admins using SAM-R (network logon) in order to build the [lateral movement path graph](use-case-lateral-movement-path.md).
> - The following ports need to be open inbound on devices on the network from the Azure ATP sensors:
>   -   NTLM over RPC (TCP Port 135) for resolution purposes
>   -   NetBIOS (UDP port 137) for resolution purposes
>   -   SAM-R queries (TCP/UDP port 445) for detection purposes





## See Also
- [Azure ATP sizing tool](http://aka.ms/aatpsizingtool)
- [Azure ATP architecture](atp-architecture.md)
- [Install ATP](install-atp-step1.md)
- [Check out the ATP forum!](https://aka.ms/azureatpcommunity)
