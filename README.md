# Ultimate Docker Guide: Master Containers 🐳

A comprehensive guide to Docker commands with detailed notes for better understanding. Use this guide to master Docker like a pro! 🛠️

---

## 1. Set Up & Verify Docker 👤

Check that Docker is installed and the daemon is running:

| **Description**                     | **Command**                                      |
|-------------------------------------|-------------------------------------------------|
| Show the Docker version             | `docker --version` 🔢                           |
| Show client and server versions     | `docker version` 🔢                             |
| Show system-wide information        | `docker info` ℹ️                                |
| Run a test container                | `docker run hello-world` 👋                     |

**Notes**:
- On Windows and macOS, install **Docker Desktop** and make sure it is running before using any command.
- On Linux, run `sudo usermod -aG docker $USER` and log out/in to use Docker without `sudo`.
- If you see "Cannot connect to the Docker daemon", the Docker service is not running.
- `docker run hello-world` downloads a tiny image and prints a message. If it works, your setup is good.
- Use `docker <command> --help` to see all options for any command.

---

## 2. Working with Images 🖼️

An **image** is a read-only template (code, runtime, libraries) used to create containers:

| **Description**                     | **Command**                                      |
|-------------------------------------|-------------------------------------------------|
| Download an image                   | `docker pull <image>:<tag>` 📥                  |
| List local images                   | `docker images` 📋                              |
| Search Docker Hub for images        | `docker search <term>` 🔍                       |
| Show image layers/history           | `docker history <image>` 🕒                     |
| Show detailed image info            | `docker image inspect <image>` 🔎               |
| Add a new tag to an image           | `docker tag <source image> <new name>:<tag>` 🏷️ |
| Remove an image                     | `docker rmi <image>` 🗑️                        |
| Save an image to a tar file         | `docker save -o <file>.tar <image>` 💾          |
| Load an image from a tar file       | `docker load -i <file>.tar` 📦                  |

**Notes**:
- If you omit the tag, Docker uses `latest`. Avoid relying on `latest` in production; pin a specific version (e.g., `postgres:16`).
- Prefer small base images like `alpine` or `slim` variants for faster pulls and a smaller attack surface.
- You cannot remove an image that is used by a container. Remove the container first or use `docker rmi -f <image>`.
- Images are built from layers. Unchanged layers are cached and reused, which makes rebuilds fast.
- Use `docker image ls` as the modern equivalent of `docker images`.

---

## 3. Running & Managing Containers 📦

A **container** is a running instance of an image:

| **Description**                          | **Command**                                      |
|------------------------------------------|--------------------------------------------------|
| Run a container                          | `docker run <image>` ▶️                         |
| Run in the background (detached)         | `docker run -d <image>` 🌙                      |
| Run interactively with a shell           | `docker run -it <image> bash` 💻                |
| Give the container a name                | `docker run --name <name> <image>` 🏷️          |
| Map a port (host:container)              | `docker run -p <host port>:<container port> <image>` 🔌 |
| Set an environment variable              | `docker run -e KEY=value <image>` ⚙️            |
| Auto-remove the container when it exits  | `docker run --rm <image>` 🧹                    |
| Restart automatically                    | `docker run --restart unless-stopped <image>` 🔁 |
| List running containers                  | `docker ps` 📋                                  |
| List all containers (including stopped)  | `docker ps -a` 📋                               |
| Stop a container                         | `docker stop <container>` ⏹️                    |
| Start a stopped container                | `docker start <container>` ▶️                   |
| Restart a container                      | `docker restart <container>` 🔄                 |
| Pause / unpause a container              | `docker pause <container>` / `docker unpause <container>` ⏸️ |
| Kill a container immediately             | `docker kill <container>` 💥                    |
| Remove a stopped container               | `docker rm <container>` 🗑️                     |
| Force remove a running container         | `docker rm -f <container>` 🗑️                  |

**Notes**:
- `<container>` can be the container **name** or the first few characters of its **ID**.
- `-it` is a combination of `-i` (keep STDIN open) and `-t` (allocate a terminal). Use `sh` instead of `bash` on Alpine images.
- Port mapping is `HOST:CONTAINER`. For example, `-p 8080:80` makes the container's port 80 available at `localhost:8080`.
- `docker stop` sends `SIGTERM` and waits 10 seconds before sending `SIGKILL`. `docker kill` sends `SIGKILL` immediately.
- Use `-e` multiple times for multiple variables, or `--env-file .env` to load them from a file.
- Options must come **before** the image name. Anything after the image name is passed to the container as a command.
- Use `--rm` for short-lived or test containers so they don't pile up.

