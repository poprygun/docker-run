## Maven over docker commands

Usefull when java and maven are  not installed locally on developer's machine.

All application files will be generated in docker image and mapped to my-app local directory.

```bash
docker run -it --rm -v "$PWD":app -w app maven:3.5-jdk-8 mvn archetype-generate\
  -DgroupId=io.microsamples.docker\
  -DartifactId=docker-run\
  -DarchetypeArtifactId=maven-archetype-quickstart
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
```
