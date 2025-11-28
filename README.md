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

---

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

# 📈 Integração do Zabbix com Grafana

## 📌 12. Instalar o plugin de datasource Zabbix no Grafana

O Grafana Enterprise já permite instalar plugins oficiais.

Acesse o Grafana: http://SEU_IP:3000

Vá em Administração → Plugins e data → Plugins

Pesquisa por Zabbix → Clique para instalar

O Grafana Enterprise já permite instalar plugins oficiais.

Se preferir pode instalar via CLI no terminal.
Execute:

```bash
sudo grafana-cli plugins install alexanderzobnin-zabbix-app
sudo systemctl restart grafana-server
```

---

## 📌 13. Habilitar o plugin no Grafana

Acesse o Grafana: http://SEU_IP:3000

Vá em Administração → Plugins e data → Plugins

Pesquise por Zabbix

Clique no plugin Zabbix e selecione Enable

---

## 📌 14. Criar o Data Source do Zabbix no Grafana

Vá em Conexão → Data Sources → Add data source

Selecione Zabbix

Configure:

URL: http://SEU_IP/zabbix/api_jsonrpc.php

Zabbix API details:

Abaixo, em Zabbix Connection:

Username: Admin ou um usuário dedicado

Password: senha definida no Zabbix

Zabbix API version: automático

Trends: habilitar (recomendado)

Cache TTL: 1h (recomendado)

Clique em Save & Test

Se tudo estiver certo, aparecerá "Zabbix API version... OK".

---

## 📌 15. Importar dashboards prontos

O plugin fornece diversos dashboards oficiais.

No menu lateral, vá para Dashboards → Browse → Zabbix

Escolha um dashboard (Hosts, Overview, Network, etc.)

Importe e selecione o Data Source Zabbix criado

---

## 📌 16. Criar dashboards personalizados
Para usar dados do Zabbix

Crie um novo dashboard

Adicione um panel

Em Query, selecione o datasource Zabbix

Tipos de consultas disponíveis:

Metrics → itens do Zabbix

Problems → eventos e triggers

Trends → histórico consolidado

Text → informações brutas

---

## ✔️ Finalizado!

Seu ambiente Zabbix + Grafana + Integração está completo.

Seu ambiente Zabbix + Grafana no Rocky Linux 9 está pronto.
