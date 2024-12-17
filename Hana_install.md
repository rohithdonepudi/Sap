SAP HANA Database Server Installation Playbook


---

Overview:

This Ansible playbook automates the installation and setup of the SAP HANA Database Server on the target host. It ensures all necessary system packages, libraries, and Python dependencies are installed and configured before invoking the SAP HANA installation role.


---

Playbook Description

Playbook Name: sap-hana-install.yml

This playbook performs the following:

1. Installs required system packages and libraries.


2. Ensures Python and necessary dependencies are installed for Ansible module execution.


3. Includes external variables from a defined file.


4. Executes the SAP HANA installation role.




---

Playbook Content

Main Playbook Structure

- name: Ansible Play for SAP HANA Database Server installation
  hosts: Hana1
  become: true
  any_errors_fatal: true
  max_fail_percentage: 0

  pre_tasks:
    - name: Install Python package manager pip3 to system Python
      ansible.builtin.package:
        name: python3-pip
        state: present

    - name: Install libatomic1 package
      zypper:
        name: 
          - libatomic1
          - insserv
          - acl
        state: present

    - name: Install libltdl7 package
      zypper:
        name: libltdl7
        state: present

    - name: Ensure OS Packages for lxml are installed
      ansible.builtin.package:
        name:
          - python3-lxml
          - libxslt-devel
          - libxml2-devel
        state: present

    - name: Install Python dependency wheel to system Python
      ansible.builtin.pip:
        name:
          - wheel

    - name: Install Python dependencies for Ansible Modules to system Python
      ansible.builtin.pip:
        name:
          - urllib3
          - requests
          - beautifulsoup4
          - lxml

    - name: Include variables
      ansible.builtin.include_vars: /home/ubuntu/ansible/playbooks/vars/sample-variables-sap-hana-install.yml

  roles:
    - { role: /home/ubuntu/ansible/roles-community-sap_install/roles/sap_hana_install }


---

Tasks Breakdown

1. Install Python Package Manager (pip3)
Ensures that pip3 is installed to manage Python packages on the target system.

- name: Install Python package manager pip3 to system Python
  ansible.builtin.package:
    name: python3-pip
    state: present


2. Install Required OS Packages
Installs critical system packages like libatomic1, insserv, acl, and libltdl7 using the zypper module (specific to SUSE Linux).

- name: Install libatomic1 package
  zypper:
    name: 
      - libatomic1
      - insserv
      - acl
    state: present

- name: Install libltdl7 package
  zypper:
    name: libltdl7
    state: present


3. Ensure OS Packages for lxml Are Installed
Installs libraries required for XML parsing and processing.

- name: Ensure OS Packages for lxml are installed
  ansible.builtin.package:
    name:
      - python3-lxml
      - libxslt-devel
      - libxml2-devel
    state: present


4. Install Python Dependencies

Installs wheel for building Python packages.

Installs essential Python libraries like urllib3, requests, beautifulsoup4, and lxml to ensure compatibility with Ansible modules.


- name: Install Python dependency wheel to system Python
  ansible.builtin.pip:
    name:
      - wheel

- name: Install Python dependencies for Ansible Modules to system Python
  ansible.builtin.pip:
    name:
      - urllib3
      - requests
      - beautifulsoup4
      - lxml


5. Include External Variables
Imports variables defined in an external file to manage configurations dynamically.

- name: Include variables
  ansible.builtin.include_vars: /home/ubuntu/ansible/playbooks/vars/sample-variables-sap-hana-install.yml


6. Execute SAP HANA Installation Role
Invokes the sap_hana_install role to handle the installation of SAP HANA on the target system.

- name: SAP HANA Installation Role
  roles:
    - { role: /home/ubuntu/ansible/roles-community-sap_install/roles/sap_hana_install }




---

Variables

External variables are defined in the file:
Path: /home/ubuntu/ansible/playbooks/vars/sample-variables-sap-hana-install.yml

Example Variables:

sap_hana_install_media: "/path/to/hana/install/media"
sap_hana_sid: "HDB"
sap_hana_instance_number: "00"
sap_hana_master_password: "YourStrongPassword"
sap_hana_system_usage: "production"


---

Prerequisites

1. Ansible Control Node:

Ansible must be installed on the control node.

Required roles (e.g., sap_hana_install) must be available in the defined path.



2. Target Host (Hana1):

The target system must be running a supported SUSE Linux distribution (SLES for SAP).

The system must have root privileges for installation.



3. SAP HANA Installation Media:

Ensure that the SAP HANA installation files are accessible on the target system or via a shared location.



4. Network and SSH Access:

The control node must have SSH access to the target host.





---

Execution

Run the playbook with the following command:

ansible-playbook -i inventory_file sap-hana-install.yml


---

Verification

After successful execution:

1. Check the SAP HANA installation logs on the target system.


2. Validate the installation by accessing the SAP HANA instance:

hdbsql -i <instance_number> -d SYSTEMDB -u SYSTEM -p <password>




---

Troubleshooting

Missing Packages: Ensure the zypper package manager is available and the repositories are configured.

Connection Issues: Verify the inventory file, SSH access, and become privileges.

Role Not Found: Ensure the sap_hana_install role exists in the specified path.



---

Conclusion

This playbook automates the SAP HANA installation process, ensuring all dependencies and prerequisites are met for a successful deployment. It simplifies system setup and reduces manual intervention.

