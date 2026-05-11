# Projeto Profissional de ETL com Airflow + PostgreSQL + Power BI

## Arquitetura do projeto:
```
Fonte de Dados (CSV/API)
        ↓
      ETL
(Apache Airflow)
        ↓
 PostgreSQL (Data Warehouse)
        ↓
    Power BI
(Dashboard Executivo)
```
## Stack utilizada:

* Apache Airflow
* PostgreSQL
* Microsoft Power BI
* Docker + Docker Compose
* Python
* Pandas
* SQL

## 1. Objetivo do Projeto

Criar um pipeline ETL profissional que:

1. Extrai dados de vendas
2. Transforma os dados
3. Carrega no PostgreSQL
4. Atualiza automaticamente
5. Consome no Power BI
6. Exibe dashboard executivo

## 2. Estrutura Profissional do Projeto
```
. etl-sales-project/
│
├── airflow/
│   ├── dags/
│   │   └── sales_etl_dag.py
│   │
│   ├── scripts/
│   │   ├── extract.py
│   │   ├── transform.py
│   │   └── load.py
│   │
│   └── requirements.txt
│
├── data/
│   └── vendas.csv
│
├── sql/
│   └── create_tables.sql
│
├── docker-compose.yml
│
└── README.md
```

## 3. Cenário do Projeto
Vamos simular uma empresa com vendas.

Arquivo CSV:
```
id_venda,cliente,produto,categoria,quantidade,preco,data_venda
1,Carlos,Notebook,Tecnologia,1,350000,2026-05-01
2,Ana,Mouse,Tecnologia,2,15000,2026-05-01
3,Paulo,Cadeira,Móveis,1,80000,2026-05-02
```
## 4. Subir Ambiente com Docker

docker-compose.yml
```
version: '3.8'

services:

  postgres:
    image: postgres:15
    container_name: postgres_dw
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: dw_vendas
    ports:
      - "5432:5432"

  airflow:
    image: apache/airflow:2.9.1
    container_name: airflow
    depends_on:
      - postgres
    environment:
      AIRFLOW__CORE__LOAD_EXAMPLES: "false"
      AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/dw_vendas
    volumes:
      - ./airflow/dags:/opt/airflow/dags
      - ./airflow/scripts:/opt/airflow/scripts
      - ./data:/opt/airflow/data
    ports:
      - "8080:8080"
    command: standalone
```

## 3. Subir Ambiente
Na raiz do projeto:
```
docker compose up -d
```

