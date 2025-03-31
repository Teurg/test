Возможные ошибки тк не установились python3-psycopg2 и acl
=========
```
sudo apt update && sudo apt install -y python3-psycopg2
```
```
sudo apt update && sudo apt install acl -y
```

Права созданного пользователя на бд
------------

Объяснение параметров:

    db: "{{ db_name }}" — указывает базу, на которую даются права.
    role: "{{ db_user }}" — указывает пользователя, которому выдаются права.
    privs: ALL — даёт полные привилегии (можно заменить на CONNECT, TEMP для ограниченного доступа).
    type: database — означает, что права применяются ко всей базе.
	
	
	
Удаление
------------ 

```
sudo apt remove --purge postgresql*
```