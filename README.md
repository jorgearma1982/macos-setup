# MacOS Setup

## Intro

En esta guia se describe la configuración del sistema MacOS para adaptarlo a un ambiente de trabajo para realizar
tareas relacionadas a la administración de:

* MacOS Personal
* Servidores Linux
* Recursos Cloud
* Clusters Kubernetes
* Desarrollo de software

Intentare usar en su mayoría software libre que estoy acostumbrado a usar en otros sistemas GNU/Linux como Kubuntu
Desktop que normalmente usaba en arquitecturas x86.

Para esto necesito dejar algunas cosas bien configuradas:

* Entornos de desarrollo
* Gestores de paquetes
* Editor de textos
* Emulador de Terminal
* Entorno del shell
* Cliente VPN
* Navegador Web

## Objetivos

Realizaremos las siguientes actividades orientadas a instalar y configurar:

* Herramientas de linea de comando XCode
* Gestor de paquetes Brew
* Editor de textos vim
* Emulador de terminal iTerm2
* El shell zsh con oh-my-zsh
* Herramientas de búsqueda en linea de comandos
* Conjunto de herramientas de linea de comandos
* Herramientas de gestión Cloud

## Herramientas de linea de comando XCode

Instalamos XCode cli tools:

```shell
$ xcode-select --install
```

Verificamos instalación de xcode cli tools:

```shell
$ xcode-select -p
```

## Gestor de paquetes Brew

Instalar el gestor de paquetes brew:

```shell
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Configurar brew:

```shell
$ echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/jmedina/.zprofile
$ eval "$(/opt/homebrew/bin/brew shellenv)"
$ brew help
```

Configurar repos:

```shell
$ brew tap
```

```shell
$ brew tap homebrew/cask
```

Mostrando taps:

```shell
$ brew tap
homebrew/cask
homebrew/core
```

Buscando paquetes:

```shell
$ brew search git
```

Actualizando lista de paquetes:

```shell
$ brew update
```

Instalando un paquete:

```shell
$ brew install git
```

Desinstalando paquetes:

```shell
$ brew uninstall git
```

Forzando desinstalacion de paquete:

```shell
$ brew unintall -f git
```
	
Actualizando paquetes:

```shell
$ brew upgrade
```

```shell
$ brew update && brew upgrade
```

Limpieza de paquetes viejos:

```shell
$ brew cleanup
```

## Instalando comandos GNU

No me gusta usar el comando `tar` que trae por defecto macos, por lo que instalo
la versión de GNU que estoy acostumbrado a usar en Linux.

```shell
$ brew install gnu-tar
```

Hace falta agregar el path:

```shell
$ vim $HOME/.zshrc
...
...
...
export PATH="/opt/homebrew/opt/gnu-tar/libexec/gnubin:$PATH"
```

Recargamos configuración:

```shell
$ source $HOME/.zshrc
```

## Editor de textos vim

Instalación de vim:

```shell
$ brew install vim
```

Editar config:

```shell
$ vim .vimrc
```

Agregamos el contenido inicial:

```shell
" .vimrc

" Configuración general
set title " Muestra el nombre del archivo en la ventana de la terminal
set number " Muestra los números de las líneas
set nowrap " No dividir la línea si es muy larga
set cursorline " Resalta la línea actual
set colorcolumn=120 " Muestra la columna límite a 120 caracteres
set nocompatible " Desactiva modo compatible
filetype plugin on " Habilita plugin para tipos de archivos
syntax on " Activa resaltado de sintaxis

" Indentación a 2 espacios
set tabstop=2
set shiftwidth=2
set softtabstop=2
set shiftround
set expandtab " Insertar espacios en lugar de <Tab>s
set imrmguicolors " Activa true colors en la terminal

" Configuracion spell check
"set spell
set nospell
setlocal spell spelllang=es,en " Corregir palabras usando diccionarios en español

" Mapeos
"inoremap " ""<left>
"inoremap ' ''<left>
"inoremap ( ()<left>
"inoremap [ []<left>
"inoremap { {}<left>
"inoremap {<CR> {<CR>}<ESC>O
"inoremap {;<CR> {<CR>};<ESC>O

" Habilitar fzf
set rtp+=/opt/homebrew/opt/fzf

