Pure-FTPd Docker Image
======================

[![GitHub release](https://img.shields.io/github/release/instrumentisto/pure-ftpd-docker-image.svg)](https://hub.docker.com/r/instrumentisto/pure-ftpd/tags) [![Build Status](https://travis-ci.org/instrumentisto/pure-ftpd-docker-image.svg?branch=master)](https://travis-ci.org/instrumentisto/pure-ftpd-docker-image) [![Docker Pulls](https://img.shields.io/docker/pulls/instrumentisto/pure-ftpd.svg)](https://hub.docker.com/r/instrumentisto/pure-ftpd) [![Uses](https://img.shields.io/badge/uses-s6--overlay-blue.svg)][21]




## What is Pure-FTPd?

Pure-FTPd is a free ([BSD][91]), secure, production-quality and standard-conformant FTP server. It doesn't provide useless bells and whistles, but focuses on efficiency and ease of use. It provides simple answers to common needs, plus unique useful features for personal users as well as hosting providers.

PureFTPd‘s mantra is ‘Security First.’ This is evident in the [low number of CVE entries][101].

> [www.pureftpd.org](https://www.pureftpd.org)

![Pure-FTPd Logo](https://www.pureftpd.org/images/pure-ftpd.png)




## How to use this image

To run Pure-FTPd server just start the container: 
```bash
docker run -d -p 21:21 -p 30000-30009:30000-30009 instrumentisto/pure-ftpd
```


### Why so many ports opened?
    
This is for `PASV` support, please see: [#5 PASV not fun :)][12]


### Configuration

By default it uses [default configuration file][10] `/etc/pure-ftpd.conf`.

1. You may either specify your own configuration file instead.

    ```bash
    docker run -d -p 21:21 \
               -v $(pwd)/my.conf:/etc/pure-ftpd.conf \
           instrumentisto/pure-ftpd
    ```

2. Or specify command line options directly.

    ```bash
    docker run -d -p 21:21 instrumentisto/pure-ftpd \
           pure-ftpd -c 50 -E -H -R
    ```
    
3. Or even specify another configuration file.

    ```bash
    docker run -d -p 21:21 \
               -v $(pwd)/my.conf:/my/pure-ftpd.conf \
           instrumentisto/pure-ftpd \
           pure-ftpd /my/pure-ftpd.conf
    ```


### Accounts

This image uses [PureDB][11] for virtual FTP accounts.

It's just enough to mount `/etc/pureftpd.passwd` file, which will be converted into `/etc/pureftpd.pdb` file on container start.

Location of `.passwd` file may be changed with `PURE_PASSWDFILE` env var. Location of `.pdb` file may be changed with `PURE_DBFILE` env var.

To generate `pureftpd.passwd` you may use `pure-pw` binary contained in image:
```bash
docker run --rm -it -v $(pwd)/my.passwd:/etc/pureftpd.passwd --entrypoint sh \
       instrumentisto/pure-ftpd \
           pure-pw useradd joe -u 90 -d /data/joe
```




## Image versions

This image is based on the popular [Alpine Linux project][1], available in [the alpine official image][2]. Alpine Linux is much smaller than most distribution base images (~5MB), and thus leads to much slimmer images in general.

This variant is highly recommended when final image size being as small as possible is desired. The main caveat to note is that it does use [musl libc][4] instead of [glibc and friends][5], so certain software might run into issues depending on the depth of their libc requirements. However, most software doesn't have an issue with this, so this variant is usually a very safe choice. See [this Hacker News comment thread][6] for more discussion of the issues that might arise and some pro/con comparisons of using Alpine-based images.


### `X`

Latest version of `X` Pure-FTPd major version.


### `X.Y`

Latest version of `X.Y` Pure-FTPd minor version.


### `X.Y.Z`

Concrete `X.Y.Z` version of Pure-FTPd.




## Important tips

As far as Pure-FTPd writes its logs only to `syslog`, the `syslogd` process runs inside container as second side-process and is supervised with [`s6` supervisor][20] provided by [`s6-overlay` project][21].


### Logs

The `syslogd` process of this image is configured to write everything to `/dev/stdout`.

To change this behaviour just mount your own `/etc/syslog.conf` file with desired log rules.


### s6-overlay

This image contains [`s6-overlay`][21] inside. So you may use all the [features it provides][22] if you need to.




## License

Pure-FTPd itself is licensed under [BSD license][91].

Pure-FTPd Docker image is licensed under [MIT license][92].




## Issues

We can't notice comments in the DockerHub so don't use them for reporting issue or asking question.

If you have any problems with or questions about this image, please contact us through a [GitHub issue][3].





[1]: http://alpinelinux.org
[2]: https://hub.docker.com/_/alpine
[3]: https://github.com/instrumentisto/pure-ftpd-docker-image/issues
[4]: http://www.musl-libc.org
[5]: http://www.etalabs.net/compare_libcs.html
[6]: https://news.ycombinator.com/item?id=10782897
[10]: https://github.com/jedisct1/pure-ftpd/blob/1.0.46/pure-ftpd.conf.in
[11]: https://download.pureftpd.org/pure-ftpd/doc/README.Virtual-Users
[12]: https://github.com/stilliard/docker-pure-ftpd/issues/5
[20]: http://skarnet.org/software/s6/overview.html
[21]: https://github.com/just-containers/s6-overlay
[22]: https://github.com/just-containers/s6-overlay#usage
[91]: https://download.pureftpd.org/pub/pure-ftpd/doc/COPYING
[92]: https://github.com/instrumentisto/pure-ftpd-docker-image/blob/master/LICENSE.md
[101]: https://nvd.nist.gov/view/vuln/search-results?query=pure-ftpd&search_type=all&cves=on
