# Docker

## Command to build Image

### Git

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t git:2.31.1 --target git "BuildTools2019"`

### MSTest 2019

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t mstest:2019 --target mstestfinal "BuildTools2019"`

### Build Tools 2019

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t buildtools2019:latest --target buildtoolsfinal "BuildTools2019"`

### Wix

`docker build --rm -f "Wix\Dockerfile" -t wix:311 "Wix"`
