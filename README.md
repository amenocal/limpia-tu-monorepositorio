# Limpieza profunda de tu Monorepositorio

<p align="center">
  <img width="150" height="150" src="https://ghicons.github.com/assets/images/blue/png/Daily%20experience.png" />
</p>
<h1 align="center">Limpieza de Monorepositorio</h1>
<h5 align="center">@amenocal</h3>

<p align="center">
  <a href="#mega-pre-requisitos">Prerrequisitos</a> •
</p>

> Este taller es una sesión práctica para ayudarte a limpiar tu monorepositorio. Usaremos herramientas populares de código abierto para descubrir y ayudar en la eliminación de archivos binarios grandes. También aprenderemos métodos para disminuir el tamaño de nuestro repositorio, pero aún preservando el historial de git usando `git replace` para unir dos repositorios juntos.

## :mega: Pre Requisitos

Para poder seguir el taller localmente, necesitarás instalar lo siguiente:

### Herramientas

- Git 2.34 o más reciente
  - [Linux](https://git-scm.com/download/linux)
  - [MacOS](https://git-scm.com/download/mac)
  - [Windows](https://git-scm.com/download/win)
- [git filter-repo](https://github.com/newren/git-filter-repo/blob/main/INSTALL.md)
  - Requiere python3 >= 3.5
  - `pip3 install git-filter-repo`
- [git-sizer](https://github.com/github/git-sizer)
  - Descarga y extrae un .zip [para la plataforma correspondiente](https://github.com/github/git-sizer#getting-started)
  - Alternativamente, si tienes `go` instalado, puedes instalar git-sizer con el gestor de paquetes go:
    - `go get github.com/github/git-sizer`

Recomendado: pueden usar el [devcontainer](.devcontainer) que se encuentra en esta en este repositorio para tener un entorno de desarrollo listo para usar con todas las herramientas necesarias. Se necesita la extension de [devcontainers](https://code.visualstudio.com/docs/devcontainers/containers) para VSCode.

>[!NOTE]
> Este tutorial asume que usaras el [devcontainer](.devcontainer/devcontainer.json) ya que este hace el clone y hace multiple copias de tu repositorio [automaticamente](.devcontainer/on-create-command.sh)

### Repositorio

Si no tienes un clon de una copia local de un monorepositorio personal para experimentar, también puedes clonar el repositorio de ejemplo que se utilizara a lo largo de estos ejercicios.

- Clonar [amenocal/monorepositorio](https://github.com/amenocal/monorepositorio)

HTTPs:

```sh
git clone https://github.com/amenocal/monorepositorio.git
```

SSH:

```sh
git clone git@github.com:amenocal/monorepositorio.git
```

Luego lo recomendado es crear una copia adicional del repositorio mientras experimentamos con diferentes métodos de limpieza.

```sh
cp -r monorepositorio monorepositorio-filter-repo
```

## :mag: Actividad 1: Analizar

### Recopilando estadisticas con `git-sizer`

[Estadisticas con Git Sizer](./actividades/git-sizer.md)

### Recopilando estadisticas con `git filter-repo`

[Estadisticas con Git Filter-repo](./actividades/git-filter-repo-estadisticas.md)

## :broom: Activity 2: Limpieza

### Reescribiendo la historia con git filter-repo

[Reescribiendo la historia](./actividades/reescribiendo-historia.md)

## :chains: Activity 3: Grafting

### Como hacer un graft de dos repositorios

[Grafting](./actividades/grafting-repositorio.md)