---

## 4. Logs, Inspect & Exec 🔎

Debug and interact with running containers:

| **Description**                          | **Command**                                      |
|------------------------------------------|--------------------------------------------------|
| View container logs                      | `docker logs <container>` 📜                    |
| Follow logs in real time                 | `docker logs -f <container>` 📡                 |
| Show only the last N lines               | `docker logs --tail 100 <container>` 📜         |
| Run a command in a running container     | `docker exec <container> <command>` ⚡          |
| Open a shell in a running container      | `docker exec -it <container> bash` 💻           |
| Show detailed container info             | `docker inspect <container>` 🔎                 |
| Show live CPU/memory usage               | `docker stats` 📊                               |
| Show running processes in a container    | `docker top <container>` 🧠                     |
| Show port mappings                       | `docker port <container>` 🔌                    |
| Copy a file from container to host       | `docker cp <container>:<path> <local path>` 📤  |
| Copy a file from host to container       | `docker cp <local path> <container>:<path>` 📥  |

**Notes**:
- `docker logs` shows whatever the main process writes to stdout/stderr. Log to the console in your app, not to files.
- Use `docker logs -t <container>` to add timestamps.
- `docker exec` only works on **running** containers. If a container keeps crashing, check `docker logs` first.
- `docker attach` connects to the main process, while `docker exec` starts a new one. Prefer `exec`.
- Use `docker inspect --format '{{.State.ExitCode}}' <container>` to see why a container exited.

---

## 5. Writing a Dockerfile 📝

A **Dockerfile** is a text file with instructions to build an image:

| **Instruction**   | **Purpose**                                                          |
|-------------------|----------------------------------------------------------------------|
| `FROM`            | Set the base image (always the first instruction) 🏁                |
| `WORKDIR`         | Set the working directory for the following instructions 📂         |
| `COPY`            | Copy files from your machine into the image 📋                      |
| `ADD`             | Like `COPY`, but also extracts archives and supports URLs ➕        |
| `RUN`             | Run a command during the build (installs, compiles, etc.) 🔧       |
| `ENV`             | Set an environment variable (available at build and run time) ⚙️   |
| `ARG`             | Define a build-time variable 🧩                                     |
| `EXPOSE`          | Document the port the app listens on 🔌                             |
| `USER`            | Set the user the container runs as 👤                               |
| `VOLUME`          | Declare a mount point for persistent data 💾                        |
| `HEALTHCHECK`     | Tell Docker how to test that the app is healthy ❤️                  |
| `ENTRYPOINT`      | The fixed executable that runs when the container starts 🚪         |
| `CMD`             | Default command or arguments (can be overridden at `docker run`) ▶️ |

### Basic Example:

```dockerfile
# Use a small official base image
FROM node:20-alpine

WORKDIR /app

# Copy dependency files first to take advantage of layer caching
COPY package*.json ./
RUN npm ci --omit=dev

# Copy the rest of the source code
COPY . .

EXPOSE 3000
USER node
CMD ["node", "server.js"]
```

### Multi-Stage Example (smaller final image):

```dockerfile
# Stage 1: build the app
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: serve only the build output
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
```

**Notes**:
- Put instructions that change **rarely** (installing dependencies) before ones that change **often** (copying source code) to get the most out of the build cache.
- Use `COPY` instead of `ADD` unless you specifically need its extra features.
- Use the **exec form** `CMD ["executable", "arg"]` instead of the shell form so signals like `SIGTERM` reach your app.
- `EXPOSE` does not publish a port. You still need `-p` when running the container.
- Create a `.dockerignore` file (like `.gitignore`) to keep `node_modules`, `.git`, `bin/`, `obj/`, and secrets out of the build context.
- Multi-stage builds keep compilers and build tools out of the final image.
- Never put secrets (passwords, API keys) in a Dockerfile. They stay in the image layers.
- Only the last `CMD` is used. If you set both `ENTRYPOINT` and `CMD`, `CMD` provides default arguments to `ENTRYPOINT`.

---

## 6. Building Images 🏗️

Turn a Dockerfile into an image:

