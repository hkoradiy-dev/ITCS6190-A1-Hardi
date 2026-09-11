# ITCS 6190/8190 — Assignment #1: Containers with Docker

**Name:** Hardi Koradiya  
**Student ID:** 801484363  
**Email:** hkoradiy@charlotte.edu  

---

## What the Stack Does

This project demonstrates a two-container application using Docker and Docker Compose.

The stack consists of:

- **PostgreSQL 16** — stores and initializes the trip data.
- **Python application** — connects to PostgreSQL, runs SQL queries, calculates the required statistics, prints the results, and writes the summary to `out/summary.json`.

The application and database communicate through the Docker Compose network using the database service name.

---

## How to Run It

Make sure **Docker Desktop is running** before starting the application.

From the root directory of the repository, run:

```bash
make
```

This builds and starts the Docker containers and runs the Python application.

You can also run the application directly with:

```bash
docker compose up --build
```

The application prints the summary to the terminal and generates the output file:

```text
out/summary.json
```

---

## How to Stop It

To stop the containers and remove the associated Docker volumes, run:

```bash
make down
```

Alternatively:

```bash
docker compose down -v
```

The `-v` option removes the PostgreSQL volume so that the database can be initialized again from `db/init.sql` the next time the stack is started.

---

## Example Output

The application produces the following output:

```text
=== Summary ===
{
  "total_trips": 6,
  "avg_fare_by_city": [
    {
      "city": "Charlotte",
      "avg_fare": 16.25
    },
    {
      "city": "New York",
      "avg_fare": 19.0
    },
    {
      "city": "San Francisco",
      "avg_fare": 20.25
    }
  ],
  "top_by_minutes": [
    {
      "city": "San Francisco",
      "minutes": 28,
      "fare": 29.3
    },
    {
      "city": "New York",
      "minutes": 26,
      "fare": 27.1
    },
    {
      "city": "Charlotte",
      "minutes": 21,
      "fare": 20.0
    },
    {
      "city": "Charlotte",
      "minutes": 12,
      "fare": 12.5
    },
    {
      "city": "San Francisco",
      "minutes": 11,
      "fare": 11.2
    },
    {
      "city": "New York",
      "minutes": 9,
      "fare": 10.9
    }
  ]
}
```

The application exits successfully with:

```text
app-1 exited with code 0
```

The `docker ps` command can be used to check the running containers:

```bash
docker ps
```

---

## Where Output Is Generated

The application generates the summary file at:

```text
out/summary.json
```

Inside the application container, the output is written to:

```text
/out/summary.json
```

The Docker Compose configuration mounts the local `out/` directory to `/out` inside the application container.

To view the generated output locally, run:

```bash
cat out/summary.json
```

---

## Database

The PostgreSQL database is initialized using:

```text
db/init.sql
```

The database contains a `trips` table with the following fields:

```text
id
city
minutes
fare
```

The application performs the following queries:

1. Counts the total number of trips.
2. Calculates the average fare for each city using:

```sql
ROUND(AVG(fare), 2) AS avg_fare
```

3. Retrieves the top trips by duration using:

```sql
ORDER BY minutes DESC, city ASC
```

The second ordering condition provides the required alphabetical city tie-break.

The top-trip output contains only:

```text
city
minutes
fare
```

---

## Troubleshooting

### Database is not ready

The application includes retry logic to wait for PostgreSQL to become available.

If the application cannot connect to the database, make sure Docker Desktop is running and restart the stack:

```bash
make down
make
```

### Stale Database Data

PostgreSQL initialization scripts run when the database is initialized.

To remove the existing database volume and initialize the database again, run:

```bash
make down
make
```

### Check Container Status

Use:

```bash
docker ps
```

To view logs from the application:

```bash
docker compose logs app
```

To view logs from PostgreSQL:

```bash
docker compose logs db
```

---

## Reflection

This assignment helped me understand how Docker Compose can coordinate multiple containers and how services communicate through the Compose service name. I learned how a PostgreSQL container can initialize its schema and seed data automatically using an SQL initialization script. I also learned how environment variables can pass database connection settings to a Python application without placing those settings directly in the application code. The healthcheck and retry logic make the application more reliable when the database is starting. In the future, I would add more application-level validation and additional queries or tests.