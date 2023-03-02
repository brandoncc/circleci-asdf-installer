circleci-asdf-installer
---

A tool that aims to simplify installing and using asdf in circleci configurations.

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

      - run: ruby --version
```
