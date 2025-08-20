## Generate go files

First, you have to compile the conjure-ir json file. Then use gödel to generate the conjure.

```sh
pushd ..; ./gradlew scout-service-api:compileIr; popd
./godelw conjure
```

Tidy and check your build:
```sh
go mod tidy
go build ./...
```

## Steps to reproduce this project from scratch

Install go 1.25. Then we need to add gödel. It's like gradle, but for golang projects, which no one asked for, but alas.

Check the wiki for more: https://github.com/palantir/godel/wiki/Add-godel

```sh
go install github.com/palantir/godel/v2/godelinit@latest
```

Now let's create the structure.

```sh
mkdir scout-service-api-go
cd scout-service-api-go
go mod init github.com/nominal-io/nominal-api
```

Initialize the gödel crap

```sh
godelinit
```

Add the gödel conjure plugin

```sh
echo 'plugins:
  resolvers:
    - "https://github.com/{{index GroupParts 1}}/{{index GroupParts 2}}/releases/download/v{{Version}}/{{Product}}-{{Version}}-{{OS}}-{{Arch}}.tgz"
  plugins:
    - locator:
        id: "com.palantir.godel-conjure-plugin:conjure-plugin:6.84.0"
        checksums:
          darwin-arm64: "ad493a2ff5a997380d0d96428f1b8dc2bdf47706b1ba3277053f3e0689043da9"' >> ./godel/config/godel.yml
```

Add the config for the conjure plugin

```sh
echo 'version: 1
projects:
  scout-service-api:
    output-dir: generated
    ir-locator: ../scout-service-api/build/conjure-ir/scout-service-api.conjure.json
' > ./godel/config/conjure-plugin.yml
```

Update gödel to download the plugin

```sh
./godelw update
```

You need to install the golang `witchcraft-go-error` package. If you don't have it installed, it generates code that uses the package without importing it. But if you install it, then it is imported and used. :shrug:

```sh
go get github.com/palantir/witchcraft-go-error
```

Now you can generate the golang code. You may need to compile the conjure IR first.

```sh
pushd ..; ./gradlew scout-service-api:compileIr; popd
./godelw conjure
```

Run `go mod tidy` to download your dependencies.

Finally, test it all works

```sh
go build ./...
```
