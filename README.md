# Ansible Role: zsh
[![CI](https://github.com/skaary/ansible-role-zsh/actions/workflows/ci.yml/badge.svg?branch=main&event=push)](https://github.com/skaary/ansible-role-zsh/actions?query=workflow%3Ci)

An Ansible Role that installs [Zsh](https://www.zsh.org/) and (optionally) [Oh my Zsh](https://github.com/ohmyzsh/ohmyzsh) on Linux. It performs the following tasks:

- Install and configure Zsh:
  - make sure it exists
  - set it as the default shell for the user specified by the role
- Intall Zsh plugins,
- Install Oh My Zsh (omz),
- and (optionally) configure (Oh My) Zsh by creating a `.zshrc` for the specified user,

## Role Variables

### defaults/main/main.yml

| Variable name  | Default value | Description |
|----------------|---------------|-------------|
| `zsh_user_name` | `-` | The name of the user. |
| `zsh_user_group` | `-` | The group of the user. |
| `zsh_package_name` | `zsh` | The package name of zsh. |
| `zsh_user_XDG_CONFIG` | `/home/{{ zsh_user_name }}/.config` | The `$XDG_CONFIG` path of the user. |
| `ZDOTDIR` | `{{ zsh_user_XDG_CONFIG }}/zsh` | The default location for `$ZDOTDIR`. |
| `HISTFILE` | `{{ zsh_user_XDG_CONFIG }}/zsh/zsh_history` | The default location for `$HISTFILE`. |
| `zshrc_path` | `{{ ZDOTDIR }}/.zshrc` | The default location for `.zshrc`. |
| `zsh_plugin_directory` | `/usr/share/zsh/plugins` | The default path for zsh plugins. |
| `zsh_plugins_git` | `""` | ZSH plugins to be installed from git repositories. See Example Playbook. |
| `zsh_plugins_package_manager` | `""` | ZSH plugins to be installed available in your distro's default repository. See Example Playbook. |

### defaults/main/oh_my_zsh.yml
| Variable name  | Default value | Description |
|----------------|---------------|-------------|
| `omz_install` | `true` | Conditional to install/not install omz. |
| `omz_configure` | `true` | Conditional to configure/not configure omz. |
| `omz_git_repository` | `https://github.com/robbyrussell/oh-my-zsh.git` | The git repository to clone Oh My Zsh from. |
| `omz_install_directory` | `oh-my-zsh` | The default directory name of Oh My Zsh. |
| `omz_install_path` | `{{ zsh_user_XDG_CONFIG }}/{{ omz_install_directory }}` | The default location to clone Oh My Zsh into. |
| `omz_shared_packages` | `git, curl` | Required packages to install omz, shared by other applications. |
| `omz_zshrc_create` | `true` | Whether or not to create `.zshrc`. If `true`, will create `.zshrc` from a template. |
| `omz_zshrc_template` | `omz_zshrc2.j2` | The template used to create the user's `.zshrc` file when `omz_zshrc_create` is `true` and `zsh_plugins_git` is empty. |
| `omz_zshrc_template_plugins` | `omz_zshrc2_plugins.j2` | The template used to create the user's `.zshrc` file when `omz_zshrc_create` is `true` and `zsh_plugins_git` is not empty. |
| `omz_zshrc_backup` | `true` | Whether or not to backup the existing `.zshrc` files when the role changes it. |
| `omz_zshrc_force` | `true` | Whether or not to replace the existing `.zshrc` if it already exists. |
| `omz_zsh_theme` | `agnoster` | See `templates/omz_zshrc`. |
| `omz_case_sensitive` | `false` | See `templates/omz_zshrc`. |
| `omz_hyphen_insensitive` | `false` | See `templates/omz_zshrc`. |
| `omz_disable_auto_update` | `false` | See `templates/omz_zshrc`. |
| `omz_update_zsh_days` | `13` | See `templates/omz_zshrc`. |
| `omz_disable_ls_colors` | `false` | See `templates/omz_zshrc`. |
| `omz_disable_auto_title` | `false` | See `templates/omz_zshrc`. |
| `omz_enable_correction` | `false` | See `templates/omz_zshrc`. |
| `omz_completion_waiting_dots` | `false` | See `templates/omz_zshrc`. |
| `omz_disable_untracked_files_dirty` | `false` | See `templates/omz_zshrc`. |
| `omz_hist_stamps` | `dd.mm.yyyy` | See `templates/omz_zshrc`. |
| `omz_zsh_custom` | `$ZSH/custom` | See `templates/omz_zshrc`. |
| `omz_plugins` | `[]` | A list of Oh My Zsh plugins to enable. |

## Role task files

### `main.yml`: task coordination

This file includes files that peform specific subsets of tasks.

### `zsh.yml`: Zsh setup

This task 
- installs and sets zsh as the default shell for a user,
- sets `$ZDOTDIR` to `{{ ZDOTDIR }}` and creates the directory with the correct permissions,
- and sets `$HISTFILE` to `{{ HISTFILE }}`.

#### Variables used

- `omz_user`
- `zsh_package_name`
- `zsh_user_name`
- `zsh_user_group`
- `ZDOTDIR`
- `HISTFILE`

### `zsh-plugins.yml`: Zsh plugin setup

This task installs zsh plugins when `zsh_plugins_git` or `zsh_plugins_package_manager` is not empty.

#### Variables used

- `zsh_plugin_directory`
- `zsh_plugins_git`
- `zsh_plugins_package_manager`

### `oh-my-zsh-install.yml`: Oh My Zsh installation

This task clones the Oh My Zsh repository into the directory as defined by `{{ omz_install_path }}` and sets the appropriate permissions on the directory.

#### Variables used

- `zsh_user`
- `zsh_group`
- `omz_git_repository`
- `omz_install_path`

### `oh-my-zsh-configure.yml`: Oh My Zsh configuration

This task creates the user a `.zshrc` file containing global values for various Oh My Zsh options based on [the `.zshrc` template in the oh-my-zsh repository](https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/templates/omz_zshrc). The task can be configured to back up any existing `.zshrc` file.

This task only runs when `omz_zshrc_create` is set to `true`.

#### Variables used

- `zsh_user_name`
- `zsh_user_group`
- `zshrc_path`
- `omz_zshrc_template`
- `omz_zshrc_template_plugins`
- `omz_zshrc_backup`
- `omz_zshrc_force`

## Demo install in Vagrant

You can test this role with Vagrant before deploying it on an actual device. In your terminal type `vagrant up` to start the Vagrant VM and `vagrant ssh` to connect to the virtual machine. For more information  consult the [Vagrant documentation](https://www.vagrantup.com/docs/cli).

## Example Playbook

```yaml
hosts: all
become: true
vars:
  zsh_user_name: molecule
  zsh_user_group: molecule
  zsh_plugins_git:
    - name: zsh-syntax-highlighting
      zsh_file: zsh-syntax-highlighting.zsh
      repo: https://github.com/zsh-users/zsh-syntax-highlighting.git
    - name: powerlevel10k
      zsh_file: powerlevel10k.zsh-theme
      repo: https://github.com/romkatv/powerlevel10k.git
    zsh_plugins_package_manager:
      - powerline
    omz_zsh_theme: "robbyrussel"
roles:
  - zsh
```