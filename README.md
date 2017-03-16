# `protoc-gen-gotemplate`
:open_file_folder: protocol generator + golang text/template (protobuf)

A generic **code**/script/data generator based on [Protobuf](https://developers.google.com/protocol-buffers/).

---

This project is a generator plugin for the Google Protocol Buffers compiler (`protoc`).

The plugin parses **protobuf** files, generates an **ast**, and walks a local **templates directory** to generate files using the [Golang's `text/template` engine](https://golang.org/pkg/text/template/).

## Philosophy

* protobuf-first
* no built-in template, only user defined templates
* kiss, *keep it stupid simple*

## Under the hood

1. the *user* `protobuf` files are parsed by [`protoc`](https://github.com/google/protobuf/releases)
2. the `ast` is generated by [`protoc-gen-go` helpers](https://github.com/golang/protobuf/tree/master/protoc-gen-go)
3. the `ast` is given to [Golang's `text/template` engine](https://golang.org/pkg/text/template/) for each *user* template files
4. the *funcmap* enriching the template engine is based on [Masterminds/sprig](https://github.com/Masterminds/sprig), and contains type-manipulation, iteration and language-specific helpers

## Usage

`protoc-gen-gotemplate` requires a **template_dir** directory *(by default `./templates`)*.

Every file ending with `.tmpl` will be processed and written to the destination folder, following the file hierarchy of the `template_dir`, and remove the `.tmpl` extension.

---

```console
$> ls -R
input.proto     templates/doc.txt.tmpl      templates/config.json.tmpl
$> protoc --gotemplate_out=. input.proto
$> ls -R
input.proto     templates/doc.txt.tmpl      templates/config.json.tmpl
doc.txt         config.json
```

---

You can specify a custom `template_dir` or enable `debug`:

```console
$> protoc --gotemplate_out=debug=true,template_dir=/path/to/template/directory:. input.proto
```

---

##### Hints

Shipping the templates with your project is very smart and useful when contributing on git-based projects.

Another workflow consists in having a dedicated repository for generic templates which is then versioned and vendored with multiple projects (npm package, golang vendor package, ...)

See [examples](./examples).

## Funcmap

This project uses [Masterminds/sprig](https://github.com/Masterminds/sprig) library and additional functions to extend the builtin [text/template](https://golang.org/pkg/text/template) helpers.

Non-exhaustive list of new helpers:

* **all the functions from [sprig](https://github.com/Masterminds/sprig)**
* `json`
* `prettyjson`
* `first`
* `last`

See the project helpers for the complete list.

## Install

* Install the **Go** compiler and tools from https://golang.org/doc/install
* Install **protobuf**: `go get -u github.com/golang/protobuf/{proto,protoc-gen-go}`
* Install **protoc-gen-gotemplate**: `go get -u github.com/moul/protoc-gen-gotemplate`

## Projects using `protoc-gen-gotemplate`

* [kafka-gateway](https://github.com/moul/kafka-gateway/): Kafka gateway/proxy (gRPC + http) using Go-Kit
* [translator](https://github.com/moul/translator): Translator Micro-service using Gettext and Go-Kit
* [acl](https://github.com/moul/acl): ACL micro-service (gRPC/protobuf + http/json)

## License

MIT
