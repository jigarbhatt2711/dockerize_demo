# POSTGRES SETUP

This guide provides the instructions to containerize a Rails application using PostgreSQL and Docker Compose.

## Steps to Setup

1. **Create a new application** with postgres database.
2. **Make the changes** on the `database.yml` file.
3. **Make the changes** on the `Dockerfile` as per your configurations.
4. **Create the new `docker-compose.yml` file** in the root directory of your app.

### docker-compose.yml file:

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
```


## 5. Build & Run Container with the following commands:
	docker compose build   		---> docker image will be created 
	docker compose up      	 	---> docker container will be running
	docker compose exec web bash 	---> docker container bash will be accessed.
	
	
## 6. Explaination of point 4 & 5:

when we run the docker compose build, that time it will execute the build from the docker-compose.yml file.


(A) docker compose build:

For web service
	Docker looks at:
	web:
	  build: .
  
     It:
	-> Goes to your project root (.)
	-> Finds your Dockerfile
	-> Builds an image (installs Ruby, Rails, gems, etc.)
	-> Stores it locally

Image for web → freshly built
Image for db → pulled (or reused)



(B) docker compose up:

Step 1: Network creation:

	Docker creates a default network:
		<project_name>_default
		
	So services can talk like:
	web → db (using hostname db)

Step 2: Volume creation:

	volumes:
	  - pgdata:/var/lib/postgresql/data
	  
	Docker creates a named volume: pgdata
	This persists PostgreSQL data even if container is removed


Step 3: Start db container:

	db:
	  image: postgres:15
	  
	Docker:
	Starts PostgreSQL

	Applies:
	 --> POSTGRES_USER: shyam
	 --> POSTGRES_PASSWORD: shyam
	 --> POSTGRES_DB: docker_postgres_development
	 
	Mounts:
	 --> pgdata → /var/lib/postgresql/data
 
 
 
Step 4: Start web container:

	web:
	  build: .
	  volumes:
	    - .:/rails
	  depends_on:
	    - db
	    	    
	    
	Final Flow:

		Browser (localhost:3002)
			↓
		Docker port mapping
			↓
		Rails app (web container :3000)
			↓
		Database connection
			↓
		Postgres (db container)




## 7. Other Commands:

	docker compose up -d        # run in background
	docker compose logs -f web  # see logs
	docker compose exec web bash
	docker compose down         # stop everything