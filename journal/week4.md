
![bootcamp-logo](https://user-images.githubusercontent.com/113374279/226582654-70a97688-d573-4fbe-8786-7ca7ae766207.png)

# Week 4 — Postgres and RDS

Instructor: Andrew Brown

Instructional Videos:

[Live stream](https://www.youtube.com/live/EtD7Kv5YCUs?feature=share)

[SQL-RDS](https://youtu.be/Sa2iB33sKFo)

[Cognito Post Confirmation Lambda](https://youtu.be/7qP4RcY2MwU)





## Database

A database is a collection of data that is organized in a structured manner to allow for efficient retrieval and manipulation of that data. Databases are used in a wide variety of applications, including business, scientific research, and social media.

a database is a critical component of a backend system as it provides a reliable, secure, and scalable way to store and manage data. It helps to ensure data consistency and allows developers to retrieve and manipulate data efficiently.

There are different types of databases, including:

- Relational databases: These are the most commonly used databases and store data in tables with rows and columns. They are based on the relational model of data, which allows for efficient querying and manipulation of data.

- NoSQL databases: These databases are designed to handle large volumes of unstructured data. They do not use a traditional table-based schema and are often used in big data and real-time web applications.

- Object-oriented databases: These databases store data in objects rather than tables, making them useful for complex data structures and programming languages.

- Graph databases: These databases use graph structures to represent and store data, making them useful for applications that require complex relationships between data.

Document databases: These databases store data in documents, such as JSON or XML, making them useful for applications that require flexible and dynamic schemas.

### Postgresql

PostgreSQL is a popular open-source relational database management system (RDBMS) that is widely used for data storage, processing, and retrieval in web applications. Amazon Relational Database Service (RDS) is a fully-managed database service provided by Amazon Web Services (AWS) that supports several relational database engines, including PostgreSQL, MySQL, Oracle, and SQL Server.

Amazon RDS for PostgreSQL is a fully-managed service that makes it easy to set up, operate, and scale PostgreSQL deployments in the cloud. It automates many common administrative tasks, such as backups, software patching, and database scaling, allowing users to focus on their applications. Amazon RDS for PostgreSQL also provides options for high availability and read scaling, making it suitable for various use cases.

Amazon Aurora PostgreSQL is a high-performance, fully-managed database service that is compatible with PostgreSQL, and it provides better performance than standard PostgreSQL databases. It is designed to be highly available, durable, and fault-tolerant, and it automatically scales up or down based on the application's needs.

To deploy PostgreSQL on AWS, users can choose between Amazon RDS for PostgreSQL or Amazon Aurora PostgreSQL, depending on their specific requirements. Both services offer various benefits and features that can help users to manage their PostgreSQL databases in the cloud effectively.

## Provision RDS Instance
On the Cli run run the following commands. Kindly note that for region, you have to specify the alphabet associated with the region.
My region in this case is us-east-1a

```
aws rds create-db-instance \
  --db-instance-identifier cruddur-db-instance \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version  14.6 \
  --master-username cruddurroot \
  --master-user-password <enter password min 8 characters> \
  --allocated-storage 20 \
  --availability-zone us-east-1a \
  --backup-retention-period 0 \
  --port 5432 \
  --no-multi-az \
  --db-name cruddur \
  --storage-type gp2 \
  --publicly-accessible \
  --storage-encrypted \
  --enable-performance-insights \
  --performance-insights-retention-period 7 \
  --no-deletion-protection
  
  ```

This command should return back json

![json1](https://user-images.githubusercontent.com/113374279/226708106-bd2c34e6-eb8a-4796-8aad-e7271132051a.png)


Go to AWS RDS and check for database
![database-created](https://user-images.githubusercontent.com/113374279/226709470-aefede8a-5b0d-44b3-95db-8a5c3775bb74.png)


Click into database, and click on actions.  I am shutting it down temporarily. This is beacause i do not have immediate use for it. 
![actions-database](https://user-images.githubusercontent.com/113374279/226709894-a98da60f-5063-4e59-8c42-b0add82c1196.png)


![stop-temp](https://user-images.githubusercontent.com/113374279/226710332-be4b35fc-b2f9-45df-b5e4-b40e64b14468.png)

### PSQL Common commands

```

\x on -- expanded display when looking at data
\q -- Quit PSQL
\l -- List all databases
\c database_name -- Connect to a specific database
\dt -- List all tables in the current database
\d table_name -- Describe a specific table
\du -- List all users and their roles
\dn -- List all schemas in the current database
CREATE DATABASE database_name; -- Create a new database
DROP DATABASE database_name; -- Delete a database
CREATE TABLE table_name (column1 datatype1, column2 datatype2, ...); -- Create a new table
DROP TABLE table_name; -- Delete a table
SELECT column1, column2, ... FROM table_name WHERE condition; -- Select data from a table
INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...); -- Insert data into a table
UPDATE table_name SET column1 = value1, column2 = value2, ... WHERE condition; -- Update data in a table
DELETE FROM table_name WHERE condition; -- Delete data from a table

```

To test connection to postgres on the terminal

```
psql -Upostgres --host localhost

```

`\l ` List all databases

![list-database](https://user-images.githubusercontent.com/113374279/226714791-db982c17-11d3-40e7-bd20-6770787589bc.png)

TO Create a Database

`CREATE database cruddur;`

- In `backend-flask` create a folder named `db`. In `db` folder create a file named `schema.sql`. Add the following into into the file.

```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";


```

`\q` to quit posgres

- In backend-flask directory, run the script in `schema.sql` with 

`psql cruddur < db/schema.sql -h localhost -U postgres` (This command takes all the content of db/schema.sql and puts it in cruddur)

- Extension created. We have successfully run an external script

![external- script](https://user-images.githubusercontent.com/113374279/226736022-84c95249-0a4c-4ab0-8c9b-23dac3e13a3c.png)

- Connection String to Postgres


```
psql postgresql://postgres:password@localhost:5432/cruddur

```

![connection-string](https://user-images.githubusercontent.com/113374279/226770026-82d8884c-f33a-463b-855c-7caf47d15794.png)


- Save Connection URL string as an enviroment variable


```

export CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"

psql $CONNECTION_URL

gp env CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"

```

![connection-url](https://user-images.githubusercontent.com/113374279/226769963-7d8f7fc5-96de-4c9f-9158-2b442fe7631f.png)


- Save production URL string as an environment Variable


```

export PROD_CONNECTION_URL="postgresql://crudderroot:<password>@<RDS-endpoint>:5432/cruddur"
gp env PROD_CONNECTION_URL="postgresql://crudderroot:<password>@<RDS-endpoint>:5432/cruddur"

```

- RDS endpoint is on the created database in AWS


### Automating DB processes using Bash Scripting

Create a folder bin in backend-flask. Create three files  in these three files in the bin folder `db-create`, `db-drop`, `db-schema-load`.

`!# /usr/bin/bash` This is called the 'she bang' It starts every bash script this is how the terminal recognises a bash script.

Grant permission to the newly created files.  "chmod u+x bin/db-create", "chmod u+x bin/db-drop", "chmod u+x bin/db-schema-load" 

![chmod](https://user-images.githubusercontent.com/113374279/226856615-ff3d4190-f267-40f4-b15e-b3febe0d69b6.png)

- Add the following code into [db-create](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/bin/db-create)

```

#! /usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-create"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
psql $NO_DB_CONNECTION_URL -c "create database cruddur;"

```

- Run the script with `./bin/db-create`

- Add the following into [db-drop](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/bin/db-drop)

```
#! /usr/bin/bash

echo db-drop

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-drop"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
psql $NO_DB_CONNECTION_URL -c "drop database cruddur;"

```
- Run the script with `./bin/db-drop`

- Add the following into [db-schema-load](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/bin/db-schema-load)

```
#! /usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-schema-load"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

schema_path="$(realpath .)/db/schema.sql"
echo $schema_path

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $schema_path


```

- Run the script with `./bin/db-schema-load`

- [Change the output Color of echo in Linux](https://stackoverflow.com/questions/5947742/how-to-change-the-output-color-of-echo-in-linux)

- Made colour alterations to `db-drop`  `db-schema-load`


- Add the following code into [db-connect](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/bin/db-connect)


```

#! /usr/bin/bash


if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $URL

```

### Creating Tables

[Helpful document for creating Databases](https://www.postgresql.org/docs/current/sql-createtable.html)

In `Db` folder create a file [schema.sql](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/db/schema.sql)

- Add the following code into the newly created file

```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

---Forcefully drop the table if it exists--- 
DROP TABLE IF EXISTS public.users;
DROP TABLE IF EXISTS public.activities;

CREATE TABLE public.users (
  uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  display_name text,
  handle text,
  cognito_user_id text,
  created_at TIMESTAMP default current_timestamp NOT NULL
);

CREATE TABLE public.activities (
  uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_uuid UUID NOT NULL, 
  message text NOT NULL,
  replies_count integer DEFAULT 0,
  reposts_count integer DEFAULT 0,
  likes_count integer DEFAULT 0,
  reply_to_activity_uuid integer,
  expires_at TIMESTAMP,
  created_at TIMESTAMP default current_timestamp NOT NULL
);

```
This code below  was added because If this code runs twice, it'll create problems. This command basically drops the table if they already exist before creating them 

```
DROP TABLE IF EXISTS public.users;
DROP TABLE IF EXISTS public.activities;

```
- Run the scripts with `./bin/db-schema-load-prod`

![db-schemaload](https://user-images.githubusercontent.com/113374279/226872037-1c91d160-67e8-4995-b021-f134364740e8.png)

- Create a new /bin/file named `db-connect` grant permission `chmod u+x .bin/db-connect`

- Run the script with `./bin/db-connect`

This connects you automatically to the database.

To check if tables were created run the `\dt` command to confirm the existence of my table

![pub-active](https://user-images.githubusercontent.com/113374279/227705142-453dbc75-92ab-48ab-bf03-d2a713866d4d.png)



- Create a file `bin/db-seed` [db-seed](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/bin/db-seed)

- Run the script  `./bin/db-schema-load` and `./bin/db-seed`

![the-end](https://user-images.githubusercontent.com/113374279/226884931-db5eb3c9-c49a-4538-9795-b971262e30fe.png)


SELECT * FROM activities;

The data the comes up is quite unorganised.

![seed-data](https://user-images.githubusercontent.com/113374279/227705784-7bde4c10-d6e4-4819-8bfa-a6c87fae4398.png)



click on `q` to exit. Then use the `\x on` command. Then type in `SELECT * FROM activities;` again the output is a better data format and structure.

![x-on](https://user-images.githubusercontent.com/113374279/227705751-b6f22215-1a80-463a-9300-029349a05ef8.png)


Drop database

`./bin/db-drop`

![drop-database](https://user-images.githubusercontent.com/113374279/227705660-6e964a12-7129-432b-abdb-51252f95cf08.png)


In Backend-flask/bin create a file named [db-sessions](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/bin/db-sessions) Add the following into the file

```
#! /usr/bin/bash
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-sessions"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

NO_DB_URL=$(sed 's/\/cruddur//g' <<<"$URL")
psql $NO_DB_URL -c "select pid as process_id, \
       usename as user,  \
       datname as db, \
       client_addr, \
       application_name as app,\
       state \
from pg_stat_activity;"

```
- Grant the file executable permission by doing `chmod u+x bin/db-drop`

- Run `./bin/db-sessions`

![db-sessions](https://user-images.githubusercontent.com/113374279/227705849-7232b694-652d-4cc0-944b-f53b50f22274.png)


-  In backend-flask/bin create another file called `db-setup` and drop the following code in it. This helps to set up the work space immediately.

```
#! /usr/bin/bash
-e # stop if it fails at any point

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-setup"
printf "${CYAN}==== ${LABEL}${NO_COLOR}\n"

bin_path="$(realpath .)/bin"

source "$bin_path/db-drop"
source "$bin_path/db-create"
source "$bin_path/db-schema-load"
source "$bin_path/db-seed"

```

- Grant the file permissions `chmod u+x bin/db-setup`

### Get Driver Installed for Postgres

- Add to `requirements.txt` to install the Postgres Client

```
psycopg[binary]
psycopg[pool]

```

```
pip install -r requirements.txt

```

- In backend-flask/lib make a new file called [db.py](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/lib/db.py)

```
from psycopg_pool import ConnectionPool
import os

def query_wrap_object(template):
  sql = f"""
  (SELECT COALESCE(row_to_json(object_row),'{{}}'::json) FROM (
  {template}
  ) object_row);
  """
  return sql

def query_wrap_array(template):
  sql = f"""
  (SELECT COALESCE(array_to_json(array_agg(row_to_json(array_row))),'[]'::json) FROM (
  {template}
  ) array_row);
  """
  return sql

connection_url = os.getenv("CONNECTION_URL")
pool = ConnectionPool(connection_url)

```
- Add this to [docker-compose.yml](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/docker-compose.yml)

```
CONNECTION_URL: "postgresql://postgres:password@db:5432/cruddur"

```

- Add the following to [home_activities.py](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023/blob/main/backend-flask/services/home_activities.py)

```
from datetime import datetime, timedelta, timezone
from opentelemetry import trace

# import connection pool
from lib.db import pool, query_wrap_object, query_wrap_array 

# import logging  

tracer = trace.get_tracer("home.activities")

class HomeActivities:
  def run(cognito_user_id=None):
    # logger.info("HomeActivities")
    with tracer.start_as_current_span("home-activities-mock-data"):
      span = trace.get_current_span()
      now = datetime.now(timezone.utc).astimezone()
      span.set_attribute("app.now", now.isoformat())    
      
    # Connection pool for psycopg 
      sql = query_wrap_array("""
      SELECT
        activities.uuid,
        users.display_name,
        users.handle,
        activities.message,
        activities.replies_count,
        activities.reposts_count,
        activities.likes_count,
        activities.reply_to_activity_uuid,
        activities.expires_at,
        activities.created_at
      FROM public.activities
      LEFT JOIN public.users ON users.uuid = activities.user_uuid
      ORDER BY activities.created_at DESC
      """)  
      print(sql)
      with pool.connection() as conn:
        with conn.cursor() as cur:
          cur.execute(sql)
          # this will return a tuple
          # the first field being the data
          json = cur.fetchone()
      return json[0]
    
 ```  

- `docker-compose up` This is because we just installed some libraries

- Hit the frontend on port 3000

![imported-seed](https://user-images.githubusercontent.com/113374279/227706507-e70bc2fe-63a4-4a4e-b20b-d6dfefead813.png)

- Log-in to the app go to the backend logs to check for any errors. I had none.

![authenticated](https://user-images.githubusercontent.com/113374279/227706557-c36b2093-5042-4ff9-9a13-e3eb64f6fbba.png)

- Check to see connection to production URL

```
psql $PROD_CONNECTION_URL
```
This connection will not work.................. In order to make it work, go to RDS, click on Databases,click on the database cruddur, click on secrurity groups and edit inbound rules.

- To get Ip adress.

- To make it persistent

- To confirm

```
curl ifconfig.me

GITPOD_IP=$(curl ifconfig.me)

echo $GITPOD_IP

```

```
psql $PROD_CONNECTION_URL

```
Exit the data base

```
export DB_SG_ID="sg-0693a455d538197b7"
gp env DB_SG_ID="sg-0693a455d538197b7"
export DB_SG_RULE_ID="sgr-073342ba3ebc920e7"
gp env DB_SG_RULE_ID="sgr-073342ba3ebc920e7"

```

```
aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={Description=GITPOD,IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"
```

- Create a file in backend/lib and call it `rds-update-sg-rule`

- Add the following into the file

```
#! /usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="rds-update-sg-rule"
printf "${CYAN}==== ${LABEL}${NO_COLOR}\n"

aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={Description=GITPOD,IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"

```

- Grant the file permission

`chmod u+x bin/rds-update-sg-rule`

```
export GITPOD_IP=$(curl ifconfig.me)

```

- Run  the script `./bin/rds-update-sg-rule`

- Update `gitpod.yml` so the script runs everytime add this

```
    command: |
      export GITPOD_IP=$(curl ifconfig.me)
      source  "$THEIA_WORKSPACE_ROOT/backend-flask/bin/rds-update-sg-rule" 
```



















