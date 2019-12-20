# Profundizando

- [Stashing para evitar commits parciales](#stashing-para-evitar-commits-parciales)
  - [¿Qué es un stash?](#¿qué-es-un-stash)
  - [Comandos para administrar stashes](#comandos-para-administrar-stashes)
  - [¿Cuándo puede cambiarse de rama sin hacer commit o stash de las modificaciones?](#¿cuándo-puede-cambiarse-de-rama-sin-hacer-commit-o-stash-de-las-modificaciones)
- [Eliminar archivos untracked](#eliminar-archivos-untracked)
- [Ignorar archivos (.gitignore)](#ignorar-archivos-gitignore)
  - [Expresiones glob vs regulares](#expresiones-glob-vs-regulares)
  - [Notación de expresiones glob](#notación-de-expresiones-glob)
  - [.gitignore](#gitignore)
  - [Ignorar archivos tracked](#ignorar-archivos-tracked)
- [Correcciones avanzadas](#correcciones-avanzadas)
  - [Reset](#reset)
    - [Uso del comando](#uso-del-comando)
    - [Recuperación de un `git reset --hard` equivocado](#recuperación-de-un-git-reset---hard-equivocado)
  - [Revert](#revert)
    - [`git revert` vs `git reset`](#git-revert-vs-git-reset)
    - [Uso del comando](#uso-del-comando-1)

## Stashing para evitar commits parciales

> Resumen de <https://git-scm.com/book/en/v2/Git-Tools-Stashing-and-Cleaning>

Al trabajar en un proyecto podría surgir la necesidad de revisar una rama distinta en la que actualmente se trabaja. En ocasiones Git reporta un mensaje de error similar al siguiente cuando se intenta cambiar de rama sin haber primero hecho commit de los cambios del working tree.

```shell
error: Your local changes to the following files would be overwritten by checkout:
        foo.txt
Please commit your changes or stash them before you switch branches.
Aborting
```

También ocurre que a la mitad de la implementación de algún patch o feature un repositorio remoto se actualiza y se desea traer los cambios al repositorio local. Si se tienen archivos modified y se realiza un pull un mensaje similar al siguiente se observa.

```shell
error: Your local changes to the following files would be overwritten by merge:
        foo.txt
Please commit your changes or stash them before you switch branches.
Aborting
```

Aquí la solución más sencilla es realizar un commit, pero qué ocurre si el trabajo en la rama actual aún no está listo para ser persistido en un commit. La solución yace en el stashing. **Para crear un stash, existen los comandos `git stash`, `git stash push` y `git stash save`. Ya que el último no es recomendado (es sintaxis antigua, sustituido por `git stash push`) y el primero no provee más que lo esencial, me centraré en crear stashes mediante `git stash push`**.

### ¿Qué es un stash?

**Un stash es un commit** (creado mediante alguno de los comandos anteriormente mencionados) que tiene la particularidad de no estar asociado a alguna rama. Los stashes se almacenan en una pila indizada a partir del cero. Es decir, al crearse un nuevo stash, su índice en la pila es cero; el que era cero se vuelve uno, el que era uno se vuelve dos, etc. El hash SHA-1 del stash con índice cero puede hallarse en `.git/refs/stash`. **Al estar desacoplado de las ramas, los stashes pueden ser referenciados en cualquier rama**.

### Comandos para administrar stashes

Mostrar todos los stashes (commits desacoplados de ramas).

```bnf
git stash list
```

Crear un nuevo stash incluyendo sólo los archivos modificados (sin banderas `-u` ni `-a`); incluyendo archivos modificados y untracked (bandera `-u`); inluyendo archivos modificados, untracked e ignorados (bandera `-a`). Adicionalmente, a diferencia de `git stash save` o simplemente `git stash`, `git stash push` permite especificar los archivos que se almacenan en el stash. También es posible utilizar la misma bandera de mensaje de commit (`-m`) para etiquetar al stash con un mensaje. Tras crear un stash, todo lo almacenado en tal commit es retirado del working tree.

```bnf
git stash push [-u | -a | [-m "<mensaje>"]] [<archivos>]
```

Aplicar los cambios de un stash al working tree. El stash permanece en la pila (1); para aplicar los cambios y eliminar el stash de la pila úsese (2). Si un stash no es proporcionado, se utiliza el stash `stash@{0}` para ambos comandos. **Nótese que si el stash tuviera conflictos con los archivos existentes en el working tree, por ejemplo, si en el stash existe un archivo foo.txt y en el working tree también, ninguno de los dos comandos realizaría cambio alguno sobre el stash o working tree**, reportando en su lugar un error sobre las líneas de *foo.txt already exists, no checkout [\n] Could not restore untracked files from stash entry*. Para evitar este error y entrar en el modo de resolución de conflictos, añadir todas las modificaciones actuales al staging area y luego realizar un `git stash pop` o `git stash apply`.

```bnf
git stash apply [stash@{<índice>}]  (1)
git stash pop [stash@{<índice>}]    (2)
```

Eliminar un stash, sin aplicar los cambios (1). Si ningún stash es proporcionado, se elimina stash@{0}. Eliminar todos los stashes de la pila (2). Los cambios de los stashes eliminados no pueden ser recuperados.

```bnf
git stash drop [stash@{<índice>}]  (1)
git stash clear                    (2)
```

### ¿Cuándo puede cambiarse de rama sin hacer commit o stash de las modificaciones?

> Simplificación de <https://stackoverflow.com/questions/22053757/checkout-another-branch-when-there-are-uncommitted-changes-on-the-current-branch>

Al inicio de esta sección se mencionó que *en ocasiones* Git reporta un mensaje de error cuando se ejecuta un checkout a otra rama teniendo cambios uncommitted en el working tree. Esto implica que, en algunas situaciones, es posible cambiar de rama sin hacer commit de los cambios y sin perder los mismos. Puntualmente, sólo una condición debe satisfacerse para que la posibilidad exsita.

> Es posible cambiar de rama teniendo modificaciones uncommitted en el working tree si el cambio **no requiere deshacer** dichas modificaciones.

Un cambio de rama conlleva un posible cambio del working tree, pues el working tree muestra el contenido almacenado en el snapshot asociado al commit al que apunta la rama. Pensemos en un ejemplo. Al cambiar de la rama `X` a la rama `Y` las siguientes acciones ocurren, donde `Xs` y `Ys` son los snapshots correspondientes de los commits a los que apuntan las ramas.

1. Para cada archivo en `Ys` y no en `Xs`, crearlo.
2. Para cada archivo en `Xs` y no en `Ys`, eliminarlo.
3. Para cada archivo en ambos snapshots, si la versión en `Ys` es distinta a la versión en `Xs`, actualizar el contenido.

Si en el cambio de rama algunas de estas acciones requiere ocurrir **sobre alguno de los archivos modificados o creados**, entonces Git cancela el checkout y muestra el mensaje de error presente al inicio de la sección. De lo contrario, el checkout es legal. Obsérvese la condición en la tercera acción; si el archivo existe en ambos snapshots, sólo se muestra la versión del snapshot `Ys` si es distinta a la del snapshot `Xs`, no si es distinta a la versión del working tree. En los siguientes casos siempre es posible un cambio de rama con cambios uncommitted.

- Se crea `rama-m` a partir de `rama-n`. Se trabaja en `rama-m`, modificando archivos y creando nuevos, pero no se realiza un commit. Es legal hacer checkout a `rama-n`. Esto se cumple siempre pues ambas ramas apuntan al mismo commit, que a su vez apunta al mismo snapshot. A pesar que la versión del working tree es distinta al snapshot, las versiones entre los snapshots son iguales, por lo que Git no intenta traer la versión del snapshot de la rama objeto del checkout.
- Se crean archivos cuyos nombres y extensiones no existen en otras ramas, es legal cambiar de rama. Puesto que el archivo está untracked y no existen en otras ramas, un cambio de rama no requiere creación, eliminación ni modificación sobre este archivo.

Ahora dirijamos nuestra atención a casos en los que no es posible un cambio de rama teniendo modificaciones uncommitted.

- Se crea un archivo cuyo nombre y extensión existen en otra rama. En la rama actual el archivo está untracked, pero en la rama objeto del checkout el archivo está tracked. Al intentar el cambio, se requiere la acción 1 (creación) sobre el nuevo archivo; en el snapshot de la rama origen no existe el archivo, pero en el snapshot de la rama destino sí, entonces se crea, pero en el proceso colisiona con el archivo modificado.
- Se modifica un archivo que en otra rama no existe. Es ilegal cambiarse a esa rama con la modificación uncommited pues se requiere la acción 2 (eliminación) sobre el archivo modificado.

## Eliminar archivos untracked

> Resumen de <https://git-scm.com/docs/git-clean>

Ya sea por un build u otra razón, a veces simplemente se quiere eliminar los archivos no versionados por Git. Aquí se presenta una sintaxis simplificada que muestra las banderas más comunes. Si la variablde configuración de Git `clean.requireForce` no tiene el valor `false`, entonces la bandera `-f` siempre es requerida para ejecutar el comando. Utilice `-d` para recursivamente eliminar directorios untracked. Utilice `-x` para eliminar también archivos ignorados.

```bnf
git clean [-d] [-f] [-x] [<path>]
```

## Ignorar archivos (.gitignore)

> Resumen de <https://git-scm.com/docs/gitignore>

En la sección dedicada al ambiente de desarrollo se mencionan los estados de los archivos de acuerdo a Git: tracked (posiblemente modified o staged) y untracked. En adición, **un archivo puede tener el estado *ignored***. Existen archivos de configuración de IDEs o editores de texto, como .vscode de VSCode o .idea/workspace.xml de IntelliJ IDEA, los cuales no se desean agregar al repositorio. De la misma intención son objeto los archivos resultantes de un build, como un directorio `target` o los `.class` de Java. En estos casos, dichos archivos se desean dejar permanentemente untracked.

La solución es ignorar los archivos, volviéndolos no seleccionables para los comandos de Git (utilizar la bandera `-a` o `--all` en algunos comandos, como `git stash push`, pueden considerar archivos ingorados). Para ignorar archivos se requiere un archivo de configuración `.gitignore`, el cual puede tener impacto global o sólo respecto a un repositorio. Los archivos a ignorar se seleccionan utilizando [expresiones glob](https://en.wikipedia.org/wiki/Glob_(programming)).

---

### Expresiones glob vs regulares

Varios comandos de Git requieren seleccionar archivos, como `git add` o `git stash push`. El archivo `.gitignore` también selecciona archivos. Las expresiones glob sirven el propósito de representar archivos mediante wildcards (caracteres especiales), dado un sistema de archivos; mientras que las expresiones regulares representan cadenas de texto, dada una secuencia de caracteres. Ambas expresiones sirven un propósito distinto aunque similar (y la sintaxis también es similar). Puesto que archivos se desean seleccionar en lugar de frases en un texto, las expresiones regulares no tienen sentido en este contexto. **Cuando los glob se utilizan en comandos, como `git add` por ejemplo, ponerlos entre comillas simples para evitar expansión del shell**.

| ✔️ Evitar expansión de shell. Git procesa el glob | ❌ Expansión de shell. El shell expande la ruta y pasa la evaluación a Git. Git no procesa un glob |
|:---:|:---:|
| `git add '**/*.txt'` | `git add **/*.txt` |

---

### Notación de expresiones glob

| Símbolo | Explicación |
|---|---|
| * | Representa cualquier número de caracteres, incluido ninguno, pero no una diagonal. |
| ** | Representa uno o más directorios, pero no el directorio actual (`.`) ni el padre (`..`). |
| ? | Representa un caracter. |
| [abc] | Representa cualquier caracter contenido en los corchetes. |
| [a-z] | Representa cualquier caracter contenido en el intervalo definido por los corchetes. |

### .gitignore

Este archivo define los archivos que Git ignorará. **Archivos tracked no son afectados por este archivo**. El archivo puede contener comentarios, los cuales inician con `#`. Para especificar archivos a ignorar, colocar un patrón glob por línea. Si se ignora un directorio, todos sus archivos y subdirectorios también son ignorados. Un repositorio puede tener más de un `.gitignore`, siendo sus patrones glob relativos a la ubicación del archivo.

Git permite definir un `.gitignore` de impacto global (afecta todos los repositorios del usuario con esta configuración). Primero es necesario crear el archivo manualmente, luego se indica su ubicación en la configuración global.

```bnf
git config --global core.excludesFile <ruta-.gitignore>
```

### Ignorar archivos tracked

Para ignorar archivos tracked (añadidos al staging environment o existentes en algún commit) primero deben ser olvidados por Git, es decir, cambiar su estado a untracked. Para conseguir esto se utiliza el comando siguiente, donde `<archivo>` acepta expresiones glob.

```bnf
git rm --cached <archivo>
```

## Correcciones avanzadas

### Reset

> Resumen de <https://www.atlassian.com/git/tutorials/undoing-changes/git-reset>

Este comando es muy interesante, poderoso y, si ha comprendido la sección de ambiente de desarrollo, sencillo de entender. Similar a git checkout, este comando permite mover la referencia HEAD entre commits, mas difiere en tanto que no sólo actúa respecto a HEAD, sino que también respecto a una rama.

![Checkout vs reset](/images/checkout_vs_reset.png)

Existen tres modalidades de reseteos seleccionables por las banderas --soft, --mixed y --hard, de los cuales --mixed es utilizado por defecto si ninguno es seleccionado. Las tres tienen en común que mueven HEAD y la rama apuntada por HEAD al commit seleccionado. Los modos difieren en lo que restauran (sobre qué tiene efecto el reset), siendo los objetivos de restauración el working tree y el staging area. En cuanto al staging area, restaurar alude a retirar los archivos del estado staged, mas los cambios se mantienen en el working directory. Respecto al working directory, restaurar significa sustituir los actual por los registrado en el snapshot del commit seleccionado.

| Modalidad | Objetivo de restauración |
|---|---|
| `--soft` | (Ni el staging area ni el working tree son restaurados, sólo HEAD y la rama apuntada por HEAD son movidos.)<br><br>*Las modificaciones de los commits descendientes del commit seleccionado son **colocadas en el staging area** (evidentemente, son visibles también en el working tree).* |
| `--mixed`<br>*modo predeterminado* | Staging area<br><br>*Las modificaciones de los commits descendientes del commit seleccionado son **colocadas en el working tree** (unstaged).* |
|`--hard`| Staging area y working tree<br><br>***Las modificaciones de los commits descendientes del commit seleccionado son ELIMINADAS***. |

#### Uso del comando

Retira del staging area los archivos especificados (1). Si alguno de los archivos inicia con un guión alto (`-`), añadir dos guiones altos antes del nombre del archivo (esto evita que Git intente procesar el archivo como una bandera). Para el reset discutido anteriormente, utilice (2). La sintaxis completa puede encontrarla en <https://git-scm.com/docs/git-reset>.

```bnf
git reset [--] <archivos>                       (1)
git reset [--soft | --mixed | --hard] <commit>  (2)
```

#### Recuperación de un `git reset --hard` equivocado

Al realizar un reseteo duro, los commits descendientes del seleccionado se vuelven inaccesibles mediante `git log` y su contenido es eliminado del working tree y staging area. En la imagen inferior podemos notar que el commit 3 no es listado tras el reseteo duro.

![Visualización de git reset --hard](/images/reset_hard.png)

Esto no significa que el commit 3 sea inaccesible, tan sólo que recorriendo el árbol de commits a partir de `HEAD` (o cualquier `head`) no es posible llegar a él. El commit no ha sido eliminado. Para recuperse de este reset basta con hallar el hash SHA-1 del commit al que deseamos regresar y ejecutar un reseteo duro respecto al mismo. Para hallar el hash, se utliza `git reflog`, que lista el historial de commits que ha visitado `HEAD`. Siguiendo el ejemplo, al hallar el hash 3, basta con realizar `git reset --hard 3`.

### Revert

> Resumen de <https://www.atlassian.com/git/tutorials/undoing-changes/git-revert>

Al igual que `git reset`, `git revert` permite eliminar cambios introducidos por uno o más commits. La distinción más importante radica en que este comando no modifica la historia, realizando la corrección no al eliminar commits, pero al agregar uno con las correcciones.

![Reset vs revert](/images/reset_vs_revert.png)

#### `git revert` vs `git reset`

Revert permite deshacer los cambios introducidos por commits selectos (incluso commits no secuenciales o muy atrás en la historia), mientras que reset sólo puede deshacer hacia atrás. Revert siempre es seguro, pues no alterla la historia del repositorio, haciendo imposible romper la historia un repo remoto mediante revert. Por otra lado, reset sí puede romper la historia de un repo remoto si es utilizado incorrectamente. **Sólo utilizar `git reset` sobre commits que aún no han sido publicados (push)**. A pesar de estas desventajas, recomiendo utilizar reset en los casos que es posible, ilustrado por el diagrama inferior, pues evita el commit extra de corrección.

![Cuándo usar revert o reset](/images/revert_or_reset.png)

#### Uso del comando

Como es usual, aquí se presenta una sintaxis simplificada respecto a las banderas y opciones más comunes. Para la sintaxis compeleta refiérase a <https://git-scm.com/docs/git-revert>. Sin el uso de la bandera `-n`, este comando crea un nuevo commit con la corrección (sin las modificaciones incorporadas en los commits seleccionados), abriendo el editor de texto especificado en `core.editor` para ingresar el mensaje del commit. Utilizar `--no-edit` para no abrir el editor de texto y usar el mensaje predeterminado. Al utilizar la banedar `-n`, en lugar de directamente crear un commit, las modificaciones son realizadas en el working tree y colocadas en el staging area.

```bnf
git revert [--no-edit] [-n] <commit>
```

Para demostrar distintas correcciones utilizando este comando, considere este repositorio.

```shell
$ git init
Initialized empty Git repository in C:/Users/hjcer/temp/.git/

$ touch file_1.txt

$ git add file_1.txt

$ git commit -m "Create file_1"
[master (root-commit) 97017e3] Add file_1
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file_1.txt

$ echo "First line of file_1" > file_1.txt

$ git commit -a -m "Add content to file_1"
[master 9bb6e59] Add content to file_1
 1 file changed, 1 insertion(+)

$ touch file_2.txt

$ git add file_2.txt

$ git commit -m "Create file_2"
[master e935221] Add file_2
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file_2.txt

$ echo "First line of file_2" > file_2.txt

$ git commit -a -m "Add content to file_2"
[master 04a0106] Add content to file_2
 1 file changed, 1 insertion(+)

$ git log --oneline
dc01f42 (HEAD -> master) Add content to file_2
386a54a Create file_2
e3646e8 Add content to file_1
98a0ead Create file_1
```

---

**Ejemplo 1. Eliminar modificaciones del último commit**

```shell
$ git revert HEAD
[master f5544d9] Revert "Add content to file_2"
 1 file changed, 1 deletion(-)

$ git log --oneline
f5544d9 (HEAD -> master) Revert "Add content to file_2"
dc01f42 Add content to file_2
386a54a Create file_2
e3646e8 Add content to file_1
98a0ead Create file_1
```

Este comando abre el editor de texto para pedir el mensaje del commit de corrección. (Si prefiere evitar el editor de texto y aceptar el menaje predeterminado, el comando sería `git revert --no-edit HEAD`.) Tras salir del editor, puede verificar que un nuevo commit ha sido creado. Esta corrección pudo haberse realizado mediante un reset duro, pero al emplear un revert se evita reescribir la historia, lo cual casi nunca no es recomendable hacer en una rama pública (los cambios han pasado a un repo remoto por `git push`).

---

---

**Ejemplo 2. Eliminar modificaciones de commits no secuenciales**

```shell
$ git revert HEAD~3
error: could not revert 98a0ead... Create file_1
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'

$ git rm file_1.txt
rm 'file_1.txt'

$ git revert --continue
[master 98b6774] Revert "Create file_1"
 1 file changed, 1 deletion(-)
 delete mode 100644 file_1.txt

$ git revert --no-edit HEAD~
[master 90e5838] Revert "Add content to file_2"
 Date: Mon Dec 16 19:43:05 2019 -0600
 1 file changed, 1 deletion(-)

$ git log --oneline
90e5838 (HEAD -> master) Revert "Add content to file_2"
98b6774 Revert "Create file_1"
dc01f42 Add content to file_2
386a54a Create file_2
e3646e8 Add content to file_1
98a0ead Create file_1
```

Aquí se hace énfasis en la falta de secuencia de los commits pues esto no sería posible utilizando `git reset`. Evidentemente, si los commits fueran secuenciales, el mismo proceso puede utilizarse. Puede verse que si se intenta revertir algún commit que tenga conflicto con un descendiente ocurre un conflicto, el cual se resuelve manualmente, se añaden las resoluciones al staging area y se ejecuta `git revert --continue`.

---
