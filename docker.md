# Docker

1. [Templates](#templates)

## Templates

### Dockerfile Base
```dockerfile
FROM zephinzer/alpine-node:latest
LABEL maintainer=you@yourself.me
RUN echo 'it works'
COPY . /app
ENTRYPOINT ["yarn", "--version"]
```

### Docker-Compose from Remote Image
> This assumes `docker-compose.yml` resides in the same directory you're running the `docker-compose` command:

```yaml
version: "3"
services:
  app:
    image: zephinzer/alpine-node:latest
    entrypoint: "yarn --version"
```

Execute it with:

```bash
$ docker-compose run app
```

### Docker-Compose from Dockerfile in Project Root
> This assumes there exists a `Dockerfile` file in the same directory at the `docker-compose.yml`:

```yaml
version: "3"
services:
  app:
    build: .
    entrypoint: "yarn --version"
```

Execute it with:

```bash
$ docker-compose run app
```

### Docker-Compose from Dockerfile not in Project Root
> This assumes that `docker-compose.yml` resides in a `./provisioning` directory and that the `Dockerfile` resides in `./path/to/Dockerfile`.

```yaml
version: "3"
services:
  app:
    build:
      context: ../
      dockerfile: ./path/to/Dockerfile
    entrypoint: "yarn --version"
```

Execute it with:

```bash
$ docker-compose -f ./provisioning/docker-compose.yml run app
```