Playbook to Delete the hdbadm User if it Exists


---

Overview

This Ansible playbook checks if the hdbadm user exists on a target system and deletes the user if it is present. It ensures clean removal without errors when the user does not exist.


---

Playbook Description

Playbook Name: delete-hdbadm-user.yml

This playbook performs the following tasks:

1. Check if the hdbadm user exists using the id command.


2. Delete the hdbadm user only if the user exists.




---

Playbook Content

---
- name: Delete hdbadm user if it exists
  hosts: test1
  become: yes

  tasks:
    - name: Check if hdbadm user exists
      command: "id hdbadm"
      ignore_errors: true
      register: hdbadm_user_check

    - name: Delete hdbadm user
      user:
        name: hdbadm
        state: absent
      when: hdbadm_user_check.rc == 0


---

Tasks Breakdown

1. Check if hdbadm User Exists:

Executes the id command to check for the presence of the hdbadm user.

If the user does not exist, the task will fail, but ignore_errors: true allows the playbook to proceed without stopping.

The result of the command is stored in the hdbadm_user_check variable.


- name: Check if hdbadm user exists
  command: "id hdbadm"
  ignore_errors: true
  register: hdbadm_user_check


2. Delete the hdbadm User:

Uses the user module to delete the hdbadm user if it exists.

The condition when: hdbadm_user_check.rc == 0 ensures this task only runs if the id command returns a success (rc == 0).


- name: Delete hdbadm user
  user:
    name: hdbadm
    state: absent
  when: hdbadm_user_check.rc == 0




---

Variables


---

Prerequisites

1. Ansible Control Node:

Ansible must be installed on the control node.



2. Target Host (test1):

The host must be accessible via SSH.

Sudo/root privileges are required to delete the user.



3. Inventory File:

Ensure the test1 host is defined in your Ansible inventory file.





---

Execution

Run the playbook with the following command:

ansible-playbook -i inventory_file delete-hdbadm-user.yml

Replace inventory_file with your Ansible inventory file.


---

Verification

After running the playbook:

1. Verify the hdbadm user is deleted by running the following command on the target host:

id hdbadm

The output should indicate the user does not exist.


2. Review the Ansible output for successful task execution.




---

Troubleshooting

Connection Errors: Ensure SSH access and proper inventory configuration.

Permission Denied: Verify become: yes is working, and the control node user has sudo privileges.

Errors While Running id:

The ignore_errors: true ensures the playbook continues execution even if the user does not exist.




---

Conclusion

This playbook provides an automated and error-tolerant method to check for and delete a specific user (hdbadm). It is safe to execute on systems where the user might or might not exist, ensuring idempotency and clean task execution.

