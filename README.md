# Ansible
O Ansible é uma ferramenta de automação usada em larga escala em diversos projetos e empresas ao redor do mundo. É uma base para construir e operar a automação em uma organização. A plataforma inclui todas as ferramentas necessárias para implementar a automação em toda a empresa. Aumente a automação, gerencie implantações complexas e acelere a produtividade com uma plataforma de automação empresarial que pode ser usada por equipes inteiras de TI. 

## Sumario

- [Ansible](#ansible)
  - [Sumario](#sumario)
    - [Good practices](#good-practices)
    - [Ansible on AWS](#ansible-on-aws)
    - [Ansible Inventory file](#ansible-inventory-file)
      - [Inventory in `YAML` format](#inventory-in-yaml-format)
      - [Graph Inventory](#graph-inventory)
    - [Ansible Ad-hoc](#ansible-ad-hoc)
      - [Ping module](#ping-module)
      - [Copy module](#copy-module)
      - [Command module](#command-module)
      - [Shell module](#shell-module)
      - [Yum module](#yum-module)
      - [Systemd module](#systemd-module)
      - [Setup module](#setup-module)
    - [Conditional Tasks](#conditional-tasks)
    - [Loops](#loops)
    - [Ansible Playbooks](#ansible-playbooks)
      - [Playbook format](#playbook-format)
      - [Playbook example](#playbook-example)
      - [Playbooks checking](#playbooks-checking)
    - [Ansible Facts](#ansible-facts)
      - [Fact example](#fact-example)
    - [Ansible Roles](#ansible-roles)
      - [The Roles structure](#the-roles-structure)
      - [How to create Ansible Roles?](#how-to-create-ansible-roles)
      - [Ansible Role example](#ansible-role-example)
      - [How to execute Ansible Roles?](#how-to-execute-ansible-roles)


### Good practices 
Tenho uma experiencia trabalhando com Ansible em atuacoes dentro de projetos e empresas, e para ajudar mais pessoas que estao comecando com a ferramenta, escrevi um artigo de boas praticas do Ansible, focado nas principais features e caracteristicas da ferramenta, vale demais a leitura. O artigo esta publicado no Medium e encontra-se disponivel [aqui](https://amaurybsouza.medium.com/as-boas-pr%C3%A1ticas-do-ansible-que-ningu%C3%A9m-te-conta-e-que-n%C3%A3o-existem-no-google-4fcc3126ad1).

### Ansible on AWS
Aproveitando esse conteúdo sobre Ansible, gostaria de compartilhar também mais um artigo que mostra o uso do Ansible na AWS, como podemos provisionar uma infraestrutura com Andible, assim como é possível com Terraform. O artigo esta postado no Medium e você pode acessar ele por [aqui](https://amaurybsouza.medium.com/ansible-2b38be85b704).

### Ansible Inventory file
Arquivo utilizado para descrever todos os `hosts` que serao afetados pelo Ansible, podendo ser servidores, banco de dados, switches, e outros ativos de rede.

#### Inventory in `YAML` format
Para manter a padronizacao com os playbooks e manter sempre o uso de codigos, recomenda-se que seu arquivo de inventario seja formatado e escrito com `YML`.

```yml
local:
  hosts:
    localhost:
      ansible_connection: local
      #ansible_python_interpreter: /usr/bin/python
      gather_facts: false
      extra_vars:

k8scluster:
  hosts:
    k8s:
      ansible_ssh_host: 192.168.1.100
      ansible_ssh_public_key_file: ~/.ssh/know_hosts
      ansible_ssh_user: root
      #ansible_password: teste
    node1:
      ansible_ssh_host: 192.168.1.101
      ansible_ssh_public_key_file: /root/.ssh/know_hosts
      ansible_ssh_user: root
    node2:
      ansible_ssh_host: 192.168.1.102
      ansible_ssh_public_key_file: ~/.ssh/know_hosts
      ansible_ssh_user: root
      #ansible_password: teste
    node3:
      ansible_ssh_host: 192.168.1.103
      ansible_ssh_public_key_file: ~/.ssh/know_hosts
      ansible_ssh_user: root
      #ansible_password: teste

ibm:
  hosts:
    ibm:
      ansible_ssh_host: 10.20.10.20
      ansible_ssh_public_key_file: /root/.ssh/know_hosts
      ansible_ssh_user: root
      #ansible_password: teste
```

#### Graph Inventory
Caso voce tenha um numero alto de hosts em diversos grupos dentro do arquivo de inventario, use sempre a opcao `--graph` para listar seu hosts:

```bash
# ansible-inventory -i inventory.yml --graph
@all:
  |--@ibmcloud:
  |  |--ibm
  |--@k8scluster:
  |  |--k8s
  |  |--node1
  |  |--node2
  |  |--node3
  |--@local:
  |  |--localhost
  |--@ungrouped:
```

### Ansible Ad-hoc
Ansible Ad-hoc commands, sao comandos executados via CLI usando os modulos do Ansible. Os comandos sao uteis para nosso dia a dia, no gerenciamento de configuracao de projeto e demais atividades. Vamos usar alguns exemplos de uso do Ansible Ad-hoc para comandos mais comuns do dia a dia.

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

- Veja como podemos prosseguir com a instalacao via Ansible usando Ad-hoc:

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

- Agora vamos remover um pacote usando o mesmo modulo:

`# ansible k8s -b -m yum -a "name=httpd state=absent" -i inventory.yml`

```bash
k8s | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": true,
    "changes": {
        "removed": [
            "httpd"
        ]
    },
    "msg": "",
    "rc": 0,
    "results": [
        "Loaded plugins: fastestmirror\nResolving Dependencies\n--> Running transaction check\n---> Package httpd.x86_64 0:2.4.6-97.el7.centos.5 will be erased\n--> Finished Dependency Resolution\n\nDependencies Resolved\n\n================================================================================\n Package      Arch          Version                       Repository       Size\n================================================================================\nRemoving:\n httpd        x86_64        2.4.6-97.el7.centos.5         @updates        9.4 M\n\nTransaction Summary\n================================================================================\nRemove  1 Package\n\nInstalled size: 9.4 M\nDownloading packages:\nRunning transaction check\nRunning transaction test\nTransaction test succeeded\nRunning transaction\n  Erasing    : httpd-2.4.6-97.el7.centos.5.x86_64                           1/1 \n  Verifying  : httpd-2.4.6-97.el7.centos.5.x86_64                           1/1 \n\nRemoved:\n  httpd.x86_64 0:2.4.6-97.el7.centos.5
 \n\nComplete!\n"
    ]
}
```

#### Systemd module
Modulo usado para gerenciamento de servicos nos sistemas Linux.

- Aqui nesse exemplo abaixo estamos verificando o servico web `HTTPD`, se ele esta inicialido no sistema:

`# ansible k8s -b -m systemd -a "name=httpd state=started" -i inventory.yml`

```bash
k8s | CHANGED => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": true,
    "name": "httpd",
    "state": "started",
    "status": {
        "ActiveEnterTimestampMonotonic": "0",
        "ActiveExitTimestampMonotonic": "0",
        "ActiveState": "inactive",
```

#### Setup module
Um exemplo  que ajuda demais no dia a dia sao os fatos do Ansible, que podemos obter com o modulo `setup`. Leia esse [artigo](https://amaurybsouza.medium.com/custom-facts-em-ansible-me-solta-quero-personalizar-minhas-tasks-4718b48b88aa) escrito no Medium  para que fique mais claro a ideia de fatos e tambem comecar  a estudar fatos customizaveis dentro do Ansible.

- Vamos colher alguns exemplos de uso:

`# ansible k8s -m setup -a "filter=ansible_distribution" -i inventory.yml`

```yml
k8s | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "CentOS",
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}
```

### Conditional Tasks
Uma forma de usar `if` e `else` no Ansible dentro de tasks.

```yml
- name: Example Facts Conditionals 
  hosts: all
  vars:
    supported_os:
      - RedHat
      - Fedora

  tasks:
  - name: Install nginx
    yum:
      name: "nginx"
      state: present
    when: ansible_facts['distribution'] in supported_os
```

### Loops
Usado para casos em que precisamos "varrer" determinado comando, arquivo, diretorio.

- Veja esse exemplo abaixo:

```yml
- name: "Create some files"
  file:
    state: touch
    path: /tmp/{{ item }}
  loop:
    - amaury01
    - amaury02
    - amaury03
```



### Ansible Playbooks
Outro importante recurso dentro do Ansible são os conceitos de **playbooks**, que abrange o seu maior uso nas tarefas que são rotineiras. Quando temos que executar mais e 2 tarefas ou `tasks` com o Ansible, passamos a usar os playbooks, isso porque fica mais endereçado e organizado o código e assim, podemos versionar e aproveitar de outras formas, caso essa tarefa precise ser aumentada.

#### Playbook format
Os playbooks do Ansible são expressos no formato `YAML` com um mínimo de sintaxe. Se você não estiver familiarizado com `YAML`, acesse o projeto no [GitHub](https://github.com/yaml), que la possui muitas ideias, conceitos e exemplos de utilizacao.

#### Playbook example
Aqui abaixo um exemplo básico de Ansible playbook:

```yml
- name: Installing Jenkins server
  hosts: all
  become: true
  remote_user: ubuntu
  gather_facts: false
  tasks:
    - name: Update all packages to their latest version
      apt:
        upgrade: yes
        update_cache: yes
        cache_valid_time: 86400
      ignore_errors: true

    - name: Installing some packages
      apt:
        name:
          - wget
          - git
          - curl
          - openjdk-11-jre
          - vim
        state: latest
      ignore_errors: true

    - name: checking repository
      shell: |
        curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \
        /usr/share/keyrings/jenkins-keyring.asc > /dev/null
        echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
        https://pkg.jenkins.io/debian binary/ | sudo tee \
        /etc/apt/sources.list.d/jenkins.list > /dev/null

      #- name: Download foo.conf
      #get_url:
      # url: https://pkg.jenkins.io/redhat-stable/jenkins.repo
      # dest: /etc/yum.repos.d/jenkins.repo
      # mode: '0440'

      #- name: Import a key from a url
      #rpm_key:
      # state: present
      # key: https://pkg.jenkins.io/redhat-stable/jenkins.io.key

    - name: Update all packages to their latest version
      apt:
        update_cache: yes
      ignore_errors: true

    - name: Installing Jenkins
      apt:
        name: jenkins
        state: latest

    - name: Make sure a service unit is running
      systemd:
        name: jenkins
        state: started
```

#### Playbooks checking
Para que possamos aproveitar da melhor forma possivel dos recursos do Ansible, sempre usamos alguns `Ad-hoc-playbooks` para permitir a analise antes da execucao da playbook. O comando `ansible-playbook` oferece várias opções para verificação, incluindo `--check`, `--diff`, `--list-hosts`, `--list-tasks` e `--syntax-check`.

- Vamos usar de exemplo o playbook abaixo:

```yml
---
- name: my playbook
  hosts: localhost
  become: true
  # remote_user: ubuntu
  gather_facts: false
  tags: Docker
  tasks:
    - name: Update all packages to their latest version
      apt:
        name: "*"
        state: latest

    - name: Install Docker on the machine
      shell: |
        curl -fsSL https://get.docker.com -o get-docker.sh
        DRY_RUN=1 sh ./get-docker.sh
        sudo sh get-docker.sh

    - name: Ensure Docker is running
      systemd:
        name: docker
        state: started
```

- Usando o `--list-tasks` para analisar quantas tasks possui dentro desse playbook:

`# ansible-playbook install_docker.yml -i inventory.yml --list-tasks`

```bash
playbook: install_docker.yml

  play #1 (k8s): my playbook    TAGS: [Docker]
    tasks:
      Update all packages to their latest version       TAGS: [Docker]
      Install Docker on the machine     TAGS: [Docker]
      Ensure Docker is running  TAGS: [Docker]
```

- Usando o `--list-hosts`  para verificar se estou atingindo corretamente os `hosts` pretendidos:

`# ansible-playbook install_docker.yml -i inventory.yml --list-hosts`

```bash
playbook: install_docker.yml

  play #1 (k8s): my playbook    TAGS: [Docker]
    pattern: ['k8s']
    hosts (1):
      k8s
```

- Usando o `--syntax-check` para verificar se o playbook esta descrito da forma correta, sem erros de identacao e sintaxe. Nota-se que, se ele nao retornar nada de erros,significa que o playbook esta identado corretamente, caso contrario, erros devem aparecer na sua saida padrao.

`# ansible-playbook install_docker.yml -i inventory.yml --syntax-check`

```bash
playbook: install_docker.yml
```

- Outra opcao super usada que ajuda demais no dia a dia e a `--start-at-task`

`# ansible-playbook playbooks/playbook_test.yml --start-at-task="Date" -i inventory.yml`

### Ansible Facts
Outro recurso MUITO usado do Ansible e a parte de facts, onde e possivel de obter os fatos da maquineta atraves de variaveis ja configuradas pelo Andible, por padrao ele ja possui setadas essas variaveis.

#### Fact example

`# ansible-playbook ansible_facts.yml -i inventory.yml`

```yml
---
- hosts: local
  gather_facts: true
  tasks:

  - name: mac address
    debug:
      msg: "{{ ansible_default_ipv4.macaddress }}"
    tags: mac_address

  - name: Time
    debug:
      msg: "{{ ansible_date_time.time }}"
    tags: time

  - name: Date
    debug:
      msg: "{{ ansible_date_time.date }}"
    tags: date

  - name: Diretório home
    debug:
      msg: "{{ ansible_env.HOME }}"
    tags: home

  - name: Endereço IP
    debug:
      msg: "{{ ansible_default_ipv4.address }}"
    tags: ip_address

  - name: Hostname
    debug:
      msg: "{{ ansible_hostname }}"
    tags: hostname

  - name: Distribuição
    debug:
      msg: "{{ ansible_distribution }}"
    tags: distribution
...
```

### Ansible Roles
Falando agora sobre as **roles** do Ansible, outro importante recurso de automação dentro da ferramenta. Bom, aqui vale destacar um ponto que muita gente acaba esquecendo ou mistura tudo… As roles do Ansible são usadas para projetos mais complexos, onde envolvem mais de 10, 20 playbooks a serem executados, pois o recurso de roles se baseia em uma estrutura definida de diretórios, onde é possível organizar de forma legal onde cada modelo/tipo de playbook/tasks podem ser inseridos, como serviços a serem reiniciados durante o processo, variáveis, tasks e outros recursos.

#### The Roles structure
Uma estrutura básica de roles com o Ansible seria algo em torno disso:

```bash
# tree devops/
devops/
├── defaults
│   └── main.yml
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── README.md
├── tasks
│   └── main.yml
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml

6 directories, 8 files
```

#### How to create Ansible Roles?
Basicamente, você precisa de executar um comando bem simples que ele cria a estrutura de roles automaticamente, ou você pode optar por criar manualmente.

`# ansible-galaxy init devops`

#### Ansible Role example
Abaixo um exemplo bem simples de `roles` com o Ansible:

```yml
---
- name: Installing Docker Engine
  hosts: "{{ inventory | default('local') }}"
  become: true
  gather_facts: false
  roles:
    - {role: 'docker', tags: 'docker_engine'}
...
```

#### How to execute Ansible Roles?
Usamos  o mesmo comando `ansible-playbook`, unica ponto que ele vai buscar essa estrutura de roles dentro do diretorio de roles.

`# ansible-playbook playbooks/install_docker.yml --extra-vars=local -i inventory.yml`

