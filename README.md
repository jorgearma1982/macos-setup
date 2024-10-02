# MacOS Setup

## Introducción

En esta guía describo las configuraciones del sistema `macOS` sobre la arquitectura `M1` para adaptarlo a un ambiente
de trabajo en donde pueda realizar tareas relacionadas a la administración de este tipo de sistemas:

* Servidores Linux
* Recursos Cloud
* Clusters Kubernetes
* Desarrollo de software

Intentare usar en su mayoría software libre que estoy acostumbrado a usar en otros sistemas `GNU/Linux` como `Kubuntu
Desktop` que normalmente usaba en arquitecturas x86.

### Objetivos

Para dejar a punto la máquina, realizaremos la configuración de los siguientes componentes:

* Entornos de desarrollo
* Gestores de paquetes
* Editor de textos
* Emulador de Terminal
* Entorno del shell
* Cliente VPN
* Navegador Web
* Herramientas de gestión Cloud

## Actualizar sistema

Primero que nada vamos a actualizar el sistema, en este caso `System Settings`, buscamos `Software Updates`, y en
la sección de `Automatic Updates` configuramos de la siguiente forma:

* Check for updates: Enabled
* Download new updates when available: Enabled
* Install macOS updates: Enabled
* Install application updates from the App Store: Disabled
* Install Security Responses and system files: Enabled

En la sección de abajo podemos listar las actualizaciones pendientes, en caso de querer aplicarlas hacemos clic en
`Upgrade Now` y esperamos a que termine el proceso, al final se recomienda reiniciar.

## Cifrar el disco

En el trabajo me dieron una laptop `MacBook Pro`, regularmente salgo con ella, para prevenir perdida o  robo de
información, vamos a cifrar los datos del disco de forma automática, para esto vamos a `System Settings`, luego a
`Privacy & Security`, en la sección de `Security` vamos a la opción de `FileVault`, con esta herramienta aseguraremos
nuestros datos, por defecto viene desactivado, hacemos clic en `Turn On...` para iniciar el proceso.

Al principio nos saldrá un mensaje para elegir el método para desbloquear el disco y resetear la contraseña en caso
de olvidarla, podemos usar el método basado en tu cuenta `iCloud` o a través de una `recovery key`, la cual no
de tu cuenta iCloud.

Una vez seleccionado el método, inicia el proceso de cifrado, el cual tarda un poco dependiendo de los datos actuales.

## Herramientas de linea de comando XCode

