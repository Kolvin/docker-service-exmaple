# Environment Visual

![Alt text](.docker/dev-env-visual.png?raw=true)

# Docker service example

This repository reflects how I typically structure an application using docker.

```
app/            // backend app, frontend app
.docker/        // docker build files
.env            // build environment variables
*-compose.yml   // docker compose files for Linux and MacOSX
```
____


This repo has been configured to run nginx with a proxy to a php-fpm container.