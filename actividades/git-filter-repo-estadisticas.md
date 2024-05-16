# Recopilando estadísticas con `git filter-repo`

Para la segunda actividad, usaremos `git filter-repo` para determinar y analizar blobs grandes en un repositorio.

While the main purpose of `git filter-repo` is to rewrite history, it can also be used to gather information about blobs in your repository.
Aunque el propósito principal de `git filter-repo` es reescribir la historia de un repositorio, también se puede usar para recopilar información sobre blobs en tu repositorio. 

Navega al directorio del monorepositorio y ejecuta el siguiente comando:

```bash
cd mal-monorepositorio
git filter-repo --analyze
```

`git filter-repo` comenzará a procesar todos los objetos en el repositorio. Una vez que haya terminado, navega a `.git/filter-repo/analysis`

```bash
cd .git/filter-repo/analysis
```

En este directorio encontrarás varios archivos, pero por ahora nos interesa el archivo `blobs-shas-and-path.txt`.


Abre el archivo:

```bash
code blobs-shas-and-path.txt
```

Aqui encontrarás una lista de archivos, algunos repetidos varias veces.

```bash
=== Files by sha and associated pathnames in reverse size ===
Format: sha, unpacked size, packed size, filename(s) object stored as
  a6fa50a34e9494afae1d37e04ce71970c94150ce   52428800   52444806 backup/data.bak
  67533d22028802e3d428e1f11304d76bcc7e07bc       2467       1367 cmd/root.go
  d0e8c2c372a102586948e047e6057cdd40b0e1cb        896        571 go.sum
  f0472fe8ce6a25ef37cb0b3278b4befa804dc22d       2442        259 cmd/root.go
  590959b75a5d490d94da2e76f47596e37fb53a9f        190        124 go.mod
  ffeeb1daa194a953ce04814a118b797c41f70d4c        127        103 README.md
  d6e68fd91b9044f86617ba345d87469bbb29af58       1141         84 main.go
  017a8c27caf4607a1401e31aa99333c31425894a       1083         42 LICENSE
  59bbbcf338f6038381da19d22a85b56746e76e26       2449         36 cmd/root.go
  d5ed1d06665bcc0fa9931288426e23e83d98f445       2353         34 cmd/root.go
  2cbe61dae57d4070d4c4dbfc1e9b1acddf1d7f3e         17         27 README.md
```

Vamos a explicar lo que vemos en este archivo:


- `sha` se refiere al SHA del objeto git en sí. Podemos usar esto para hacer cosas como listar el contenido del blob con `git cat-file`, similar a lo que hicimos en el ejercicio de `git sizer`.
- `unpacked size` se refiere al tamaño descomprimido del blob en bytes. Este es el tamaño del archivo tal como sería si se extrajera del repositorio de git. (es decir, al ejecutar `git checkout`)
- `packed size` se refiere al tamaño comprimido del blob en bytes. Este es el tamaño del archivo tal como está almacenado en el repositorio de git.
- `filename(s)` se refiere a la ruta del archivo dentro del repositorio. Si el archivo se encuentra en varios lugares, se enumerará como una lista separada por comas.

Con esta información, ahora podemos determinar los blobs más grandes en la historia del repositorio e investigarlos más a fondo. Un ejemplo puede ser que queremos encontrar en qué commit en particular se introdujo un archivo grande. Podemos hacer esto ejecutando el siguiente comando:

```bash 
git --no-pager log --follow  --diff-filter=A -- backup/data.bak
```
<details><summary>Output</summary>

```bash
commit 73f627b6e277d2ab576ae4ebbcd4408a362c5437
No signature
Author: Alejandro Menocal <amenocal@github.com>
Date:   Tue May 14 17:35:38 2024 -0500

    usando fmt para imprimir mensaje
```
</details>

Con estos detalles, podemos ver que el archivo fue introducido por primera vez en el commit `73f627` junto con el autor y la fecha del commit. Podemos verificar y ver todos los archivos que se agregaron en este commit ejecutando el siguiente comando:


```bash
git diff-tree --no-commit-id --name-only -r 73f627b6e277d2ab576ae4ebbcd4408a362c5437
```
<details><summary>Output</summary>

```bash
backup/data.bak
cmd/root.go
```
</details>

You will notice that in some cases, a filename is listed multiple times. This indicates that changes to the file have been commited multiple times in the history of the repository. For large files (in particular large, compressed binary files), this can be problematic as large binaries do not tend to compress nor diff well.

En algunos casos notaras que el archivo se repite varias veces. Esto indica que los cambios en el archivo ha pasado por commits varias veces en la historia del repositorio. Para archivos grandes (en particular archivos binarios grandes y comprimidos), esto puede ser problemático ya que los binarios grandes no tienden a comprimirse ni a diferenciarse bien.

## Conclusion

In this lesson, we learned how to use `git filter-repo` to gather information about blobs in a repository. In particular, we learned how to use `git filter-repo` to find the largest blob, find the commit that it was introduced, and determine what other files were introduced in the same commit.

En esta actividad, aprendimos cómo usar `git filter-repo` para recopilar información sobre blobs en un repositorio. En particular, aprendimos cómo usar `git filter-repo` para encontrar el blob más grande, encontrar el commit en el que se introdujo y determinar qué otros archivos se introdujeron en el mismo commit.

:arrow_backward: [Regresar al README](../README.md)