Instalamos el entorno de desarrollo de apps de apple [XCode](https://developer.apple.com/xcode/):

```shell
xcode-select --install
```

En una máquina nueva este componente no vienen instalado, por lo que aparece una ventana para seleccionar la
instalación, se aceptan las condiciones y nos vamos a tomar un café porque va a tomar un buen rato.

Al terminar verificamos la instalación de xcode:

```shell
xcode-select -p
```

## Gestor de paquetes Brew

Instalamos el gestor de paquetes [Brew](https://brew.sh/):

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Configuramos el perfil del shell para usar brew:

```shell
echo >> /Users/jmedina/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/jmedina/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

Configuramos los repositorios de paquetes:

```shell
brew tap homebrew/cask
```

Mostrando taps:

```shell
$ brew tap
homebrew/cask
homebrew/core
```

Instalando los paquetes [git](https://git-scm.com/) y [wget](https://www.gnu.org/software/wget/):

```shell
brew install git wget
```

Actualizando paquetes:

```shell
brew update && brew upgrade
```

Con el siguiente comando realizamos una limpieza de paquetes viejos:

```shell
brew cleanup
```

## Instalando comandos GNU

No me gusta usar el comando `tar` que trae por defecto macos, por lo que instalo
la versión de GNU que estoy acostumbrado a usar en Linux.

```shell
brew install gnu-tar
```

Edito la configuración del shell para agregar el path:

```shell
export PATH="/opt/homebrew/opt/gnu-tar/libexec/gnubin:$PATH"
```

Recargamos configuración:

```shell
source $HOME/.zshrc
```

## Editor de textos vim

Instalamos el editor de textos [vim](https://www.vim.org/):

```shell
brew install vim
```

Actualizamos la configuración del editor para realizar las personalizaciones locales:

```shell
vim .vimrc
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
```

Configuramos el corrector ortográfico `spell`, primero creamos directorio para spell check:

```shell
mkdir -p ~/.vim/spell
cd ~/.vim/spell
```

Descargaremos los siguientes archivos:

```shell
wget --no-check-certificate https://ftp.vim.org/vim/runtime/spell/es.latin1.spl
wget --no-check-certificate https://ftp.vim.org/vim/runtime/spell/es.latin1.sug
wget --no-check-certificate https://ftp.vim.org/vim/runtime/spell/es.utf-8.spl
wget --no-check-certificate https://ftp.vim.org/vim/runtime/spell/es.utf-8.sug
```

Creamos los archivos para los diccionarios locales:

```shell
touch ~/.vim/spell/es.utf-8.add
touch ~/.vim/spell/en.utf-8.add
```

Al final del archivo `.vimrc` agregamos las siguientes líneas:

```shell
" Configuracion spell check
"set spell
set nospell
setlocal spell spelllang=es,en " Corregir palabras usando diccionarios en español
```

Los atajos de teclado para manejar el spelling:

* **]s** – Siguiente falta ortográfica
* **[s** – Anterior falta ortográfica
* **z=** – Mostrar sugerencias para una palabra incorrecta.
* **zg** – Añadir una palabra al diccionario.
* **zug** – Deshacer la adición de una palabra al diccionario.
* **zw** – Eliminar una palabra del diccionario.

Ahora instalamos la herramienta para gestión de plugins: [vim-plug](https://github.com/junegunn/vim-plug):

```shell
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Editamos la configuración de vim para añadir la Configuración de Plugins:

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

## Emulador de terminal Kitty

Instalamos el paquete [Kitty](https://github.com/kovidgoyal/kitty):

```shell
brew install kitty
```

## El shell zsh con oh-my-zsh

Instalamos el shell [zsh](https://www.zsh.org/):

```shell
brew install zsh
```

Instalamos [oh my zsh](https://ohmyz.sh/):

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

Cambiamos el shell default:

```shell
chsh -s $(which zsh)
```

### Configuración de plugins integrados en zsh

Editamos el archivo de configuración de zsh:

```shell
vim .zshrc
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
source .zshrc
```

### Configuración del tema integrado en zsh

Editamos el archivo de configuración de zsh:

```shell
vim .zshrc
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
source .zshrc
```

Lista de temas: https://github.com/ohmyzsh/ohmyzsh/wiki/themes.

### Configurando tema powerlevel19k

Instalamos el tema powerlevel9k:

```shell
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
```

Editamos el archivo de configuración de zsh:

```shell
vim .zshrc
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
source .zshrc
```

Proyecto: https://github.com/Powerlevel9k/powerlevel9k/wiki/Install-Instructions#option-2-install-for-oh-my-zsh

### Configuración del plugin syntax hightlighting en zsh

Instalar resaltado de sintaxis en zsh:

```shell
brew install zsh-syntax-highlighting
```

Editamos el archivo de configuración de zsh:

```shell
vim .zshrc
```

Agregar a la lista de sources:

```shell
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

Guardamos y re cargamos configuración:

```shell
source .zshrc
```

Proyecto: https://mimosa-pudica.net/zsh-incremental.html

### Configuración del plugin auto suggestions en zsh

Instalamos auto sugerencias en zsh:

```shell
brew install zsh-autosuggestions
```

Editamos el archivo de configuración de zsh:

```shell
vim .zshrc
```

Agregar a la lista de sources:

```shell
source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh
```

Guardamos y re cargamos configuración:

```shell
source .zshrc
```

### Configuración del plugin auto completar incr en zsh

Instalamos plugin incr:

```shell
cd $HOME/.oh-my-zsh/custom/plugins
mkdir incr
cd incr
wget http://mimosa-pudica.net/src/incr-0.2.zsh
cd
```

Editamos el archivo de configuración de zsh:

```shell
vim .zshrc
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
source .zshrc
```

Proyecto: https://mimosa-pudica.net/zsh-incremental.html

### iterm2 y zsh con fuente Powerline y tema Solarized.

Instalamos la fuente Powerline:

```shell
cd data/vcs/jorge.medina
git clone https://github.com/powerline/fonts.git --depth=1
cd fonts
./install.sh
cd
```

Configurar fuente en iterm3, ir "Preferences", "Profiles", "Text":

  En Fonts, cambiar a `ProFont for Powerline`.

Configurar tema en iterm2, ir "Preferences", "Profiles", "Colors":

  En `Color Presets...`, cambiar a `Solarized Dark`.

### Integrar tmux en zsh

Instalar tmux:

```shell
brew install tmux
```

Habilitamos el plugin:

```shell
plugins=(cp osx colored-man-pages colorize pip python brew git tmux)
```

## Herramientas de búsqueda en linea de comandos

Instalamos paquete de coreutils:

```shell
brew install coreutils
```

Editamos el archivo de configuración de zsh:

```shell
vim .zshrc
```

Agregamos el PATH:

```shell
# PATHS
export PATH="/opt/homebrew/opt/coreutils/libexec/gnubin:$PATH"
```

Instalamos tree:

```shell
brew install tree
```

Instalamos watch:

```shell
brew install watch
```

Instalamos fzf:

```shell
brew install fzf
```

Habilitamos integraciones:

```shell
/opt/homebrew/opt/fzf/install
```

Usar fzf en vim:

```shell
vim .vimrc
```

Al final agregar: 

```shell
set rtp+=/opt/homebrew/opt/fzf
```

Instalar ack ak para búsquedas:

```shell
brew install ack
```

Instalamos the silver searcher

```shell
brew install the_silver_searcher
```

## Herramientas de linea de comandos

Instalamos la siguiente lista de paquetes:

```shell
brew install wget curl nmap make autoconf mcrypt gpg2 gettext readline openssl unzip
```

Agregamos al final de .zshrc:

```shell
vim .zshrc
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
brew install google-cloud-sdk
```

Editamos el archivo de configuración de zsh:

```shell
vim .zshrc
```

Agregar a la lista de sources:

```shell
source /opt/homebrew/Caskroom/google-cloud-sdk/latest/google-cloud-sdk/completion.zsh.inc
```

Instalación de kubectl:

```shell
brew install kubectl
```

Instalación de ansible:

```shell
brew install ansible
```

Instalamos generador de contraseñas en cli:

```shell
brew install pwgen
```

Instalamos KeepassXC:

```shell
brew install --cask keepassxc
```

Instalamos inso:

```shell
brew install inso
```

## Gestor de paquetes helm

Instalamos helm:

```shell
brew install helm
```

Agregamos los repositorios de los charts que usaremos:

```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add jetstack https://charts.jetstack.io
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo add datadog https://helm.datadoghq.com
```

Actualizamos los repositorios:

```shell
helm repo update
```

## Herramientas de Redes

Instalamos la calculadora de IPs

```shell
brew install ipcalc
```

Instaláremos herramientas trazado de tráfico:

```shell
brew install mtr
```

## Herramientas de Databases

```shell
brew install postgresql
```

Instalamos dbeaver:

```shell
brew install dbeaver-community
```

## DEV

En esta sección instalamos algunas herramientas que se usan en el desarrollo de software,
en especifico para la gestión de paquetes para elixir, python y nodejs:

```shell
brew install asdf python npm yarn
```

Instalamos la herramienta markdown link check:

```shell
npm install -g markdown-link-check
```

Instalamos terraform y otras herramientas necesarias:

```shell
brew install terraform tflint terraform-docs
```

Instalamos template de terraform para m1:

```shell
brew install kreuzwerker/taps/m1-terraform-provider-helper
m1-terraform-provider-helper install hashicorp/template -v v2.2.0
```

Instalamos yamllint e yq:

```shell
brew install yamllint yq
```

Instalamos cookiecutter:

```shell
brew install cookiecutter
```

Instalamos el framework pre-commit:

```shell
pip3 install pre-commit
```

Instalamos colima y docker:

```shell
brew install colima docker
```

Ahora instalamos kind:

```shell
brew install kind 
```

Instalamos k6:

```shell
brew install k6
```

## Otras herramientas de Escritorio

Adicional al software que instalo desde repositorios públicos también hago uso de estos otros programas que vienen
en formato de paquetes `dmg`.

* Google Chrome, iniciar sesión y sincronizar configuraciones: googlechrome.dmg
* Brave Browser, iniciar sesión y sincronizar configuraciones: Brave-Browser-arm64.dmg
* Tunnelblick, importar configuración y realizar conexión: Tunnelblick_3.8.6a_build_5711.dmg
* Controlador Kensington y configurar mapa de teclas: kensingtonworks_2.3.1_1632814867.pkg
