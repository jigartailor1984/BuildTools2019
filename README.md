# Docker

## Command to build Image

### nuget

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t nuget:5.8.0 --target nugetfinal "BuildTools2019"`

### Git

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t git:2.31.1 --target gitfinal "BuildTools2019"`

### MSTest 2019

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t mstest:2019 --target mstestfinal "BuildTools2019"`

### Build Tools 2019

`docker build --pull --rm -f "BuildTools2019\Dockerfile" -t buildtools:2019 --target buildtoolsfinal "BuildTools2019"`

### Wix

`docker build --rm -f "Wix\Dockerfile" -t wix:311 "Wix"`
