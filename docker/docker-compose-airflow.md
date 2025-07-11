![[Screenshot 2025-07-11 at 6.32.53 PM.png]] 

- Each of these services work together

- Remove celery stuff, convert to local executor, simplify airflow
	- AIRFLOW__CORE__LOAD_EXAMPLES to false
-  mkdir -p ./dags ./logs ./plugins ./config
- echo -e "AIRFLOW_UID=$(id -u)" > .env
- docker compose up airflow-init
- docker compose up -d
- docker ps
- docker exec -t mlops- bash
