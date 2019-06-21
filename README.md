## About
This repo is a holding area for NWB:N extensions destined to be a registered, public 
extension. To find out more about conda-forge, see 
https://github.com/nwb-extensions/nwb-extensions-smithy.

## Getting started

1. [Fork](https://help.github.com/en/articles/fork-a-repo) this repository.

2. Clone your fork to your local filesystem.

    ```bash
    git clone https://github.com/<your-github-username>/staged-extensions.git
    ```

2. Copy the directory `staged-extensions/example` to a new directory
with the name of your extension.

    ```bash
    cp -r staged-extensions/example staged-extensions/<your-extension-name>
    ```

3. Edit `staged-extensions/<your-extension-name>/ndx-meta.yaml`
with information on where to find your NWB extension.
    - The YAML file MUST contain a dict with the following keys:
      - `name` - The name of the extension
      - `version` - Version number of your extension
      - `src` - URL to the public repository with the sources of the extension
      - `pip` - URL for installing the extensions from PyPI
      - `license` - name of the license of the extension
      - `maintainers` - list of GitHub
      usernames of those who will reliably maintain the extension
      
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
to add information about your extension.

5. Commit and push your changes to GitHub. 

    ```bash
    git add .
    git commit -m "Created new extension"
    git push origin master
    ```

5. [Create a pull request](https://help.github.com/en/articles/creating-a-pull-request) for your changes. 

    Building of your extension will be tested on Windows,
Mac, and Linux. The technical team will review your extension shortly after
and provide feedback and request changes, if any.

6. When your pull request is merged, a new repository, called
`<your-extension-name>-feedstock` will be created in the [nwb-extensions
GitHub organization](https://github.com/nwb-extensions) with your 
modified `ndx-meta.yaml` and `README.md` files, 
and you will be added as a maintainer for that repository. This new repo
is the catalog entry for your extension. Please keep it up-to-date as you
develop your extension and publish new versions. 
