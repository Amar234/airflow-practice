# airflow-practice
Writing DAG code in python for orchestrating task
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime


# Define dummy Python callables
def task_a():
    print("Running Task A")

def task_b():
    print("Running Task B")

def task_c():
    print("Running Task C - after A")

def task_d():
    print("Running Task D - after B and C")

# DAG definition
with DAG(
    dag_id='parallel_and_sequential_dag',
    start_date=datetime(2023, 1, 1),
    schedule_interval='@daily',  # Or use None for manual runs
    catchup=False,
    tags=['example'],
) as dag:

    t1 = PythonOperator(
        task_id='task_a',
        python_callable=task_a,
    )

    t2 = PythonOperator(
        task_id='task_b',
        python_callable=task_b,
    )

    t3 = PythonOperator(
        task_id='task_c',
        python_callable=task_c,
    )

    t4 = PythonOperator(
        task_id='task_d',
        python_callable=task_d,
    )

    # Define dependencies
    [t1, t2]  # A and B run in parallel
    t1 >> t3  # C after A
    [t2, t3] >> t4  # D after both B and C
