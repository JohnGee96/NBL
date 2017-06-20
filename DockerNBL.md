# Docker Nota Bene List 

## Best Practice Writing Dockerfile:

-   [**Offical Documentation**](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices)

- **Build Cache**
    * If you do not want to use the cache, use the `--no-cache=true` option on the docker build command

    * **CLEAN UP** apt caches by removing `/var/lib/apt/lists`

    * Rules:
        1. Instructions are compared to validate a cache hit
        2. For `ADD` and `COPY` instructions, files in the images are compared.

- **Instructions**
    * FROM
        * `FROM <image>` or 
        * `FROM <image>:<tag>` or
        * `FROM <image>@<digest>`
            * Omitting `<tag>` or `<digest` will assume the latest version
            * Debian image is preferred over Ubuntu (Offical Docker site)

    * LABEL
        * Labeling the image can record licensing info and better manage the image itself.
        * Use quotation or to escape the space or `"` character
        * Ex)

                # Set one or more individual labels
                LABEL com.example.version="0.0.1-beta"
                LABEL vendor="ACME Incorporated"
                LABEL com.example.release-date="2015-02-12"
                LABEL com.example.version.is-production=""

                # Set multiple labels at once, using line-continuation characters to break long lines
                LABEL vendor=ACME\ Incorporated \
                    com.example.is-beta= \
                    com.example.is-production="" \
                    com.example.version="0.0.1-beta" \
                    com.example.release-date="2015-02-12"

        
    * RUN
        * Avoid `RUN apt-get upgrade` or `dist-upgrade` in unpriviledged container
        * Always combine `RUN apt-get update` with `apt-get install`
            * Using apt-get update alone in a RUN statement causes caching issues and subsequent apt-get install instructions fail.

                    # This is called cache busting
                    RUN apt-get update && apt-get install -y \
                        package-bar \
                        package-baz \
                        package-foo
            
            * The `RUN apt-get update` line may cause Docker to reuse cache from previous steps, failing to update the package.
        
    * Pipe
        * Docker uses `/bin/sh -c` interpreter which only evaluates the exit code of the last operation in the pipe to determine success

                RUN wget -O - https://some.site | wc -l > /number

            - Ex Above: build step succeeds and produces a new image so long as the `wc -l` command succeeds, even if the `wget` command fails.

            - If you want the command to fail due to an error at any stage in the pipe, prepend `set -o pipefail &&` to ensure that an unexpected error prevents the build from inadvertently succeeding. For example:

                    RUN set -o pipefail && wget -O - https://some.site | wc -l > /number

    * CMD
        * Run software

                CMD [“executable”, “param1”, “param2”…]

        * Run services (by including service-based image)

                CMD ["apache2","-DFOREGROUND"]

        * In most cases, CMD is used to engage user in interactive shell
          after running the image. For example `CMD ["python"]` will drop to shell when running `docker run -it python`

    * EXPOSE
        * [Offical Documentaion for this instruction](https://docs.docker.com/engine/reference/builder/#expose)
        * Indicate the ports on which docker container listen for connection

    * ENV
        * Update the `PATH` environment variable.

        * For example `ENV PATH /usr/local/nginx/bin:$PATH`

        * Lastly, ENV can also be used to set commonly used version numbers so that version bumps are easier to maintain, as seen in the following example:

                ENV PG_MAJOR 9.3
                ENV PG_VERSION 9.3.4
                RUN curl -SL http://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /usr/src/postgress && …
                ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH

    * ADD or COPY

        * Both are similar, but `COPY` is preferred. 

        * `COPY` only copy from local directories, but `ADD` can add packages from URL and external links

        * To copy mulitples files, declare `COPY` command for each file to utilize caching (copy only when a file is updated)

        * Used `wget` or `curl` instead of `ADD` for external packages so
        you can remove them when necessary and avoid adding layers in the image

            * Instead of 

                    ADD http://example.com/big.tar.xz /usr/src/things/
                    RUN tar -xJf /usr/src/things/big.tar.xz -C /usr/src/things
                    RUN make -C /usr/src/things all

            * Do

                    RUN mkdir -p /usr/src/things \
                        && curl -SL http://example.com/big.tar.xz \
                        | tar -xJC /usr/src/things \
                        && make -C /usr/src/things all

    * ENTRYPOINT
        * The best use for `ENTRYPOINT` is to set the image’s main command, allowing that image to be run as though it was that command (and then use CMD as the default flags).

                ENTRYPOINT ["s3cmd"]
                CMD ["--help"]