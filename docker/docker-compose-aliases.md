
# 🐳 Docker Compose Custom Aliases Documentation

These aliases are shortcuts for common `docker compose` commands to
speed up development workflows.

------------------------------------------------------------------------

## 📌 Defined Aliases

``` bash
alias dd='docker compose down'
alias du='docker compose up -d'
alias dub='docker compose up --build -d'
alias ddu='docker compose down && docker compose up -d'
alias ddub='docker compose down && docker compose up --build -d'
alias dp='docker ps'
alias dpg='docker ps | grep'
alias de='docker exec -it'
alias dl='docker logs -f'
```

------------------------------------------------------------------------

## 🚀 Alias Explanations

### `dd`

Stops and removes containers, networks, and volumes defined in your
`docker-compose.yml`.

------------------------------------------------------------------------

### `du`

Starts containers in **detached mode** (background).

------------------------------------------------------------------------

### `dub`

Rebuilds images and starts containers in detached mode.

Use this when: - You changed Dockerfile - You updated dependencies - You
modified build context

------------------------------------------------------------------------

### `ddu`

Stops everything and restarts fresh (without rebuilding).

------------------------------------------------------------------------

### `ddub`

Full clean restart with rebuild.

Use this when: - Something is broken - You want a clean rebuild -
Environment changes

------------------------------------------------------------------------

### `dp`

Shows running containers.

------------------------------------------------------------------------

### `dpg <name>`

Filters running containers by name.

Example:

``` bash
dpg postgres
```

------------------------------------------------------------------------

### `de <container> <command>`

Executes a command inside a running container interactively.

Example:

``` bash
de backend bash
```

------------------------------------------------------------------------

### `dl <container>`

Shows live logs of a container.

------------------------------------------------------------------------

# 📂 Where These Aliases Are Stored

They are usually stored inside one of these files:

-   `~/.bashrc`
-   `~/.bash_aliases`
-   `~/.zshrc`
-   `~/.profile`

------------------------------------------------------------------------

# 🔍 How to Find Them

## Check Bash

``` bash
cat ~/.bashrc
grep alias ~/.bashrc
```

## Check Zsh

``` bash
cat ~/.zshrc
grep alias ~/.zshrc
```

------------------------------------------------------------------------

# 🔄 Reload After Editing

After modifying `.bashrc`:

``` bash
source ~/.bashrc
```

For Zsh:

``` bash
source ~/.zshrc
```

------------------------------------------------------------------------

# 💡 How to Add Them Permanently

``` bash
nano ~/.bashrc
```

Paste the aliases at the bottom of the file, save, then run:

``` bash
source ~/.bashrc
```
