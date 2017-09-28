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

### Docker-Compose from Dockerfile
> This assumes there exists a `Dockerfile` file in the same directory at the `docker-compose.yml`:

```yaml
version: "3"
services:
  app:
    build: .
    entrypoint: "yarn --version"
```

### Docker-Compose from Remote Image
```yaml
version: "3"
services:
  app:
    image: zephinzer/alpine-node:latest
    entrypoint: "yarn --version"
```
