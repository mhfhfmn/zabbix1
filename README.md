# Домашнее задание к занятию "`Система мониторинга Zabbix`" - `Гуреев Юрий`

---

### Задание 1

Установите Zabbix Server с веб-интерфейсом.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите PostgreSQL. Для установки достаточна та версия, что есть в системном репозитороии Debian 11.
3. Пользуясь конфигуратором команд с официального сайта, составьте набор команд для установки последней версии Zabbix с поддержкой PostgreSQL и Apache.
4. Выполните все необходимые команды для установки Zabbix Server и Zabbix Web Server.

#### Требования к результатам
1. Прикрепите в файл README.md скриншот авторизации в админке.
2. Приложите в файл README.md текст использованных команд в GitHub.

### Решение

1. ![Скриншот авторизации на web-портале zabbix](https://github.com/mhfhfmn/zabbix1/blob/main/img/zabbix_auth.jpg)
2. Команды для установки Zabbix server(установка проводилась с web-сервером nginx, при использовании nginx портал управления zabbix server доступен по прямому URL из коробки(без /zabbix), что является более удобным способом.)

```
sudo -s
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb
apt update
apt install zabbix-server-pgsql zabbix-frontend-php php8.3-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
nano /etc/zabbix/zabbix_server.conf
Отредактировать параметр "DBPassword=password" указав пароль который устанавливали ранее на эьапе создания роли в СУБД postgresql
nano /etc/zabbix/nginx.conf
Раскоментировать параметры:
# listen 8080;
# server_name example.com;
и заменить порт 8080 на 80, чтобы портал усправления zabbix открывался по прямому URL, и заменить example.com на ip адрес сервера Zabbix
systemctl restart zabbix-server zabbix-agent nginx php8.3-fpm
systemctl enable zabbix-server zabbix-agent nginx php8.3-fpm
```
После чего портал будет доступен по URL http://<ip адрес сервера zabbix>


### Задание 2

Установите Zabbix Agent на два хоста.

#### Процесс выполнения
1. Выполняя ДЗ, сверяйтесь с процессом отражённым в записи лекции.
2. Установите Zabbix Agent на 2 вирт.машины, одной из них может быть ваш Zabbix Server.
3. Добавьте Zabbix Server в список разрешенных серверов ваших Zabbix Agentов.
4. Добавьте Zabbix Agentов в раздел Configuration > Hosts вашего Zabbix Servera.
5. Проверьте, что в разделе Latest Data начали появляться данные с добавленных агентов.
#### Требования к результатам
1. Приложите в файл README.md скриншот раздела Configuration > Hosts, где видно, что агенты подключены к серверу
2. Приложите в файл README.md скриншот лога zabbix agent, где видно, что он работает с сервером
3. Приложите в файл README.md скриншот раздела Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
4. Приложите в файл README.md текст использованных команд в GitHub

### Решение

1. ![Скриншот Hosts в портале управления Zabbix server](https://github.com/mhfhfmn/zabbix1/blob/main/img/zabbix_hosts.jpg)
2. ![Скриншот лога zabbix agent](https://github.com/mhfhfmn/zabbix1/blob/main/img/zabbix_agent_log.jpg)
3. ![Скриншот Latest Data в портале управления Zabbix server](https://github.com/mhfhfmn/zabbix1/blob/main/img/zabbix_latest_data.jpg)
4. Команды для установки Zabbix agent

```
sudo -s
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb
apt update
apt install zabbix-agent
nano /etc/zabbix/zabbix-agentd.conf
Необходимо найти параметры Server, ServerActive и Hostname и установить значениями этих параметров ip адрес сервера мониторинга Zabbix
systemctl restart zabbix-agent
systemctl enable zabbix-agent
```