| **Description**                          | **Command**                                      |
|------------------------------------------|--------------------------------------------------|
| Build an image from the current folder   | `docker build -t <name>:<tag> .` 🏗️            |
| Use a specific Dockerfile                | `docker build -f <Dockerfile name> -t <name>:<tag> .` 📝 |
| Build without using the cache            | `docker build --no-cache -t <name>:<tag> .` 🧼  |
| Pass a build argument                    | `docker build --build-arg KEY=value -t <name>:<tag> .` 🧩 |
| Build only up to a specific stage        | `docker build --target <stage> -t <name>:<tag> .` 🎯 |
| Build for multiple platforms             | `docker buildx build --platform linux/amd64,linux/arm64 -t <name>:<tag> .` 🌍 |

**Notes**:
- The `.` at the end is the **build context** (the folder sent to Docker). Don't forget it!
- Use `-t` to tag your image. Without a tag, you'll get an unnamed image that is hard to manage.
- Use `--no-cache` when you suspect a stale layer is causing problems.
- After building, run it with `docker run -p 8080:3000 <name>:<tag>`.

---

## 7. Registries (Docker Hub & Others) 🌐

Share images with others or deploy them to servers:

| **Description**                     | **Command**                                      |
|-------------------------------------|-------------------------------------------------|
| Log in to a registry                | `docker login` 🔑                               |
| Log out                             | `docker logout` 🚪                              |
| Tag an image for a registry         | `docker tag <name>:<tag> <username>/<name>:<tag>` 🏷️ |
| Push an image                       | `docker push <username>/<name>:<tag>` 📤        |
| Pull an image                       | `docker pull <username>/<name>:<tag>` 📥        |

**Notes**:
- For Docker Hub, the image name must start with your username: `<username>/<name>:<tag>`.
- For other registries, include the registry host: `docker login ghcr.io` and `ghcr.io/<username>/<name>:<tag>`.
- Use an **access token** instead of your password when logging in from scripts or CI.
- Use meaningful tags like `1.0.0` or a commit hash, and optionally also push `latest`.

---

## 8. Volumes & Persistent Data 💾

Data inside a container is **lost** when the container is removed. Use volumes to keep it:

| **Description**                          | **Command**                                      |
|------------------------------------------|--------------------------------------------------|
| Create a volume                          | `docker volume create <volume name>` ➕          |
| List volumes                             | `docker volume ls` 📋                           |
| Show volume details                      | `docker volume inspect <volume name>` 🔎        |
| Remove a volume                          | `docker volume rm <volume name>` 🗑️            |
| Remove all unused volumes                | `docker volume prune` 🧹                        |
| Mount a named volume                     | `docker run -v <volume name>:<container path> <image>` 💾 |
| Mount a host folder (bind mount)         | `docker run -v <host path>:<container path> <image>` 📂 |
| Mount a volume as read-only              | `docker run -v <volume name>:<container path>:ro <image>` 🔒 |

**Notes**:
- **Named volumes** are managed by Docker and are best for databases and persistent app data.
- **Bind mounts** map a folder from your machine into the container. They are great for development (live code reload).
- Use `$(pwd)` in Linux/macOS/Git Bash, `${PWD}` in PowerShell, or `%cd%` in Windows CMD to get the current folder.
- The newer `--mount type=volume,src=<name>,dst=<path>` syntax is more explicit than `-v`.
- Removing a container does **not** remove its named volumes. Use `docker rm -v <container>` or `docker volume rm` to clean them up.

---

## 9. Networking 🔌

Let containers talk to each other and to the outside world:

| **Description**                          | **Command**                                      |
|------------------------------------------|--------------------------------------------------|
| List networks                            | `docker network ls` 📋                          |
| Create a network                         | `docker network create <network name>` ➕        |
| Show network details                     | `docker network inspect <network name>` 🔎      |
| Run a container on a network             | `docker run --network <network name> <image>` 🌐 |
| Connect a running container to a network | `docker network connect <network name> <container>` 🔗 |
| Disconnect a container from a network   | `docker network disconnect <network name> <container>` ✂️ |
| Remove a network                         | `docker network rm <network name>` 🗑️          |

