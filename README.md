
Ansible Role for pkcs11
=========

Example Requirements
--------------------

```yml
---
roles:
  - name: ansible-role-pkcs11
    src: git+https://github.com/moukail/ansible-role-pkcs11.git
    version: main
  - name: ansible-role-softhsm
    src: git+https://github.com/moukail/ansible-role-softhsm.git
    version: main

```

Example Playbook
----------------

```yaml
---
- name: Install pkcs11
  hosts: all
  become: true

  vars:
    softhsm_version: 2.7.0

  tasks:
    - name: Install SoftHSM
      ansible.builtin.include_role:
        name: ansible-role-softhsm

    - name: Configure pkcs11
      ansible.builtin.include_role:
        name: ansible-role-pkcs11

#####################  pkcs11 commands  ###############################

[vagrant@openbao ~]$ rpm -ql softhsm | grep -E "so$"
[vagrant@openbao ~]$ find /usr -name "libsofthsm*.so" 2>/dev/null

[vagrant@openbao ~]$ softhsm2-util --show-slots

[vagrant@openbao ~]$ softhsm2-util --init-token \
  --slot 0 \
  --label OpenBao \
  --so-pin 1234 \
  --pin 4321

[vagrant@openbao ~]$ pkcs11-tool --module /usr/lib64/pkcs11/libsofthsm2.so -L

[vagrant@openbao ~]$ pkcs11-tool \
  --module "/lib64/softhsm/libsofthsm2.so" \
  --token-label "OpenBao" \
  --pin 4321 \
  --keypairgen \
  --key-type rsa:4096 \
  --label "bao-root-key-rsa"