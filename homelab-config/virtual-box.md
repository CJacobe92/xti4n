---
icon: flask-gear
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
  actions:
    visible: true
---

# Virtual Box

## Installation

1. Download VirtualBox from **https://www.virtualbox.org/wiki/Downloads** or go to Google and search for **VirtualBox download**.
2.  Select the appropriate VirtualBox installation for your host system.



    <div align="left"><figure><img src="../.gitbook/assets/image (198).png" alt="" width="563"><figcaption></figcaption></figure></div>
3.  Don't forget to download the extension pack as well. This will allow us to use the NVME feature (If you are using an NVME SSD)



    <div align="left"><figure><img src="../.gitbook/assets/image (199).png" alt="" width="563"><figcaption></figcaption></figure></div>
4. Run the VirtualBox setup and hit Next until you finished the installation (We are just using the default configurations).
5. Install the VirtualBox extension pack. Just hit Accept and hit Next to finish the installation.

## Post Installation Setup

### **Setting up VBoxManage Variable Path**

I would like to have the capability to utilize VBoxManage to make changes on my VirtualBox configuration via terminal. This will allow us to make changes on VirtualBox if the option is not available via the GUI.

1.  To check if VBoxManage is installed open a terminal and type **VBoxManage --version**. If it did not show any version number just like below, then it is not yet configured on your host machine.



    <div align="left"><figure><img src="../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure></div>
2.  On your windows computer, open a run command (Window keys + R) and type **sysdm.cpl** to open System Properties.



    <div align="left"><figure><img src="../.gitbook/assets/image (201).png" alt="" width="335"><figcaption></figcaption></figure></div>
3.  Then on the **System Properties,** go to **Advanced**>**Environmental Variables.**



    <div align="left"><figure><img src="../.gitbook/assets/image (81).png" alt="" width="406"><figcaption></figcaption></figure></div>
4.  On the Environment Variables > Click **New** and on the **Variable Name** type **Path**



    <div align="left"><figure><img src="../.gitbook/assets/image (202).png" alt="" width="529"><figcaption></figcaption></figure></div>
5.  On the **Variable value** we will use the **Browse Directory** to fill up that information. Click Browse Directory and go to the path **C:\Program Files\Oracle\VirtualBox** and click **OK**&#x20;



    <div align="left"><figure><img src="../.gitbook/assets/image (86).png" alt="" width="556"><figcaption></figcaption></figure></div>
6. Once added restart your terminal if open and run the **VBoxManage --version** just like earlier and it show the appropriate version number.

### Enabling Headless Mode on VirtualBox Globally

We want to enable the headless mode by default. It will make things easier if we want to switch between virtual machines and keep them running in the background without the clutter of having all the virtual machine windows open.

1.  Open terminal and type the following command below



    ```powershell
    VBoxManage setproperty defaultfrontend headless
    ```
2.  Now when you start a virtual machine, you don't need to specifically start it on headless mode all you have to is to click start and it will launch in headless mode by default. When you close the virtual machine window it will give you an option to continue running the VM on the background.



    <div align="left"><figure><img src="../.gitbook/assets/image (200).png" alt="" width="563"><figcaption></figcaption></figure></div>

## Isolate NAT Network Setup

On my setup I used an isolate NAT Network for added security for example to avoid malware spreading to my own physical network

1. On **VirtualBox** go to **File**>**Tools**>**Network Manager**
2.  Select **NAT Networks**



    <div align="left"><figure><img src="../.gitbook/assets/image (68).png" alt="" width="563"><figcaption></figcaption></figure></div>
3. Under General Options setup your preferred NAT Network name and IPv4 Prefix. In my case I am using a Class A IP Address.

**Note:** You can use any IP Address you want as long it is a valid IP. Also, the Default gateway of this network is the first IP on the subnet in my case my default gateway is 10.10.2.1.

## Kali Linux Installation

We can easily spawn a Kali Linux VM in seconds by downloading the VDI from the official site and opening it in VirtualBox. But here I proceed on installing the Kali Linux manually on my VirtualBox and made customizations along the way.

### Downloading Kali from the Kali Linux Office Website

1.  Go to https://www.kali.org/ and click Download



    <div align="left"><figure><img src="../.gitbook/assets/image (88).png" alt="" width="563"><figcaption></figcaption></figure></div>
2.  Select Installer Images



    <div align="left"><figure><img src="../.gitbook/assets/image (89).png" alt="" width="563"><figcaption></figcaption></figure></div>
3.  Click the download button on the Installer Image Card



    <div align="left"><figure><img src="../.gitbook/assets/image (90).png" alt="" width="563"><figcaption></figcaption></figure></div>

### Preparing VirtualBox for Kali VM

1.  On VirtualBox click **New**



    <div align="left"><figure><img src="../.gitbook/assets/image (91).png" alt="" width="563"><figcaption></figcaption></figure></div>
2.  Under **Create Virtual Machine** > **Name and Operating System** enter the following configuration



    * Name: Kali
    * Folder: \<Don't change to use default folder or select your preferred folder for VM location>
    * ISO Image:  \<Path where you downloaded the ISO file from Kali website>
    * Type: Linux
    * Subtype: Ubuntu
    *   Version: Ubuntu (64-bit)



    <div align="left"><figure><img src="../.gitbook/assets/image (92).png" alt="" width="563"><figcaption></figcaption></figure></div>
3.  Select **Hardware** and set the **Base Memory** and **Processors** to the following configuration:



    * **Base Memory:** 4096 mb ----> On my setup I used 8192 mb but 4096 should be enough
    *   **Processors:** 2 ----> This is the sweet spot as per other users



    <div align="left"><figure><img src="../.gitbook/assets/image (93).png" alt="" width="563"><figcaption></figcaption></figure></div>
4.  Select **Hard Disk** and set your disk space size. The recommended from Kali Linux is 20gb but here I will be setting up at least 40gb.



    * **Hard Disk File Location and Size:** 40gb | Keep the location default or select your preferred path
    * **Hard Disk File Type and Variant:** Pre-allocate Full Size



    <div align="left"><figure><img src="../.gitbook/assets/image (94).png" alt="" width="563"><figcaption></figcaption></figure></div>
5. Click **Finish** to complete your Kali installation

