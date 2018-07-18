Home Active Directory Lab
=========================

Building AD Home Lab
^^^^^^^^^^^^^^^^^^^^

1. Download Windows Server 2012 from Microsoft: `180-day free trial <https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2012-r2>`_

2. Using Virtualbox to set up the Windows Server

The Virtual Network
^^^^^^^^^^^^^^^^^^^

When considering the networking of your virtual lab, it's important to consider what you're trying to achieve. Virtual Networks come in three flavors:

* **NAT**: The virtualization software creates a new virtual network that VMs are a part of. The software acts as a router, natting traffic between the virtual network and the real network that a host computer is associated with. This is the default networking setup for most Virtualization software

* **Host-Only**: The virtualization software creates a new virtual network, but in this case, it does NOT act as a router. VMs in a host-only network can talk among themselves, but their traffic can't leave the host device

* **Bridged**: The virtualization software puts the VM on the same network as the host computer, so the VM acts just like another computer on the network

For this lab, we'll be creating a Host-Only network:

1. In VirtualBox, go to **File > Host network Manager**

2. Click on the **Create** button in the upper left. This will create a new network interface on your computer for VirtualBox

3. Select the new interface, make sure **Properties** is selected from the top of the window and **give it an IP address scheme** that agrees with you

.. image:: ad_lab_virtualbox_network01.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Now selec the VM that was created and edit its settings to use the network created

.. image:: ad_lab_virtualbox_network02.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center
   
Setup Windows 
^^^^^^^^^^^^^

To setup the Domain Controller, we need to do three things:

1. Install the Virtualization Guest Extensions. In VMWare these are called VM Tools, in VirtualBox they're Guest Additions. 

.. image:: dc_install03.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

2. Give it a static IP address. The actual address doesn't matter, typically, you'd give the DCs the last IP in the subnet. We're also going to set out gateway to **.2** and use Google's DNS servers for name resolution.

.. image:: ip_addressing.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

3. Rename the computer something sensible so you'll know what the DC is named

.. image:: rename_computer01.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

4. Reboot

Installing the Domain Controller Role
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Installing the ADDS Role
########################

Open up "Server Manager" from the start menu and from the Quick Start, select **Add Roles and Features**

.. image:: add_role.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Click next until you reach the step to select roles, select **Active Directory Domain Services** and click **Add Features** to the window that pops up.

.. image:: add_role02.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Promoting the Server to a Domain Controller
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once the role is installed, you should see a little warning sign in Server Manager, click that and then select **Promote this Server to Domain Controller**

.. image:: promotion01.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

The first thing we're going to have to do is create our forest, so select **New Forest** and enter a domain name. In this example we use "**example.com**"

.. image:: promotion02.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

On the next screen we'll leave the defaults and we're going to create a **recovery password**. You will most likely never use this in a lab, its typically used when very bad things happen to your domain.

.. image:: promotion03.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

On the next screen, you're going to see this error message. It just means that the server couldn't find an existing DNS infrastructure for the domain you specified earlier. Of course it couldn't because it hasn't been created yet. Click "Next"

.. image:: promotion04.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

For the next series of prompts, we'll just accept the defaults. It's good enough for the lab (they're also good enough for most production networks too). The wizard will check some pre-requisites and t hen you'll end up at the install screen. There will be some warnings but it's okay. Click "Install"

.. image:: promotion05.png
   :width: 800px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Next Steps?
^^^^^^^^^^^

After the server comes back up, you'll have a domain controller. You can log in using the same Administrator account as before, onyl now it's a **Domain Administrator**. *In interesting characteristic of Domain Controllers* is that there are no longer local accounts on the server. Domain Controllers **are** the domain, they're what host everything that makes the domain work, so the concept of "local accounts" doesn't apply here.


