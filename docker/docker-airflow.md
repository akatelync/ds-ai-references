## Tutorial: Set Up Apache Airflow with Docker (LocalExecutor, Minimal Configuration)

This tutorial will guide you through setting up Apache Airflow using Docker Compose with the following characteristics:

- Uses `LocalExecutor` (suitable for a single machine, no Celery or Redis)
- Disables example DAGs
- Simplifies the setup for development and testing

### 1. Prepare the Project Structure

Create your working directory and the required subdirectories:

```bash
mkdir -p airflow
cd airflow
mkdir -p ./dags ./logs ./plugins ./config
```

These folders will be mounted into the Docker containers and used by Airflow to store DAGs, logs, plugins, and any custom configurations.

### 2. Create a `.env` File for Docker Permissions

Apache Airflow’s Docker setup requires your user ID to avoid permission issues with mounted volumes:

```bash
echo -e "AIRFLOW_UID=$(id -u)" > .env
```

This creates a `.env` file that will be automatically picked up by Docker Compose.

### 3. Create the `docker-compose.yaml` File

Save the following as `docker-compose.yaml` in the project root:

```yaml
version: "3"
x-airflow-common:
  &airflow-common
  image: apache/airflow:2.9.1-python3.10
  environment:
    &airflow-env
    AIRFLOW__CORE__EXECUTOR: LocalExecutor
    AIRFLOW__CORE__LOAD_EXAMPLES: "false"
    AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
    AIRFLOW__WEBSERVER__EXPOSE_CONFIG: "true"
  volumes:
    - ./dags:/opt/airflow/dags
    - ./logs:/opt/airflow/logs
    - ./plugins:/opt/airflow/plugins
  depends_on:
    - postgres

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
    volumes:
      - postgres-db-volume:/var/lib/postgresql/data

  airflow-webserver:
    <<: *airflow-common
    command: webserver
    ports:
      - "8080:8080"

  airflow-scheduler:
    <<: *airflow-common
    command: scheduler

  airflow-init:
    <<: *airflow-common
    command: version

volumes:
  postgres-db-volume:
```

Key configurations:

- `LocalExecutor` means tasks will run in parallel on the local machine without Celery.
- Example DAGs are disabled with `AIRFLOW__CORE__LOAD_EXAMPLES: "false"`.

### 4. Initialize the Airflow Database

This step initializes the metadata database:

```bash
docker compose up airflow-init
```

You should see a message indicating successful initialization.

### 5. Start the Airflow Webserver and Scheduler

Run the following to bring up Airflow in the background:

```bash
docker compose up -d
```

This starts:

- `airflow-webserver` on port 8080
- `airflow-scheduler`
- `postgres`

### 6. Verify the Running Containers

Check that all services are up:

```bash
docker ps
```

You should see the `webserver`, `scheduler`, and `postgres` containers running.

### 7. Access the Airflow Web Interface

Go to `http://localhost:8080` in your browser.

The default login credentials are:

- Username: `airflow`
    
- Password: `airflow`
    

If prompted to create an admin user instead, follow the on-screen instructions or use:

```bash
docker exec -it <webserver_container_name> airflow users create \
    --username airflow \
    --password airflow \
    --firstname Admin \
    --lastname User \
    --role Admin \
    --email admin@example.com
```

Replace `<webserver_container_name>` with the actual container name from `docker ps`.

### 8. Add Your DAGs

Save your DAG `.py` files inside the `./dags` directory. Airflow will automatically detect and load them.

### 9. Optional: Shut Down the Stack

To stop all containers and remove volumes:

```bash
docker compose down --volumes --remove-orphans
```
