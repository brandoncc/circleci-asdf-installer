circleci-asdf-installer
---

A tool that aims to simplify installing and using asdf 0.16+ in circleci configurations. This tool is built for ubuntu, but it
will probably work with other distributions. You will need to install any dependencies as your first step, before
running the other steps shown below.

**Note**: This tool now supports ASDF 0.16+ only. The binary version of ASDF is downloaded and installed automatically.

### Supported features

#### Legacy files

`legacy_version_file = yes` is turned on by default. If you want to turn it off, you need to add a step that erases
`~/.asdfrc` after the `install-asdf-plugins-and-versions` step.

The two files that are specifically supported are `.ruby-version` and `.nvmrc`. If one or both of these files are
present, the applicable plugins are installed using `plugin-add`.

### Usage

The following steps should be run in your configuration:

- `git clone https://github.com/brandoncc/circleci-asdf-installer.git ~/circleci-asdf-installer`
- `~/circleci-asdf-installer/bin/clone-asdf`
- `~/circleci-asdf-installer/bin/install-asdf-plugins-and-versions`

It is also a good idea to cache these directories:

- ~/.asdf/plugins
- ~/.asdf/installs

### Example configuration

```yaml
version: 2
jobs:
  build:
    docker:
      - image: cimg/base:2023.02

    steps:
      - checkout

      # Install build dependendencies
      - run: sudo apt-get update
      - run: sudo apt-get install libyaml-dev

      # Specify a Ruby version so we can test the installation
      - run: echo "ruby 3.2.1" > .tool-versions

      - run: git clone https://github.com/brandoncc/circleci-asdf-installer.git ~/circleci-asdf-installer
      - run: ~/circleci-asdf-installer/bin/clone-asdf

      - restore_cache:
          keys:
            - tools-cache-{{ checksum ".tool-versions" }}

      - run: ~/circleci-asdf-installer/bin/install-asdf-plugins-and-versions

      - save_cache:
          key: tools-cache-{{ checksum ".tool-versions" }}
          paths:
            - "~/.asdf/plugins"
            - "~/.asdf/installs"

      # Should print 3.2.1
      - run: ruby --version
```

### Inspiration

This repository was inspired by https://github.com/micke/asdf-docker. If you can use alpine for your project, I
recommend that you give that docker image a try.

### License

This repository uses the [MIT license](LICENSE).
