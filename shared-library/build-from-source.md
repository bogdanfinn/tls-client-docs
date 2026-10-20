# Build from source

When you want to build the shared libraries from source you can just run the following script: `cffi_dist/build.sh SOME_BUILD_IDENTIFIER` and it should build the binaries for you.

## Requirements

The script uses [xgo](https://github.com/crazy-max/xgo) to cross compile the shared libraries for the different platforms. xgo runs the build inside a Docker container that ships all the needed C/C++ cross compilers.

1. Install [Docker](https://docs.docker.com/get-docker/) and make sure the Docker daemon is running.
2. Pull the xgo image:

```shell
docker pull crazymax/xgo:latest
```

3. Install the xgo wrapper:

```shell
go install github.com/crazy-max/xgo@latest
```

4. Make sure that the Go bin directory is in your `PATH`, so that the `xgo` command can be found:

```shell
export PATH="$PATH:$(go env GOPATH)/bin"
xgo -h
```

The Go version inside the xgo image has to match at least the Go version in `cffi_dist/go.mod`. You can select the Go version with the `-go` flag, for example `xgo -go 1.27.x ...`. A list of the available versions can be found on [Docker Hub](https://hub.docker.com/r/crazymax/xgo/tags).
