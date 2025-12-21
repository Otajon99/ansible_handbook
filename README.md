# Ansible Core Modules & Concepts (Homework)

This document explains common **Ansible modules and configuration options** one by one, in detail, with examples. It is suitable for **GitHub**, **EX294**, and **DevOps learning**.

---

## 1. `blockinfile`

### What it does
Manages **a block of multiple lines** in a file. Ansible adds special markers to track the block.

### Why use it
- Manage related configuration lines together
- Prevent duplicate blocks
- Idempotent behavior

### Example
```yaml
- name: Add Apache configuration block
  blockinfile:
    path: /etc/httpd/conf/httpd.conf
    block: |
      ServerAdmin admin@example.com
      Timeout 60
```

### Result in file
```conf
# BEGIN ANSIBLE MANAGED BLOCK
ServerAdmin admin@example.com
Timeout 60
# END ANSIBLE MANAGED BLOCK
```

### Use cases
- Web server configs
- Application config blocks

---

## 2. `lineinfile`

### What it does
Ensures **a single line** exists, is replaced, or removed in a file.

### Example
```yaml
- name: Disable root SSH login
  lineinfile:
    path: /etc/ssh/sshd_config
    regexp: '^PermitRootLogin'
    line: 'PermitRootLogin no'
```

### Use cases
- Change one configuration value
- Enforce security settings

---

## 3. `set_fact`

### What it does
Creates **variables dynamically at runtime**.

### Example
```yaml
- name: Set OS family variable
  set_fact:
    os_family: "{{ ansible_facts['os_family'] }}"
```

### Conditional example
```yaml
- name: Set web port based on OS
  set_fact:
    web_port: "{{ 80 if ansible_facts['os_family'] == 'RedHat' else 8080 }}"
```

### Use cases
- Dynamic logic
- Conditional configuration

---

## 4. `debug`

### What it does
Prints messages or variable values during play execution.

### Examples
```yaml
- debug:
    msg: "Apache installation started"
```

```yaml
- debug:
    var: ansible_facts['hostname']
```

### Use cases
- Troubleshooting
- Exam debugging

---

## 5. `get_url`

### What it does
Downloads files from a URL to a managed node.

### Example
```yaml
- name: Download application archive
  get_url:
    url: https://example.com/app.tar.gz
    dest: /opt/app.tar.gz
    mode: '0644'
```

### Useful options
- `checksum` – verify integrity
- `force: yes` – overwrite existing file

---

## 6. `authorized_key`

### What it does
Manages SSH public keys in a user's `authorized_keys` file.

### Example
```yaml
- name: Add SSH key for student user
  authorized_key:
    user: student
    key: "{{ lookup('file', '/home/student/.ssh/id_rsa.pub') }}"
```

### Use cases
- Passwordless SSH
- Secure automation

---

# Homework Concepts

## 7. `ignore_errors: true`

### What it does
Allows play execution to continue even if a task fails.

### Example
```yaml
- name: Remove optional file
  file:
    path: /tmp/old.txt
    state: absent
  ignore_errors: true
```

### Use carefully
- Good for non-critical tasks
- Avoid hiding important failures

---

## 8. `force_handlers: true`

### What it does
Ensures handlers run **even if the play fails**.

### Example
```yaml
- hosts: all
  force_handlers: true

  tasks:
    - name: Update config
      copy:
        src: app.conf
        dest: /etc/app.conf
      notify: restart app

    - name: Force failure
      command: /bin/false

  handlers:
    - name: restart app
      service:
        name: app
        state: restarted
```

---

## 9. `ansible.cfg` – `forks = 5`

### What it does
Controls how many hosts Ansible manages **in parallel**.

### Example
```ini
[defaults]
forks = 5
```

### Notes
- Default is 5
- Higher forks = faster execution
- Requires more CPU/RAM

---

## Quick Summary

| Item | Purpose |
|-----|--------|
| blockinfile | Manage multiple lines |
| lineinfile | Manage one line |
| set_fact | Create runtime variables |
| debug | Print variables/messages |
| get_url | Download files |
| authorized_key | Manage SSH keys |
| ignore_errors | Ignore task failure |
| force_handlers | Always run handlers |
| forks | Parallel execution control |

---

**Prepared for DevOps / EX294 / GitHub documentation**

