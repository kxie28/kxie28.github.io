Upgrading Phantom and Installing Apps
=====================================

Important notes before you start:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Customers who wish to upgrade must have a Phantom portal account (on my.phantom.us). The upgrade script below will prompt you for a username and password for portal authentication. 

* This is your login to my.phantom.us, not a local OS username and password.

* Phantom Warm Standby should not be enabled on the instance. Instructions on how to check Warm Standby status can be found in KB-64.

* Upgrades to or from Early Access or Beta releases are not supported.

* Always take a VM snapshot of your existing instance before upgrading.

* Access to the Phantom RPM bundles used for offline or AWS installs are limited to paid customers and users in POV (Proof of Value) evaluation. Paid customers may email support@phantom.us for access to the RPM bundles and users in POV will need to request access from their Sales Person or Sales Engineer.

* It is highly recommended that you upgrade all Phantom published apps when upgrading to 3.5.

* You must be running the most recent version of Phantom (3.0.284) and you must update your operating system to the latest packages with yum.

* If upgrading a Phantom instance which utilizes an external database see KB-86: Upgrading Phantom When Utilizing an External PostgreSQL Database: Additional Steps for additional steps that must performed.

* See the instructions below (Upgrading Phantom - Versions up to 2.1.486) to upgrade to 2.1.486 before attempting a 3.5 upgrade.

* The upgrade process duration depends on how much data your Phantom system contains, as well as the underlying system hardware and configuration. Systems with large amounts of data can take quite a while (10+ hours) to run through the upgrade process.

* Case Management in 2.1 is completely different from Case Management in 3.0+. Migration of existing cases and templates is not part of the upgrade process and requires contacting support. You can safely upgrade to 3.0+, and later migrate the cases, but your cases will not be accessible via the UI.

* After upgrade clear your browser cache to ensure correct UI behavior.

Online - Upgrade (2.1.486 and above to 3.5)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Always take a snapshot or backup of your Phantom instance before proceeding with any upgrade.

Update all currently installed OS packages, except tuned which currently causes a python package conflict:
::
	yum clean all
	yum update

Note: If the yum update results in a kernel upgrade, a restart of the instance is strongly recommended:
::
	shutdown -r now

If your system is on 2.1.486 or Phantom 3.0 versions prior to 3.0.284: Install the Phantom 3.0 repo package to obtain the upgrade script
::
	rpm -Uvh https://repo.phantom.us/phantom/3.0/base/6/x86_64/phantom_repo-3.0.284-1.x86_64.rpm

This will make the Phantom repositories available to yum, install the phantom upgrade script, and install/import the Phantom and dependency RPM GPG keys. Next, run the Phantom upgrade script:
::
	/opt/phantom/bin/phantom_setup.sh upgrade

You will be prompted for your login to my.phantom.us, not a local OS username and password.

Once your system is up to date with 3.0.284 or higher installed: Install the Phantom 3.5 repo package to obtain the upgrade script
::
	rpm -Uvh https://repo.phantom.us/phantom/3.5/base/6/x86_64/phantom_repo-3.5.210-1.x86_64.rpm

This will make the Phantom repositories available to yum, install the phantom upgrade script, and install/import the Phantom and dependency RPM GPG keys. Next, run the Phantom upgrade script:
::
	/opt/phantom/bin/phantom_setup.sh upgrade

You will be prompted for your login to my.phantom.us, not a local OS username and password.

You can also opt-out of upgrading the apps during the upgrade process. You can always upgrade apps and install new ones via the Phantom UI:
::
	/opt/phantom/bin/phantom_setup.sh upgrade --without-apps

You can also specify the exact version of Phantom you wish to install:
::
	/opt/phantom/bin/phantom_setup.sh upgrade --version=3.5.210-1
	
Offline - Upgrade (2.1.486 and above to 3.5)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Always take a snapshot or backup of your Phantom instance before proceeding with any upgrade.

Update all currently installed OS packages (assuming configured local repository connectivity to a RHEL or Centos mirror):
::
	yum clean all
	yum update

Note: If the yum update results in a kernel upgrade, a restart of the instance is strongly recommended:
::
	shutdown -r now
	
Offline upgrades are available for the following operating systems:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Red Hat Enterprise Linux 6.9, 7.4

* CentOS 6.9 (Official Phantom OVA), 7.4

**RHEL Only**:

**Since RHEL packages are from a commercial repository, Phantom does not supply required dependencies for installation. As a prerequisite to offline upgrade, you must follow the RedHat instructions to create a local repo or use a RedHat satellite server.**
::
	Follow the RHEL instructions here: https://access.redhat.com/solutions/29269
	As of the latest update of this KB, approaches #1 (RedHat Satellite Server) and #5 (Create a Local Repository) on the RedHat site are supported.

**Centos and RHEL**:

Contact Phantom support to obtain portal access to our offline upgrade packages. Once support has approved your account change, login to my.phantom.us and click on the "Product" button. You will see an option to download the offline setup tarball. Note there is a drop-down menu to select the tarball for your OS.
Download the appropriate tarball to your Phantom instance and untar it:
::
	mkdir /usr/local/src/upgrade-<version>
	cd /usr/local/src/upgrade-<version>
	tar -xvzf phantom_offline_setup_<OS>-<version>.tgz
	cd phantom_offline_setup_<OS>-<version>

To upgrade phantom, run:
::
	./phantom_offline_setup_<OS>.sh upgrade

By default phantom installs and upgrades most available apps during the installation process. If you do not wish any apps to be installed during installation, run this upgrade with the following parameter:
::
	./phantom_offline_setup_<OS>.sh upgrade --without-apps

Once the upgrade is complete, you can login to Phantom. 

Peer certificate cannot be authenticated with known CA certificates
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is caused most commonly by the system's clock being out of sync. This can happen when you take a snapshot of a virtual machine, as the clock is effectively frozen during the snapshot process. Forcing an NTP sync should resolve the problem:
::
	sudo service ntp stop
	sudo ntpd -gq
	sudo service ntp start
	
Upgrading Phantom - Versions up to 2.1.486
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Log on as the root user, either from the console or via SSH. If your Phantom VM was first created with 1.2GA or later, you may have to SSH as "user" first, then sudo su - to root. If you log on via the console, you will get the setup menu. Press 3 to get to the shell. Once at the shell, issue the following commands to clear and update the repo cache and then to perform the update:
::
	yum --enablerepo=phantom-base --enablerepo=phantom-product clean all
	yum --enablerepo=phantom-base --enablerepo=phantom-product update

If performing an upgrade between versions that points to a different Repo file, such as when upgrading from 2.0 to 2.1, the product update command will need to be run twice. The first time the product update command runs, it will update the Repo file. The second time the product update command runs it will use the updated Repo file to finish the Phantom upgrade.

The provided Yum commands do not update Phantom Apps. Updating an Ingestion App installed on the Phantom instance will cause the App to poll the Asset and query for all available data. Specifically in the case of Email Apps, but can apply to other ingestion Apps, the query for all available data can be a significant undertaking and take several days to complete. Upgrading Phantom Apps can be performed with Yum as documented below, or starting in 2.1 via the Phantom UI by navigating to Apps > App Updates.


**Note**: When upgrading from Phantom 2.0.291 to Phantom 2.1.x the Phantom upgrade process will prompt for acceptance of the Elasticsearch key. In future installations or upgrades of Phantom, this key will be signed by Phantom and a prompt for acceptance will not be shown.
::
	warning: rpmts_HdrFromFdno: Header V4 RSA/SHA512 Signature, key ID d88e42b4: NOKEY
	Retrieving key from file:///opt/phantom/etc/Phantom-GPG-KEY.public
	Retrieving key from file:///etc/pki/rpm-gpg/GPG-KEY-elasticsearch
	Importing GPG key 0xD88E42B4:
		Userid : Elasticsearch (Elasticsearch Signing Key) <dev_ops@elasticsearch.org>
		Package: phantom_repo-2.1.457-1.x86_64 (installed)
		From   : /etc/pki/rpm-gpg/GPG-KEY-elasticsearch
	Is this ok [y/N]: y

Installing all available updates

If you have not run a Yum update before as part of your regular maintenance of the virtual appliance, you will likely find that some CentOS updates are required as well. Phantom recommends that OS updates be applied regularly in order to consume security fixes.

**Note**: Updating an Ingestion App installed on the Phantom instance will cause the App to poll the Asset and query for all available data. Specifically in the case of Email Apps, but can apply to other ingestion Apps, the query for all available data can be a significant undertaking and take several days to complete.

Issue the following commands to install all available updates:
::
	yum --enablerepo=phantom-\* clean all
	yum --enablerepo=phantom-\* update

Proceed with the upgrade, and watch the output carefully for errors. Please note that there may be database upgrade steps as part of this process and it may take a significant amount of time.

If performing an upgrade between versions that points to a different Repo file, such as when upgrading from 2.0 to 2.1, the product update command will need to be run twice. The first time the product update command runs, it will update the Repo file. The second time the product update command runs it will use the updated Repo file to finish the Phantom upgrade.

Installing New Apps
^^^^^^^^^^^^^^^^^^^

The above steps will upgrade the Phantom platform version and Apps, but will not install any completely new Apps. You may also want to check if there are new Apps you do not have installed. Starting in Phantom 2.1, finding new Apps and upgrading Apps can be performed in the UI from the Apps page.
::
	yum --disablerepo=\* --enablerepo=phantom-apps list available

If, for example, the Panorama App was not installed, the command to install that is:
::
	yum --enablerepo=phantom-apps install phantom_panorama

The command to install all new Apps and update current the Apps is:
::
	yum --disablerepo=\* --enablerepo=phantom-apps install phantom_\*

**Note**: Updating an Ingestion App installed on the Phantom instance will cause the App to poll the Asset and query for all available data. Specifically in the case of Email Apps, but can apply to other ingestion Apps, the query for all available data can be a significant undertaking and take several days to complete.


**Note**: In releases prior to 2.0.264, there was an error in how grub.conf was created on the OVA. As a consequence, yum update would not update the right copy of grub.conf, and new kernel updates would never be used. If you upgraded to 2.0 from something earlier, you need to apply a manual fix to correct this. These commands can be dangerous if done incorrectly, potentially rendering your Phantom instance unbootable. We recommend you take a snapshot ahead of time just in case. Then run this command as root:
::
	rm /etc/grub.conf && ln -s /boot/grub/grub.conf /etc/grub.conf

The next kernel update after running this will then correctly modify grub.conf to use the newer kernel. 