**Notes**:
- Common network drivers: **bridge** (default, isolated network on one host), **host** (shares the host's network, mainly on Linux), and **none** (no networking).
- Containers on the same **user-defined** network can reach each other by **container name** (e.g., `db:5432`). The default bridge network does not offer this.
- `localhost` inside a container refers to the container itself, not your machine.
- To reach a service running on your host from inside a container (Docker Desktop), use `host.docker.internal`.
- Only publish ports (`-p`) for services that need outside access. Keep databases on an internal network.

---

## 10. Docker Compose 🧩

Define and run multi-container apps with a single `docker-compose.yml` (or `compose.yaml`) file:

| **Description**                          | **Command**                                      |
|------------------------------------------|--------------------------------------------------|
| Start all services (foreground)          | `docker compose up` ▶️                          |
| Start all services in the background     | `docker compose up -d` 🌙                       |
| Rebuild images and start                 | `docker compose up -d --build` 🏗️              |
| Stop and remove containers and networks  | `docker compose down` ⏹️                        |
| Also remove volumes                      | `docker compose down -v` 🗑️                    |
| List the project's containers            | `docker compose ps` 📋                          |
| View logs of all services                | `docker compose logs -f` 📜                     |
| View logs of one service                 | `docker compose logs -f <service>` 📜           |
| Open a shell in a service                | `docker compose exec <service> sh` 💻           |
| Run a one-off command in a new container | `docker compose run --rm <service> <command>` ⚡ |
| Build or rebuild services                | `docker compose build` 🏗️                      |
| Stop / start services without removing   | `docker compose stop` / `docker compose start` ⏯️ |
| Restart a service                        | `docker compose restart <service>` 🔄           |
| Validate and view the final config       | `docker compose config` ✅                       |

### Example `docker-compose.yml`:

```yaml
services:
  web:
    build: .
    ports:
      - "8080:3000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/appdb
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: appdb
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

**Notes**:
- Modern Docker uses `docker compose` (with a space). The old standalone `docker-compose` (with a hyphen) is deprecated.
- The top-level `version:` key is obsolete and can be removed.
- Services in the same Compose file share a network automatically and can reach each other by **service name** (e.g., `db`).
- `depends_on` only controls **start order**, not whether the service is ready. Use a `healthcheck` with `condition: service_healthy` when you need to wait.
- Keep secrets out of the file: use a `.env` file (and add it to `.gitignore`) or `env_file:`.
- `docker compose down` keeps named volumes by default, so your data is safe unless you add `-v`.

---

## 11. Cleaning Up 🧹

Free disk space by removing unused Docker objects:

| **Description**                          | **Command**                                      |
|------------------------------------------|--------------------------------------------------|
| Show Docker disk usage                   | `docker system df` 📊                           |
| Remove all stopped containers            | `docker container prune` 🧹                     |
| Remove dangling images                   | `docker image prune` 🧹                         |
| Remove all unused images                 | `docker image prune -a` 🧹                      |
| Remove unused networks                   | `docker network prune` 🧹                       |
| Remove unused volumes                    | `docker volume prune` 🧹                        |
| Remove stopped containers, networks, dangling images | `docker system prune` 🧹            |
| Remove everything unused (including volumes) | `docker system prune -a --volumes` ⚠️      |
| Stop all running containers              | `docker stop $(docker ps -q)` ⏹️                |
| Remove all containers                    | `docker rm -f $(docker ps -aq)` 🗑️             |

**Notes**:
- **Dangling images** are untagged layers left over from rebuilds. They are safe to remove.
- `docker system prune -a --volumes` can delete data you care about (like database volumes). Review `docker system df` first.
- Prune commands ask for confirmation. Add `-f` to skip the prompt (use with care).
- The `$(...)` shortcuts work in Bash/Zsh. In PowerShell, use `docker ps -q | ForEach-Object { docker stop $_ }`.

---

## 12. Troubleshooting & Quick Fixes 🩺

| **Problem**                               | **Quick Fix**                                    |
|-------------------------------------------|--------------------------------------------------|
| Container exits immediately               | Check `docker logs <container>` and `docker inspect --format '{{.State.ExitCode}}' <container>` 🔍 |
| "port is already allocated"               | Use another host port (e.g., `-p 8081:80`) or stop the container using it 🔌 |
| "Cannot connect to the Docker daemon"     | Start Docker Desktop or the Docker service 🔄   |
| "permission denied" on the Docker socket  | Add your user to the `docker` group (Linux) 👤  |
| Changes not showing after rebuild         | Rebuild with `--no-cache` or check your `.dockerignore` 🧼 |
| Container can't reach another container   | Put both on the same user-defined network and use the container/service name 🌐 |
| Data disappeared after removing container | Use a named volume or bind mount 💾             |
| Out of disk space                         | Run `docker system df`, then prune unused objects 🧹 |

**Notes**:
- Use `docker run --rm -it <image> sh` to explore an image's file system quickly.
- Use `docker events` to watch what Docker is doing in real time.
- Use `docker compose config` to catch YAML or variable mistakes before starting.

---

That's it! You're now ready to use Docker like a pro. 🎉
