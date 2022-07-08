## Ad-hoc Commands

### File Transfer
```
$ ansible atlanta -m copy -a "src=/etc/hosts dest=/tmp/hosts"
$ ansible webservers -m file -a "dest=/srv/foo/a.txt mode=600"
$ ansible webservers -m file -a "dest=/srv/foo/b.txt mode=600 owner=mdehaan group=mdehaan"
$ ansible webservers -m file -a "dest=/path/to/c mode=755 owner=mdehaan group=mdehaan state=directory" (The file module can also create directories, similar to mkdir -p)
$ ansible webservers -m file -a "dest=/path/to/c state=absent" (As well as delete directories (recursively) and delete files:)
```

### Basic Commands
```
$ ansible prod -m ping -i inventory.yml (verifica se os servers afestados estao up)
$ ansible webservers -m yum -a "name=acme-1.5 state=present" (garante que a versao correta esteja instalada nos servers)
$ ansible webservers -m ansible.builtin.service -a "name=httpd state=started" (garante que o service esteja iniciado)
```

### Manage Services
```
ansible prod -m service -a "name=httpd state=started" - Inicia o service desejado.
ansible prod -m service -a "name=httpd state=restarted" - Restarta o service desejado.
ansible prod -m service -a "name=httpd state=stopped" - Pausa o service desejado.
```

### Manage Packages
```
ansible prod -m apt -a "name=giropops state=present" - Instala o pacote desejado no servidor
ansible prod -m apt -a "name=giropops-1.2 state=present" - Instala o pacote com a versão desejada
ansible prod -m apt -a "name=giropops state=latest" - Instala a última versão do pacote desejado
ansible prod -m apt -a "name=giropops state=absent" - Desinstala o pacote desejado no servidor
```
