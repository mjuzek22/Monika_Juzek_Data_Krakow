# Monika_Juzek_Data_Krakow


# Part 1 – Data Modelling [SQL] Setting Up MySQL with Docker

At first, the packages from the requirements.txt should be installed (in virtual environment if possible).

Then, to run the MySQL database locally, I used Docker for a clean and reproducible environment. Below are the steps I followed:

1. Run MySQL Docker container:
```
docker run --name mysql-container   -e MYSQL_ROOT_PASSWORD=rootpassword   -e MYSQL_DATABASE=mydatabase   -v mysql-data:/var/lib/mysql   -p 3306:3306   -d mysql:9.3
```
Wait for the container to initialize, then connect using your preferred MySQL client (e.g., MySQL Workbench, DBeaver, or programmatically via Python).

2. Create and populate the database using the provided droptime.sql file:

```
docker exec -i mysql-container mysql -uroot -prootpassword < droptime.sql
```

droptime.sql has been sent by the recruiters and is not included in this repository.

# Querying Data with Python and SQLAlchemy
I used SQLAlchemy in a Jupyter Notebook to connect to the database and execute SQL queries. Below is an example of the setup:

```
from sqlalchemy import create_engine
import pandas as pd

# Connection details
username = "root"
password = "rootpassword"
host = "localhost"          # or the IP address of your MySQL server
port = 3306                 # default MySQL port
database = "droptime"

# SQLAlchemy connection string
connection_string = f"mysql+pymysql://{username}:{password}@{host}:{port}/{database}"

# Create engine
engine = create_engine(connection_string)

# Define and execute the SQL query
query = """
SELECT
    p.product_id AS productId,
    SUM(p.weight * op.quantity) AS totalWeight
FROM
    orders o
JOIN orders_products op ON o.order_id = op.order_id
JOIN products p ON op.product_id = p.product_id
JOIN route_segments rs ON o.order_id = rs.order_id
WHERE
    o.customer_id = 32
    AND rs.segment_type = 'STOP'
    AND DATE(rs.segment_end_time) = '2024-02-13'
GROUP BY
    p.product_id
ORDER BY
    totalWeight ASC;
"""

df = pd.read_sql_query(query, engine)

# Export results to CSV
df.to_csv("name_surname.csv", index=False)```
