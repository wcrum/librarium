---
sidebar_label: "Install VerteX"
title: "Install VerteX"
description: "Learn how to install VerteX in an airgap VMware environment."
icon: ""
sidebar_position: 30
hide_table_of_contents: false
tags: ["vertex", "enterprise", "airgap", "vmware", "vsphere"]
keywords: ["self-hosted", "vertex"]
---

You install Palette VerteX in an airgap environment through the Palette Command Line Interface (CLI). The CLI provides
you with an interactive experience that guides you through the installation process. You can invoke the Palette CLI on
any Linux x86-64 system with the Docker daemon installed and connectivity to the VMware vSphere environment where
Palette VerteX will be deployed.

## Prerequisites

:::warning

If you are installing Palette VerteX in an airgap environment, ensure you complete all the airgap pre-install steps
before proceeding with the installation. Refer to the
[VMware vSphere Airgap Instructions](./vmware-vsphere-airgap-instructions.md) guide for more information.

:::

- An AMD64 Linux environment with connectivity to the VMware vSphere environment.

- [Docker](https://docs.docker.com/engine/install/) or equivalent container runtime installed and available on the Linux
  host.

- Palette CLI installed and available. Refer to the Palette CLI
  [Install](../../../../palette-cli/install-palette-cli.md#download-and-setup) page for guidance.

- An Ubuntu Pro Subscription and token. Ubuntu Pro provides access to FIPS 140-2 certified cryptographic packages.

- Review the required VMware vSphere [permissions](../vmware-system-requirements.md). Ensure you have created the proper
  custom roles and zone tags.

- We recommended the following resources for Palette VerteX. Refer to the
  [Palette VerteX size guidelines](../../install-palette-vertex.md#instance-sizing) for additional sizing information.

  - 8 CPUs per VM.

  - 16 GB Memory per VM.

  - 100 GB Disk Space per VM.

- The following network ports must be accessible for Palette VerteX to operate successfully.

  - TCP/443: Inbound to and outbound from the Palette VerteX management cluster.

  - TCP/6443: Outbound traffic from the Palette VerteX management cluster to the deployed cluster's Kubernetes API
    server.

- The network IP address range you specify during the installation must not overlap with any existing IP addresses in
  your environment. The IP address range must also have connectivity to the VMware vSphere environment.

- Ensure you have an SSL certificate that matches the domain name you will assign to Palette VerteX. You will need this
  to enable HTTPS encryption for Palette VerteX. Reach out to your network administrator or security team to obtain the
  SSL certificate. You need the following files:

  - x509 SSL certificate file in base64 format.

  - x509 SSL certificate key file in base64 format.

  - x509 SSL certificate authority file in base64 format. This file is optional.

- Zone tagging is required for dynamic storage allocation across fault domains when provisioning workloads that require
  persistent storage. Refer to [Zone Tagging](../vmware-system-requirements.md#zone-tagging) for information.

- Assigned IP addresses for application workload services, such as Load Balancer services.

- Shared Storage between VMware vSphere hosts.

:::info

Self-hosted Palette VerteX installations provide a system Private Cloud Gateway (PCG) out-of-the-box and typically do
not require a separate, user-installed PCG. However, you can create additional PCGs as needed to support provisioning
into remote data centers that do not have a direct incoming connection from the Palette console. To learn how to install
a PCG on VMware, check out the [Deploy to VMware vSphere](../../../../clusters/pcg/deploy-pcg/vmware.md) guide.

:::

:::warning

Palette VerteX does not support insecure connections. Ensure you have the Certificate Authority (CA) available, in PEM
format, when using a custom packs and image registry. Otherwise, VerteX will not be able to pull packs and images from
the registry. The Palette CLI will prompt you to provide the CA certificate file path when necessary.

:::

## Deployment

The video below demonstrates the installation wizard and the prompts you will encounter. Take a moment to watch the
video before you begin the installation process. Make sure to use values that are appropriate for your environment. Use
the **three-dots Menu** in the lower right corner of the video to expand the video to full screen and to change the
playback speed.

<Video title="palette-cli-install" src="/videos/vertex-airgap-install.mp4"></Video>

Use the following steps to install Palette VerteX.

1.  Log in to your vCenter environment.

2.  Create a vSphere VM and Template folder with the name `spectro-templates`. Ensure this folder is accessible by the
    user account you will use to deploy the airgap VerteX installation.

3.  Use the URL below to import the Operating System and Kubernetes distribution OVA required for the install. Place the
    OVA in the `spectro-templates` folder. Refer to the
    [Import Items to a Content Library](https://docs.vmware.com/en/VMware-vSphere/8.0/vsphere-vm-administration/GUID-B413FBAE-8FCB-4598-A3C2-8B6DDA772D5C.html?hWord=N4IghgNiBcIJYFsAOB7ATgFwAQYKbIjDwGcQBfIA)
    guide for information about importing an OVA in vCenter.

    ```url
     https://vmwaregoldenimage-console.s3.us-east-2.amazonaws.com/u-2204-0-k-12711-0.ova
    ```

4.  Append an `r_` prefix to the OVA name and remove the `.ova` suffix after the import. For example, the final output
    should look like `r_u-2204-0-k-12711-0`. This naming convention is required for the install process to identify the
    OVA. Refer to the [Supplement Packs](../../airgap/supplemental-packs.md#additional-ovas) page for a list of
    additional OVAs you can download and upload to your vCenter environment.

    :::tip

    You can also use the **Deploy OVF Template** wizard in vSphere to make the OVA available in the `spectro-templates`
    folder. Append the `r_` prefix, and remove the `.ova` suffix when assigning a name and target location. You can
    terminate the deployment after the OVA is available in the `spectro-templates` folder. Refer to the
    [Deploy an OVF or OVA Template](https://docs.vmware.com/en/VMware-vSphere/8.0/vsphere-vm-administration/GUID-AFEDC48B-C96F-4088-9C1F-4F0A30E965DE.html)
    guide for more information about deploying an OVA in vCenter.

    :::

5.  Open a terminal window and invoke the Palette CLI by using the `ec` command to install the enterprise cluster. The
    interactive CLI prompts you for configuration details and then initiates the installation. For more information
    about the `ec` subcommand, refer to [Palette Commands](../../../../palette-cli/commands/ec.md).

        ```bash
        palette ec install
        ```

6.  At the **Enterprise Cluster Type** prompt, choose **Palette VerteX**.

7.  Type `y` to enable Ubuntu Pro, and provide your Ubuntu Pro token when prompted.

    :::warning

    To ensure FIPS compliance, be sure to enter your Ubuntu Pro token.

    :::

8.  Specify the URL or IP address of the Spectro Cloud Repository that is provided to you by the airgap setup script.
    Make sure to specify the file path to the CA certificate when prompted.

    :::info

    If you are using the Palette CLI from inside an [airgap support VM](./vmware-vsphere-airgap-instructions.md), the
    CLI will automatically detect the airgap environment and prompt you to **Use local, air-gapped Spectro Cloud
    Artifact Repository (SCAR) configuration**. Type `y` to use the local resources and skip filling in the repository
    URL and credentials.

    :::

9.  Enter the repository credentials. Our support team provides the credentials you need to access the public Spectro
    Cloud repository. Airgap installations, provide the credentials to your private repository provided to you by the
    airgap setup script .

10. Choose `VMware vSphere` as the cloud type. This is the default.

11. Type an enterprise cluster name. Your VM instances will use this name as a prefix.

12. When prompted, enter the information listed in each of the following tables.

        #### Environment Configuration

    | **Parameter**                     | **Description**                                                                                                                                                                                                                                                                                                                |
    | :-------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
    | **HTTPS Proxy**                   | Leave this blank unless you are using an HTTPS Proxy. This setting will be propagated to all EC nodes and all of its target cluster nodes. Example: `https://USERNAME:PASSWORD@PROXYIP:PROXYPORT`.                                                                                                                             |
    | **HTTP Proxy**                    | Leave this blank unless you are using an HTTP Proxy. This setting will be propagated to all EC nodes and all of its target cluster nodes. Example: `http://USERNAME:PASSWORD@PROXYIP:PROXYPORT`.                                                                                                                               |
    | **No Proxy**                      | The default is blank. You can add a comma-separated list of local network CIDR addresses, hostnames, and domain names that should be excluded from being a proxy. This setting will be propagated to all the nodes to bypass the proxy server. Example if you have a self-hosted environment: `maas.company.com,10.10.0.0/16`. |
    | **Proxy CA Certificate Filepath** | The default is blank. You can provide the filepath of a CA certificate on the installer host. If provided, this CA certificate will be copied to each host in the PCG cluster during deployment. The provided path will be used on the PCG cluster hosts. Example: `/usr/local/share/ca-certificates/ca.crt`.                  |
    | **Pod CIDR**                      | Enter the CIDR pool IP that will be used to assign IP addresses to pods in the EC cluster. The pod IP addresses should be unique and not overlap with any machine IPs in the environment.                                                                                                                                      |
    | **Service IP Range**              | Enter the IP address range that will be used to assign IP addresses to services in the EC cluster. The service IP addresses should be unique and not overlap with any machine IPs in the environment.                                                                                                                          |

13. Select the OCI registry type and provide the configuration values. Review the following table for more information.
    If you are using the Palette CLI from inside an [airgap support VM](./vmware-vsphere-airgap-instructions.md), the
    CLI will automatically detect the airgap environment and prompt you to **Use local, air-gapped Pack Registry?** Type
    `y` to use the local resources and skip filling in the OCI registry URL and credentials.

    :::warning

    For self-hosted OCI registries, ensure you have the server Certificate Authority (CA) certificate file available on
    the host where you are using the Palette CLI. You will be prompted to provide the file path to the OCI CA
    certificate. Failure to provide the OCI CA certificate will result in self-linking errors. Refer to the
    [Self-linking Error](../../../../troubleshooting/enterprise-install.md#scenario---self-linking-error)
    troubleshooting guide for more information.

    :::

    #### Pack & Image Registry Configuration

    | **Parameter**                                    | **Description**                                                                                                                                                                                                                                                                                                                                     |
    | ------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | **Registry Type**                                | Specify the type of registry. Allowed values are `OCI` or `OCI ECR`. Airgap users, select `OCI`.                                                                                                                                                                                                                                                    |
    | **Registry Name**                                | Enter the name of the registry.                                                                                                                                                                                                                                                                                                                     |
    | **Registry Endpoint**                            | Enter the registry endpoint. Airgap users, provide the **Spectro Cloud Repository** URL or hostname shared by the airgap setup script.                                                                                                                                                                                                              |
    | **Registry Base Path**                           | Enter the registry base path.                                                                                                                                                                                                                                                                                                                       |
    | **Allow Insecure Connection**                    | Bypasses x509 verification. Type `n` to specify a certificate authority in the follow-up prompt. Airgap user, ensure you select `n`.                                                                                                                                                                                                                |
    | **Registry CA certificate filepath**             | Specify the file path to the certificate authority. Use absolute paths. Airgap users, provide the filepath displayed by the aurgap setup script.                                                                                                                                                                                                    |
    | **Registry Username** or **Registry Access Key** | Enter the registry username or the access key if using `OCI ECR`.                                                                                                                                                                                                                                                                                   |
    | **Registry Password** or **Registry Secret Key** | Enter the registry password or the secret key if using `OCI ECR`.                                                                                                                                                                                                                                                                                   |
    | **Registry Region**                              | Enter the registry region. This option is only available if you are using `OCI ECR`.                                                                                                                                                                                                                                                                |
    | **ECR Registry Private**                         | Type `y` if the registry is private. Otherwise, type `n`.                                                                                                                                                                                                                                                                                           |
    | **Use Public Registry for Images**               | Type `y` to use a public registry for images. Type `n` to a different registry for images. If you are using another registry for images, you will be prompted to enter the registry URL, base path, username, and password. Airgap users, select `n` so that you can specify the values for the OCI registry that contains all the required images. |

    When prompted to **Pull images from public registry**, type `n` and specify the OCI registry configuration values
    for your image registry. If you are an [airgap support VM](./vmware-vsphere-airgap-instructions.md), the CLI will
    automatically detect the airgap environment and prompt you to **Use local, air-gapped Image Registry?**. Type `y` to
    use the local resources and skip filling in the OCI registry URL and credentials. Refer to the table above for more
    information.

    :::info

    You will be provided with an opportunity to update the mirror registries values. To exit `vi` press the `Escape` key
    and type `:wq` to save and exit.

    :::

14. The next set of prompts is for the VMware vSphere account information. Enter the information listed in the following
    table.

    #### VMware vSphere Account Information

    | **Parameter**                 | **Description**                                                                                                                                                                               |
    | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | **vSphere Endpoint**          | VMware vSphere endpoint. Must be a fully qualified domain name (FQDN) or IP address without a scheme - that is, without an IP protocol, such as `https://`. Example: `vcenter.mycompany.com`. |
    | **vSphere Username**          | VMware vSphere account username.                                                                                                                                                              |
    | **vSphere Password**          | VMware vSphere account password.                                                                                                                                                              |
    | **Allow Insecure Connection** | Bypasses x509 verification. Type `Y` if using a VMware vSphere instance with self-signed Transport Layer Security (TLS) certificates. Otherwise, type `n`.                                    |

        #### VMware vSphere Cluster Configuration

        This information determines where Palette will be deployed in your VMware vSphere environment. The Palette CLI will use
        the provided VMware credentials to retrieve information from your VMware vSphere environment and present options for you
        to select from.

        | **Parameter**       | **Description**                                                                                                                                                                                                                                                                                                           |
        | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
        | **Datacenter**      | The installer retrieves the Datacenter automatically.                                                                                                                                                                                                                                                                     |
        | **Folder**          | Select the folder that contains the VM instance.                                                                                                                                                                                                                                                                          |
        | **Cluster**         | Select the cluster where you want to deploy Palette.                                                                                                                                                                                                                                                                      |
        | **Network**         | Select the network where you want to deploy Palette.                                                                                                                                                                                                                                                                      |
        | **Resource Pool**   | Select the resource pool where you want to deploy Palette.                                                                                                                                                                                                                                                                |
        | **Datastore**       | Select the datastore where you want to deploy Palette.                                                                                                                                                                                                                                                                    |
        | **Fault Domains**   | Configure one or more fault domains by selecting values for these properties: Cluster, Network (with network connectivity), Resource Pool, and Storage Type (Datastore or VM Storage Policy). Note that when configuring the Network, if you are using a distributed switch, choose the network that contains the switch. |
        | **NTP Servers**     | You can provide a list of Network Time Protocol (NTP) servers, such as `pool.ntp.org`.                                                                                                                                                                                                                                                            |
        | **SSH Public Keys** | Provide any public SSH keys to access your Palette VMs. This option opens up your system's default text editor. Vi is the default text editor for most Linux distributions. To review basic vi commands, check out the [vi Commands](https://www.cs.colostate.edu/helpdocs/vi.html) reference.                            |

15. Specify the IP pool configuration. The placement type can be Static or Dynamic Domain Name Server (DDNS). Choosing
    static placement creates an IP pool from which VMs are assigned IP addresses. Choosing DDNS assigns IP addresses
    using DNS.

        #### Static Placement Configuration

        | **Parameter**                   | **Description**                                                                             |
        | ------------------------------- | ------------------------------------------------------------------------------------------- |
        | **IP Start range**              | Enter the first address in the EC IP pool range.                                            |
        | **IP End range**                | Enter the last address in the EC IP pool range.                                             |
        | **Network Prefix**              | Enter the network prefix for the IP pool range. Valid values are in [0, 32]. Example: `18`. |
        | **Gateway IP Address**          | Enter the IP address of the static IP gateway.                                              |
        | **Name servers**                | Comma-separated list of DNS name server IP addresses.                                       |
        | **Name server search suffixes** | An optional comma-separated list of DNS search domains.                                     |

16. The last set of prompts are for the vSphere machine and database configuration. Use the following table for
    guidance.

        #### vSphere Machine Configuration

        | **Parameter** | **Description**                                                                                                                                                             |
        | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
        | **Small**     | Deploy VM nodes with 8 CPU, 16 GB memory, 60 GB storage. The database specs are 20 GB database with 2 CPU limit and 4 GB memory limit.                                      |
        | **Medium**    | Deploy VM nodes with 16 CPU, 32 GB memory, 100 GB storage. The database specs are 60 GB database with 4 CPU limit and 8 GB memory limit.                                    |
        | **Large**     | Deploy VM nodes with 32 CPU, 64 GB memory, 120 GB storage. The database specs are 80 GB database with 8 CPU limit and 16 GB memory limit.                                   |
        | **Custom**    | Deploy VM nodes with custom CPU, memory, storage, database size, CPU limit, and memory limit. If you specify custom, you will be prompted for the CPU, memory, and storage. |

        #### Additional vSphere Machine Configuration

        | **Parameter**     | **Description**                                                                          |
        | ----------------- | ---------------------------------------------------------------------------------------- |
        | **Node Affinity** | Select the node affinity. Enter `y` to schedule all Palette pods on control plane nodes. |

    The installation process stands up a [kind](https://kind.sigs.k8s.io/) cluster locally that will orchestrate the
    remainder of the installation. The installation takes some time.

    Upon completion, the enterprise cluster configuration file named `ec.yaml` contains the information you provided,
    and its location is displayed in the terminal. Credentials and tokens are encrypted in the YAML file.

    ```bash hideClipboard
    ==== Enterprise Cluster config saved ====
    Location: :/home/spectro/.palette/ec/ec-20230706150945/ec.yaml
    ```

    :::tip

    If an error occurs during installation, remove the `kind` cluster that was created and restart the installation. To
    remove the `kind` cluster, issue the following command. Replace `spectro-mgmt-cluster` with the name of your cluster
    if you used a different name.

    ```bash
    kind delete cluster spectro-mgmt-cluster
    ```

    Restart the install process by referencing the `ec.yaml` file that was created during the first installation
    attempt. For example:

    ```bash
    palette ec install --config /home/spectro/.palette/ec/ec-20230706150945/ec.yaml
    ```

    :::

    When the installation is complete, Enterprise Cluster Details that include a URL and default credentials are
    displayed in the terminal. You will use these to access the Palette VerteX System Console. The Palette CLI has the
    kubectl CLI included. You can find the kubectl binary in the **bin** directory of the Palette CLI configuration
    directory, located at **~/.palette/bin/kubectl**.

    ```bash hideClipboard
    ===========================================
    ==== Enterprise Cluster System Console ====
    ===========================================
    Console URL: https://10.10.100.0/system
    Username:    ************
    Password:    ************

    The first of three Enterprise Cluster nodes is online and will now provision nodes two and three.

    It will take another ~30-45 minutes for the installation to complete.

    You can monitor its progress via kubectl/k9s or by viewing the system console.

    export KUBECONFIG=/ubuntu/.palette/ec/ec-20231012215923/spectro_mgmt.conf
    ```

17. Copy the URL and paste it in your browser's URL field to access the system console. You will be prompted to reset.

    :::info

    The first time you visit the Palette VerteX system console, a warning message about an untrusted SSL certificate may
    appear. This is expected, as you have not yet uploaded your SSL certificate to Palette VerteX. You can ignore this
    warning message and proceed.

    :::

    ![Screenshot of the Palette VerteX system console showing Username and Password fields.](/vertex_installation_install-on-vmware_vertex-system-console.webp)

18. Log in to the System Console using the credentials provided in the Enterprise Cluster Details output. After login,
    you will be prompted to create a new password. Enter a new password and save your changes. You will be redirected to
    the Palette VerteX system console.

19. After login, a Summary page is displayed. Palette VerteX is installed with a self-signed SSL certificate. To assign
    a different SSL certificate you must upload the SSL certificate, SSL certificate key, and SSL certificate authority
    files to Palette VerteX. You can upload the files using the Palette VerteX system console. Refer to the
    [Configure HTTPS Encryption](/vertex/system-management/ssl-certificate-management) page for instructions on how to
    upload the SSL certificate files to Palette VerteX.

20. The last step is to start setting up a tenant. To learn how to create a tenant, check out the
    [Tenant Management](../../../system-management/tenant-management.md) guide.

    ![Screenshot of the Summary page showing where to click Go to Tenant Management button.](/vertex_installation_install-on-vmware_goto-tenant-management.webp)

## Validate

You can verify the installation is successful if you can access the system console using the IP address provided in
Enterprise Cluster Details and if the Summary page displays the **Go to Tenant Management** button.

You can also validate that a three-node Kubernetes cluster is launched and Palette VerteX is deployed on it.

1. Log in to the vCenter Server by using vSphere Client.

2. Navigate to your vSphere Datacenter and locate your Palette VM instances. The VMs are prefixed with the name you
   provided during the installation. For example, if you provided `spectro-mgmt-cluster` as the name, the VMs are named
   `spectro-mgmt-cluster-`, followed by a unique set of alphanumeric values. Verify three nodes are available.

3. Open a web browser session, and use the IP address provided in Enterprise Cluster Details at the completion of the
   installation to connect to the Palette system console. Copy the IP address to the address bar and append `/system`.

4. Log in using your credentials.

5. A **Summary** page will be displayed that contains a tile with a **Go to Tenant Management** button. After initial
   installation, the **Summary** page shows there are zero tenants.

## Next Steps

You have successfully installed Palette VerteX in vSphere. Your next steps are to configure Palette VerteX for your
organization. Start by creating the first tenant to host your users. Refer
to [Create a Tenant](../../../system-management/tenant-management.md) for instructions.

After you create the tenant, you are ready to configure authentication types in tenant settings and create users and
teams.

## Resources

- [Environment Setup](./vmware-vsphere-airgap-instructions.md)

- [Create a Tenant](../../../system-management/tenant-management.md)

- [Enterprise Install Troubleshooting](../../../../troubleshooting/enterprise-install.md)

- [Palette CLI](../../../../palette-cli/install-palette-cli.md#download-and-setup)

- [System Management](../../../system-management/system-management.md)

- [VMware System Requirements](../vmware-system-requirements.md)