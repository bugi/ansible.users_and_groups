# Users\_And\_Groups

This role allows for treating users as data.
It combines management of users, group and
ssh public keys.  All of the
[user module](http://docs.ansible.com/user_module.html)
parameters are optional except `name` and `ssh_keys`.

All v1.7 users parameters are supported except a few.  They will be supported
once the `default(omit)` feature is released.  Support for these are presently
omitted:

* login\_class
* ssh\_key\_\*
* group


## Role Variables

There are two variables, `users_and_groups` and `users_and_groups_defaults`.
You can probably ignore the latter.  You can view their defaults in
[defaults/main.yml](defaults/main.yml).


## Examples

### example 1

Here's a likely playbook.  I'll get to the vars\_files later:

```yaml
---

- hosts: all
  sudo: True
  vars_files:
    - vars/users/alice.yml
    - vars/users/bob.yml
  roles:
    - { role: users_and_groups , users_and_groups: "{{ users_and_groups }}" }
```

You'd probably define the `users_and_groups` variable in a host\_vars file.


### example 2

Here's pretty much the same thing, but inline.  From this you can figure
out what to put in that host\_vars file.

```yaml
- hosts: all
  sudo: True
  vars_files:
    - vars/users/alice.yml
    - vars/users/bob.yml
  vars:
    users_and_groups_default:
      shell: /bin/bash
      home_prefix: /home
  roles:
    - role: users_and_groups
      users_and_groups:
        users:
          - "{{ user_alice }}"
          - "{{ user_bob }}"
        users_removed:
          - name: cindy
            remove: yes
        groups: []
        groups_removed: []
```

### example 3

Here's the vars/users/alice.yml file:

```yaml
---

# Each user is an assoc array consisting of
#       name
#       ssh_keys (a list)
#       revoked_ssh_key_files (a list)
#       and a lot of optional stuff
# see also:
#       http://docs.ansible.com/user_module.html
#       http://docs.ansible.com/authorized_key_module.html

user_alice:
  name: alice
  uid: 1000
  groups: [ adm , sudo ]
  append: yes
  password: '$6$encrypted_password'
  shell: /bin/bash
  update_password: on_create
  ssh_keys:
    - file: /etc/ansible/assets/public_keys/alice_0.pub
      state: present
```


## Notes

Patches welcome...


## Dependencies

None


## License

MIT


## Author Information

```yaml
- name: Brian Grossman
  github: https://github.com/bugi
```


## Influences

*	https://github.com/ANXS/generic-users
*	https://github.com/ajsalminen/ansible-role-user_management
*	https://github.com/debops/ansible-users/blob/master/tasks/users.yml
*	https://github.com/ansible/ansible/issues/8233
*	https://groups.google.com/forum/#!msg/ansible-project/W9bG0tAI3aU/GFtrD9Ao9EwJ
