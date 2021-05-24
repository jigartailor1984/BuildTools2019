# Docker

## Command to build Image

### Build Tools 2019

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t buildtools2019:latest "BuildTools2019"`

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t buildtools2019:latest --target mstest "BuildTools2019"`

### Wix

`docker build --rm -f "Wix\Dockerfile" -t wix:311 "Wix"`