" Syntax for markdown enabled
au BufNewFile,BufFilePre,BufRead *.md set filetype=markdown
au BufNewFile,BufFilePre,BufRead *.txt set filetype=markdown
set syntax=markdown

"
" PLUGINS: https://github.com/junegunn/vim-plug
"

" Specify a directory for plugins
call plug#begin('~/.vim/plugged')
" fzf
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }
" Challenger-deep-theme: https://github.com/junegunn/vim-plug
Plug 'challenger-deep-theme/vim', { 'as': 'challenger-deep' }
" NERDTree
Plug 'preservim/nerdtree'
" vimwiki
Plug 'vimwiki/vimwiki'
" editorconfig
Plug 'editorconfig/editorconfig-vim'
" vim-terraform
Plug 'hashivim/vim-terraform'
" Initialize plugin system
call plug#end()

"
" THEME:
"
colorscheme challenger_deep

```

Creamos directorio para spell check:

```shell
$ mkdir -p ~/.vim/spell
$ cd ~/.vim/spell
```

Descargaremos los siguientes archivos:

* es.latin1.spl
* es.latin1.sug
* es.utf-8.spl
* es.utf-8.sug

Descargamos los archivos:

```shell
$ wget https://ftp.vim.org/vim/runtime/spell/es.latin1.spl
$ wget -k https://ftp.vim.org/vim/runtime/spell/es.latin1.spl
$ wget --no-check-certificate https://ftp.vim.org/vim/runtime/spell/es.latin1.spl
$ wget https://ftp.vim.org/vim/runtime/spell/es.latin1.sug
$ wget --no-check-certificate https://ftp.vim.org/vim/runtime/spell/es.latin1.sug
$ wget --no-check-certificate https://ftp.vim.org/vim/runtime/spell/es.utf-8.spl
$ wget --no-check-certificate https://ftp.vim.org/vim/runtime/spell/es.utf-8.sug
```

Creamos los archivos para los diccionarios locales:

```shell
$ touch ~/.vim/spell/es.utf-8.add
$ touch ~/.vim/spell/en.utf-8.add
```

Shortcuts:

* **]s** – Siguiente falta ortográfica
* **[s** – Anterior falta ortográfica
* **z=** – Mostrar sugerencias para una palabra incorrecta.
* **zg** – Añadir una palabra al diccionario.
* **zug** – Deshacer la adición de una palabra al diccionario.
* **zw** – Eliminar una palabra del diccionario.

Instalar herramienta para gestión de plugins: plug-vim:

```shell
$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Configuración de Plugins:

```shell
"
" PLUGINS: https://github.com/junegunn/vim-plug
"

" Specify a directory for plugins
call plug#begin('~/.vim/plugged')
" fzf
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }
" Challenger-deep-theme: https://github.com/junegunn/vim-plug
Plug 'challenger-deep-theme/vim', { 'as': 'challenger-deep' }
" NERDTree
Plug 'preservim/nerdtree'
" vimwiki
Plug 'vimwiki/vimwiki'
" Initialize plugin system
call plug#end()

"
" THEME:
"
colorscheme challenger_deep

```

Guardar vim, salir, y volver a entrar, entonces:

```shell
:PlugInstall
```

## Emulador de terminal iTerm2

Instalamos el paquete iterm2 de cask:

```shell
$ brew install --cask iterm2
```

Shortcuts:

* **Win + N**: Nuevo ventana
* **Win + T**: Nuevo tab
* **Shift + Win + D**: Split Horizontal
* **Win + D**: Split Vertical

## El shell zsh con oh-my-zsh

Instalamos el shell zsh:

```shell
$ brew install zsh
```

Instalamos oh my zsh:

