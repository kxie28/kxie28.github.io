Active Directory Info
=====================

Active Directory is a collection of technoligies used to manage a group or users and computers. This collection of users and computers falls under a **domain**. 

Typically, compoanies will have their Active Directory domain as a subdomain of their main domain for exmaple, **ad.company.com**

Another term is Forest**, which is a collection of domains. A domain is always part of a forest even if there's just one domain.

Services that make an Active Directory Domain
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**DNS** 
DNS is absolutely vital for Active Directory to work. Active Directory relies on a series of DNS records to establish what services are available on the domain and who provides what. For the most part, these records are management automatically, all we need to worry about right now is making sure DNS is available.

**LDAP** 
Light weight Directory Access Protocol. This service is responsible for keeping track of what is on the network

**Kerberos** 
Kerberos handles Single Sign On throughout the domain. It is what allows you to use one username and password to log into multiple computers throughout the domain.

**SMB** 
SMB is used to share files throughout a domain. Domain Controllers use it to share group policy objects among other things.


