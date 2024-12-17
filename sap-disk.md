---

Documentation for sap-disk.yml Ansible Playbook: Complete Infrastructure Setup

Overview:

This Ansible playbook is used to set up and configure the infrastructure required for a SAP HANA environment. The playbook defines storage setup for SAP HANA, mounts storage volumes, and applies appropriate configurations. It is designed to automate the provisioning and management of disk storage for the SAP system.

Playbook Structure:

1. Play Header:

- name: Complete Infrastructure setup
  hosts: all
  become: true
  any_errors_fatal: true

name: The name of the playbook, describing its purpose (i.e., complete infrastructure setup).

hosts: Specifies that this playbook runs on all targeted hosts.

become: Ensures that tasks are executed with elevated privileges (sudo).

any_errors_fatal: If any task in the playbook fails, the execution will stop immediately.


2. Variables Section:

vars:
  sap_system_sid: "S01"
  sap_system_hana_db_sid: "H01"
  sap_system_hana_db_instance_nr: "90"
  sap_storage_setup_definition: hana_primary
  sap_storage_setup_host_type:
    - hana_primary
  sap_storage_setup_definition:

sap_system_sid: Defines the SAP system identifier (SID) for the environment.

sap_system_hana_db_sid: Defines the SID for the SAP HANA database.

sap_system_hana_db_instance_nr: Specifies the instance number for the SAP HANA database.

sap_storage_setup_definition: This variable holds the definitions of the different storage mounts, including file systems and disk sizes.

sap_storage_setup_host_type: Specifies the type of host for the storage setup (in this case, hana_primary).


3. Storage Setup:

The playbook defines several storage volumes, including mount points and disk configurations. Each storage configuration specifies the disk size, file system type, and mount point.

- name: hana_data
  mountpoint: /hana/data
  disk_size: 32
  filesystem_type: xfs

- name: hana_log
  mountpoint: /hana/log
  disk_size: 32
  filesystem_type: xfs

- name: hana_shared
  mountpoint: /hana/shared
  disk_size: 64
  filesystem_type: xfs

- name: usr_sap
  mountpoint: /usr/sap
  disk_size: 32
  filesystem_type: xfs

- name: swap
  swap_path: /swapfile
  disk_size: 2
  filesystem_type: swap

hana_data: This storage is mounted at /hana/data and is used for SAP HANA data. It is 32GB with an XFS file system.

hana_log: Mounted at /hana/log for SAP HANA logs. This storage is 32GB with an XFS file system.

hana_shared: Mounted at /hana/shared, it is shared storage for SAP HANA with a disk size of 64GB and an XFS file system.

usr_sap: Mounted at /usr/sap, used for SAP-related files, with a 32GB disk and XFS file system.

swap: Defines the swap file at /swapfile with a size of 2GB. The swap file uses the swap file system.


4. Optional Storage Configuration:

# - name: software
#   mountpoint: /software
#   disk_size: 100
#   filesystem_type: xfs

This section is commented out and defines an optional storage configuration for mounting a software volume. The size is 100GB and the file system type is XFS.

5. Task Execution:

tasks:
  - name: Execute Ansible Role sap_storage_setup
    ansible.builtin.include_role:
      name: { role: /home/ubuntu/ansible/roles-community-sap_install/roles/sap_storage_setup }

name: This task executes the sap_storage_setup Ansible role, which is responsible for configuring the SAP storage as defined in the sap_storage_setup_definition variable.

include_role: This module is used to include the sap_storage_setup role located at the specified path. The role is responsible for carrying out the tasks such as partitioning and formatting the disks, creating the necessary mount points, and configuring the file systems.



---

How It Works:

1. The playbook is executed on all specified hosts (defined under hosts).


2. It uses sudo privileges to run tasks that require elevated permissions.


3. The variables section defines SAP HANA-related system configurations and storage parameters.


4. Storage is set up for various directories like /hana/data, /hana/log, /usr/sap, and swap.


5. The role sap_storage_setup is executed, which provisions the storage as per the defined configurations.



Usage:

1. Make sure the required disks are available on the target system.


2. Place the Ansible playbook on the management server or any other host with access to the target system.


3. Execute the playbook using the ansible-playbook command:

ansible-playbook -i inventory_file complete_infrastructure_setup.yml




---

This document provides a clear and comprehensive overview of the playbook's purpose and functionality, making it easier to understand and maintain the setup.

