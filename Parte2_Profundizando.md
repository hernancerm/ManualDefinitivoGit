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
- [El confuso archivo `.gitkeep` y los directorios vacíos](#el-confuso-archivo-gitkeep-y-los-directorios-vacíos)
- [Correcciones avanzadas](#correcciones-avanzadas)
  - [Reset](#reset)
    - [Uso del comando](#uso-del-comando)
    - [Recuperación de un `git reset --hard` equivocado](#recuperación-de-un-git-reset---hard-equivocado)
  - [Revert](#revert)
    - [¿Cuándo utilizar `git revert` sobre `git reset`?](#¿cuándo-utilizar-git-revert-sobre-git-reset)
    - [Uso del comando](#uso-del-comando-1)
- [Reescribiendo la historia](#reescribiendo-la-historia)
  - [¿Qué es *reescribir la historia*?](#¿qué-es-reescribir-la-historia)
  - [¿Qué es la *historia pública*?](#¿qué-es-la-historia-pública)
  - [¿Cómo se corrigen commits públicos?](#¿cómo-se-corrigen-commits-públicos)

## Stashing para evitar commits parciales

> Resumen de <https://git-scm.com/book/en/v2/Git-Tools-Stashing-and-Cleaning>

Al trabajar en un proyecto podría surgir la necesidad de revisar una rama distinta en la que actualmente se trabaja. En ocasiones Git reporta un mensaje de error similar al siguiente cuando se intenta cambiar de rama sin haber primero hecho commit de los cambios del working tree.

```shell
error: Your local changes to the following files would be overwritten by checkout:
        foo.txt
Please commit your changes or stash them before you switch branches.
Aborting
```

También ocurre que a la mitad de la implementación de algún patch o feature, un repositorio remoto se actualiza y se desea traer los cambios al repositorio local. Si se tienen archivos modified y se realiza un pull, un mensaje similar al siguiente se observa.

```shell
error: Your local changes to the following files would be overwritten by merge:
        foo.txt
Please commit your changes or stash them before you switch branches.
Aborting
```

Si el trabajo en la rama actual aún no está listo para ser persistido en un commit, la solución yace en el stashing. **Para crear un stash, existen los comandos `git stash`, `git stash push` y `git stash save`. Ya que el último no es recomendado (es sintaxis antigua, sustituida por `git stash push`) y el primero está limitado, me centraré en crear stashes mediante `git stash push`**.

### ¿Qué es un stash?

**Un stash es un commit** (creado mediante alguno de los comandos anteriormente mencionados) que tiene la particularidad de no estar asociado a alguna rama. Los stashes se almacenan en una pila indizada a partir del cero. Es decir, al crearse un nuevo stash, su índice en la pila es cero; el que era cero se vuelve uno, el que era uno se vuelve dos, etc. El hash SHA-1 del stash con índice cero puede hallarse en `.git/refs/stash`. (Recuerde que un commit almacena las referencias a sus padres, por lo que basta con almacenar sólo el stash más reciente de la pila.) **Al estar desacoplado de las ramas, los stashes pueden ser referidos en cualquier rama**.

### Comandos para administrar stashes

Mostrar todos los stashes.

```bnf
git stash list
```

Crear un nuevo stash incluyendo sólo los archivos modificados (comportamiento dado su uso sin banderas `-u` ni `-a`); incluyendo archivos modificados y untracked (bandera `-u`); incluyendo archivos modificados, untracked e ignorados (bandera `-a`). Adicionalmente, a diferencia de `git stash save` o simplemente `git stash`, `git stash push` permite especificar los archivos que se almacenan en el stash. También es posible utilizar la misma bandera de mensaje de commit (`-m`) para etiquetar al stash con un mensaje. Tras crear un stash, todo lo almacenado en tal commit es retirado del working tree.

```bnf
git stash push [-u | -a] [-m "<mensaje>"] [<archivos>]
```

Aplicar los cambios de un stash al working tree. El stash permanece en la pila (1); para aplicar los cambios y eliminar el stash de la pila úsese (2). Si un stash no es proporcionado, se utiliza el stash `stash@{0}` para ambos comandos.

**Nótese que si el stash tuviera conflictos con los archivos existentes en el working tree, por ejemplo, si en el stash existe un archivo foo.txt y en el working tree también, ninguno de los dos comandos realizaría cambio alguno sobre el stash o working tree**, reportando en su lugar un error. Para evitar este error y entrar en el modo de resolución de conflictos, añadir todas las modificaciones actuales al staging area y luego realizar un `git stash pop` o `git stash apply`.

🔍 **Tip.** [Recuperar stashes perdidos](https://stackoverflow.com/questions/32517870/how-to-undo-git-stash-clear) puede ser complicado, por ello recomiendo utilizar `git commit` incluso para cambios parciales si estos son muy significativos. Luego siempre es posible realizar un `git commit --amend` para terminar de componer el commit.

```bnf
git stash apply [stash@{<índice>}]  (1)
git stash pop [stash@{<índice>}]    (2)
```

Eliminar un stash, sin aplicar los cambios (1). Si ningún stash es proporcionado, se elimina `stash@{0}`. Eliminar todos los stashes de la pila (2).

```bnf
git stash drop [stash@{<índice>}]  (1)
git stash clear                    (2)
```

### ¿Cuándo puede cambiarse de rama sin hacer commit o stash de las modificaciones?

> Simplificación de <https://stackoverflow.com/questions/22053757/checkout-another-branch-when-there-are-uncommitted-changes-on-the-current-branch>

Al inicio de esta sección se mencionó que *en ocasiones* Git reporta un mensaje de error cuando se ejecuta un checkout a otra rama teniendo cambios uncommitted en el working tree. Esto implica que, en algunas situaciones, es posible cambiar de rama sin hacer commit de los cambios y sin perder los mismos. Puntualmente, sólo una condición debe satisfacerse para que la posibilidad exista.

> Es posible cambiar de rama teniendo modificaciones uncommitted en el working tree si el cambio **no requiere deshacer** dichas modificaciones.

Un cambio de rama conlleva un posible cambio del working tree, pues el working tree muestra el contenido almacenado en el snapshot asociado al commit al que apunta la rama. Pensemos en un ejemplo. Al cambiar de la rama `X` a la rama `Y` las siguientes acciones ocurren, donde `Xs` y `Ys` son los snapshots correspondientes de los commits a los que apuntan las ramas.

1. Para cada archivo en `Ys` y no en `Xs`, crearlo.
2. Para cada archivo en `Xs` y no en `Ys`, eliminarlo.
3. Para cada archivo en ambos snapshots, si la versión en `Ys` es distinta a la versión en `Xs`, actualizar el contenido.

Si en el cambio de rama algunas de estas acciones requiere ocurrir **sobre alguno de los archivos modificados o creados**, entonces Git cancela el checkout y muestra el mensaje de error presente al inicio de la sección. De lo contrario, el checkout es legal. Nótese la condición en la tercera acción; si el archivo existe en ambos snapshots, sólo se muestra la versión del snapshot `Ys` si es distinta a la del snapshot `Xs`, no si es distinta a la versión del working tree. En los siguientes casos siempre es posible un cambio de rama con cambios uncommitted.

- Se crea `rama-m` a partir de `rama-n`. Se trabaja en `rama-m`, modificando archivos y creando nuevos, pero no se realiza un commit. Es legal hacer checkout a `rama-n`. Esto se cumple siempre pues ambas ramas apuntan al mismo commit, que a su vez apunta al mismo snapshot. A pesar que la versión del working tree es distinta al snapshot, las versiones entre los snapshots son iguales, por lo que Git no intenta traer la versión del snapshot de la rama objeto del checkout.
- Se crean archivos cuyos nombres y extensiones no existen en otras ramas, es legal cambiar de rama. Puesto que el archivo está untracked y no existen en otras ramas, un cambio de rama no requiere creación, eliminación ni modificación sobre este archivo.

Ahora dirijamos nuestra atención a casos en los que no es posible un cambio de rama teniendo modificaciones uncommitted.

- Se crea un archivo cuyo nombre y extensión existen en otra rama. En la rama actual el archivo está untracked, pero en la rama objeto del checkout el archivo está tracked. Al intentar el cambio, se requiere la acción 1 (creación) sobre el nuevo archivo; en el snapshot de la rama origen no existe el archivo, pero en el snapshot de la rama destino sí, entonces se crea, pero en el proceso colisiona con el archivo modificado.
- Se modifica un archivo que en otra rama no existe. Es ilegal cambiarse a esa rama con la modificación uncommitted pues se requiere la acción 2 (eliminación) sobre el archivo modificado.

## Eliminar archivos untracked

> Resumen de <https://git-scm.com/docs/git-clean>

Ya sea por un build u otra razón, a veces simplemente se quiere eliminar los archivos no versionados por Git (untracked). Si la variable de configuración `clean.requireForce` no tiene el valor `false`, entonces la bandera `-f` siempre es requerida para ejecutar el comando. Utilice `-d` para recursivamente eliminar directorios untracked. Utilice `-x` para eliminar también archivos ignorados. Si `<paths>` no es proporcionado, limpia el working tree desde el directorio en el que el comando es ejecutado.

```bnf
git clean [-d] [-f] [-x] [<path>]
```

## Ignorar archivos (.gitignore)

> Resumen de <https://git-scm.com/docs/gitignore>

En la sección de [Parte 1: Fundamentos](Parte1_Fundamentos.md) dedicada al ambiente de desarrollo, se mencionan los estados de los archivos de acuerdo a Git: tracked (posiblemente modified o staged) y untracked. En adición, **un archivo puede tener el estado *ignored***. Existen archivos de configuración de IDEs o editores de texto, como `.vscode` de VSCode o `.idea/workspace.xml` de IntelliJ IDEA, los cuales no se desean agregar al repositorio. De la misma intención son objeto los archivos resultantes de un build, como un directorio `target` o los `.class` de Java. Agregando un ejemplo más, las dependencias externas de un proyecto también suelen ser ignoradas, como el directorio `node_modules` de Node.js. En estos casos, dichos archivos se desean dejar permanentemente untracked.

La solución es ignorar los archivos, volviéndolos no elegibles para los comandos de Git. (Algunas banderas de algunos comandos permiten seleccionar archivos ignorados, como `-a` en `git stash push`o `-x` en `git clean`.) Para ignorar archivos se requiere un archivo de configuración `.gitignore`, el cual puede tener impacto global o sólo respecto a un repositorio. Los archivos a ignorar se seleccionan utilizando [expresiones glob](https://en.wikipedia.org/wiki/Glob_(programming)).

---

### Expresiones glob vs regulares

Varios comandos de Git requieren seleccionar archivos, como `git add` o `git stash push`. A pesar que pueda parecer que aceptan expresiones regulares, en realidad lo que aceptan son expresiones glob. Las expresiones glob sirven el propósito de representar archivos mediante wildcards (caracteres especiales), dado un sistema de archivos; mientras que las expresiones regulares representan cadenas de texto, dada una secuencia de caracteres.

Ambas expresiones sirven un propósito distinto aunque similar (y la sintaxis también es similar). Puesto que archivos se desean seleccionar en lugar de frases en un texto, las expresiones regulares no son ideales en este contexto. **Cuando los glob se utilizan en comandos, como `git add` por ejemplo, ponerlos entre comillas para evitar expansión del shell**.

| ✔️ Evitar expansión de shell. Git procesa el glob | ❌ Expansión de shell. El shell expande la ruta y pasa la evaluación a Git. Git no procesa un glob |
|:---:|:---:|
| `git add '**/*.txt'` | `git add **/*.txt` |

---

### Notación de expresiones glob

| Símbolo | Explicación |
|:---:|---|
| * | Representa cualquier número de caracteres, incluido ninguno, pero no una diagonal. |
| ** | Representa uno o más directorios, pero no el directorio actual (`.`) ni el padre (`..`). |
| ? | Representa un carácter. |
| [abc] | Representa cualquier carácter contenido en los corchetes. |
| [a-z] | Representa cualquier carácter contenido en el intervalo definido por los corchetes. Este intervalo es dependiente de la configuración regional. |

### .gitignore

Este archivo define los archivos que Git ignorará. **Archivos tracked no son afectados**. El archivo puede contener comentarios de una línea, los cuales inician con `#`. Para especificar archivos a ignorar, colocar un patrón glob por línea. Si se ignora un directorio, todos sus archivos y subdirectorios también son ignorados. Un repositorio puede tener más de un `.gitignore`, siendo sus patrones glob relativos a la ubicación del archivo.

Git permite definir un `.gitignore` de impacto global (afecta todos los repositorios del usuario con esta configuración). Primero es necesario crear el archivo manualmente, luego se indica su ubicación en la configuración global.

```bnf
git config --global core.excludesFile <ruta-.gitignore>
```

### Ignorar archivos tracked

Para ignorar archivos tracked (añadidos al staging environment o existentes en algún commit) primero deben ser olvidados por Git, es decir, cambiar su estado a untracked. Para conseguir esto se utiliza el comando siguiente, donde `<archivo>` acepta expresiones glob.

```bnf
git rm --cached <archivo>
```

## El confuso archivo `.gitkeep` y los directorios vacíos

Consideremos el siguiente experimento.

```bash
$ git init
Initialized empty Git repository in C:/Users/hjcer/temp/.git/

$ mkdir foo

$ git status
On branch master

No commits yet

# A pesar que el directorio foo existe en el working tree, Git
# no lo reconoce, negando la posibilidad de agregarlo al staging
# area y almacenarlo en un commit.
nothing to commit (create/copy files and use "git add" to track)
```

¿Nota algo extraño? **Git no versiona directorios vacíos**.

<p align="center">
 <img src="images/gitkeep.png" width="800px" />
</p>

> Recuperado de <https://git.wiki.kernel.org/index.php/GitFaq#Can_I_add_empty_directories.3F>

 En algunas situaciones podría resultar deseable almacenar en los commits ciertos directorios vacíos. Existen varias recomendaciones respecto a cómo conseguir esto, siendo una de las más populares crear un archivo con nombre `.gitkeep` en el directorio en cuestión, para que ya no esté vacío y pueda ser registrado por Git. En mi opinión, esta convención es muy confusa y no la recomiendo. `.gitkeep` no es un archivo que Git trate de forma especial, como lo serían por ejemplo [`.gitignore`](#ignorar-archivos-(.gitignore)) o `.gitconfig`, por lo que no tiene documentación oficial su uso; el prefijo `git` sugiere que es un archivo de configuración, cuando en realidad no lo es.

 Para registrar directorios vacíos recomiendo utilizar un `readme.md` explicando la razón de la necesidad de hacer tracking de tal carpeta.

## Correcciones avanzadas

### Reset

> Resumen de <https://www.atlassian.com/git/tutorials/undoing-changes/git-reset>

Este comando es muy interesante, poderoso y directo de comprender si se tiene un claro entendimiento del ambiente de desarrollo de Git, discutido en [Parte 1: Fundamentos](Parte1_Fundamentos.md). Similar a `git checkout`, este comando permite mover la referencia `HEAD` entre commits, mas difiere en tanto que no sólo actúa respecto a `HEAD`, sino que también respecto a una rama. Por ejemplo, considere el siguiente árbol de commits.

<p align="center">
 <img src="images/reset_1.png" width="250px" />
</p>

Se observa que `git checkout` mueve `HEAD`, pero `git reset` también mueve a la rama apuntada por `HEAD`.

<p align="center">
 <img src="images/reset_2.png" width="550px" />
</p>

Existen tres modalidades de reseteos, las cuales son elegibles por las banderas `--soft`, `--mixed` y `--hard`. Observe que `--mixed` es utilizada por defecto si ninguna bandera es seleccionada. **Las tres modalidades tienen en común que mueven `HEAD` y la rama apuntada por `HEAD` al commit seleccionado**. Los modos difieren en lo que restauran (sobre qué tiene efecto el reset), siendo los objetivos de restauración el working tree y el staging area. En cuanto al staging area, restaurar alude a retirar los archivos del estado staged, mas los cambios se mantienen en el working tree. Respecto al working tree, restaurar significa actualizar el mismo respecto al snapshot del commit seleccionado.

| Modalidad | Objetivo de restauración |
|---|---|
| `--soft` | (Ni el staging area ni el working tree son restaurados, sólo `HEAD` y la rama apuntada por `HEAD` son movidos.)<br><br>*Las modificaciones de los commits descendientes del commit seleccionado son **colocadas en el staging area** (evidentemente, son visibles también en el working tree).* |
| `--mixed`<br><br>*modo predeterminado* | Staging area<br><br>*Las modificaciones de los commits descendientes del commit seleccionado son **colocadas en el working tree** (unstaged).* |
|`--hard`| Staging area y working tree<br><br>⚠️ ***Las modificaciones de los commits descendientes del commit seleccionado son ELIMINADAS***. |

#### Uso del comando

Retira del staging area los archivos especificados (1). Si alguno de los archivos inicia con un guión alto (`-`), añadir dos guiones altos antes del nombre del archivo (esto evita que Git intente procesar el archivo como una bandera). Para el reset discutido anteriormente, utilice (2). La sintaxis completa puede encontrarla en <https://git-scm.com/docs/git-reset>.

```bnf
git reset [--] <archivos>                       (1)
git reset [--soft | --mixed | --hard] <commit>  (2)
```

Para demostrar el uso del comando considere el siguiente repositorio.

```shell
$ git log --oneline
ca4ae71 (HEAD -> master) Add a body to foo.txt
edea02a Add title to foo.txt
d96dc66 Add file foo.txt
35554aa Create README.md
```

---

**Ejemplo 1. Mover últimos commits de `master` a nueva rama `feature/foo`**

```shell
$ git branch feature/foo

$ git reset --hard HEAD~3
HEAD is now at 35554aa Create README.md
```

Ahora sólo el primer commit (crear un `README.md`) pertenece a `master`. Porque una imagen vale mil palabras, a continuación se explican los comandos con diagramas.

<p align="center">
  <img src="images/reset_4.png" width="450px">
</p>

---

---

**Ejemplo 1.1. Compactar commits de `feature/foo` en tan sólo uno**

Veamos que los commits de `feature/foo` cumplen un sólo propósito: crear `foo.txt` con el contenido deseado. Digamos que en este proyecto tiene más sentido tener un sólo commit para contener estos cambios.

```shell
$ git checkout feature/foo
Switched to branch 'feature/foo'

$ git reset --soft HEAD~2

$ git commit --amend -m "Create foo.txt"
[feature/foo 3cbc27e] Create foo.txt
 Date: Thu Dec 26 09:44:59 2019 -0600
 1 file changed, 3 insertions(+)
 create mode 100644 foo.txt
```

<p align="center">
  <img src="images/reset_5.png" width="450px">
</p>

---

#### Recuperación de un `git reset --hard` equivocado

Al realizar un reset duro, los commits descendientes del seleccionado se vuelven inaccesibles mediante `git log` y su contenido es eliminado del working tree y staging area. En la imagen inferior podemos notar que el commit 3 no es listado tras el reset duro.

<p align="center">
 <img src="images/reset_3.png" width="700px" />
</p>

Esto no significa que el commit 3 sea inaccesible, tan sólo que recorriendo el árbol de commits a partir de `HEAD` (o cualquier `head`) no es posible llegar a él. El commit no ha sido eliminado. Para recuperase de este reset basta con hallar el hash SHA-1 del commit al que deseamos regresar y ejecutar un reset duro respecto al mismo. Para hallar el hash, se utiliza `git reflog`, que lista el historial de commits que ha visitado `HEAD`. Para el ejemplo presentado la solución es dada por `git reset --hard 3`.

### Revert

> Resumen de <https://www.atlassian.com/git/tutorials/undoing-changes/git-revert>

Al igual que `git reset`, `git revert` permite eliminar cambios introducidos por uno o más commits. La distinción más importante radica en que este comando no modifica la historia, realizando la corrección no al eliminar commits, pero al agregar uno con las correcciones.

<p align="center">
 <img src="images/revert_1.png" width="600px" />
</p>

#### ¿Cuándo utilizar `git revert` sobre `git reset`?

Revert permite deshacer los cambios introducidos por commits selectos (incluso commits no secuenciales o muy atrás en la historia), mientras que reset sólo puede deshacer hacia atrás a partir de la punta de una rama. Revert siempre es seguro, pues no altera la la historia del repositorio, haciendo imposible reescribir la historia un repo remoto mediante revert. Por otra lado, reset sí puede reescribir la historia de un repo remoto si es utilizado incorrectamente. **Sólo utilizar `git reset` sobre commits que aún no han sido publicados (push)**. A pesar de estas desventajas, recomiendo utilizar reset en los casos que es posible, ilustrado por el diagrama inferior, pues evita el commit extra de corrección.

<p align="center">
 <img src="images/revert_2.png" width="400px" />
</p>

#### Uso del comando

Como es usual, aquí se presenta una sintaxis simplificada respecto a las banderas y opciones más comunes. Para la sintaxis completa refiérase a <https://git-scm.com/docs/git-revert>. Crea un nuevo commit con la corrección, abriendo el editor de texto especificado en `core.editor` para ingresar el mensaje del commit. Utilizar `--no-edit` para no abrir el editor de texto y usar el mensaje predeterminado. Al utilizar la bandera `-n`, en lugar de directamente crear un commit, las modificaciones son realizadas en el working tree y colocadas en el staging area.

```bnf
git revert [--no-edit] [-n] <commit>
```

Para demostrar distintas correcciones utilizando este comando, considere este repositorio.

```shell
$ git log --oneline
dc01f42 (HEAD -> master) Add content to file_2
386a54a Create file_2
e3646e8 Add content to file_1
98a0ead Create file_1
```

---

**Ejemplo 1. Eliminar modificaciones del último commit**

```bash
$ git revert HEAD
[master f5544d9] Revert "Add content to file_2"
 1 file changed, 1 deletion(-)

$ git log --oneline
f5544d9 (HEAD -> master) Revert "Add content to file_2" # Commit con la corrección
dc01f42 Add content to file_2
386a54a Create file_2
e3646e8 Add content to file_1
98a0ead Create file_1
```

Este comando abre el editor de texto para pedir el mensaje del commit de corrección. (Si prefiere evitar el editor de texto y aceptar el mensaje predeterminado, use `git revert --no-edit HEAD`.) Tras salir del editor, puede verificar que un nuevo commit ha sido creado. Esta corrección pudo haberse realizado mediante un reset duro, pero al emplear un revert se evita reescribir la historia, lo cual casi nunca no es recomendable hacer en los commits de una rama pública (rama publicada a un repo remoto).

---

---

**Ejemplo 2. Eliminar modificaciones de commits no secuenciales**

```bash
# Recuerde que `git revert` revierte sólo los cambios del commit especificado.
# Intentar revertir el primer commit (creación de file_1) entra en conflicto
# con el segundo commit (HEAD~2), pues éste agrega contenido al archivo que
# se intenta eliminar.
$ git revert HEAD~3
error: could not revert 98a0ead... Create file_1
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'

# Resolver el conflicto eliminando el archivo manualmente.
$ git rm file_1.txt
rm 'file_1.txt'

# Continuar el revert.
$ git revert --continue
[master 98b6774] Revert "Create file_1"
 1 file changed, 1 deletion(-)
 delete mode 100644 file_1.txt

# Revertir añadir contenido a file_2 no entra en conflicto con las
# modificaciones del commit siguiente, entonces el proceso de revert
# es completamente automático y directo.
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

## Reescribiendo la historia

**Al colaborar en un repositorio visto y utilizado por otras personas, existe una regla de oro: no reescribir la historia pública.**

Más que una regla, presento esto como una fuerte recomendación, pues es posible que los colaboradores acuerden reescribir parte de la historia y se haga de forma segura, pero lograr esto puede resultar complejo, muy confuso e innecesario. En general, se reescribe la historia pública por equivocación y no por una decisión meditada.

### ¿Qué es *reescribir la historia*?

Primero hay que recordar que la historia de Git se compone por el árbol de commits, donde cada commit es identificado por un hash SHA-1. Cualquier sustitución o eliminación a esta secuencia de hashes se considera reescribir la historia.

> **Se dice que la historia es reescrita si la secuencia de hashes en el árbol de commits cambia por acción de sustitución (`git commit --amend` ó `git rebase`) o eliminación ([`git reset`](#reset) ó `git rebase`). Agregar commits secuenciales (`git commit`) no reescribe la historia, pues no altera el pasado.**

Veamos un ejemplo sencillo utilizando `git commit --amend`.

```bash
$ git log --oneline -a --graph
 # Observe que el hash del commit al que apunta HEAD es 1c0e104.
*   1c0e104 (HEAD -> master, origin/master) Merge branch 'feature'
|\
| * 1c27aea (feature) Add more content to f1
| * 5a4b4dd Add content to f2
* | 494804a Create f3
|/
* a9e3559 Create f2
* 44b6f5d Modify f1
* d04b4ee Start version control

$ git commit --amend --no-edit
[master 5b9a0ca] Merge branch 'feature'
 Date: Sun Dec 22 12:20:44 2019 -0600

$ git log --oneline -a --graph
 # El hash ha cambiado de 1c0e104 a 5b9a0ca,
 # no se trata del mismo objeto commit.
*   5b9a0ca (HEAD -> master, origin/master) Merge branch 'feature'
|\
| * 1c27aea (feature) Add more content to f1
| * 5a4b4dd Add content to f2
* | 494804a Create f3
|/
* a9e3559 Create f2
* 44b6f5d Modify f1
* d04b4ee Start version control
```

¿Por qué es importante que los hashes cambien si su contenido es el deseado (y en este ejemplo incluso es el mismo)? Esto sólo es importante en el contexto de repositorios remotos y colaboradores. Considere a un colaborador que trabajaba con la versión antigua y ejecuta un `git fetch origin` para revisar los cambios.

```bash
$ git fetch origin
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 2 (delta 0), reused 2 (delta 0), pack-reused 0
Unpacking objects: 100% (2/2), done.
From https://github.com/HerCerM/RewritingHistory
 + 1c0e104...5b9a0ca master     -> origin/master  (forced update)

$ git log --oneline -a --graph
    # Después del amend
*   5b9a0ca (origin/master, origin/HEAD) Merge branch 'feature'
|\      # Antes del amend
| | *   1c0e104 (HEAD -> master) Merge branch 'feature'
| | |\
| |/ /
|/| /
| |/
| * 1c27aea Add more content to f1
| * 5a4b4dd Add content to f2
* | 494804a Create f3
|/
* a9e3559 Create f2
* 44b6f5d Modify f1
* d04b4ee Start version control
```

De pronto `origin/master` y `master` apuntan a commits distintos a pesar que la versión de los archivos entre ellos es la misma. Esta divergencia, quizá no anticipada, puede ocasionar problemas y hacer la historia de commits confusa.

### ¿Qué es la *historia pública*?

Todo commit que exista en un repositorio remoto con colaboradores es parte de la historia pública. La regla de oro dice *no reescribir la historia **pública***, pues no existe ningún peligro en reescribir la historia local. Mientras los commits no hayan sido publicados (`git push`) a un repositorio remoto, siéntase libre de sustituirlos o eliminarlos utilizando `git commit --amend`, `git reset` ó `git rebase`. Por esta razón es una excelente práctica sólo hacer `git push` de sus commits cuando esté totalmente satisfecho con ellos, pues una vez publicados es mejor considerarlos escritos en piedra.

### ¿Cómo se corrigen commits públicos?

A pesar que es posible reescribir la historia de forma segura si los colaboradores lo pueden manejar, es recomendable no hacerlo. En lugar de reescribir la historia, considere utilizar [`git revert`](#revert) para añadir commits con las correcciones.
