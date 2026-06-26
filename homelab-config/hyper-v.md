---
icon: flask-gear
---

# Hyper-V

## **Enabling the Hyper-V**&#x20;

### **Using Programs and Features**

1. On your Windows 10/Windows 11 machine, open the Run dialog by pressing the **Windows + R** keys.
2.  Once opened, type `appwiz.cpl` and press **Enter**.



    <div align="left"><figure><img src="../.gitbook/assets/image (207).png" alt="" width="340"><figcaption></figcaption></figure></div>
3.  This command will take you directly to the **Programs and Features** section of the Control Panel.



    <div align="left"><figure><img src="../.gitbook/assets/image (208).png" alt="" width="563"><figcaption></figcaption></figure></div>
4.  Once the Programs and Features page is opened click on Turn Windows Features on or off.



    <div align="left"><figure><img src="../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure></div>
5.  Click the checkbox right next to Hyper-V to enable the feature and click Ok.



    <div align="left"><figure><img src="../.gitbook/assets/image (210).png" alt="" width="413"><figcaption></figcaption></figure></div>
6. The installation will start, and a restart is required. Make sure you saved all of your documents if you have any! :smile:

### Using PowerShell

1.  On your windows computer, right click on the Windows Icon.



    <div align="left"><figure><img src="../.gitbook/assets/image (211).png" alt="" width="263"><figcaption></figcaption></figure></div>
2.  A context menu will open, select Terminal (Admin)

    <div align="left"><figure><img src="../.gitbook/assets/image (212).png" alt="" width="189"><figcaption></figcaption></figure></div>
3.  On the PowerShell terminal type the following command below:

    `DISM /Online /Enable-Feature /All /FeatureName:Microsoft-Hyper-V`&#x20;



    <figure><img src="../.gitbook/assets/image (214).png" alt=""><figcaption></figcaption></figure>
4. It will enable the Hyper-V feature, and a restart is required to complete the process.

## Creating Isolated NAT Network in Hyper-V

We want to make sure that our laboratory is on an isolated network for security purposes. All of the steps below can be done via the PowerShell command.

1.  Create the Virtual Network Adapter:

    `New-VMSwitch -Name "WANSwitch" -SwitchType Internal`
2.  On created we need to get the Interface Index of the virtual switch we just created. Take note of the `ifIndex` value.&#x20;

    `Get-NetAdapter -Name "vEthernet (WANSwitch)"`&#x20;



    <figure><img src="../.gitbook/assets/image (215).png" alt=""><figcaption></figcaption></figure>
3.  Next is we will assign a static IP on the Virtual Network Adapter that we just created. The IP Address value that we will be assigning will be our Network's default gateway. This can be done using the following command`"Net-NetIPAddress"`.



    `Net-NetIPAddress -IPAddress 10.10.100.1 -PrefixLength -InterfaceIndex 73`&#x20;


4.  To allow internet access for our VMs, we need to create a NAT network that will enable our internal IP addresses to use our physical network's single public IP address for communication with the internet. To do this we will use the `"New-NetNat`" command.



    `New-NetNat -Name MyWanNatNetwork -InternalIPInterfaceAddressPrefix 10.10.100.0/24`&#x20;



    <figure><img src="../.gitbook/assets/image (216).png" alt=""><figcaption></figcaption></figure>

## Additional Information

If you want to access your VM's via RDP or SSH you need to setup a port forwarding.&#x20;

On this example let us imagine an Ubuntu server that has an Internal IP of **10.10.100.10**. We want to SSH on this VM using the external 22 port of the virtual switch (Take note our virtual switch acts up as router) and the internal 22 port of the VM. You can enter the following command below to configure port forwarding on your network

```
Add-NetNatStaticMapping -NatName "MyWanNatNetwork" -Protocol TCP -ExternalIPAddress 0.0.0.0 -InternalIPAddress 10.10.100.10 -InternalPort 22 -ExternalPort 8022
```

Voila! You now have your own Isolated NAT Network in Hyper-V. :tada:
