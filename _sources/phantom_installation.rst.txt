Installing Phantom 3.5
======================

Important notes before starting:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Users who wish to install Phantom must have a portal account on my.phantom.us. The install script below will prompt you for a username and password for portal authentication. 

	- This is your login to my.phantom.us, not a local OS username and password.

* Access to the Phantom RPM bundles used for offline or AWS installs are limited to paid customers and users in POV (Proof of Value) evaluation. Paid customers may email support@phantom.us for access to the RPM bundles and users in POV will need to request access from their Sales Person or Sales Engineer.

* Upgrades to or from Early Access or Beta releases are not supported.

Phantom officially supports the Phantom OVA or official supplied minimal images for the following operating systems:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Red Hat Enterprise Linux 6.9

* Red Hat Enterprise Linux 7.4

* CentOS 6.9

* CentOS 7.4

* AWS: CentOS 7 (x86_64) - with Updates HVM - Available in the AWS Marketplace, "Sold by Centos"

* Azure: Red Hat Enterprise Linux 7.4 - Provided by Red Hat

Phantom minimum hardware specifications:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Production instance minimum supported hardware specifications

	* 4 CPU cores

	* 16GB RAM

	* 500GB disk space

* Non-production instance minimum supported hardware specifications

	* 2 CPU cores

	* 4GB RAM

	* 50GB disk space
	
**Phantom OVA**
^^^^^^^^^^^^^^^

You can download the latest Phantom distribution as a Virtual machine image (OVA). This is quickest path to get Phantom installed and running. These virtual machine images can be imported directly from various virtualization products such as VMWare Fusion, ESXI, and VirtualBox. Simply download the OVA package from the Phantom portal, and use the "Import" option in the product of your choice. Be sure to follow the minimum CPU/RAM recommendations at the top of this KB. 

Online - CentOS or RHEL on onprem or AWS instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Proceed with a standard installation of the operating system.

Update all currently installed OS packages, except for tuned which currently creates a conflict with pip packages: ::

	yum clean all
	yum update

**Note**: If the yum update results in a kernel upgrade, a restart of the instance is strongly recommended:
::

	shutdown -r now


Install the phantom repo package to obtain the installation script: ::

	rpm -Uvh https://repo.phantom.us/phantom/3.5/base/6/x86_64/phantom_repo-3.5.210-1.x86_64.rpm

This will make the phantom repositories available to yum, install the phantom installation script, and install/import the Phantom and dependency RPM GPG keys. Next, run the phantom installation script:
::

	/opt/phantom/bin/phantom_setup.sh install

**The script will prompt for login and password information.**  
.. NOTE::
	This is your login to my.phantom.us, not a local OS username and password.

You can also opt-out of installing the apps during the upgrade process. You can always upgrade apps and install new ones via the Phantom UI:
::

	/opt/phantom/bin/phantom_setup.sh install --without-apps

Online - CentOS or RHEL on onprem or AWS instance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Update all currently installed OS packages (assuming configured local repository connectivity to a RHEL or Centos mirror):
::

	yum clean all
	yum update

**Note**: If the yum update results in a kernel upgrade, a restart of the instance is strongly recommended:
::

	shutdown -r now


Offline installations are available for the following operating systems:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Red Hat Enterprise Linux 6.9, 7.4

* CentOS 6.9 (Official Phantom OVA), 7.4

RHEL Only:
^^^^^^^^^^

**Since RHEL packages are from a commercial repository, Phantom does not supply required dependencies for installation. As a prerequisite to offline installation, you must follow the RedHat instructions to create a local repo or use a RedHat satellite server.**
::

	Follow the RHEL instructions here: https://access.redhat.com/solutions/29269
	As of the latest update of this KB, approaches #1 (RedHat Satellite Server) and #5 (Create a Local Repository) on the RedHat site are supported.

CentOS and RHEL:
^^^^^^^^^^^^^^^^

Contact Phantom support to obtain portal access to our offline installation packages. Once support has approved your account change, login to my.phantom.us and click on the "Product" button. You will see an option to download the offline setup tarball. Note there is a drop-down menu to select the tarball for your OS. Download the appropriate tarball to your Phantom instance and untar it:
::
	mkdir /usr/local/src/upgrade-<version>
	cd /usr/local/src/upgrade-<version>
	tar -xvzf phantom_offline_setup_<OS>-<version>.tgz
	cd phantom_offline_setup_<OS>-<version>

To install phantom, run:
::
	./phantom_offline_setup_<OS>.sh install

By default phantom installs and upgrades most available apps during the installation process. If you do not wish any apps to be installed during installation, run with the following parameter:
::
	./phantom_offline_setup_<OS>.sh install --without-apps

Once the install is complete, you can login to Phantom. 
