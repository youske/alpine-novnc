novnc
=================================

# about
A dockerfile to run X11 through the browser with noVNC
Inspired by
https://github.com/codenvy/dockerfiles/tree/master/x11_vnc


# settings

# build
## BUILD DOCKER:
    docker build -t              toastie/x11-novnc .

## using docker compose
    docker-compose build

# run
RUN DOCKER:	docker run  -it -p 8080:8080 toastie/x11-novnc
TEST DOCKER:	docker exec -it -p 8080:8080 toastie/x11-novnc /bin/bash
