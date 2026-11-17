# Mind Card Notes - Dockerized

This repository was created to practice straightforward application containerization **without using Docker Compose**. 

It serves as a continuation of a project where the application was initially developed and run locally within a Python virtual environment (`.venv`) using a local MySQL database.

## Key Features & Architecture

- **Containerization**: The FastAPI application has been packaged into a Docker container.
- **File System Isolation**: The code was updated to store file-system interactions (such as `bio.txt` and JSON backups) inside a dedicated `data/` directory. This directory is linked to the host machine using a **Bind Mount** when starting the container.
- **Environment Secrets**: Sensitive credentials (including database passwords) are safely managed via an isolated `.env` file.
- **Database Persistence**: MySQL data is persisted using an isolated Docker volume created during the database container setup.
- **Custom Docker Network**: A dedicated Docker network provides a built-in DNS server, allowing the application container to communicate with the database container using its container name (`mind_card_db`) as specified in the environment variables.

## Documentation

For detailed setup, deployment steps, and database migration commands, please refer to [INSTRUCTION.md](INSTRUCTION.md). It covers:
- Creating the Docker network and volume.
- Running and configuring the MySQL and application containers.
- Building and pushing Docker images to Docker Hub (`nickptrnko/mind-card_notes`).
- Migrating existing database dumps from the host machine into the containerized database.