# 📘 Guia de Instalação – Zabbix Server 7.4 + Grafana no Rocky Linux 9

Este documento descreve o procedimento completo para instalar e configurar o **Zabbix Server 7.4** com **PostgreSQL** e o **Grafana Enterprise** no **Rocky Linux 9**.

Mais informações na documentação do Zabbix em https://www.zabbix.com/ e Grafana em https://grafana.com/

---

## 📌 1. Atualizar o sistema

```bash
sudo dnf update -y
```

---

## 📌 2. Instalar banco de dados PostgreSQL

```bash
sudo dnf install -y postgresql-server postgresql-contrib
```

Configurar PostgreSQL

```bash
sudo postgresql-setup --initdb
sudo systemctl enable postgresql --now
```

---

## 📌 3. Adicionar o repositório oficial do Zabbix 7.4

```bash
sudo rpm -Uvh https://repo.zabbix.com/zabbix/7.4/release/rocky/9/noarch/zabbix-release-latest-7.4.el9.noarch.rpm
sudo dnf clean all
```

---

## 📌 4. Instalar os pacotes do Zabbix

```bash
sudo dnf install -y zabbix-server-pgsql zabbix-web-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-selinux-policy zabbix-agent2
```

### Plugin para monitoramento do PostgreSQL

```bash
sudo dnf install -y zabbix-agent2-plugin-postgresql
```

---

## 📌 5. Criar usuário e banco de dados no PostgreSQL

O PostgreSQL deve estar previamente instalado.

### Criar usuário

```bash
sudo -u postgres createuser --pwprompt zabbix
```

### Criar banco de dados

```bash
sudo -u postgres createdb -O zabbix zabbix
```

---

## 📌 6. Importar o schema do Zabbix

```bash
zcat /usr/share/zabbix/sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```

---

## 📌 7. Configurar o arquivo do servidor Zabbix

Edite o arquivo:

```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

Adicione a senha:

```
DBPassword=password
```

---

## 📌 8. Habilitar e iniciar os serviços

```bash
sudo systemctl restart zabbix-server zabbix-agent2 httpd php-fpm
sudo systemctl enable zabbix-server zabbix-agent2 httpd php-fpm
```

---

## 📌 9. Acessar o Zabbix Web

Abra no navegador:

```
http://SEU_IP/zabbix
```

Exemplo:

```
http://192.168.35.35/zabbix
```

Login padrão:

* **Usuário:** Admin
* **Senha:** zabbix

---

# 📊 Instalação do Grafana Enterprise

## 📌 10. Instalar o Grafana

```bash
sudo yum install -y https://dl.grafana.com/grafana-enterprise/release/12.3.0/grafana-enterprise_12.3.0_19497075765_linux_amd64.rpm
```

## 📌 11. Iniciar e habilitar o Grafana

```bash
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

Acessar:

```
http://SEU_IP:3000
```

Login padrão:

* **Usuário:** admin
* **Senha:** admin

---

# 🔥 Complementos Recomendados

### Abrir portas do firewall

```bash
sudo firewall-cmd --permanent --add-service={http,https}
sudo firewall-cmd --permanent --add-port=10050/tcp
sudo firewall-cmd --permanent --add-port=10051/tcp
sudo firewall-cmd --permanent --add-port=5432/tcp
sudo firewall-cmd --permanent --add-port=3000/tcp
sudo firewall-cmd --reload
```

---

# ✔️ Finalizado!

Seu ambiente Zabbix + Grafana no Rocky Linux 9 está pronto.
