# Домашнее задание к занятию "08.03 Использование Yandex Cloud"

---
## Основная часть

##### 1. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает lighthouse.

##### 2. При создании tasks рекомендую использовать модули: `get_url`, `template`, `yum`, `apt`.

##### 3. Tasks должны: скачать статику lighthouse, установить nginx или любой другой webserver, настроить его конфиг для открытия lighthouse, запустить webserver. 

##### 4. Приготовьте свой собственный inventory файл `prod.yml`.

<details><summary></summary>

```
[root@localhost playbook]# cat inventory/prod.yml 
---
clickhouse:
  hosts:
    clickhouse-01:
      ansible_host: 53.253.106.13
      ansible_ssh_user: kirill.adm

      
lighthouse:
  hosts:
    clickhouse-02:
      ansible_host: 53.253.106.13
      ansible_ssh_user: kirill.adm
      
vector:
  hosts:
    clickhouse-03:
      ansible_host: 53.253.106.13
      ansible_ssh_user: kirill.adm


```

</details>

##### 5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.

<details><summary></summary>

Уменьшить данную строку не имеется возможности, но плейбук отрабатывает с ней без проблем.  


```
[root@localhost playbook]# ansible-lint site.yml
[204] Lines should be no longer than 160 chars
site.yml:98
      ansible.builtin.command: "clickhouse-client -h 127.0.0.1 -q 'CREATE TABLE IF NOT EXISTS  logs.access_logs ( message String ) ENGINE = MergeTree() ORDER BY tuple()'"
```

</details>

##### 6. Попробуйте запустить playbook на этом окружении с флагом `--check`.
    
<details><summary></summary>

```
[root@localhost playbook]# ansible-playbook site.yml -i inventory/prod.yml --check

PLAY [Install Nginx] ******************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

TASK [NGINX | Install eper-release] ***************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

TASK [NGINX | Install Nginx] **********************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

TASK [NGINX | Create general config] **************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

PLAY [Install Lighthouse] *************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | install git] *******************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | Copy from git] *****************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | Create lighthouse config] ******************************************************************************
ok: [clickhouse-02]

PLAY [Install Clickhouse] *************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] *********************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "pavel", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "pavel", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] *********************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ****************************************************************************************
ok: [clickhouse-01]

TASK [Pause for 10 second for start servises] *****************************************************************************
Pausing for 10 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Create database] ****************************************************************************************************
skipping: [clickhouse-01]

TASK [Create table] *******************************************************************************************************
skipping: [clickhouse-01]

TASK [CLICKHOUSE | Create general config] *********************************************************************************
ok: [clickhouse-01]

PLAY [Install Nginx (vector)] *********************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-03]

PLAY [Install Vector] *****************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Install rpm] ***********************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Template config] *******************************************************************************************
[WARNING]: The value 1000 (type int) in a string field was converted to u'1000' (type string). If this does not look like
what you expect, quote the entire value to ensure it does not change.
ok: [clickhouse-03]

TASK [VECTOR | Create systemd unit] ***************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Start service] *********************************************************************************************
ok: [clickhouse-03]

PLAY RECAP ****************************************************************************************************************
clickhouse-01              : ok=5    changed=0    unreachable=0    failed=0    skipped=2    rescued=1    ignored=0   
clickhouse-02              : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
clickhouse-03              : ok=10   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

</details>

##### 7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.
    
<details><summary></summary>

```
[root@localhost playbook]# ansible-playbook site.yml -i inventory/prod.yml --diff

PLAY [Install Nginx] ******************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-03]
ok: [clickhouse-02]

TASK [NGINX | Install eper-release] ***************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

TASK [NGINX | Install Nginx] **********************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

TASK [NGINX | Create general config] **************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

PLAY [Install Lighthouse] *************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | install git] *******************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | Copy from git] *****************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | Create lighthouse config] ******************************************************************************
ok: [clickhouse-02]

PLAY [Install Clickhouse] *************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] *********************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "pavel", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "pavel", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] *********************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ****************************************************************************************
ok: [clickhouse-01]

TASK [Pause for 10 second for start servises] *****************************************************************************
Pausing for 10 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Create database] ****************************************************************************************************
ok: [clickhouse-01]

TASK [Create table] *******************************************************************************************************
changed: [clickhouse-01]

