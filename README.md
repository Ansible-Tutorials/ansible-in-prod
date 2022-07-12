# Ansible
O Ansible é uma ferramenta de automação usada em larga escala em diversos projetos e empresas ao redor do mundo. É uma base para construir e operar a automação em uma organização. A plataforma inclui todas as ferramentas necessárias para implementar a automação em toda a empresa. Aumente a automação, gerencie implantações complexas e acelere a produtividade com uma plataforma de automação empresarial que pode ser usada por equipes inteiras de TI. Este documento destina-se a ser usado como um documento de orientação para nível de especialização iniciante/intermediário.

## Sumario

- [Ansible](#ansible)
  - [Sumario](#sumario)
    - [Boas praticas](#boas-praticas)
    - [Ansible Ad-hoc](#ansible-ad-hoc)
      - [Ping module](#ping-module)
      - [Copy module](#copy-module)
      - [Command module](#command-module)
      - [Shell module](#shell-module)
      - [Yum module](#yum-module)


### Boas praticas 
Tenho uma experiencia bacana com Ansible em atuacoes dentro de projetos e empresas e para ajudar mais pessoas que estao comecando com a ferramenta, escrevi um artigo de boas praticas do Ansible, focados nas principais features e caracteristicas da ferramenta, vale demais a leitura. O artigo esta publicado no Medium e encontra-se disponivel [aqui](https://amaurybsouza.medium.com/as-boas-pr%C3%A1ticas-do-ansible-que-ningu%C3%A9m-te-conta-e-que-n%C3%A3o-existem-no-google-4fcc3126ad1).

### Ansible Ad-hoc
Ansible Ad-hoc commands, sao comandos executados via CLI usando os modulos do Ansible. Os comandos sao uteis para nosso dia a dia, no gerenciamento de configuracao de projetoe e demais atividades. Vamos usar alguns exemplos de uso do Ansible Ad-hoc para comandos mais comuns do dia a dia.

#### Ping module
Usado em MUITAS situações antes mesmo de executar qualquer atividade com Ansible, o modulo [ping](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ping_module.html) fornece um comando de `ping` para o host que esta sendo acessado.

- Nesse exemplo abaixo estou usando esse modulo para verificar a disponibilidade dos meus nodes do cluster de Kubernetes:

`# ansible k8scluster -m ping -i inventory.yml`

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

- Escolhendo apenas um dos nodes para gerencia de configuracao:

`# ansible k8s -m ping -i inventory.yml`

```bash
k8s | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

#### Copy module
Usado para gerenciamento de arquivos dentro do Ansible, esse modulo ajuda demais no dia a dia. Imagina o poder do SCP com o Ansible, para isso temos modulo `copy`.

- Copiando o arquivo de hosts para outro diretorio do sistema:

`# ansible k8s -m copy -a "src=/etc/hosts dest=/tmp/hosts -i inventory`

```bash
k8s | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": true,
    "checksum": "f2f3b68db455edc39c07d95e2e49a09c2b8edfe2",
    "dest": "/tmp/hosts",
    "gid": 0,
    "group": "root",
    "md5sum": "e37656c96ab039eebc722da8bdd64c8f",
    "mode": "0644",
    "owner": "root",
    "secontext": "unconfined_u:object_r:admin_home_t:s0",
    "size": 659,
    "src": "/root/.ansible/tmp/ansible-tmp-1657627785.0906672-921-140613648946906/source",
    "state": "file",
    "uid": 0
}
```

#### Command module
Modulo muito usado para executar comandos aleatorios dentro do Ansible.

- Verificando as permissoes do arquivo hosts:

`# ansible k8s -b -m command -a "ls -l /tmp/hosts" -i inventory.yml`

```bash
k8s | CHANGED | rc=0 >>
-rw-r--r--. 1 root root 659 Jun 30 21:34 /tmp/hosts
```

#### Shell module
Outro modulo de extremo uso dentro do Ansible para muitos comandos e scripts baseados no sistema Linux.

- Obtendo o `uptime` do sistema:

`# ansible k8s -b -m shell -a "uptime" -i inventory.yml`

```bash
k8s | CHANGED | rc=0 >>
 22:13:06 up 6 days, 11:22,  1 user,  load average: 0.66, 0.83, 0.87
```

- Executando um Shell Script:

```bash
#!/bin/bash

echo Amaury
```

`# ansible k8s -m shell -a "/tmp/print_name.sh" -i inventory.yml`

```yml
k8s | CHANGED | rc=0 >>
Amaury
```

#### Yum module
Modulo bastante usados para gerenciamento de pacotes baseados no CentOS.

`# ansible k8s -b -m yum -a "name=net-tools state=present" -i inventory.yml`

```bash
k8s | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "msg": "",
    "rc": 0,
    "results": [
        "net-tools-2.0-0.25.20131004git.el7.x86_64 providing net-tools is already installed"
    ]
}
```
