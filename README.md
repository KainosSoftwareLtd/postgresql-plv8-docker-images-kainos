# postgresql-plv8-docker-images
Docker images of PostgreSQL with PLV8 extensions installed.

### Preparing a new image

1. Decide for which version of Postgres you want to create an image with the `plv8` extension. Let's use Postgres 12.10 in the example.
2. Check [in the matrix of Postgresql extension versions supported by AWS](https://docs.aws.amazon.com/AmazonRDS/latest/PostgreSQLReleaseNotes/postgresql-extensions.html) what version of the `plv8` extension is supported. In our example, this is `2.13.4`.
3. Create a new directory named by a template `postgresql-{postgresVersion}-plv8-{plv8Version}`, e.g. `postgresql-12.10-plv8-2.3.14`
4. Create a `Dockerfile` file inside a new directory based on the file taken from another directory. A directory with the highest version in the name will be the best choice because it will most likely work.
5. Adjust the `plv8` and Postgresql versions in the Dockerfile.
    ```
    FROM postgres:12.10
    (...)
    ENV PLV8_VERSION=2.3.14
    ```
6. Add a new image definition in the `docker-compose.yml` file in the root directory of this repo, e.g.:
    ```
    postgresql-12.10-plv8-2.3.14:
      build: postgresql-12.10-plv8-2.3.14
    ```
7. Build a docker image from a command line:
    ```
    docker build postgresql-12.10-plv8-2.3.14
    ```
8. When docker finishes building an image, look for the image ID at the end of the docker build log:
    ```
    => => writing image sha256:71119e257b8c271519293393c350b71f50210f8038dad83e0886ce0517f554e8
    ```
    alternatively, you can find the image ID by running a command:
    ```
    docker images
    ```
    and checking the `IMAGE ID` column, e.g.
    ```
    REPOSITORY   TAG      IMAGE ID       CREATED          SIZE
    <none>       <none>   71119e257b8c   23 minutes ago   420MB
    ```
9. Tag a new docker image with an appropriate name containing Postgresql and `plv8` extension versions, e.g.:
    ```
    docker tag 71119e257b8c draakhan/postgresql-plv8:12.10-2.3.14
    ```
    You can check if it is tagged correctly by running again `docker images` command:
    ```
    REPOSITORY                 TAG            IMAGE ID       CREATED             SIZE
    draakhan/postgresql-plv8   12.10-2.3.14   71119e257b8c   About an hour ago   420MB
    ```
10. Login to your docker repository (by default, it is [Docker Hub](https://hub.docker.com/)) using `docker login` command and then push the tagged image to the Docker Hub:
    ```
    docker push draakhan/postgresql-plv8:12.10-2.3.14
    ```
11. Now, you're ready to use the uploaded image in your project's `Dockerfile` by adding this statement:
    ```
    FROM draakhan/postgresql-plv8:12.10-2.3.14
    ```