```shell
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

Cambiamos el shell default:

```shell
$ chsh -s $(which zsh)
```

### Configuración de plugins integrados en zsh

Editamos el archivo de configuración de zsh:

```shell
$ vim .zshrc
```

Para habilitar los diferentes plugins cambiamos:

```shell
plugins=(git)
```

por:

```shell
plugins=(cp colored-man-pages colorize pip python brew git vi-mode)
```

Guardamos y re cargamos configuración:

```shell
$ source .zshrc
```

### Configuración del tema integrado en zsh

Editamos el archivo de configuración de zsh:

```shell
$ vim .zshrc
```

Para cambiar el tema cambiamos:

```shell
ZSH_THEME="robbyrussell"
```

Por:

```shell
ZSH_THEME="agnoster"
```

Guardamos y re cargamos configuración:

```shell
$ source .zshrc
```

Lista de temas: https://github.com/ohmyzsh/ohmyzsh/wiki/themes.

### Configurando tema powerlevel19k

Instalamos el tema powerlevel9k:

```shell
$ git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
```

Editamos el archivo de configuración de zsh:

```shell
$ vim .zshrc
```

Agregamos la lista de sources:

```shell
ZSH_THEME="powerlevel9k/powerlevel9k"
# Powerlevel9k settings
POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(user host dir vcs)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(status battery time)
POWERLEVEL9K_PROMPT_ON_NEWLINE=true
POWERLEVEL9K_SHORTEN_DIR_LENGTH=2
POWERLEVEL9K_OS_ICON_BACKGROUND="white"
POWERLEVEL9K_OS_ICON_FOREGROUND="blue"
POWERLEVEL9K_TIME_FORMAT="%D{%H:%M:%S | %d.%m.%y}"

```

Otras opciones:

```shell
POWERLEVEL9K_MULTILINE_FIRST_PROMPT_PREFIX="↱"
POWERLEVEL9K_MULTILINE_LAST_PROMPT_PREFIX="↳ "
```

Guardamos y re cargamos configuración:

```shell
$ source .zshrc
```

Proyecto: https://github.com/Powerlevel9k/powerlevel9k/wiki/Install-Instructions#option-2-install-for-oh-my-zsh

### Configuración del plugin syntax hightlighting en zsh

Instalar resaltado de sintaxis en zsh:

```shell
$ brew install zsh-syntax-highlighting
```

Editamos el archivo de configuración de zsh:

```shell
$ vim .zshrc
```

Agregar a la lista de sources:

```shell
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

Guardamos y re cargamos configuración:

```shell
$ source .zshrc
```

Proyecto: https://mimosa-pudica.net/zsh-incremental.html

### Configuración del plugin auto suggestions en zsh

Instalamos auto sugerencias en zsh:

```shell
$ brew install zsh-autosuggestions
```

Editamos el archivo de configuración de zsh:

```shell
$ vim .zshrc
```

Agregar a la lista de sources:

```shell
source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh
```

Guardamos y re cargamos configuración:

```shell
$ source .zshrc
```

### Configuración del plugin auto completar incr en zsh

Instalamos plugin incr:

```shell
$ cd $HOME/.oh-my-zsh/custom/plugins
$ mkdir incr
$ cd incr
$ wget http://mimosa-pudica.net/src/incr-0.2.zsh
$ cd
```

Editamos el archivo de configuración de zsh:

```shell
$ vim .zshrc
```

Después de la linea:

```shell
source $ZSH/oh-my-zsh.sh
```

Insertar:

```shell
source $ZSH/custom/plugins/incr/incr*.zsh
```

Guardamos y re cargamos configuración:

```shell
$ source .zshrc
```

Proyecto: https://mimosa-pudica.net/zsh-incremental.html

### iterm2 y zsh con fuente Powerline y tema Solarized.

Instalamos la fuente Powerline:

```shell
$ cd data/vcs/jorge.medina
$ git clone https://github.com/powerline/fonts.git --depth=1
$ cd fonts
$ ./install.sh
$ cd
```

Configurar fuente en iterm3, ir "Preferences", "Profiles", "Text":

  En Fonts, cambiar a `ProFont for Powerline`.

Configurar tema en iterm2, ir "Preferences", "Profiles", "Colors":

  En `Color Presets...`, cambiar a `Solarized Dark`.

### Integrar tmux en zsh

Instalar tmux:

```shell
$ brew install tmux
```

Habilitamos el plugin:

```shell
plugins=(cp osx colored-man-pages colorize pip python brew git tmux)
```

## Herramientas de búsqueda en linea de comandos

Instalamos paquete de coreutils:

```shell
$ brew install coreutils
```

Editamos el archivo de configuración de zsh:

```shell
$ vim .zshrc
```

Agregamos el PATH:

```shell
# PATHS
export PATH="/opt/homebrew/opt/coreutils/libexec/gnubin:$PATH"
```

Instalamos tree:

```shell
$ brew install tree
```

