
# 📚 Circleci Orb Common Tools

<p align="center">
    <a href="https://circleci.com/orbs/registry/orb/onimur/common-tools" title="Orb Version">
        <img src="https://img.shields.io/endpoint.svg?url=https://badges.circleci.io/orb/onimur/common-tools">
    </a>
    <a href="./LICENSE" title="License">
        <img src="https://img.shields.io/github/license/onimur/circleci-common-tools">
    </a>
    <a href="https://circleci.com/gh/onimur/circleci-common-tools" title="onimur">
        <img src="https://circleci.com/gh/onimur/circleci-common-tools.svg?style=shield">
    </a>
</p>

Collection of tools to help create commands, jobs and workflows.
Orb for CircleCI to help with files or folders that should `persist_to_workspace` and retrieve the path easily with `attach_workspace`.Now you can persist files or directories between jobs and workflows.
And tools to help with git commands.

This orb is registered with CircleCI, see [here](https://circleci.com/orbs/registry/orb/onimur/common-tools).

<p align="center">
    <a href="https://circleci.com/orbs/registry/orb/onimur/common-tools" title="Circleci Orb Common Tools">
        <img width="35%" src=".github/resources/logo_git.png">
    </a>
</p>

## 💞 Support us

We are developing this structure in the open source community without financial planning.
If you like this project and would like to help us, make a donation:

<p align="center">
    <a href="https://www.patreon.com/onimur" target="_blank">
        <img width="30%" alt="Check my Patreon" src="https://raw.githubusercontent.com/onimur/.github/master/.resources/support-patreon.png"/>
    </a>
    <a href="https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=YUTBBKXR2XCPJ" target="_blank">
        <img width="30%" alt="Donate with Paypal" src="https://raw.githubusercontent.com/onimur/.github/master/.resources/support-paypal.png"/>
    </a>
    <a href="https://www.buymeacoffee.com/onimur" target="_blank">
        <img width="30%" alt="Buy me a coffee" src="https://raw.githubusercontent.com/onimur/.github/master/.resources/support-buy-coffee.png"/>
    </a>
</p>

## 📝 Content

- [Usage](#-usage)
- [Commands](#%EF%B8%8F-commands)
  - [Git](#git)
  - [Persist Attach](#persist-attach)
- [Projects using this library](#-projects-using-this-library)
- [Main features](#-main-features)
- [Contributing](#-contributing)
- [License](#-license)

## 💡 Usage
In your config.yml

### Example with Git Commands
For these example you can use [these commands](#git).

```yml
version: 2.1

orbs:
  tools: onimur/common-tools@x.y.z

orb_promotion_filters: &orb_promotion_filters
  branches:
    ignore: /.*/
  tags:
    only: /.*/ # Trigger for any tags

jobs:
  push-to-git:
    machine: true
    steps:
      - tools/git-checkout:           # Checkout tag to branch
          branch: master
      - run:
          name: Create a file.
          command: |
            mkdir -p second
            echo "Hello world!" >> hello.txt
            echo "Hello world, again!" >> second/hello-again.txt
      - tools/git-config    # Cofnig bot-name and bot-email to push
      - tools/git-push:     # Push the file or folder to remote repository
          paths: hello.txt, second

workflows:
  prepare-workflows:
    jobs:
      - push-to-git:
          filters: *orb_promotion_filters
```

### Example with Persist and Attach Commands
For these example you can use [these commands](#persist-attach).

```yml
version: 2.1

orbs:
  tools: onimur/common-tools@x.y.z

jobs:
  prepare:
    machine: true
    steps:
      - run:
          name: Create a file.
          command: |
            mkdir -p second
            echo "Hello world!" >> hello.txt
            echo "Hello world, again!" >> second/hello-again.txt
      # pass the file or folder
      - tools/persist-dir:
          paths: hello.txt, second

  recover-files:
    machine: true
    steps:
      # recover file or folder
      - tools/attach-dir
      - run:
          name: Get the file
          command: |
            cat "./persist/hello.txt"
            cat "./persist/second/hello-again.txt"

workflows:
  prepare-workflows:
    jobs:
      - prepare
      - recover-files:
          requires: [prepare]
```

## 🛠️ Commands

### Git
<details>
  <summary>Click to expand!</summary>

#### git-config

| parameter |      description     | required |          default         |  type  |
|:---------:|:--------------------:|:--------:|:------------------------:|:------:|
| bot-email | Email to commit      |     -    | circleci@bot-noreply.com | string |
| bot-name  | User name to commit  |     -    | circleci-bot             | string |

#### git-checkout

| parameter |     description     | required | default |  type  |
|:---------:|:-------------------:|:--------:|:-------:|:------:|
| branch    | Branch to checkout  |     -    | master  | string |

#### git-push

|    parameter   |                                    description                                    | required |          default         |     type     |
|:--------------:|:---------------------------------------------------------------------------------:|:--------:|:------------------------:|:------------:|
| bot-email      | Email to commit                                                                   |     -    | circleci@bot-noreply.com | string       |
| bot-name       | User name to commit                                                               |     -    | circleci-bot             | string       |
| branch         | Branch to push changelog                                                          |     -    | master                   | string       |
| commit-message | Commit message                                                                    |     -    | Auto Update changelog    | string       |
| paths          | Output files or directories. If it is more than one then separate it with commas. |     ✔️    | -                        | string       |
| project        | Name of project on GitHub.                                                        |     -    | CIRCLE_PROJECT_REPONAME  | env_var_name |
| push-force     | Force the push.                                                                   |     -    | false                       | boolean |
| token          | To make push to github remote                                                     |     -    | GITHUB_TOKEN             | env_var_name |
| user           | Username of the owner of target GitHub repo.                                      |     -    | CIRCLE_PROJECT_USERNAME  | env_var_name |

</details>

### Persist Attach
<details>
  <summary>Click to expand!</summary>

#### persist-dir

|     parameter    |                                   description                                   | required | default |  type  |
|:----------------:|:-------------------------------------------------------------------------------:|:--------:|:-------:|:------:|
| custom-directory | The name of the directory that will be created to store your files or folders.  |     -    | persist | string |
| paths            | The path to files or directories you want to store.                             |     ✔️    | -       | string |

#### attach-dir

|     parameter    |                                                                                                   description                                                                                                   | required | default |  type  |
|:----------------:|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:--------:|:-------:|:------:|
| custom-directory | The  name of the directory that was created to store your files or folders.  If custom-directory was passed in the persist-to-dir command, then it is  necessary to pass the same dir in the custom-directory.  |     -    | persist | string |
| destination      | The  destination that wants to restore the files or directories. E.g.  ./my-persist-files. If empty, files or directories will be restored to  your Working Directory, in the persist folder.                   |     -    | ''      | string |
| paths            | The  path to files or directories you want to restore. If it is empty, any  files or directories that have been persisted will be recovered.                                                                    |     -    | ''      | string |

</details>

## 🚀 Projects using this library

Here's a [wikipage list of projects](https://github.com/onimur/circleci-common-tools/wiki/Projects-using-Circleci-Common-Tools).

If you've used this orb, please let me know! Nothing makes me happier than seeing someone else take my work and go wild with it.

## 🔍 Main features

- [Orb-CircleCI](https://circleci.com/orbs/)

## 🧩 Contributing

This project is open-source, so feel free to fork, or to share your ideas and changes to improve the project, check with more details below.

- 💬 [Contributing](docs/CONTRIBUTING.md)
- 👮🏼 [Code of conduct](docs/CODE_OF_CONDUCT.md)
- 😷 [Support](docs/SUPPORT.md)

## 📃 License

    MIT License

    Copyright (c) 2020 Onimur

    Permission is hereby granted, free of charge, to any person obtaining a copy
    of this software and associated documentation files (the "Software"), to deal
    in the Software without restriction, including without limitation the rights
    to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
    copies of the Software, and to permit persons to whom the Software is
    furnished to do so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.

  * [MIT License](./LICENSE)
