**Name:** Hardi Koradiya  
**Student ID:** 801484363 
**Email:**hkoradiy@charlotte.edu 

## What the Stack Does

This project demonstrates a two-container application using Docker and Docker Compose. One container runs a PostgreSQL database that is initialized with a `trips` table and sample data from `db/init.sql`. The second container runs a Python application that connects to PostgreSQL, executes SQL queries, calculates basic statistics, prints the results, and writes the summary to `out/summary.json`.


## How to Run

Make sure Docker Desktop is running.

From the root directory of the project, run:

```bash
make
```

This command cleans the previous Docker environment, builds the required images, starts the PostgreSQL and Python services, and generates the application output.

You can also run the stack directly with:

```bash
docker compose up --build
```

## How to Stop the Application

To stop the containers and remove the Docker volumes, run:

```bash
make down
```

Alternatively:

```bash
docker compose down -v
```

The `-v` option removes the database volume so that the initialization script can run again when the database is recreated.

## Example Output

The application produced the following summary:

```json
=== Summary ===
app-1  | {
app-1  |   "total_trips": 6,
app-1  |   "avg_fare_by_city": [
app-1  |     {
app-1  |       "city": "Charlotte",
app-1  |       "avg_fare": 16.25
app-1  |     },
app-1  |     {
app-1  |       "city": "New York",
app-1  |       "avg_fare": 19.0
app-1  |     },
app-1  |     {
app-1  |       "city": "San Francisco",
app-1  |       "avg_fare": 20.25
app-1  |     }
app-1  |   ],
app-1  |   "top_by_minutes": [
app-1  |     {
app-1  |       "id": 6,
app-1  |       "city": "San Francisco",
app-1  |       "minutes": 28,
app-1  |       "fare": 29.3
app-1  |     },
app-1  |     {
app-1  |       "id": 4,
app-1  |       "city": "New York",
app-1  |       "minutes": 26,
app-1  |       "fare": 27.1
app-1  |     },
app-1  |     {
app-1  |       "id": 2,
app-1  |       "city": "Charlotte",
app-1  |       "minutes": 21,
app-1  |       "fare": 20.0
app-1  |     },
app-1  |     {
app-1  |       "id": 1,
app-1  |       "city": "Charlotte",
app-1  |       "minutes": 12,
app-1  |       "fare": 12.5
app-1  |     },
app-1  |     {
app-1  |       "id": 5,
app-1  |       "city": "San Francisco",
app-1  |       "minutes": 11,
app-1  |       "fare": 11.2
app-1  |     },
app-1  |     {
app-1  |       "id": 3,
app-1  |       "city": "New York",
app-1  |       "minutes": 9,
app-1  |       "fare": 10.9
app-1  |     }
app-1  |   ]
app-1  | }
app-1 exited with code 0
```

## Where Output Is Generated
The application writes the summary to:
```text
out/summary.json
```
Inside the Python container, the file is written to:
```text
/out/summary.json
```

The Docker Compose configuration bind-mounts the local `out/` directory to `/out` in the application container.

To view the generated file locally, run:

```bash
cat out/summary.json
```

## Database

The PostgreSQL database is initialized using:

```text
db/init.sql
```

The database contains the following table:

```text
trips
├── id
├── city
├── minutes
└── fare
```

The database service uses PostgreSQL 16.

The Python application connects to the database using the Docker Compose service name:

```text
DB_HOST=db
```

The application also receives the database port, username, password, and database name through environment variables.

## Troubleshooting

### Database is not ready

The Compose configuration uses a PostgreSQL healthcheck and waits for the database service to become healthy before starting the application. The Python application also retries the database connection if necessary.

If the connection still fails, check that the database credentials in `compose.yml` match the values used by the Python application.

### Permission errors with `out/`

If a permission error occurs when writing to the `out/` directory, check the permissions of the directory and recreate it with:

```bash
make clean
```

On Linux, the following command can also be used:

```bash
sudo chown -R $USER out
```

### Stale Database Data

The PostgreSQL initialization script runs only when the database is initialized for the first time.

To remove the existing database volume and initialize the database again, run:

```bash
make down
make
```

## Docker Services

The project contains two services:

| Service | Purpose |
|---|---|
| `db` | PostgreSQL 16 database |
| `app` | Python application that queries PostgreSQL |

The application communicates with PostgreSQL over the internal Docker Compose network.

## Technologies Used

- Docker
- Docker Compose
- PostgreSQL 16
- Python 3.11
- psycopg
- SQL
- Git and GitHub
