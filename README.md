# Rails 7 + Docker + PostgreSQL Setup

This guide explains how to set up a Rails 7 application with PostgreSQL using Docker.

---

## 1. PostgreSQL Setup

1. Create a new Rails application with a PostgreSQL database.
2. Update the `config/database.yml` file with your database configuration.
3. Modify the `Dockerfile` according to your project setup.
4. Create a `docker-compose.yml` file in the root directory of your application.

---

### `docker-compose.yml` Example

```yaml
services:
  web:
    build: .
    ports:
      - "3002:3000"
    volumes:
      - .:/rails
    depends_on:
      - db
    environment:
      RAILS_ENV: development
      DATABASE_URL: postgres://shyam:shyam@db:5432/docker_postgres_development
    command: ./bin/rails server -b 0.0.0.0

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: shyam
      POSTGRES_PASSWORD: shyam
      POSTGRES_DB: docker_postgres_development
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata: