# PLAYBOOK - galera-mariadb-ubuntu2004.yml

## Este Playbook foi testado em um ambiente Azure com 3 nós para MariaDB com Galera

- ★░░░░░░░░░░░████░░░░░░░░░░░░░░░░░░░░★
- ★░░░░░░░░░███░██░░░░░░░░░░░░░░░░░░░░★
- ★░░░░░░░░░██░░░█░░░░░░░░░░░░░░░░░░░░★
- ★░░░░░░░░░██░░░██░░░░░░░░░░░░░░░░░░░★
- ★░░░░░░░░░░██░░░███░░░░░░░░░░░░░░░░░★
- ★░░░░░░░░░░░██░░░░██░░░░░░░░░░░░░░░░★
- ★░░░░░░░░░░░██░░░░░███░░░░░░░░░░░░░░★
- ★░░░░░░░░░░░░██░░░░░░██░░░░░░░░░░░░░★
- ★░░░░░░░███████░░░░░░░██░░░░░░░░░░░░★
- ★░░░░█████░░░░░░░░░░░░░░███░██░░░░░░★
- ★░░░██░░░░░████░░░░░░░░░░██████░░░░░★
- ★░░░██░░████░░███░░░░░░░░░░░░░██░░░░★
- ★░░░██░░░░░░░░███░░░░░░░░░░░░░██░░░░★
- ★░░░░██████████░███░░░░░░░░░░░██░░░░★
- ★░░░░██░░░░░░░░████░░░░░░░░░░░██░░░░★
- ★░░░░███████████░░██░░░░░░░░░░██░░░░★
- ★░░░░░░██░░░░░░░████░░░░░██████░░░░░★
- ★░░░░░░██████████░██░░░░███░██░░░░░░★
- ★░░░░░░░░░██░░░░░████░███░░░░░░░░░░░★
- ★░░░░░░░░░█████████████░░░░░░░░░░░░░★
- ★░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░★

```
Sistema testado e utilizado: UBUNTU Server 20.04 LTS

Informações da VM testada:
Computer name Server-UbuntuXX
Operating system Linux (ubuntu 20.04)
Publisher canonical
Offer 0001-com-ubuntu-server-focal
Plan 20_04-lts-gen2
VM generation V2
Agent status Ready
Agent version 2.3.1.1
Host group None
Host -
Proximity placement group -
Colocation status N/A
```
# Preparando o Ansible

## Inventário

Os três nós deverão possuir já o ip e deverão estar presentes em Hosts do Ansible da seguinte forma:
``` d
[galeracluster]
node1 ansible_host=52.234.148.203
node2 ansible_host=104.211.45.110
node3 ansible_host=104.211.27.153
```
```
Obs: Os ips acima foram utilizados para o teste no Azure. É possivel apontar também o IP da rede Virtual quando estes servidores estiverem disponíveis para comunicação
```
## Configuração do Ansible

Utilizamos no ansible.cfg as seguintes configurações:
``` 
[defaults]
remote_user = userazure
private_key_file = ~/.ssh/id_rsa
host_key_checking = False
inventory      = ~/ansible/inventario
```
## Arquivos de transferência

Colocamos em 3 pastas as configurações respectivas de cada nó e colocamos na raiz um arquivo de hosts que substituirá de cada server do Linux com os respectivos endereços.

Altere o arquivo Hosts para colocar o ip em que os servidores receberão da Virtual Network:

ex:
``` d
127.0.0.1	localhost
10.10.10.4  node1.local node1
10.10.10.5  node2.local node2
10.10.10.6  node3.local node3
```
Nomeamos cada nó como node1, node2 e node3.

# Antes de executar o Playbook 

Antes de executar o playbook, recomendo que configure o playbook com as configurações desejadas de:
- Database
- Senha de root
- User do Banco

Edite o yml e altere nas seguintes linhas:

## Senha de root
```
linha 9 e 123 - Altera entre aspas a senha de root (as duas precisam estar com a mesma senha)
mysql_root_password: "Password1"    
```
Obs: A criação de senha de root acontece apenas uma vez, apos executar novamente o playbook, setamos a configuração de ignorar erro para dar sequencia a finalização do PlayBook.

## Nome do DataBase
```
linha 124 - Altera entre aspas o nome do Database que será criado
db_name: "dbsystem"    
```
## Nome do user do Banco de Dados
```
linha 125 - Altera entre aspas o nome do usuário que terá acesso ao Database que será criado
user_db: "usersystem_rw"    
```
```
Obs: A senha deste usuário será configurada com a mesma da senha de root definida acima.
```

# Para implementar use o comando

```
ansible-playbook galera-mariadb-ubuntu2004.yml
```