TASK [CLICKHOUSE | Create general config] *********************************************************************************
ok: [clickhouse-01]

PLAY [Install Nginx (vector)] *********************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-03]

PLAY [Install Vector] *****************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Install rpm] ***********************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Template config] *******************************************************************************************
[WARNING]: The value 1000 (type int) in a string field was converted to u'1000' (type string). If this does not look like
what you expect, quote the entire value to ensure it does not change.
ok: [clickhouse-03]

TASK [VECTOR | Create systemd unit] ***************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Start service] *********************************************************************************************
ok: [clickhouse-03]

PLAY RECAP ****************************************************************************************************************
clickhouse-01              : ok=7    changed=1    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
clickhouse-02              : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
clickhouse-03              : ok=10   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   


```

</details>

##### 8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
    
<details><summary></summary>

```
[root@localhost playbook]# ansible-playbook site.yml -i inventory/prod.yml --diff

PLAY [Install Nginx] ******************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

TASK [NGINX | Install eper-release] ***************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

TASK [NGINX | Install Nginx] **********************************************************************************************
ok: [clickhouse-02]
ok: [clickhouse-03]

TASK [NGINX | Create general config] **************************************************************************************
ok: [clickhouse-03]
ok: [clickhouse-02]

PLAY [Install Lighthouse] *************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | install git] *******************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | Copy from git] *****************************************************************************************
ok: [clickhouse-02]

TASK [Lighthouse | Create lighthouse config] ******************************************************************************
ok: [clickhouse-02]

PLAY [Install Clickhouse] *************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-01]

TASK [Get clickhouse distrib] *********************************************************************************************
ok: [clickhouse-01] => (item=clickhouse-client)
ok: [clickhouse-01] => (item=clickhouse-server)
failed: [clickhouse-01] (item=clickhouse-common-static) => {"ansible_loop_var": "item", "changed": false, "dest": "./clickhouse-common-static-22.3.3.44.rpm", "elapsed": 0, "gid": 1000, "group": "pavel", "item": "clickhouse-common-static", "mode": "0664", "msg": "Request failed", "owner": "pavel", "response": "HTTP Error 404: Not Found", "secontext": "unconfined_u:object_r:user_home_t:s0", "size": 246310036, "state": "file", "status_code": 404, "uid": 1000, "url": "https://packages.clickhouse.com/rpm/stable/clickhouse-common-static-22.3.3.44.noarch.rpm"}

TASK [Get clickhouse distrib] *********************************************************************************************
ok: [clickhouse-01]

TASK [Install clickhouse packages] ****************************************************************************************
ok: [clickhouse-01]

TASK [Pause for 10 second for start servises] *****************************************************************************
Pausing for 10 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [clickhouse-01]

TASK [Create database] ****************************************************************************************************
ok: [clickhouse-01]

TASK [Create table] *******************************************************************************************************
changed: [clickhouse-01]

TASK [CLICKHOUSE | Create general config] *********************************************************************************
ok: [clickhouse-01]

PLAY [Install Nginx (vector)] *********************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-03]

PLAY [Install Vector] *****************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Install rpm] ***********************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Template config] *******************************************************************************************
[WARNING]: The value 1000 (type int) in a string field was converted to u'1000' (type string). If this does not look like
what you expect, quote the entire value to ensure it does not change.
ok: [clickhouse-03]

TASK [VECTOR | Create systemd unit] ***************************************************************************************
ok: [clickhouse-03]

TASK [VECTOR | Start service] *********************************************************************************************
ok: [clickhouse-03]

PLAY RECAP ****************************************************************************************************************
clickhouse-01              : ok=7    changed=1    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0   
clickhouse-02              : ok=8    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
clickhouse-03              : ok=10   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

 
```

</details>

##### 9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

<details><summary></summary>
    
[Ссылка на README.md](https://github.com/ryzhakovks/Ansible8_3/blob/main/README.md)

</details>

##### 10. Готовый playbook выложите в свой репозиторий, поставьте тег `08-ansible-03-yandex` на фиксирующий коммит, в ответ предоставьте ссылку на него.
     
<details><summary></summary>

[Ссылка на репозиторий](https://github.com/ryzhakovks/Ansible8_3)

</details>
