# Airflow ETL Docker Setup: A Comprehensive Beginner's Guide

Welcome! This guide will walk you through setting up a complete, production-style Apache Airflow environment on your local machine using Docker. We'll also build and run a sample ETL (Extract, Transform, Load) pipeline.

What is Apache Airflow?
Apache Airflow is a powerful tool used to programmatically author, schedule, and monitor workflows. Think of it as a way to manage complex chains of tasks, like data pipelines, in a reliable and observable way. For example: "Fetch data from an API every hour, clean it up, load it into a database, and then train a machine learning model."

## Part 1: Setting Up the Environment
### Prerequisites
Docker Desktop: A tool that allows you to build and run "containers" (isolated application environments). If you don't have it, you can download it from the official Docker website.
Docker Compose: A tool included with Docker Desktop that lets you manage multi-container applications using a simple configuration file.

### Project Structure
First, you need to create a specific folder structure on your computer. Airflow and Docker are configured to look for these exact folders.

airflow-etl-project/
├── dags/
├── logs/
├── plugins/
├── .env
├── docker-compose.yaml
├── Dockerfile
└── requirements.txt

### What are these folders for?

dags/: This is the most important folder. You will place all your workflow definitions (your Python DAG files) here. Airflow automatically scans this folder and loads your workflows.
logs/: Airflow will store the execution logs from your workflow runs in this folder.
plugins/: If you create custom Airflow plugins (like custom UI elements or hooks), they go here.
The other files are configuration files that we will use to define our setup.

## Step 1: Place the Project Files

Put the docker-compose.yaml, Dockerfile, .env, and requirements.txt files directly inside your airflow-etl-project/ directory. Place the stream_etl_dag.py file inside the dags/ subdirectory.

## Step 2: Configure Your User ID
This is a critical step to avoid file permission errors. When Airflow runs inside Docker, it needs to write files to the logs and dags folders. We need to make sure the Airflow user inside the container has the same user ID as your user outside the container.

Open your terminal (on Mac/Linux) or PowerShell (on Windows), navigate to your airflow-etl-project directory, and run this command:

```
echo "AIRFLOW_UID=$(id -u)" > .env
```
(For most Windows users with Docker Desktop, this command will also work in PowerShell. If not, you can manually open the .env file and type AIRFLOW_UID=1000)

## Step 3: Initialize the Airflow Database
Airflow needs a database to store its metadata (information about your DAGs, their runs, users, etc.). The first time you start Airflow, you need to initialize this database. Our docker-compose.yaml file has a special one-time service for this.

Run this command from your project directory:
```
docker-compose up airflow-init
```
You'll see a lot of logs as Airflow sets up its database tables. Once it's finished, it will create the default airflow user for you to log in with.

## Step 4: Start All Airflow Services
Now you can start the entire Airflow platform. This command starts all the services defined in docker-compose.yaml (webserver, scheduler, database, etc.) and runs them in the background (-d for "detached").

```
docker-compose up -d
```
To check if everything is running correctly, use: docker-compose ps. You should see several services with a "running" or "healthy" state.

To see the live logs from all services (very useful for debugging!), run: docker-compose logs -f

## Part 2: Using Airflow

## Step 5: Access the Airflow UI
Open your web browser and go to http://localhost:8080.

You will see the Airflow login screen. Use the default credentials:

Username: admin
Password: admin

You'll land on the main DAGs page. You should see our stream_data_etl DAG in the list.
