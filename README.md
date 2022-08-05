## Datapipeline de busca incremental em Banco de dados (MySQL/MariaDB)
  Este repositório contém alguns arquivos e códigos utilizados durante curso de `Data Pipelines com Apache Airflow `.
### Requisitos
- Compreender como o MySQL operator nativo do Airflow trabalha
## 💻 Principais Tecnologias, Softwares e Bibliotecas
- Docker
- Python
- Apache AirFlow (Data Pipeline)
- MySQL
## ⚙ Instalação e configuração de ambiente
### Docker
  - Seguir a [documentação](https://docs.docker.com/engine/install/) oficial
### Container - Apache AirFlow
  - `Observação: `Abra o terminal em um diretório raiz para subir seu container com Airflow, dessa forma não perdemos os arquivos de DAGs gerados caso seja necessário algumas reinstalação ou configuração adicional do container.
  - Crie container com seguinte comando em seu terminal:
    ```bash
    docker run -d -p 8080:8080 -v "$PWD/airflow/dags:/opt/airflow/dags/" \
    --entrypoint=/bin/bash \
    --name airflow apache/airflow:2.1.1-python3.8 \
    -c '(\
    airflow db init && \
    airflow users create --username <nome de usuário> --password <sua senha> --firstname <Seu nome> --lastname <Seu Nome> --role Admin --email <Seu e-mail>); \
    airflow webserver & \
    airflow scheduler\
    '
    ```
  - Verificando o container em execução.
    ```bash
    docker container ls
    ```
  - Verificando os logs do container
    ```bash
    docker container logs airflow
    ```
  - Se nenhum erro aparecer, acesse a interface web do Apache Airflow pelo endereço:
    
    **https://localhost:8080**
### Container - MySQL/MariaDB (OLTP)
- `Observação: `Assim como na criação do container do Airflow é importante abrir o terminal em um diretório raiz para subir o container.
- Crie o container con o seguinte comando em seu terminal:
  ```bash
  docker run -d --name mysql_oltp -p "3306:3306" -v "$PWD/data:/home/" -e MYSQL_ROOT_PASSWORD=airflow mysql
  ou
  docker run -d --name mariadb_oltp -p "3306:3306" -v "$PWD/data:/home" -e MARIADB_ROOT_PASSWORD=airflow mariadb:latest
  ```
- Verificando o container em execução.
  ```bash
  docker container ls
  ```
- Verificar o mapeamento dos volumes
  ```bash
  docker container exec -it mysql_oltp bash
  ls /home/
  ```
- Conectar com o MySQL/MariaDB dentro do container
  ```bash
  mysql -u root -pairflow
  ```
- Listar os bancos de dados
    ```bash
  show databases;
  ```
- Instalar e configurar recursos adicionais
  - Acesse o container pelo seguinte comando
    ```bash
    docker container exec -it mysql_oltp bash
    ```
  - Executar o seguinte comando
      ```bash
    apt-get update && apt-get install vim iputils-ping -y
    ```
### Preparando banco de dados
  - Executar o script `create-table-sales.sql` presente no diretório `scrips` deste repositório.
### Configurando o Aiflow (DB credenciais)
  - Na aba Admin do Airflow, selecionar `Connections`, e criar uma nova conexão.
  - Antes de inserir as informações, rode o seguinte comando para identificar o ip do container:
    ```bash
    docker inspect <db container>
    ```
  - Inclua as seguintes informações para estabelecer a conexão com o banco:
    ```bash
    Conn Id: mysql_oltp
    Conn Type: MySQL
    Description: Instancia de conexão do MySQL em ambiente OLTP
    Host: <ip advindo do inspect command>
    Schema: employee
    Login: root
    Password: airflow
    Port: 3306
    ```