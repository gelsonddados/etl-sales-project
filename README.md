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

Acessar Airflow:

```
http://localhost:8080
```
Usuário e senha normalmente:

```
airflow
airflow

```
## 6. Criar Banco e Tabelas
sql/create_tables.sql

```
CREATE TABLE fato_vendas (
    id_venda INT PRIMARY KEY,
    cliente VARCHAR(100),
    produto VARCHAR(100),
    categoria VARCHAR(100),
    quantidade INT,
    preco NUMERIC(10,2),
    valor_total NUMERIC(10,2),
    data_venda DATE
);

```
Execute no PostgreSQL.

Pode usar:

* pgAdmin
* DBeaver

## 7. Criar Processo ETL

#### 7.1 Extract

#### airflow/scripts/extract.py

```
import pandas as pd

def extract_data():
    df = pd.read_csv('/opt/airflow/data/vendas.csv')
    return df
```

### 7.2 Transform

#### airflow/scripts/transform.py

```
def transform_data(df):

    # remover nulos
    df = df.dropna()

    # criar valor total
    df['valor_total'] = df['quantidade'] * df['preco']

    return df
```

### 7.3 Load
#### airflow/scripts/load.py

```
from sqlalchemy import create_engine

def load_data(df):

    engine = create_engine(
        'postgresql://airflow:airflow@postgres:5432/dw_vendas'
    )

    df.to_sql(
        'fato_vendas',
        engine,
        if_exists='append',
        index=False
    )
    ```
    
## 8. Criar DAG do Airflow

#### airflow/dags/sales_etl_dag.py

```
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

from scripts.extract import extract_data
from scripts.transform import transform_data
from scripts.load import load_data

default_args = {
    'owner': 'data-team'
}

def etl():

    df = extract_data()

    df = transform_data(df)

    load_data(df)

with DAG(
    dag_id='sales_etl_pipeline',
    start_date=datetime(2026, 1, 1),
    schedule='@daily',
    catchup=False,
    default_args=default_args
) as dag:

    run_etl = PythonOperator(
        task_id='run_etl',
        python_callable=etl
    )

    run_etl
```

## 9. Instalar Dependências no Airflow

#### airflow/requirements.txt

```
pandas
sqlalchemy
psycopg2-binary
```
## 10. Executar Pipeline

No Airflow:

1. Abrir DAG
2. Ativar DAG
3. Clicar em "Trigger DAG"

#### Fluxo:

```
CSV → ETL → PostgreSQL
```

## 11. Validar Dados no PostgreSQL

```
SELECT * FROM fato_vendas;
```
## 12. Conectar Power BI

Abrir:

* Microsoft Power BI

### Passos

#### Obter Dados

```
PostgreSQL
```

#### Configurar conexão
Servidor:

```
localhost
```

Banco:

```
dw_vendas
```

Usuário:

```
airflow
```

Senha: 

```
airflow
```

## 13. Modelagem no Power BI

Criar medidas:

### Total Vendas

```
Total Vendas = SUM(fato_vendas[valor_total])
```
