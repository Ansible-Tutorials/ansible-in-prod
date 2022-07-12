# Ansible

## Sumario

- [Ansible](#ansible)
  - [Sumario](#sumario)
    - [Ansible Ad-hoc](#ansible-ad-hoc)
      - [Modulo Ping](#modulo-ping)


### Ansible Ad-hoc

Vamos usar alguns exemplos de uso do Ansible Ad-hoc para comandos mais comuns do dia a dia.

#### Modulo Ping

`# ansible kubernetes -m ping -i inventory.yml`

```bash
node3 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
k8s | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
node2 | SUCCESS => {  
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
node1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```


