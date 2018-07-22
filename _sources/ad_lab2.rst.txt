.. _lab-part-2:

Home Active Directory Lab Part 2
================================

Creating Our Users
^^^^^^^^^^^^^^^^^^

We'll be using the **"Active Directory Administrative Center"** (or **ADAC**) to work with AD. To get started, go to the Start Menu and start **"Active Directory Administrative Center"**

.. image:: ad_lab_administrative_center01.png
   :width: 750px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

ADAC is relatively new, for the longest time Administrators used "Active Directory Users and Computers" (ADUC) to manage users and computers in their domain. ADAC is a little bit more capable and does a good job of surfacing common tasks. Of course in addition to the GUI tools, you can use PowerShell to manage AD. Like a lot of modern Windows management tools, this is actualyl all ADAC is doing. You can even view the PowerShell commands that it's running by expanding the **"Windows PowerShell History"** bar at the bottom of the window.

.. image:: ad_lab_adac_powershell.png
   :width: 750px
   :height: 650px
   :scale: 100 %
   :alt: alternate text
   :align: center

Now before we create the users, we need to establish some structure. Switch your view to "Tree View" by clicking the "Tree View" icon (it's the one under the "Ac" in Active Directory in the screenshot below) and then select the domain from the sidebar in ADAC. As you can see the domain comes prepopulated with a bunch of folders. These are Containers. We'll create our own containers, except we'll be creating **Organization Units** or "OUs". The only difference between OUs and Containers that awe care about is that you can't apply Group Policy to Containers.

OUs and Containers are party of the LDAP standards and are used to organize the objects in a domain. Until you get really into AD, you won't have to worry about Containers again. *Just call everything an "**OU**" and you'll sound like you know what you're talking about*.

.. image:: ad_lab_adac_overview01.png
   :width: 750px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

We'll be creating a new OU that we'll use for our environment. In production, it's typical to not touch the default Containers too much and put all your objects in new OUs that make sense for the company.

To create a new OU, right click on "Example (local)" and choose **New > Organization Unit**.

.. image:: ad_lab_adac_create_ou.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

We'll name this new OU "**Lab**"

.. image:: ad_lab_adac_create_ou02.png
   :width: 700px
   :height: 650px
   :scale: 100 %
   :alt: alternate text
   :align: center

Next, repeat this process to create a "**Users**" and "**Computers**" OU under the newly created "Lab" OU. You can do this by right-clicking the "Lab" OU and then following the same process to create an OU.

.. image:: ad_lab_adac_create_ou03.png
   :width: 700px
   :height: 650px
   :scale: 100 %
   :alt: alternate text
   :align: center

Once done, your domain should look like this.

.. image:: ad_lab_adac_create_ou04.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Actually Creating Users
^^^^^^^^^^^^^^^^^^^^^^^

To create a user, **right-click the "Users" OU** that you created and choose **New > User**.

Fill in the **Full name, User UPN Logon, UserSamAccountName**, and **Password**. You'll notice some fields will fill in others as you type. You can also toggle the "Password Options" so that you won't have to change this password when you first log in.

.. image:: ad_lab_adac_create_user01.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Let's also add this user to the "Domain Admins" group. In the new user screen, **select "Member of"** from the sidebar.

1. Click **"Add"** from the right-hand side

2. In the "Select Groups" window that opens up type **"Domain Admins"** and **click the "Check Names" button.**

3. The words "Domain Admins" should become underlines. **Hit "OK"**.

.. image:: ad_lab_adac_create_user02.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Create a Client Computer
^^^^^^^^^^^^^^^^^^^^^^^^

Our domain isn't all that interesting with just a single Domain Controllers, so let's add a client. `Download a copy of Windows 10 from Technet <https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise>`_. We're going to create a new VM. For client VMs, typically you can use 2 vCPUs, 1 GB of RAM and 30 GB of hard drive space. We'll also want to **make sure that this VM is put on the same host-only network that our other VMs were put on**.

Go through the install for Windows and accept the defaults. When the install is finished install the Virtualization Tools for your platform.

Join the Domain
###############

Once the install has finished and the computer has reboots, we're ready to join this computer to the domain.

1. Open up Explorer, **right click on "This PC"** and go to **"Properties"**.

.. image:: ad_lab_join_domain01.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

2. Click on **"Change Settings"** on the right hand side.

.. image:: ad_lab_join_domain02.png
   :width: 625px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

3. In the window that opens up, click the **"Change"** button and then name the Computer, switch **"Member of"** to **"Domain"** and enter **"example.com"**. Hit OK

.. image:: ad_lab_join_domain03.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

4. You'll be prompted for credentials. You can use the "Administrator" account or the account that you created earlier.

.. image:: ad_lab_join_domain04.png
   :width: 600px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

5. After entering your credentials, the computer will join the domain and prompt you to reboot. Do that.

.. image:: ad_lab_join_domain05.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

While the client is rebooting go to your Domain Controller and **open up ADAC**. Your computer should be in the "Computers" OU at the root of the domain. **Right click on the computer** and choose **"Move.."** and send it to the "Computers" OU under the Lab OU.

.. image:: ad_lab_join_domain06.png
   :width: 650px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Group Policy
^^^^^^^^^^^^

Group Policy is one of the main reasons Active Directory has been so successful. It allows you to granularly configure User and Computer settings throughout a domain. For the puposes of this lab, we'll just cover the very surface of Group Policy.

For our example, we'll be going ahead and turning off Windows Firewall for Computers in our "Lab\Computers" OU. While this is obviously a bad practice, it's a nice example of Group Policy settings and is fairly typical in production Windows environments.

Creating Group Policy Objects
#############################

1. Go to your start menu and run **Group Policy Management**

.. image:: ad_lab_group_policy01.png
   :width: 750px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

2. Expand our Forest > Domains > Example.com > Lab. Right click the "Computers" OU and choose "Create a GPO in this domain..."

.. image:: ad_lab_group_policy02.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

3. Name the GPO "Firewall Rules"

.. image:: ad_lab_group_policy_03a.png
   :width: 650px
   :height: 275px
   :scale: 100 %
   :alt: alternate text
   :align: center

4. Right click the newly created GPO and choose "Edit" 

.. image:: ad_lab_group_policy03b.png
   :width: 650px
   :height: 325px
   :scale: 100 %
   :alt: alternate text
   :align: center

5. In the "Group Policy Management Editor" window that opens up, expand **Computer Configuration > Policies > Windows Settings > Security Settings > Windows Firewall..** and select **"Windows Firewall.."**. Click the **"Windows Firewall Properties"** link

.. image:: ad_lab_group_policy04.png
   :width: 750px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

6. This brings up a standard Windows firewall settings window. **Set the Firewall to "Off"** and click OK.

.. image:: ad_lab_group_policy05.png
   :width: 700px
   :height: 600px
   :scale: 100 %
   :alt: alternate text
   :align: center

Things to know about Group Policy settings
##########################################

Quick rundown of things to know when workign with Group Policy:

* Group Policy takes some time to take effect. Computers will check for updates every 45 minutes or so. You can speed this up by running `gpupdate` on the computer that you want to update. You can also just reboot the computer, it will pull new updates when it boots.

* You'll notice that Group Policy settings are split between User and Computer and sometimes the same setting exists in both areas (for example, a lot of Internet Explorer settings). A common mistake is setting something in the Computer settings and then applying that GPO to an OU full of users (or vice versa). Those settings won't take effect since they have nothing to act on.

Wrapping UP
^^^^^^^^^^^

We now have a completely functional Active Directory Lab! Suggestions for next steps would be to try and replicate configurations that you're used to such as:

* Setup drives to auto mount when people log in.

* Deploy software through Group Policy

* Password Policies

* LAPS

* Startup scripts 

* Add another server instance and play with different server roles

* Add a second domain controller

* Add multiple clients

* Play with roaming profiles
