Role Name
=========

This role will configure a Spine/Leaf environment with BGP, VXLAN Active-Active and MLAG according to nVidia documentation you can find here: https://docs.nvidia.com/networking-ethernet-software/cumulus-linux-54/Network-Virtualization/VXLAN-Active-Active-Mode/

The role will be able to configure N Spine connection to N Leaves. It will also be able to set uplinks (vLAN Aware) with or without a Bond to another equipment.

The role will also accept Site as configuration in the host to use Site dependant configuration such as Loopback IP, ASN, Hostnames, NTP Servers, ...

Requirements
------------

Tested with Cumulus Linux 5.4 using NVUE

Role Variables
--------------

Inventory

The inventory should have a group for the spine and for the leaf and one named cumulus to set the variable on the two groups.

Some variable are required in the inventory such as :
  domain: The domain that will be use for the FQDN when setting dynamically the hostnames of the nodes
  site: Name of the site. This will also be used in the hostname and will get a configuration for the current site. The role will try to find a configuration files in the vars folder named site_[name of your site].yml (see below)

For each host, here's the variable that can be used :
  bonds: To be used with mlag_shared_id. This value will tell which switchport to create a bond on. You can use more than one value separated by comma.
  uplinks: Switchport list to create an uplink (vLAN Aware). You can use more than one value separated by comma.

Inventory sample:

[spine]
172.17.0.10
172.17.0.11

[leaf]
172.17.0.20 mlag_pair_id=2 bonds=swp8,swp9
172.17.0.21 mlag_pair_id=1 bonds=swp8,swp9
172.17.0.22 uplinks=swp8

[cumulus:children]
spine
leaf

[cumulus:vars]
ansible_ssh_user=ansible
domain=Lab.local
site=AM

Main variable file (main.yml)

The sample in the project will have all required variable. The file is documented to explained the roles of the variables.

Site specific variable file (site_yoursite.yml)

Theses files are to setup some variable that will be specific to a specific site. You can set NTP servers and a site id.
The site id will be used in many configuration to get different configuration for each site, it will be used for the BGP and for the mLAG

There's a default site that contain the default variable, if the site provided in the inventory file doesn't exist in the vars folder, the default file will set the variables for the configuration. As the default file have all the required variables, you can change just a part of the configuration for a specific site. By example, if you set only the first NTP server in the site configuration, the second will be the one from the default site.

Switch specific variable (switch_product_name.yml)

Theses variables files will define switch specific information such as port quantity, which port to use for the spine/leaf connection, peerlink, etc.

The playbook will automatically look for a file named with the product name from the facts (ansible_facts['product_name']).


Dependencies
------------

None

Example Playbook
----------------

---

- hosts: cumulus
  gather_facts: true
  roles:
    - spine-leaf


License
-------

BSD

Author Information
------------------

Gilles Auclair
gauclair@sarius.ca
Sarius
http://www.sarius.ca/
