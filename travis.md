# Travis

1. [Pushing To Docker Hub](#pushing-to-docker-hub)

## Pushing To Docker Hub

Add following to your `.travis.yml` file:

```yaml
before_script:
  - docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD
after_script:
  - docker logout
```

In the build script, you can simply do:

```yaml
...
  - docker push username/repo-name:tag 
...
```