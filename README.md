## About
This repo is a place to submit NWB extensions for registration in the official
[Neurodata Without Borders Extensions Catalog](https://github.com/nwb-extensions).

## Registering an extension

To register an extension, you need to have a GitHub account and git installed on your
local machine.

Briefly, you will create a Pull Request on this repo with metadata about
the extension (e.g. name, version, URL to public Git repo, etc.), and then the catalog
maintainers will review the extension. When the extension is approved, a new repo
will be created in the [NDX Catalog](https://github.com/nwb-extensions) containing
the metadata that you entered. Other users will be able to browse and search the catalog
to find information about your extension and how to install and use it.

1. [Fork](https://help.github.com/en/articles/fork-a-repo) this repository.

2. Clone your fork to your local filesystem.

    ```bash
    git clone https://github.com/<your-github-username>/staged-extensions.git
    ```

2. Copy the folder `staged-extensions/example` to a new folder with the name of your
extension.

    ```bash
    cp -r staged-extensions/example staged-extensions/<your-extension-name>
    ```

3. Edit `staged-extensions/<your-extension-name>/ndx-meta.yaml`
with information about your extension.
    - The YAML file MUST contain a dictionary with the following keys:
      - `name` - name of the extension
      - `version` - version number of the extension
      - `src` - URL of the public Git repository with the sources of the extension
      - `pip` - URL of the package on PyPI of the extension
      - `license` - name of the license of the extension
      - `maintainers` - list of GitHub
      usernames of people who will reliably maintain the extension

    - For example, your YAML file should look something like this:
        ```yaml
        name: ndx-miniscope
        version: 0.1.0
        src: https://github.com/bendichter/ndx-miniscope
        pip: https://pypi.org/project/ndx-miniscope/
        license: BSD
        maintainers:
          - bendichter
        ```

4. Edit `staged-extensions/<your-extension-name>/README.md`
with a description of your extension and how to install and use it. You may
want to copy the README.md of your extension.

5. Add and commit your changes locally, and push your changes to GitHub.

    ```bash
    cd staged-extensions
    git add .
    git commit -m "Created new extension"
    git push origin master
    ```

5. [Create a pull request](https://help.github.com/en/articles/creating-a-pull-request)
on this repo for your changes.

    Building of your extension will be tested on Windows,
Mac, and Linux. The technical team will review your extension shortly after,
provide feedback, and request changes, if needed.

6. When your pull request is approved and merged, a new repository, called
`<your-extension-name>-record` will be created in the [nwb-extensions
GitHub organization](https://github.com/nwb-extensions) with your
extension's `ndx-meta.yaml` and `README.md` files. You will be added as a maintainer for that repository.

    This new repo is the NDX Catalog entry for your extension. It will be used to provide basic
information about your extension, including how to install it and where to find the source code --
similar to a card in a card catalog of early libraries. Please keep this repo up-to-date as you
develop your extension and release new versions.

## Behind the scenes

See [nwb-extensions-smithy](https://github.com/nwb-extensions/nwb-extensions-smithy) for more information
about the automation scripts that run behind the scenes.

## Copyright

Neurodata Extensions Catalog (NDX Catalog) Copyright (c) 2020,
The Regents of the University of California, through Lawrence
Berkeley National Laboratory (subject to receipt of any required
approvals from the U.S. Dept. of Energy).  All rights reserved.

If you have questions about your rights to use or distribute this software,
please contact Berkeley Lab's Intellectual Property Office at
IPO@lbl.gov.

NOTICE.  This Software was developed under funding from the U.S. Department
of Energy and the U.S. Government consequently retains certain rights.  As
such, the U.S. Government has been granted for itself and others acting on
its behalf a paid-up, nonexclusive, irrevocable, worldwide license in the
Software to reproduce, distribute copies to the public, prepare derivative
works, and perform publicly and display publicly, and to permit others to do so.
