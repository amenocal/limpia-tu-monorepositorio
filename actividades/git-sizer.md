# Recopilando estadísticas con `git-sizer`

Para la primera actividad, usaremos `git-sizer` para analizar el estado de tu monorepositorio. `git-sizer` es una herramienta que te ayudará a comprender el tamaño de su repositorio e identificar archivos y commits grandes.

Antes de comenzar, echemos un vistazo al contenido de nuestro monorepositorio actual.

```bash
cd /source/mal-monorepositorio
ls -l -h
```

<details><summary>Output</summary>

```bash
total 24K    
-rw-r--r--    1 root     root        1.1K May 15 22:24 LICENSE
-rw-r--r--    1 root     root         127 May 15 22:24 README.md
drwxr-xr-x    2 root     root        4.0K May 15 22:24 cmd
-rw-r--r--    1 root     root         190 May 15 22:24 go.mod
-rw-r--r--    1 root     root         896 May 15 22:24 go.sum
-rw-r--r--    1 root     root        1.1K May 15 22:24 main.go
```
</details>

```bash
git ls-tree --name-only -r HEAD
```

<details><summary>Ouput</summary>

```bash
LICENSE
README.md
cmd/root.go
go.mod
go.sum
main.go
```
</details>

No pareciera que haya nada inusual aqui. Tenemos varios archivos de go y un README, todos de un tamaño razonable.

Por los momemntos, todo parece bien, pero veamos si `git-sizer` nos puede dar una mejor idea de la historia de nuestro repositorio. Ejecuta el siguiente comando en el directorio del monorepositorio:

```bash
git sizer --verbose
```

You should see an output similar to the following:
Deberias de ver un output similar al siguiente:

<details><summary>Output</summary>

```bash
Processing blobs: 11                        
Processing trees: 12                        
Processing commits: 8                        
Matching commits to trees: 8                        
Processing annotated tags: 0                        
Processing references: 3                        
| Name                         | Value     | Level of concern               |
| ---------------------------- | --------- | ------------------------------ |
| Overall repository size      |           |                                |
| * Commits                    |           |                                |
|   * Count                    |     8     |                                |
|   * Total size               |  5.34 KiB |                                |
| * Trees                      |           |                                |
|   * Count                    |    12     |                                |
|   * Total size               |  1.44 KiB |                                |
|   * Total tree entries       |    43     |                                |
| * Blobs                      |           |                                |
|   * Count                    |    11     |                                |
|   * Total size               |  50.0 MiB |                                |
| * Annotated tags             |           |                                |
|   * Count                    |     0     |                                |
| * References                 |           |                                |
|   * Count                    |     3     |                                |
|     * Branches               |     1     |                                |
|     * Remote-tracking refs   |     2     |                                |
|                              |           |                                |
| Biggest objects              |           |                                |
| * Commits                    |           |                                |
|   * Maximum size         [1] |  1.14 KiB |                                |
|   * Maximum parents      [1] |     2     |                                |
| * Trees                      |           |                                |
|   * Maximum entries      [2] |     7     |                                |
| * Blobs                      |           |                                |
|   * Maximum size         [3] |  50.0 MiB | *****                          |
|                              |           |                                |
| History structure            |           |                                |
| * Maximum history depth      |     8     |                                |
| * Maximum tag depth          |     0     |                                |
|                              |           |                                |
| Biggest checkouts            |           |                                |
| * Number of directories  [2] |     3     |                                |
| * Maximum path depth     [4] |     2     |                                |
| * Maximum path length    [2] |    15 B   |                                |
| * Number of files        [2] |     7     |                                |
| * Total size of files    [2] |  50.0 MiB |                                |
| * Number of symlinks         |     0     |                                |
| * Number of submodules       |     0     |                                |

[1]  ee317685018714b2143ee1e91d4e563273c5bdb0
[2]  060ef32262422c838505ae557dafb7733dec22e4 (73f627b6e277d2ab576ae4ebbcd4408a362c5437^{tree})
[3]  a6fa50a34e9494afae1d37e04ce71970c94150ce (73f627b6e277d2ab576ae4ebbcd4408a362c5437:backup/data.bak)
[4]  9c7a571683a510ece67d243860054a9fc38d9e00 (refs/heads/main^{tree})
```

