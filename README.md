## Maven over docker commands

Usefull when java and maven are  not installed locally on developer's machine.

All application files will be generated in docker image and mapped to my-app local directory.

```bash
docker run -it --rm -v "$PWD":app -w app maven:3.5-jdk-8 mvn archetype-generate\
  -DgroupId=io.microsamples.docker\
  -DartifactId=docker-run\
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DinteractiveMode=false

cd my-app

docker run -it --rm -v "$PWD":app -w app maven:3.5-jdk-8 mvn package

docker run -it --rm -v "$PWD":app -w app maven:3.5-jdk-8 java -cp target/my-app-1.0-SNAPSHOT.jar io.microsamples.docker.App

```
## Multistage build with attached `Dockerfile`

```bash
docker build --build-arg url=https://github.com/poprygun/echo.git\
  --build-arg project=echo\
  --build-arg artifactid=echo\
  --build-arg version=0.0.1-SNAPSHOT\
  -t poprygun/echo - < Dockerfile

docker run -it -p8080:8080 poprygun/echo

docker tag poprygun/echo host.pcfdev.io:5000/echo

<!-- for new version of PCF Dev -->
docker tag poprygun/echo host.cfdev.sh:5000/echo
```

## Create local docker registry

_* Note, for new version of PCF Dev, use `host.cfdev.sh` instead of `host.pcfdev.io`_

```bash
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

## Push image to local registry

```bash
docker push host.pcfdev.io:5000/echo
```

## Verify image was pushed

```bash
http http://localhost:5000/v2/_catalog
HTTP/1.1 200 OK
Content-Length: 26
Content-Type: application/json; charset=utf-8
Date: Fri, 07 Sep 2018 15:29:05 GMT
Docker-Distribution-Api-Version: registry/2.0
X-Content-Type-Options: nosniff

{
    "repositories": [
        "echo"
    ]
}
```

## Make sure PCF Dev is started specifying local docker registry

```bash
cf dev start -r host.pcfdev.io:5000
```

## Push application from local docker registry to PCF Dev

```bash
cf push echo --docker-image host.pcfdev.io:5000/echo -m 512M
```

## Cleanup when done

```bash
docker container stop registry
cf dev stop
cf dev destroy
```