Instalamos watch:

```shell
$ brew install watch
```

Instalamos fzf:

```shell
$ brew install fzf
```

Habilitamos integraciones:

```shell
$ /opt/homebrew/opt/fzf/install
```

Usar fzf en vim:

```shell
$ vim .vimrc
```

Al final agregar: 

```shell
set rtp+=/opt/homebrew/opt/fzf
```

Instalar ack ak para búsquedas:

```shell
$ brew install ack
```

Instalamos the silver searcher

```shell
$ brew install the_silver_searcher
```

## Herramientas de linea de comandos

Instalamos la siguiente lista de paquetes:

```shell
$ brew install wget curl nmap make autoconf mcrypt gpg2 gettext readline openssl unzip
```

Agregamos al final de .zshrc:

```shell
$ vim .zshrc
```

Agregamos al final:

```shell
# PATHS
export PATH="/opt/homebrew/opt/curl/bin:$PATH"
export PATH="/opt/homebrew/opt/make/libexec/gnubin:$PATH"
export PATH="/opt/homebrew/opt/openssl@3/bin:$PATH"
```

## Herramientas de gestión Cloud

Instalamos google-cloud-sdk:

```shell
$ brew install google-cloud-sdk
```

Editamos el archivo de configuración de zsh:

```shell
$ vim .zshrc
```

Agregar a la lista de sources:

```shell
$ source /opt/homebrew/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/completion.zsh.inc
```

Instalación de kubectl:

```shell
$ brew install kubectl
```

Instalación de ansible:

```shell
$ brew install ansible
```

Instalamos generador de contraseñas en cli:

```shell
$ brew install pwgen
```

Instalamos keepassxc:

```shell
$ brew install --cask keepassxc
```

Instalamos drone-cli:

```shell
$ brew install drone-cli
```

```shell
$ source .dronerc
```

Instalamos inso:

```shell
$ brew install inso
```

## Gestor de paquetes helm

Instalamos helm:

```shell
$ brew install helm
```

Agregamos los repositorios de los charts que usaremos:

```shell
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm repo add jetstack https://charts.jetstack.io
$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
$ helm repo add datadog https://helm.datadoghq.com
```

Actualizamos los repositorios:

```shell
$ helm repo update
```

## Herramientas de Redes

Instalamos la calculadora de IPs

```shell
$ brew install ipcalc
```

Instaláremos herramientas trazado de tráfico:

```shell
$ brew install mtr
```

## Herramientas de Databases

```shell
$ brew install postgresql
```

Instalamos dbeaver:

```shell
$ brew install dbeaver-community
```

## DEV

En esta sección instalamos algunas herramientas que se usan en el desarrollo de software,
en especifico para la gestión de paquetes para elixir, python y nodejs:

```shell
$ brew install asdf python npm yarn
```

Instalamos la herramienta markdown link check:

```shell
$ npm install -g markdown-link-check
```

Instalamos terraform y otras herramientas necesarias:

```shell
$ brew install terraform tflint terraform-docs
```

Instalamos template de terraform para m1:

```shell
$ brew install kreuzwerker/taps/m1-terraform-provider-helper
$ m1-terraform-provider-helper install hashicorp/template -v v2.2.0
```

Instalamos checkov:

```shell
$ pip3 install -U checkov
```

Instalamos yamllint e yq:

```shell
$ brew install yamllint yq
```

Instalamos cookiecutter:

```shell
$ brew install cookiecutter
```

Instalamos el framework pre-commit:

```shell
$ pip3 install pre-commit
```

Instalamos colimay docker:

```shell
$ brew install colima docker
```

Ahora instalamos kind y skaffold:

```shell
$ brew install kind skaffold
```

Instalamos k6:

```shell
$ brew install k6
```

## Otras herramientas de Escritorio

Instalamos el siguiente software:

* Descargar Google Chrome, iniciar sesión y sincronizar configuraciones.
googlechrome.dmg

* Descargar Brave Browser, iniciar sesión y sincronizar configuraciones.
Brave-Browser-arm64.dmg

* Descargar Tunnelblick, importar configuración y realizar conexión.
Tunnelblick_3.8.6a_build_5711.dmg

* Descargar el controlador de mi mouse keninston y configurar mapa de teclas.
kensingtonworks_2.3.1_1632814867.pkg
