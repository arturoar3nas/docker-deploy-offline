[![GitPitch](https://gitpitch.com/assets/badge.svg)](https://gitpitch.com/awesome-inc/docker-deploy-offline/master)

# docker-deploy-offline

How to use docker for (almost) continuous delivery to an offline environment.

## Packaging

Start a local registry, then build and push your images

```shell
docker-compose up -d registry ui
docker-compose build
docker-compose push
```

You can also configure another registry, that should be used as [pull through cache](https://docs.docker.com/registry/recipes/mirror/). That enables you to pull
custom images, e.g. from your company registry. To achieve this add the environment variable `REGISTRY_PROXY_REMOTEURL` to `docker-compose.override.yml`:

```yaml
environment:
    ...
    REGISTRY_PROXY_REMOTEURL: https://<your-awesome-registry>
```

Afterwards you can just pull your images into your local registry via:

```shell
docker pull localhost:5000/<repo>/<image>:<tag>
```

Fire up the registry frontend on [http://localhost:8080](http://localhost:8080) and watch your built images.
Docker images are contained in the docker volume `docker_images`.

After you have pushed all docker images to the registry, export the registry volume container

```shell
docker-compose run export
```

For motivational purposes wipe everything we just created, i.e.

```shell
docker-compose down
docker volume rm dockerdeployoffline_docker_images
docker rmi localhost:5000/mycompany/myapp:latest
```

## Deployment

At the customer site, create & import the registry volume container

```shell
docker-compose run import
```

Finally, start up the registry and you're good to go

```shell
docker-compose up -d registry ui
```
