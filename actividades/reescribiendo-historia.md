# Reescribiendo la historia con `git filter-repo`

Ahora que ya hemos recopilado información sobre nuestros archivos grandes, veamos cómo podemos reescribir la historia para eliminarlos. Usaremos la herramienta `git filter-repo`.

En nuestro ejemplo, usamos `git-sizer` y `git filter-repo` para determinar que nuestro repositorio contenía un archivo de respaldo de base de datos(un archivo binario) que accidentalmente fue agregado al repositorio.
Para este ejercicio, vamos a limpiar nuestro repositorio y eliminaremos todas las instancias del archivo `backup/data.bak` del historial del repositorio.
`git filter-repo` nos permite mandarle una sola ruta o una lista de rutas a todas las instancias del archivo del historial del repositorio.

Primero, naveguemos a una copia fresca de nuestro repositorio y ejecutemos el siguiente comando:

```bash
cd /source/monorepositorio-filter-repo
git filter-repo --path backup/data.bak --invert-paths
```

> [!CAUTION]
> Asegúrate de que la bandera `--invert-paths` esté incluida en tu comando, de lo contrario, eliminarás todas las instancias de todos los archivos **excepto** el archivo `backup/data.bak`.

El output debería ser algo similar a lo siguiente:

```bash
Parsed 8 commits
New history written in 0.02 seconds; now repacking/cleaning...
Repacking your repo and cleaning out old unneeded objects
HEAD is now at d77a382 usando fmt para imprimir mensaje
Enumerating objects: 27, done.
Counting objects: 100% (27/27), done.
Delta compression using up to 10 threads
Compressing objects: 100% (13/13), done.
Writing objects: 100% (27/27), done.
Total 27 (delta 9), reused 20 (delta 8), pack-reused 0
Completely finished after 0.03 seconds.
```

Este comando eliminará todas las instancias del archivo del historial del repositorio. Podemos verificar esto ejecutando el mismo comando que usamos anteriormente para obtener el primer commit en el que se introdujo el archivo:

```bash
git --no-pager log --follow  --diff-filter=A -- backup/data.bak
```

El cual no debería producir ningún output....

También podemos correr `git log --oneline | wc -l` para obtener el conteo de commits en el repositorio:

```bash
git log --oneline | wc -l
```

<details><summary>Output</summary>

```bash
7
```

</details>

Aquí podemos notar que el número de commits ha sido reducido de 8 a 7. Cualquier commit donde el archivo era el único objeto que se agregaba/modificaba ha sido eliminado del historial.

Finalmente, veamos el output de `git-sizer` para ver si algo ha cambiado:

```bash
git-sizer --verbose
```

<details><summary>Output</summary>

```bash
Processing blobs: 10                        
Processing trees: 10                        
Processing commits: 7                        
Matching commits to trees: 7                        
Processing annotated tags: 0                        
Processing references: 8                        
| Name                         | Value     | Level of concern               |
| ---------------------------- | --------- | ------------------------------ |
| Overall repository size      |           |                                |
| * Commits                    |           |                                |
|   * Count                    |     7     |                                |
|   * Total size               |  1.69 KiB |                                |
| * Trees                      |           |                                |
|   * Count                    |    10     |                                |
|   * Total size               |  1.17 KiB |                                |
|   * Total tree entries       |    35     |                                |
| * Blobs                      |           |                                |
|   * Count                    |    10     |                                |
|   * Total size               |  12.9 KiB |                                |
| * Annotated tags             |           |                                |
|   * Count                    |     0     |                                |
| * References                 |           |                                |
|   * Count                    |     8     |                                |
|     * Branches               |     1     |                                |
|     * Other                  |     7     |                                |
|                              |           |                                |
| Biggest objects              |           |                                |
| * Commits                    |           |                                |
|   * Maximum size         [1] |   339 B   |                                |
|   * Maximum parents      [1] |     2     |                                |
| * Trees                      |           |                                |
|   * Maximum entries      [2] |     6     |                                |
| * Blobs                      |           |                                |
|   * Maximum size         [3] |  2.41 KiB |                                |
|                              |           |                                |
| History structure            |           |                                |
| * Maximum history depth      |     7     |                                |
| * Maximum tag depth          |     0     |                                |
|                              |           |                                |
| Biggest checkouts            |           |                                |
| * Number of directories  [2] |     2     |                                |
| * Maximum path depth     [2] |     2     |                                |
| * Maximum path length    [2] |    11 B   |                                |
| * Number of files        [2] |     6     |                                |
| * Total size of files    [2] |  5.75 KiB |                                |
| * Number of symlinks         |     0     |                                |
| * Number of submodules       |     0     |                                |

[1]  4c597d6660f80165e832a49de75e5c35f36c6a8c (refs/replace/ee317685018714b2143ee1e91d4e563273c5bdb0)
[2]  9c7a571683a510ece67d243860054a9fc38d9e00 (refs/heads/main^{tree})
[3]  67533d22028802e3d428e1f11304d76bcc7e07bc (refs/replace/d56d6b47466b132ec054eee397e8c02643e66f20:cmd/root.go)
```

</details>

Con este nuevo output, podemos ver que ya no tenemos commits que contengan el archivo `backup/data.bak`. También podemos ver que el blob mas grande se ha reducido de 50.0 MiB a 2.41KiB, además de que el tamaño total de los blobs ha disminuido de 50.0 MiB a 12.9 KiB.

Después de realizar una reescritura, `git filter-repo` también producirá una lista de shas reescritos. Esto puede ser util para cualquier implementación que haga referencia a commits por su sha.

Estos contenidos se encuentran en el archivo `.git/filter-repo/commit-map` en el directorio de trabajo del repositorio. El contenido de este archivo se ve algo como esto:

```bash
old                                      new
332addd7607c12bcb0d0f955970533a41a321e90 0000000000000000000000000000000000000000
f9a40dc6c3d492dacfea5ac574ad3909eebd2661 63b335be9ea5c29ec492ba0da0a0dbdc7819b50b
d56d6b47466b132ec054eee397e8c02643e66f20 84ec6862266ec994131a13d0feeb2dcd6d382df6
4fa2962b6ebd2e9f1870a976d73c8cbf001d76bc 7203fa79d6692311ea1d2d281ccfe10b88565ea1
4a86093d6bac23a9c879284225ea94b29bf6ec51 3bd89186cd406a7c62af8f1f09dd41d5ab1adfab
b4cf337e6face4f4cca3297d85eefa908b30a23e d721441a97b1c0123a75d01c5c55ee07c637c121
ee317685018714b2143ee1e91d4e563273c5bdb0 4c597d6660f80165e832a49de75e5c35f36c6a8c
73f627b6e277d2ab576ae4ebbcd4408a362c5437 d77a382460bf5d38c7c3d27cf29372425142b89c
```

En los casos donde se vean todos 0's, esto significa que el commit fue eliminado del historial.

> [!CAUTION]
> Es importante notar que ejecuciones subsecuentes de `git filter-repo` reescribirán la historia multiples veces, y por lo tanto los shas y estos archivos cambiaran nuevamente. Es importante combinar tus parámetros en un solo comando para solo tener que ejecutar `git filter-repo` y reescribir la historia una vez.

## Conclusion

En esta actividad, aprendimos como utilizar `git filter-repo` para remover archivos grandes de nuestro historial de repositorio. En particular, aprendimos como remover todas las instancias de un solo blob grande del historial de commits, y verificar que ha sido removido exitosamente.

:arrow_backward: [Regresar al README](../README.md)