</details>

Que tipo de informacion podemos obtener de esta tabla?

```bash
| Overall repository size      |           |                                |
| * Commits                    |           |                                |
|   * Count                    |     8     |                                |
|   * Total size               |  5.34 KiB |                                |
```

El numero de commits es sinónimo de `git log --oneline | wc -l`.

Este es el numero total de commits en un repositorio. El tamaño total es el tamaño total de todos los commits en el repositorio. Esto no incluye el tamaño de los blobs o los "trees", los cuales son calculados en una sección diferente.

```bash
| Biggest objects              |           |                                |
| * Commits                    |           |                                |
|   * Maximum size         [1] |  1.14 KiB |                                |
|   * Maximum parents      [1] |     2     |                                |
| * Trees                      |           |                                |
|   * Maximum entries      [2] |     7     |                                |
| * Blobs                      |           |                                |
|   * Maximum size         [3] |  50.0 MiB | *****                          |
```
El tamaño máximo de blob(Maximum Blob size) indica el archivo más grande que existe en cualquier commit en la historia de un repositorio.

```bash
| Biggest checkouts            |           |                                |
| * Number of directories  [2] |     3     |                                |
| * Maximum path depth     [4] |     2     |                                |
| * Maximum path length    [2] |    15 B   |                                |
| * Number of files        [2] |     7     |                                |
| * Total size of files    [2] |  50.0 MiB |                                |
| * Number of symlinks         |     0     |                                |
| * Number of submodules       |     0     |                                |
```

Los checkouts mas grandes (Biggest checkouts) nos dice en un solo checkout en cualquier punto de la historia del repositorio, cuales son los tamaños mas grandes o maximos de archivos, directorios, etc. En nuestro caso, un checkout de la rama main en HEAD no contiene 50.0MiB, pero en algun punto de la historia del repositorio, podemos ver que es mucho mas grande...

Al final de la tabla, `git-sizer` también muestra los SHAs de los objetos de git, los SHAs de los commits y las rutas de los objetos de git relevantes.

```bash
[1]  ee317685018714b2143ee1e91d4e563273c5bdb0
[2]  060ef32262422c838505ae557dafb7733dec22e4 (73f627b6e277d2ab576ae4ebbcd4408a362c5437^{tree})
[3]  a6fa50a34e9494afae1d37e04ce71970c94150ce (73f627b6e277d2ab576ae4ebbcd4408a362c5437:backup/data.bak)
[4]  9c7a571683a510ece67d243860054a9fc38d9e00 (refs/heads/main^{tree})
```

Estos SHAs o rutas pueden ser utilizados para investigar objetos de git problemáticos en un repositorio. 
Por ejemplo, `git cat-file` puede ser usado para ver el contenido del blob mas grande en el repositorio. 
En este caso, podemos ver el contenido del blob mas grande mandando el SHA del objeto.

```bash
git cat-file -p a6fa50a34e9494afae1d37e04ce71970c94150ce | head -n 10
```

<details><summary>Output (truncado para brevedad)</summary>