## O resultado será:
```
PLAY [galeracluster] 
TASK [Gathering Facts] 
ok: [node2]
ok: [node3]
ok: [node1]

TASK [Update] 
changed: [node3]
changed: [node2]
changed: [node1]

TASK [Instalando software utilitarios] 
ok: [node2]
ok: [node3]
ok: [node1]

TASK [Adicionando repositorio MariaDB] 
changed: [node3]
changed: [node2]
changed: [node1]

TASK [Instalando pymysql] 
changed: [node3]
changed: [node2]
changed: [node1]

TASK [Instalando MariaDB Server] 
changed: [node2]
changed: [node3]
changed: [node1]

TASK [Instalando MariaDB Client] 
changed: [node3]
changed: [node2]
changed: [node1]

TASK [Restart MariaDB] 
changed: [node1]
changed: [node2]
changed: [node3]

TASK [Configurando a senha de root] 
[WARNING]: Module did not set no_log for update_password
changed: [node2]
changed: [node3]
changed: [node1]

TASK [Informacao da senha de root] 
ok: [node1] => {
    "msg": "MariaDB root password was set to Password1"
}
ok: [node2] => {
    "msg": "MariaDB root password was set to Password1"
}
ok: [node3] => {
    "msg": "MariaDB root password was set to Password1"
}

TASK [Delete anonymous users] 
ok: [node2]
ok: [node3]
ok: [node1]

TASK [Remove database de Test] 
ok: [node2]
ok: [node3]
ok: [node1]

PLAY [galeracluster] 

TASK [Gathering Facts] 
ok: [node2]
ok: [node1]
ok: [node3]

TASK [Copiando Arquivos Hosts para os no's] 
changed: [node3]
changed: [node2]
changed: [node1]

PLAY [node1] 

TASK [Gathering Facts] 
ok: [node1]

TASK [Copiando configuracao node1] 
changed: [node1]

TASK [Iniciar Cluster do Galera node 1] 
changed: [node1]

TASK [Restart MariaDB node 1] 
changed: [node1]

PLAY [node2] 

TASK [Gathering Facts] 
ok: [node2]

TASK [Copiando configuracao node2] 
changed: [node2]

TASK [Restart MariaDB node 2] 
changed: [node2]

PLAY [node3] 

TASK [Gathering Facts] 
ok: [node3]

TASK [Copiando configuracao node3] 
changed: [node3]

TASK [Restart MariaDB node 3] 
changed: [node3]

PLAY [node1] 

TASK [Gathering Facts] 
ok: [node1]

TASK [Criando Diretorio do MariaDB] 
changed: [node1] => (item=db)
changed: [node1] => (item=log)

TASK [Verificando arquivos em /data/db] 
ok: [node1]

TASK [Informacao dos arquivos em /data/db] 
ok: [node1] => {
    "msg": "Quantidade de arquivos e': {'files': [], 'changed': False, 'msg': '', 'matched': 0, 'examined': 0, 'failed': False}"
}

TASK [Executa o mysql_install_db apenas se /data/db estiver Vazio] 
changed: [node1]

TASK [Criando o database para aplicacao] 
changed: [node1]

TASK [Informacao do nome do DB criado] 
ok: [node1] => {
    "msg": "Database Criado com o nome de dbsystem"
}

TASK [Criando usuario do DB para aplicacao] 
changed: [node1]

TASK [Informacao do nome do usuario e do Banco criado] 
ok: [node1] => {
    "msg": "Usuario Criado com o nome de usersystem_rw e senha Password1 "
}

PLAY RECAP 
node1                      : ok=27   changed=15   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=17   changed=10   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node3                      : ok=17   changed=10   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
# Testando as configurações

Ao terminar o Playbook, precisa testar:
- Banco de dados se está em cluster do Galera
- Testar replicação do DB
- Ver configurações do Cluster

## Banco de dados está em Cluster?

Para verificar se está em cluster, conecte no nó1 (node1) via ssh e execute os seguintes comandos:

``` bash
mysql -u root -p
```
Utilize a senha cadastrada do banco alterada do playbook (padrão: Password1 )

Execute o comando a seguir para ver se os nós estão ativos e configurados
``` SQL
show status like 'wsrep_%';
```
Será apresentado um resultado parecido com este:
```
+-------------------------------+--------------------------------------+
| Variable_name                 | Value                                |
+-------------------------------+--------------------------------------+
| wsrep_applier_thread_count    | 1                                    |
| wsrep_apply_oooe              | 0.000000                             |
| wsrep_apply_oool              | 0.000000                             |
| wsrep_apply_window            | 1.000000                             |
| wsrep_causal_reads            | 0                                    |
| wsrep_cert_deps_distance      | 1.000000                             |
| wsrep_cert_index_size         | 2                                    |
| wsrep_cert_interval           | 0.000000                             |
| wsrep_cluster_conf_id         | 3                                    |
| wsrep_cluster_size            | 3                                    |
| wsrep_cluster_state_uuid      | f9aa0bb1-0516-11ec-b462-071fc64f6bef |
| wsrep_cluster_status          | Primary                              |
| wsrep_cluster_weight          | 3                                    |
| wsrep_commit_oooe             | 0.000000                             |
| wsrep_commit_oool             | 0.000000                             |
| wsrep_commit_window           | 1.000000                             |
| wsrep_connected               | ON                                   |
| wsrep_desync_count            | 0                                    |
| wsrep_evs_delayed             |                                      |
| wsrep_evs_evict_list          |                                      |
| wsrep_evs_repl_latency        | 0/0/0/0/0                            |
| wsrep_evs_state               | OPERATIONAL                          |
| wsrep_flow_control_paused     | 0.000000                             |
| wsrep_flow_control_paused_ns  | 0                                    |
| wsrep_flow_control_recv       | 0                                    |
| wsrep_flow_control_sent       | 0                                    |
| wsrep_gcomm_uuid              | f9a8b2d3-0516-11ec-ac70-dabce2e4badc |
| wsrep_incoming_addresses      | node2:3306,node3:3306,node1:3306     |
| wsrep_last_committed          | 4                                    |
| wsrep_local_bf_aborts         | 0                                    |
| wsrep_local_cached_downto     | 1                                    |
| wsrep_local_cert_failures     | 0                                    |
| wsrep_local_commits           | 0                                    |
| wsrep_local_index             | 2                                    |
| wsrep_local_recv_queue        | 0                                    |
| wsrep_local_recv_queue_avg    | 0.142857                             |
| wsrep_local_recv_queue_max    | 2                                    |
| wsrep_local_recv_queue_min    | 0                                    |
| wsrep_local_replays           | 0                                    |
| wsrep_local_send_queue        | 0                                    |
| wsrep_local_send_queue_avg    | 0.166667                             |
| wsrep_local_send_queue_max    | 2                                    |
| wsrep_local_send_queue_min    | 0                                    |
| wsrep_local_state             | 4                                    |
| wsrep_local_state_comment     | Synced                               |
| wsrep_local_state_uuid        | f9aa0bb1-0516-11ec-b462-071fc64f6bef |
| wsrep_open_connections        | 0                                    |
| wsrep_open_transactions       | 0                                    |
| wsrep_protocol_version        | 9                                    |
| wsrep_provider_name           | Galera                               |
| wsrep_provider_vendor         | Codership Oy <info@codership.com>    |
| wsrep_provider_version        | 3.29(ra60e019)                       |
| wsrep_ready                   | ON                                   |
| wsrep_received                | 7                                    |
| wsrep_received_bytes          | 689                                  |
| wsrep_repl_data_bytes         | 1605                                 |
| wsrep_repl_keys               | 4                                    |
| wsrep_repl_keys_bytes         | 128                                  |
| wsrep_repl_other_bytes        | 0                                    |
| wsrep_replicated              | 4                                    |
| wsrep_replicated_bytes        | 2000                                 |
| wsrep_rollbacker_thread_count | 1                                    |
| wsrep_thread_count            | 2                                    |
+-------------------------------+--------------------------------------+
6
```

# Testar Data Base
Para testar a replicação entre nós, conecte no nó1 (node1) via ssh e execute os seguintes comandos:
``` bash
mysql -u root -p
```
Utilize a senha cadastrada do banco alterada do playbook (padrão: Password1 )


``` SQL
CREATE DATABASE playground;
CREATE TABLE playground.equipment ( id INT NOT NULL AUTO_INCREMENT, type VARCHAR(50), quant INT, color VARCHAR(25), PRIMARY KEY(id));
INSERT INTO playground.equipment (type, quant, color) VALUES ("slide", 2, "blue");
```
Para listar se o DB playgroud foi criado, execute:
``` SQL
show databases;
```
Se logar em outros nós o database criado deverá ser apresentado também.
```
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| dbsystem           |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.000 sec)
```
Para sair da console do MariaDB execute:
```
quit
```
Caso possua alguma dúvida, fale com Lincoln Taets Katachinski
lincoln.katachinski@softwareone.com

Referencia para criação do Ansible e apoio:

- https://mariadb.com/kb/en/getting-started-with-mariadb-galera-cluster/
- https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#group-by-roles
- https://computingforgeeks.com/install-mariadb-galera-cluster-on-ubuntu-with-proxysql/
- https://www.digitalocean.com/community/tutorials/how-to-configure-a-galera-cluster-with-mariadb-on-ubuntu-18-04-servers
- https://docs.ansible.com/ansible/2.5/modules/mysql_db_module.html
- https://churrops.io/2017/11/29/ansible-automatizando-a-instalacao-do-mariadb-e-mysql_secure_instalation/
## Curso:
- https://learn.acloud.guru/course/intro-ansible/dashboard

