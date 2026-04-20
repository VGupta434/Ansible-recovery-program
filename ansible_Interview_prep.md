# Ansible Interview prep 



---

## day-002

### Q1: What are facts in Ansible?

Facts are automatically gathered system information about managed nodes, such as OS type, IP address, hostname, memory, etc. Ansible collects them using the setup module and stores them as variables (called ansible_facts) which can be used in playbooks for dynamic decision-making.

💡 Example:
```yaml
ansible_os_family
ansible_hostname
ansible_default_ipv4.address
```
### Q2: What is when used for?

The when clause is used to apply conditional logic in Ansible, allowing tasks to run only when a specified condition is true.

💡 Example:
```yaml
- name: Install multiple packages on appservers
  hosts: app_servers
  become: yes
  vars:
    packages:
    - nginx
    - git
    - curl
    firewall_services:
    - http
    - https
  tasks:
  - name: print system info
    debug:
      msg : "Running playbook on {{ ansible_hostname }} with IP {{ ansible_default_ipv4.address }}"
  - name: install packages
    dnf:
      name: "{{ item }}"
      state: present
    loop: "{{ packages }}"
    when: ansible_os_family == "RedHat"
```

👉 This ensures task runs only on RedHat systems.

### Q3: Why use loop instead of multiple tasks?

Loops are used to avoid repetition and make playbooks more concise and maintainable. Instead of writing multiple similar tasks, a loop allows us to iterate over a list of items in a single task.

💡 Example:
```yaml
loop:
  - nginx
  - git
  - curl
```

👉 This replaces 3 separate tasks.

### Q4: What is Jinja2?

Jinja2 is a templating engine used in Ansible for variable interpolation and dynamic content generation. It allows us to use variables, expressions, and logic inside playbooks and templates using {{ }} syntax.

💡 Example:
```yaml
name: "{{ package_name }}"
```
---

## day-003

### Q1: What is the template module?

The template module is used to generate dynamic configuration files using Jinja2 templates. It copies a .j2 file from the control node to the managed node and replaces variables at runtime.

```yaml
template:
  src: nginx.conf.j2
  dest: /etc/nginx/conf.d/custom.conf
```
👉 Used for:

1. config files
2. environment-specific settings
3. dynamic values

### Q2: Why use handlers instead of tasks?

Handlers are used to perform actions like restarting services only when a change occurs. Unlike normal tasks, handlers run only when notified, which avoids unnecessary executions and improves efficiency.

:Example:
```yaml
notify: restart nginx
```

👉 Benefit:

1. Prevents unnecessary restarts
2. Maintains idempotency

### Q3: What happens if nginx config is invalid?

If the nginx configuration is invalid, the service will fail to restart and may go down. This can cause application downtime. That’s why it’s important to validate the configuration before restarting the service.

### Q4: Why validate config before restart?

Validating the configuration using commands like ```nginx -t``` ensures there are no syntax errors before applying changes. This prevents service failure and ensures safe deployments in production.

## day-004

### Q1: What is a role in Ansible?

A role is a structured way of organizing Ansible content such as tasks, handlers, variables, templates, and files into a reusable and modular unit. It helps in managing complex automation by separating concerns and improving maintainability.

### Q2: Difference between vars and defaults?

The main difference is priority:

```defaults``` → lowest priority, can be easily overridden

```vars``` → higher priority, harder to override

### Q3: Why use roles instead of playbooks?

Roles help in structuring Ansible code in a modular and reusable way. Instead of writing everything in a single playbook, roles allow separation of concerns, making the code easier to maintain, reuse, and scale in large projects.

### Q4: Where do you store templates in a role?

Templates are stored in the templates/ directory inside the role, and they are typically Jinja2 (.j2) files used with the template module.