```bash
���ֶ���� ���t�F�j������Y�Iv���sMq}��\ZD���e��4$�\km�Z1O���i^Hp����ۙx�.x�~�7�`T�L.͠7׺��jApfeӝ�?0���]�|结�k�_�P��tV0��$�C�ﵻw�J|���35��0��I�D�������N���/���l�Buz��������8�I*��F��|��啕���o/�)څ�<     n+�����,r��������rZ���������%�bB<Xc�a�,Z���n������-�c�����,
��.���׳������.,w��B3#�XG���r
                            3j�0)~~۸���!wP��̻NМ��bn�������ޡ���]jk�̯S+-�[�Y��!la�ڙ�LT�
                                                                                   �3��|x�o(U�����9�6�c��;y����W��Ϋ�iҼ7h
                                                                                                                        G�ؓI�d^��b�q���%�zg�h
                                                                                                                                            �ȏ(a:�;04l��~;�B*�T���SG�o���h%�9�5]bU_o�m�a?�F-u�%>�H5��|e3T�j��t��D��>Q���U>�=2TF.�тus��I�L[�;��(�덾����ze�*�1^�8E�X�6z�#4��"��A��`J"�Dr�G�$�lÜS�/>
        ��^�,��s�����7��ݶ52v    �yn�iU1�{ ?[o�|{�uX�;�7���L�]6|ٓ��b �-����m�a/$�,��MK�-<����1��迼����pl>EC+b�#�YE�N��q�7�Z�Ěi90�iè/�Y:'�5c�v�tq/}x�͞,������Ѐc5Y����"��}�Օ
��p�i���pp��e�T]�Oco��w+;�      p�B�8�
!��7��[HQ�B�!�x����*w硁��\���F��IE�K��A�I�'
                                 �
                                  �7Вg��
                                        �4��~��nĩ�:Mg� Ea�87����aO���q\ګ�֠��GI76Q�3XL1�9ע��:����v���YQW$�"�@�@�_�}q��J���*Q0f��CՏUz. ��3�N��|A,�^�]i�\2�3�V�H��8�M`_a���NB:ͽ3�
                                                                                                                                                                             _D�C3�=�   ��&75��$�4ƊCŐ��2&�z����h2ѱ ���1�ya9N��Z&��R�����"t҂<<@�d�zU3t��ؖ­MP&����jǷ���k�a�J�hJ@��8A��v�/��-��g<ut��L�)�7�Q�2>F7�8M����p��e@~a�             �'�)�g�R��Y�E�g���q�N�tz�'c�p�!�%�q^�6D�恧�
ڭ"�W*�_�                                                                                  ��aLF�\M.�7�G0ݕ�]�8b
        �`���?���P�� Y�
F�Y�R�I�}�d��s!U�Ts�R�Qt-"�"T�ս!���0�@����X3��z�HҠb��x
9���F�z�ŧ��HV�t�h�[T�Y�]�l6�yԢܓB��;}��|e���=�q�zFt�z{D&�$��T�m#-A�bʼ�ZǶ�8<we���[�$U\b�Au�������H@��S>`/b�Cl1����v�_���M�.�&���9�i�x\�,{�7J�[pUmpQ���Eț�$�������,a����%�x���s�ź"Dqc44�� 5>c<[��"O6��Vp�*}@~�n��{��}����1�'�2�B
                                                                                                                                                                                                                             rD�0hϵy�ݷ���!��M8�������Ã'xA���?�LyQ+�R�� ҩH
```

</details>

Por el output, podemos ver claramente que el objeto es un archivo binario... Usemos el SHA del commit y `git ls-tree` para ver que contiene el repositorio:

```bash
git ls-tree --name-only -r 060ef32262422c838505ae557dafb7733dec22e4
```

<details><summary>Output</summary>

```bash
LICENSE
README.md
backup/data.bak
cmd/root.go
go.mod
go.sum
main.go
```
</details>

Como se esperaba, podemos ver que en este punto de la historia, el repositorio contenia `backup/data.bak` el cual fue eliminado, pero permanece en la historia del repositorio.

## Conclusion

Git sizer es un metodo rapido y simple para obtener una idea general del tamaño y salud de un repositorio git. Puede ser usado para ayudar a identificar archivos grandes, directorios y commits en la historia de un repositorio.

:arrow_backward: [Regresar al README](../README.md)