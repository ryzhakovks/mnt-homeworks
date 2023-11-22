Playbook
Playbook производит установку и настройку приложений для сбора, передачи и отображения логов на стэк
серверов clickhouse, vector и lighthouse. Первый play объединяет последовательность задач по
инсталяции Clickhouse. Блоку соответствует тэг clickhouse. Второй play объединяет последовательность
задач по инсталяции Vector. Блоку соответствует тэг vector. Третий play объединяет последовательность
задач по инсталяции Lighthouse. Блоку соответствует тэг lighthouse.

Variables
Значения переменных устанавливаются в файлах vars.yml в соответствующих директориях в group_vars.
При помощи них задаются следующие параметры:

clickhouse_version, vector_version - версии устанавливаемых приложений;
clickhouse_database_name - имя базы данных в clickhouse;
clickhouse_create_table_name - имя таблицы в clickhouse;
vector_config - содержимое конфигурационного файла для приложения vector;
lighthouse_home_dir - домашняя директория lighthouse;
nginx_config_dir - директория расположения конфига nginx.
Tags
Тэг ping - проверяет доступность серверов clickhouse-01,vector-01,lighthouse-01;
тэг clickhouse - позволяет производить отдельную настройку приложения clickhouse;
тэг vector - позволяет производить отдельную настройку приложения vector;
по тэгу vector_config - возможно производить изменение в конфиге приложения vector;
тэг lighthouse - позволяет производить отдельную настройку приложения lighthouse на одноименных серверах.

