# Projeto Profissional de ETL com Airflow + PostgreSQL + Power BI

## Arquitetura do projeto:
```
Fonte de Dados (CSV/API)<br/>
        ↓<br/>
      ETL<br/>
(Apache Airflow)<br/>
        ↓<br/>
 PostgreSQL (Data Warehouse)<br/>
        ↓<br/>
    Power BI<br/>
(Dashboard Executivo)<br/>
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
