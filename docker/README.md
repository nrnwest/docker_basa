In this directory, the php_fpm image is provided, which can be used in both local and remote registries.
To build the image for the local registry, use the Makefile. Make sure make is installed on your system.

To build the image:
`cd docker`
`make create_image_php-fpm`

Other commands are described in the Makefile itself.
The Makefile is created for testing in a local environment.

The files in the php-fpm/ directory can be used for dev, stage, and prod environments after full testing on the dev server.

The php-fpm/config/ directory contains PHP configuration files, which need to be adjusted to fit the needs of your project.

Don't forget to set or pass the correct local user data when building the image. On most host machines, 
the values are typically `1000`:

`
ARG PUID=101
ARG PGID=102
`
