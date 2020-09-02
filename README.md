你好！
很冒昧用这样的方式来和你沟通，如有打扰请忽略我的提交哈。我是光年实验室（gnlab.com）的HR，在招Golang开发工程师，我们是一个技术型团队，技术氛围非常好。全职和兼职都可以，不过最好是全职，工作地点杭州。
我们公司是做流量增长的，Golang负责开发SAAS平台的应用，我们做的很多应用是全新的，工作非常有挑战也很有意思，是国内很多大厂的顾问。
如果有兴趣的话加我微信：13515810775  ，也可以访问 https://gnlab.com/，联系客服转发给HR。
# debos -  Debian OS images builder

## Sypnosis

    debos [options] <recipe file in YAML>
    debos [--help]

Application Options:

          --artifactdir=
      -t, --template-var=   Template variables
          --debug-shell     Fall into interactive shell on error
      -s, --shell=          Redefine interactive shell binary (default: bash)
          --scratchsize=    Size of disk backed scratch space


## Description

debos is a tool to make the creation of various Debian-based OS images
simpler. While most other tools focus on specific use-cases, debos is
more meant as a tool-chain to make common actions trivial while providing
enough rope to do whatever tweaking that might be required behind the scene.

debos expects a YAML file as input and will run the actions listed in the
file sequentially. These actions should be self-contained and independent
of each other.

Some of the actions provided by debos to customize and produce images are:

* apt: install packages and their dependencies with 'apt'
* debootstrap: construct the target rootfs with debootstrap
* download: download a single file from the internet
* filesystem-deploy: deploy a root filesystem to an image previously created
* image-partition: create an image file, make partitions and format them
* ostree-commit: create an OSTree commit from rootfs
* ostree-deploy: deploy an OSTree branch to the image
* overlay: do a recursive copy of directories or files to the target filesystem
* pack: create a tarball with the target filesystem
* raw: directly write a file to the output image at a given offset
* run: allows to run a command or script in the filesystem or in the host
* unpack: unpack files from archive in the filesystem

A full syntax description of all the debos actions can be found at:
https://godoc.org/github.com/go-debos/debos/actions

## Installation (under Debian)

    sudo apt install golang git libglib2.0-dev libostree-dev qemu-system-x86 \
         qemu-user-static debootstrap systemd-container
    export GOPATH=/opt/src/gocode # or whatever suites your needs
    go get -u github.com/go-debos/debos/cmd/debos
    /opt/src/gocode/bin/debos --help

## Simple example

The following example will create a arm64 image, install several
packages in it, change the file /etc/hostname to "debian" and finally
make a tarball.

    {{- $image := or .image "debian.tgz" -}}

    architecture: arm64

    actions:
      - action: debootstrap
        suite: "buster"
        components:
          - main
          - non-free
        mirror: https://deb.debian.org/debian
        variant: minbase

      - action: apt
        packages: [ sudo, openssh-server, adduser, systemd-sysv, firmware-linux ]

      - action: run
        chroot: true
        command: echo debian > /etc/hostname

      - action: pack
        file: {{ $image }}
        compression: gz

To run it, create a file named `example.yaml` and run:

    debos example.yaml

The final tarball will be named "debian.tgz" if you would like to modify
this name, you can provided a different name for the variable image like
this:

    debos -t image:"debian-arm64.tgz" example.yaml

## Other examples

This example builds a customized image for a Raspberry Pi 3.
https://github.com/go-debos/debos-recipes


## See also
fakemachine at https://github.com/go-debos/fakemachine

