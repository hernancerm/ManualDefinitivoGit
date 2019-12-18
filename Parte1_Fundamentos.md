# Fundamentos

- [¿Qué es Git?](#¿qué-es-git)
  - [Ventajas de los sistemas de control de versiones (VCS)](#ventajas-de-los-sistemas-de-control-de-versiones-vcs)
  - [¿Por qué Gity no otro VCS?](#¿por-qué-gity-no-otro-vcs)
- [Instalación](#instalación)
- [Configuración](#configuración)
- [Ambiente de desarrollo](#ambiente-de-desarrollo)
- [Comandos básicos para la gestión de un repositorio](#comandos-básicos-para-la-gestión-de-un-repositorio)
- [Correcciones básicas](#correcciones-básicas)
- [Concepto de rama](#concepto-de-rama)
  - [Objetos de Git](#objetos-de-git)
- [Comandos básicos para ramas](#comandos-básicos-para-ramas)
- [Fusión de ramas](#fusión-de-ramas)
  - [Fast-formard merge](#fast-formard-merge)
  - [Recursive merge](#recursive-merge)
  - [Comandos básicos para merges](#comandos-básicos-para-merges)
- [Conflictos al realizar un merge](#conflictos-al-realizar-un-merge)
- [Repositorios remotos](#repositorios-remotos)
  - [Acceso a repositorios remotos](#acceso-a-repositorios-remotos)
  - [Operaciones de lectura y escritura (fetch, pull y push)](#operaciones-de-lectura-y-escritura-fetch-pull-y-push)
  - [Obtener información detallada de un repositorio remoto](#obtener-información-detallada-de-un-repositorio-remoto)
  - [Configurar upstreams](#configurar-upstreams)

## ¿Qué es Git?

> Resumen de <https://www.atlassian.com/git/tutorials/what-is-git>

Git es un VCS open source originalmente desarrollado en 2005 por Linus Torvalds, el creador del kernel de Linux, quien se encontraba muy insatisfecho con las herramientas de mantenimiento de código contemporáneas, entre las cuales CVS era la más popular. En particular, Git es un VCS distribuido (DVCS), lo cual significa que en lugar de destinar un lugar único para almacenar la historia de un proyecto, como sus antecesores CVS o Subversion (SVN), cada desarrollador tiene una copia de la historia completa de los cambios de cada archivo.

### Ventajas de los sistemas de control de versiones (VCS)

> Resumen de <https://www.atlassian.com/git/tutorials/what-is-version-control>

Cabe destacar que Git no es la única herramienta para el control de versiones, mas Git supera a las demás opciones en integraciones y características. Sin importar qué VCS se utilice, siempre se pueden esperar las siguientes ventajas.

- Prevención de cambios conflictivos sobre un conjunto de archivos.
- Historial completo y detallado del proyecto. Esto abarca todos los cambios realizados por todos los contribuidores, incluyendo modificaciones, eliminación y creación de archivos, así como su movimiento y cambio de nombre. El beneficio más aparente de esto radica en su cualidad de respaldo, pues bajo el control de un VCS, ninguna modificación resulta fatal, siempre se puede regresar a alguna versión anterior. Más aún, la identificación de la causa raíz de algun bug puede ser rastreada con mucha más facilidad.
- Ramas y su fusión. Un VCS provee ramificaciones del proyecto, es decir, versiones aisladas en las cuales algún desarrollador puede trabajar. Terminada su modificación, puede incorporar estos cambios a la rama principal, fusionando sus cambios y verificando si existen conflictos. El VCS informa de todos los conflictos y queda a responsabilidad de programador resolverlos para poder concluir el proceso de fusión.

### ¿Por qué Gity no otro VCS?

> Resumen de <https://www.atlassian.com/git/tutorials/what-is-git>

Git es un sistema maduro y open source al que activamente se le proporciona mantenimiento. El sistema está altamente opimizado para tener el mejor rendimiento, resultando en un programa con tiempos de espera muy cortos y, a menudo, nulos. Incluso en la naturaleza distribuida de Git pueden encontrarse beneficios en rendimiento. Cada programador tiene una copia entera de la historia del proyecto, almacenada en la memoria local de su máquina, evitando comunicaciones de red con cada acción a realizar respecto al VCS (en sistema de control de versiones CVS, por ejemplo, la mayoría de las operaciones sufren de latencia de red). Esto no significa que no pueda existir un lugar centralizado y remoto para almacenar el proyecto; es para esta común necesidad que GitHub y otros sistemas de almacenamiento remoto para Git fueron creado (véanse las últimas secciones de este manual para una explicación de GitHub). Entonces con Git, un desarrollador trabaja con el proyecto de forma local, al completar sus tareas y desear compartir su colaboración, realiza un push de sus cambios al repositorio remoto y es sólo en ese momento que requiere de conexión a una red.

Uno de los principales objetivos de Git es la flexibilidad. La herramienta puede utilizarse en una gran variedad de SOs, incluyendo Windows, MacOS, Linux, FreeBSD y otros; y para gestionar proyectos de cualquier tipo, con cualquier tipo de archivo basados en caracteres. Más importante aún, Git no impone una modalidad específica de desarrollo, sino los desarrolladores establecen su propio estándar de cómo usar la herramienta y cómo acoplarla a su estilo de trabajo. Dado que la mayoría de las operaciones de Git son locales, merced su naturaleza distribuida, no existe una fuerte dependencia respecto a internet para poder trabajar.

Git es, por mucho, el sistema de control de versiones estándar. Git ha sido ampliamente adoptado, lo que implica que tiene soporte activo, existe mucho material para ser aprendido, tiene fuerte integración con muchas herramientas, como IDEs o editores de texto (p. ej.: NetBeans, IntelliJ, VSCode), sistemas de ticketing (p. ej.: Jira), etc.

## Instalación

| Sistema operativo                      | Proceso de instalación                                                                         |
|----------------------------------------|------------------------------------------------------------------------------------------------|
| (Linux) Fedora                         | `sudo dnf install git-all`                                                                     |
| (Linux) Basados en Debian, como Ubuntu | `sudo apt install git-all`                                                                     |
| MacOS                                  | Instalador: <https://git-scm.com/download/mac><br>Mavericks (10.9) o superior: `git --version` |
| Windows                                | Instalador: <https://git-scm.com/download/win><br>Chocolatey: `choco install git`              |

## Configuración

> Resumen de <https://git-scm.com/book/en/v1/Getting-Started-First-Time-Git-Setup>

Git trae una herramienta llamada git config que permite cambiar variables de configuración acerca de cómo Git luce y funciona. La configuración sólo requiere realizarse una vez, mas en cualquier momento los valores pueden ser modificados si así se desea.

La configuración global para un usuario es almacenada en `~/.gitconfig`, en Linux, y `%USERPROFILE%\.gitconfig`, en Windows. Alternativamente, mediante el archivo `.git/config` se puede ignorar la configuración global e imponer una para sólo el repositorio en cuestión.

Para visualizar la configuración global a un usuario de Git, se utiliza el siguiente comando. Si acaba de instalar Git, es posible que este comando no reporte nada; realice las siguientes configuraciones antes.

```bnf
git config --global --list
```

Para visualizar toda la configuración disponible (incluso en `.git`), ingresar:

```bnf
git config --list
```

Para cambiar el valor de cualquier variable global de configuración se usa la sintaxis siguiente. Nótese que si el valor contiene algún espacio en blanco, es necesario encerrarlo en comillas dobles.

```bnf
git config --global <variable> <valor>
```

La configuración necesaria antes de poder usar Git comprende el nombre y correo electrónico, pues todos los commits utilizan esta información. Ejemplo de configuración:

```bnf
git config --global user.name "Hernán Cervera"
git config --global user.email hernancervera@example.com
```

El editor de texto predeterminado suele ser Vi o Vim, pero puede utilizarse otro, por ejemplo, nano:

```bnf
git config --global core.editor nano
```

## Ambiente de desarrollo

> Resumen de <https://git-scm.com/book/en/v1/Getting-Started-Git-Basics>

Antes de abordar los comandos de Git, es muy importante conocer los fundamentos de Git. Puedo corroborar esto haciendo alusión a mis primeras experiencia con la herramienta, durante las cuales el desconocimiento de los fundamentos causaron más frustración de la necesaria. Al trabajar con Git es muy importante conocer el ambiente de desarrollo, el cual es componente central del flujo de trabajo con esta herramienta. Los archivos son visualizados por Git, en momentos distingos, en tres áreas: el working tree, staging area y local repository.

---

![Ambiente de desarrollo](/images/dev_env.png "Ambiente de desarrollo")

- El **local repository**, también conocido como directorio de Git o repositorio, almacena metadatos y los estados de los archivos, actuando efectivamente como una base de datos para la gestión del proyecto. El nombre de directorio de Git está fundamentado en que este repositorio local efectivamente se almacena en un directorio, usualmente oculto, con el nombre `.git`.
- El **working tree** abarca todos los archivos contenidos en el directorio que contiene a `.git` y sus subdirectorios. Representa todos los posibles archivos que Git puede versionar.
- El **staging area** define qué archivos y en qué estado serán persistidos para la siguiente versión del proyecto. Físicamente, el staging area es el archivo `.git/index`.

---

El flujo de trabajo usual con Git es como sigue:

1. El desarrollador modifica o crea archivos en el working tree.
2. Al estar satisfecho con las modificaciones, se añaden archivos selectos al staging area, definiendo así el contenido que esos archivos del proyecto tendrán en la siguiente versión.
3. Se realiza un commit, el cual toma lo añadido en el staging area y genera una versión actualizada del proyecto, la cual es mostrada en el working tree y almacenada en el repositorio local.

En particular, un archivo se considera tracked si existe una versión registrada de éste en el repositorio local, lo cual ocurre al realizar un commit o añadirlo al staging area. Un archivo **tracked** podría ser marcado por Git como **modified** o **staged**. Si un archivo es añadido al staging area, entonces el archivo está staged. Si un archivo tracked ha sido modificado, pero no añadido al staging area, está modified. Los archivos que no han formado parte de algún commit y tampoco han sido añadidos al staging area se dice que están **untracked**. Todo archivo nuevo empieza con el status de untracked.

## Comandos básicos para la gestión de un repositorio

> Para algunos de estos comandos se presentan también banderas u argumentos que me han resultado útiles, mas cabe mencionar que existen muchas más opciones; algunas se pueden combiar, otras no. Git tiene integrado un sistema de ayuda; para cada comando se puede solicitar su documentación mediante las siguientes dos opciones de sintaxis.
>
>```bnf
> git <comando> --help  (1)
> git help <comando>    (2)
>```

Crear un respositorio local.

```bnf
git init
```

Muestra archivos modified y staged. Sólo cuando existen archivos en el staging area se puede realizar un commit. Para ver una versión resumida del estado, úsese la bandera `-s` o `--short` (2).

```bnf
git status     (1)
git status -s  (2)
```

Agrega uno o varios archivos al staging area. Como muchos de los comandos de Git que seleccionan archivos, acepta [patrones glob](https://en.wikipedia.org/wiki/Glob_(programming)). Por ejemplo, el comando (1) añade un archivo específico al staging area; si como valor de `<archivo>` se utiliza un punto (`.`), que denota el directorio actual, todos los archivos modificados o untracked se añaden al index. Al utilizar glob, es preferible siempre ponerlo entre comillas simples para evitar la expasnsión del shell. En ocasiones se modifican archivos que ya habían sido añadidos al staging area, para actualizar el index con los cambios del working tree utilizar (2), el cual no añade archivos nuevos. Si desea agregar todos los archivos nuevos, eliminados o modificados utilice (3). Para retirar todos los archivos del staing area (pero preservar los cambios en el working tree), utilizar (4). En secciones avanzadas se disctue con detalle el comando git reset.

```bnf
git add <archivo>  (1)
git add -u         (2)
git add -a         (3)
git reset          (4)
```

Realiza un commit. La redacción del mensaje puede hacer de Git una herramienta mucho más o menos útil. Guía para redactar mensajes relevantes: <https://chris.beams.io/posts/git-commit/>. En caso que se desee redactar el mensaje incluyendo un cuerpo, úsese el comando (2). Esto abrirá el editor de texto especificado en la configuración core.editor para redactar el mensaje. Al terminar, guardar y cerrar el editor.  ¿Cuándo realizar un commit? <https://jasonmccreary.me/articles/when-to-make-git-commit/>. La bandera `-a` incluye al staging area archivos modified (tracked modificados, ignora los untracked) e inicia el proceso de realizar un commit.

```bnf
git commit -m "<mensaje del commit>"  (1)
git commit                            (2)
git commit -a                         (3)
```

Recorre el árbol de commits desde la posición de `HEAD`. El comando sin banderas (1) muestra autor, fecha y hora de commit,  encabezado y cuerpo del mensaje, rama del commit y hash SHA-1 completo. El comando con la bandera `--oneline` (3) muestra una versión más compacta, sólo desplegando el encabezado del mensaje, los primeros 7 caracteres del hash del commit y la rama en la que fue realizado. Si se desean ver todos los commits del repositorio (todos los commits accesibles mediante alguna rama) en lugar a sólo los accesibles a través de `HEAD`, utilice la bandera `-a` (2).

```bnf
git log            (1)
git log -a         (2)
git log --oneline  (3)
```

Lista cronológica inversa (se muestra primero lo más reciente) de los objetos a los que `HEAD` ha apuntado. Este comando imprime un log de las referencias. A diferencia del comando log, reflog puede mostrar commits que no son accesibles mediante una rama, pues muestra un historial en lugar de recorrer el árbol.
Véase: <https://stackoverflow.com/questions/17857723/whats-the-difference-between-git-reflog-and-log>

```bnf
git reflog
```

## Correcciones básicas

Revierte los cambios de archivos especificados a su estado representado en el snapshot del commit al que apunta `HEAD`. Si el nombre del archivo inicia con un guión (`-`), usar el comando (2).

```bnf
git checkout <archivo>    (1)
git checkout -- <archivo> (2)
```

Un error usual es olvidar añadir archivos a un commit o redactar mal el mensaje del commit. Si este comando (1) se ejecuta con el staging area vacío, entonces el editor de texto especificado en `core.editor` muestra el mensaje del commit pasado y permite modificarlo. Por otro lado, si el staging area contiene cambios, al commit pasado se le añaden estos cambios, al igual que resulta posible modificar el mensaje del commit. En caso que no se desea modificar el mensaje del commit, utilizar (2).

```bnf
git commit --amend                     (1)
git commit --amend --no-edit           (2)
```

Retirar un archivo del staging area. Otro error usual es añadir al staging area algún archivo que no se desea agregar al siguiente commit.

```bnf
git reset `HEAD` <archivo>
```

Renombrar un archivo y comunicarle a Git de este tipo de cambio.

```bnf
git mv <nombre-antiguo> <nombre-nuevo>
```

Retira al archivo del repositorio, pero lo mantiene en el working tree. Esto es muy útil cuando se desea ignorar un archivo o directorio que tiene el estado commited.

```bnf
git rm --cached <archivo>
```

## Concepto de rama

> Resumen de <https://git-scm.com/book/en/v1/Git-Branching-What-a-Branch-Is>

Para poder tener un sólido entendimiento de las ramas de Git, es necesario explorar con más detalle el funcionamiento de bajo nivel de Git en cuanto a cómo almacena las distintas versiones de un proyecto. Entendido esto, el concepto de rama es tan sólo una extensión del sistema. Esta sección acaso sea una de las más difíciles de entender del manual, mas su compresión lo vale en absoluto.

---

### Objetos de Git

![Commit desglosado](/images/branches_1.png)

Imagínese el inicio de un repositorio simple en el que se guardarán archivos batch (`.bat`). En este repositorio, actualmente sólo existe un archivo batch, `user.bat`, y otro archivo, `README.md`, describiendo el contenido general del repositorio.

Durante esta sección, la carpeta objects hace referencia a los contenidos de `.git/objects`. Recuérdese que .git está oculto por defecto, pues sus contenidos no deben modificarse directamente, mas para fines de estudio puede resultar provechoso inspeccionar los archivos.

Comencemos el análisis desde la ejecución del comando `git init`. Tras correr este comando, un nuevo repositorio de Git es creado. Examinando el directorio objects, podemos comprobar que sólo las carpetas pack e info existen; para esta sección, no resulta relevante conocer su propósito. Ahora, imaginemos que el desarrollador codifica su primer archivo batch: `user.bat`. Luego, redacta una breve descripción del repositorio: `README.md`. Hasta ahora, esos archivos sólo existen en el working tree.

El desarrollador desea realizar un commit con su progreso. Para lograr esto, añade ambos archivos al staging area. Si en este momento se inspecciona objects, puede comprobarse que dos directorios han aparecido: 5b y 91 (los nombres de estos directorios concuerdan con la figura del inicio de esta sección, mas si usted realiza el experimento muy probablemente los nombres sean distintos, pero es seguro que serán dos directorios). ¿Qué significa esto? Significa que Git ha registrado el estado de los archivos `README.md` y `user.bat` en objetos blob.

Ahora el desarrollador ejecuta el comando `git commit -m "Start of VC"`, con lo cual se genera un objeto tree, el cual apunta a los blobs, y un objeto commit, el cual apunta al tree. Esto se ilustra en la figura del inicio de la sección presente. En este punto, si se revisa objects, dos objetos más habrán aparecido; hasta ahora Git ha registrado cuatro objetos: dos blobs, un tree y un commit.

| Objeto | Función |
|--------|---------|
|blob    | Representa los contenidos de un archivo que no es directorio. |
|tree    | Almacena los nombres de los archivos de un directorio, al igual que un apuntador al blob o tree correspondiente que guarda el contenido de los archivos. |
|commit  | Apunta a un tree que representa el staging area en un momento determinado; almacena el nombre del autor, el commiter y el mensaje del commit. |

**Todos los objetos son identificados por Git mediante un hash SHA-1**. Aquí es importante recordar que SHA-1 produce códigos de 40 caracteres de longitud (SHA-1 produce valores hash de 160 bits, en hexadecimal). Entonces, los identificadores incompletos sobre los objetos de la figura son hashes SHA-1, etiqueta que Git les asigna para poder identificarlos y referenciarlos. A continuación, se presenta un término más del vocabulario de Git.

> **Snapshot**. Tree que representa el staging area asociado a un commit.

Ahora bien, continuemos el ejemplo. El programador continua trabajando en su repositorio, añadiendo más archivos batch y actualizando su `README.md` y acaso algunos archivos antiguos. También puede que elimine algunos scripts. Tras dos commits más, se tiene una jerarquía oredenada de objetos commit, tree y blob. Esta jerarquía puede ser representada como una secuencia de commits que apuntan a sus snapshots (trees que representan el staging area al momento del commit) correspondientes.

![Commits y sus snapshots](/images/branches_2.png)

Entendido este sistema, podemos ahora comprender con formalidad qué es una rama en Git.

> **Rama**. Referencia a un objeto commit.

Al igual que todas las referencias en Git, esta referencia es un hash SHA-1 que identifica a un commit. Al crearse nuevos commits, la referencia avanza al último commit realizado, por lo que se dice que éstas referencias pueden moverse (son movable). También son ligeras (lightweight) pues se requiere de poco más de 160 bits para ser almacenadas. **Los programadores suelen usar la palabra rama; Git se refiere a estos apuntadores con el término `head`**. Esto puede comprobarse al inspeccionar `.git/refs/heads`.

Por defecto, Git proporciona la rama `master` como inicial. Completando la figura anterior y omitiendo el contenido de los commits por brevedad, tenemos que la imagen completa de los objetos es la mostrada próximamente.

![Commit desglosado](/images/branches_3.png)

**Al crear una nueva rama, la nueva rama apunta al commit que es apuntado durante su creación**. Luego, en el ejemplo, si el desarrollador crea una rama `buf-fix`, ésta apunta al commit identificado por el hash SHA-1 iniciando con f30ab. Si el desarrollador se cambia a esta rama, arregla el bug, añade los archivos relevantes al staging area y realiza un commit, podemos notar algo muy interesante.

![Commit desglosado con rama buf-fix](/images/branches_4.png)

Podemos observar que la referencia (rama) `buf-fix` avanzó al último commit tras ser realizado, mas el commit al que master apunta es el mismo. Ahora cabe preguntar, ¿cómo Git sabe en qué rama un usuario está? La respuesta yace en otro apuntador, también de la forma de un hash SHA-1 al que Git denomina `HEAD` (almacenado en `.git/HEAD`). Completando la figura anterior, puede verse que Git conoce que el usuario está trabajando en la rama `buf-fix` pues `HEAD` apunta a `buf-fix`, que a su vez apunta al último commit realizado por el usuario.

![Commit desglosado con rama buf-fix y HEAD](/images/branches_5.png)

---

Las ramas son una parte fundamental de Git. A diferencia de otros sistemas de control de versiones, como CVS, el proceso de creación de ramas en Git es rápido, pues implica tan sólo cambiar una referencia de 160 bits. **Las ramas son el mecanismo principal por el cual se organiza la colaboración en proyectos**.

## Comandos básicos para ramas

Muestra todas las ramas del repositorio local (1). Muestra todas las ramas del repositorio local y los remotos asociados (2). La bandera `-a` es breve para denotar all. La distinción entre ramas locales y remotas es examinada en otra sección del manual.

```bnf
git branch     (1)
git branch -a  (2)
```

Muestra una descripción completa por rama del repositorio local (utilizar también `-a` para describir todas las ramas). La bandera `-vv` es breve para denotar verbose; puede utilizarse `--verbose` en su lugar. La descripción por rama incluye su nombre, hash SHA-1 que las identifica, el encabezado del mensaje del commit al que apuntan y, en caso de existir, la rama upstream asociada.

```bnf
git branch -vv
```

Crea una rama. Nótese que al crear una rama no se cambia automáticamente a la misma, vea el siguiente comando para cambiarse a una rama existente.

```bnf
git branch <rama>
```

Cambiar a una rama existente.

```bnf
git checkout <rama>
```

Crear una rama y cambirase a ella con un sólo comando.

```bnf
git checkout -b <rama>
```

## Fusión de ramas

> Resumen de <https://git-scm.com/book/en/v1/Git-Branching-Basic-Branching-and-Merging>

Si las ramas son el mecanismo principal por el cual se organiza la colaboración entre proyectos, debe existir una manera por la cual los cambios de cada rama se integren, obteniendo una versión unificada de todas las colaboraciones. A través de un ejemplo que asemeja una situación que podría presentarse en la realidad, veamos cómo la fusión de ramas ocurre. En esta sección se presentan los dos tipos de merge que existen en Git: (1) **fast-forward** y (2) **recursive**. En adición al merge existe el comando rebase para integrar cambios entre ramas, mas su uso es más avanzado y por lo tanto, reservado para secciones más avanzadas del manual.

Imagínese que un desarrollador, llamado Juan, trabaja en un proyecto aún temprano en etapa de desarrollo; sólo se han realizado tres comits.

![Start of commit tree](/images/merge_1.png)

Ahora Juan decide dedicarse a implementar una característica nueva, para lo cual crea la rama `feature`, se cambia a ésta y comienza a trabajar. Realiza un commit.

![New commit on new branch feature](/images/merge_2.png)

En este momento Juan es informado que existe un bug en `master` cuyo patch debe ser priorizado sobre la característica que está implementando. Para realizar la corrección del bug, Juan crea la nueva rama `bug-fix` a partir de `master`. Es decir, el desarrollador requirió ejecutar los siguientes comandos justo después de haber realizado su commit:

```shell
$ git checkout master
$ git branch bug-fix
$ git checkout bug-fix
```

En esta rama el desarrollador soluciona el bug con un único commit. Nótese que el progreso que Juan llevaba en la rama `feature` no existe en la rama `master`, pues se encuentra atrás en la historia. **Generalizando, al cambiar de rama, el working tree muestra sólo los contenidos del snapshot del commit de la rama**. Ahora la relación de los commits luce como lo indica la figura siguiente.

![New commit on new branch bug-fix](/images/merge_3.png)

### Fast-formard merge

Juan ahora debe integrar su solución del bug a `master`. Es decir, `bug-fix` debe ser fusionada en `master`. Para incorporar los cambios de `bug-fix` a `master`, Juan usa los siguientes comandos:

```shell
$ git checkout master
$ git merge bug-fix
Updating f42c576..3a0874c
Fast-forward
 App.java | 1 -
 1 file changed, 1 deletion(-)
```

La estrategia utilizada para el merge es fast-forward pues basta con adelantar la referencia `master` al commit 5 para incorporar los cambios de la rama `bug-fix`. Se elimina la rama `bug-fix` utilizando el commando `git branch -d bug-fix` pues no se necesita más y ahora las ramas y commits tienen la siguiente estructura y estado.

![Fast-forward merge buf-fix into master](/images/merge_4.png)

Juan ya puede continuar trabajando en la característica que estaba implementando en `feature`, para lo cual se cambia a esta rama y logra terminar la implementación en un commit más, como se muestra a continuación.

![Checkout feature and make one commit](/images/merge_5.png)

### Recursive merge

Puesto que la implementación de la nueva característica está terminada, Juan desea incorporar los cambios de `feature` en `master`. Puede notarse que el commit 6 no es descendiente directo del commit 5, por lo que un merge fast-forward no es posible. En estos casos Git utiliza la estrategia recursiva. Al fusionar `feature` en `master` con los siguientes comandos vemos el siguiente mensaje:

```shell
$ git checkout master
$ git merge feature
Merge made by the 'recursive' strategy.
 Reader.java | 3 +++
 1 file changed, 3 insertions(+)
 create mode 100644 Reader.java
```

**En este caso, Git utiliza tres snapshots para realizar la fusión de contenidos y genera un nuevo commit (asociado a un nuevo snapshot) que representa la fusión de las dos ramas. Los tres snapshots pertenecen a los dos últimos commits de las ramas `master` y `feature` y el tercero, al ancestro común, que en este caso es el commit 3**. Los commits y sus relaciones están mostrados por la siguiente figura.

![Recursive merge feature into master](/images/merge_6.png)

### Comandos básicos para merges

Si `HEAD` apunta a la rama A, incorpora los cambios de la rama B en la rama A mediante un merge (la estrategia es seleccionada por Git).

```bnf
git merge B
```

Elimina la rama especificada si todos los cambios de la rama son accesibles por `HEAD` o por algún ancestro de la rama. Simplemente, si la rama ha sido fusionada con una no derivada de ella, entonces este comando puede utilizarse efectivamente.

```bnf
git branch -d <rama>
```

Forzosamente borra la rama seleccionada, incluso si tiene cambios no fusionados. Al utilizar la bandera `-D` es posible que se pierdan commits.

```bnf
git branch -D <rama>
```

## Conflictos al realizar un merge

En ocasiones, las modificaciones de dos ramas distintas alteran algunas líneas en común de los mismos archivos. En estos casos, cuando al intentar realizar un merge, Git no puede ejecutarlo de forma automática; la intervención del programador es necesaria. Del ejemplo anterior, imaginemos que la característica agregada en `feature` alteró App.java, archivo que también fue modificado por la rama `bug-fix` y cuyos cambios ya se encuentran fusionados en `master`. En particular, del ejemplo anterior, nos ubicamos en este momento.

![Checkout feature y hacer un commit](/images/merge_5.png)

Al intentar fusionar `feature` en `master`, ocurrirá un conflicto sobre el archivo App.java, pues tanto el commit 6 (perteneciente a `feature`) como el 5 (perteneciente a `master`) tienen cambios particulares de este archivo.

```shell
$ git checkout master
$ git merge feature
Auto-merging App.java
CONFLICT (content): Merge conflict in App.java
Automatic merge failed; fix conflicts and then commit the result.
```

Al ocurrir esto, el proceso de merge es pausado y Git espera que el desarrollador resuelva los conflictos antes de proseguir con el merge. En cualquier momento durante esta pausa, utilizar el comando `git status` informa cuáles archivos no han podido ser fusionados dado uno o más conflictos.

```shell
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both modified:   App.java

no changes added to commit (use "git add" and/or "git commit -a")
```

Para resolver el conflicto, basta con abrir el archivo utilizando cualquier editor de texto y seleccionar los cambios de la rama deseada. En las capturas de pantalla inferiores se muestra App.java abierto en Vim (izquierda) y VSCode (derecha). Nótese la conveniente integración de VSCode con Git, que despliega opciones seleccionables en la parte superior del conflicto para una rápida resolución. Otros editores de texto e IDEs cuentan con estas facilidades.

![Conflictos en Vim y VSCode](/images/editors_conflict.png)

Aquí puede verse la estructura por la cual Git infroma de un conflicto, que se conforma por los siguientes marcadores.

```shell
<<<<<<< HEAD
<cambios de la rama actual>
=======
<cambios de la rama especificada>
>>>>>>> <rama especificada>
```

Tras eliminar los marcadores de conflicto (mediante las facilidades de un editor de texto o IDE integrado o simplemente eliminando los caracteres) y seleccionar los cambios deseados, se añade el archivo al staging area para señalar que el conflicto ha sido resuelto. Si se corre git status en este momento Git reporta lo siguiente.

```shell
$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)
```

Ahora sólo resta realizar un commit que representará el commit de merge que, como se ha estudiado, cuando no existe un conflicto y la estrategia de merge es recursivo, Git lo crea automáticamente. En este caso, lo realiza el programador.

## Repositorios remotos

> Resumen de <https://git-scm.com/book/en/v2/Git-Basics-Working-with-Remotes>

Hasta ahora, todos los ejemplos y explicaciones han estado limitados a repositorios locales. Es decir, el directorio `.git` únicamente existe en la máquina del desarrollador. Una de las más grandes ventajas de un VCS es la facilidad para orquestar colaboraciones sobre un proyecto. **Para poder colaborar sobre un proyecto gestionado por Git es necesario administrar repositorios remotos**. A pesar que no existe definición formal de repositorio remoto, aquí menciono mi entendimiento del concepto.

> **Repositorio remoto**. Sea R un respositorio, cualquier otro repositorio (`.git`) que represente el mismo proyecto que R y sea accesible por R (ya sea mediante una red o acceso a memoria local) se considera remoto a R.

Nótese que el término *repositorio remoto* es popularmente asociado con repositorios almacenados en GitHub, GitLab u otro servicio de cloud storage para repos de Git. Sin embargo, el repo remoto puede incluso residir en el mismo host, aunque esto es muy raro en la práctica. El servicio de cloud storage más popular para repositorios de Git es GitHub.

!["Remote" no implica necesariamente que se encuentra en una red](/images/remote_repos.png)

### Acceso a repositorios remotos

Para crear un repositorio local que represente el mismo proyecto que un repositorio remoto se utiliza el comando `git clone` que efectivamente clona el `.git` de la fuente especificada.

```bnf
git clone <dirección-repo-Git>
```

En GitHub existen muchos repositorios, todos los cuales pueden ser clonados. Un ejemplo de dirección legal para el comando es <https://github.com/HerCerM/BatchScripts>.

```shell
$ git clone https://github.com/HerCerM/BatchScripts
Cloning into 'BatchScripts'...
remote: Enumerating objects: 81, done.
remote: Counting objects: 100% (81/81), done.
remote: Compressing objects: 100% (59/59), done.
remote: Total 81 (delta 31), reused 57 (delta 18), pack-reused 0
Unpacking objects: 100% (81/81), done.
```

Tras ejecutar el comando un directorio con el nombre del repositorio (en este caso “BatchScripts”) aparece en el directorio en el que ejecutamos el comando. En esta carpeta se encuentra el `.git` que contiene la historia completa del proyecto.

Iniciar un repositorio utilizando `git clone` en lugar de `git init` tiene una consecuencia interesante. Al realizar git clone, ahora existen al menos dos repositorios que representan el mismo proyecto. Por lo tanto, ahora puede hablarse de repositorios remotos. Al clonar un repositorio, Git almacena información de la fuente (en `.git\logs\refs\remotes`) permitiendo establecer ya sea una relación de sólo lectura o lectura/escritura respecto a la fuente.

Listar aliases de repositorios remotos (1). Los aliases abrevian la dirección de un repositorio remoto, el cual puede ser un URL o dirección del sistema de archivos local. Para ver la dirección asociada a cada alias, utilizar la bandera `-v` ó `--verbose` (2).

```bnf
git remote     (1)
git remote -v  (2)
```

Por defecto, el alias creado al clonar un repositorio es `origin`. Por cada alias, dos direcciones son mostradas, una de lectura (fetch) y otra de escritura (push). **Para todo repositorio remoto se tiene acceso al menos de lectura (es posible que privilegios de escritura estén deshabilitados para ciertos usuario. Por ejemplo, si clona un repositorio de GitHub que no sea de usted y no está registrado como colaborador, sólo tendrá permisos de lectura - fetch)**.

```shell
$ git remote
origin
$ git remote -v
origin  https://github.com/HerCerM/BatchScripts (fetch)
origin  https://github.com/HerCerM/BatchScripts (push)
```

Es posible añadir (1), renombrar (2) y eliminar (3) aliases de repositorior remotos de forma manual.

```bnf
git remote add <alias> <dirección-repo-Git>      (1)
git remote rename <alias-antiguo> <alias-nuevo>  (2)
git remote remove <alias>                        (3)
```

Utilizando el comando para añadir (1) podemos ver una forma alterna equivalente al ejemplo del comando `git clone`.

```shell
$ mkdir BatchScripts && cd BatchScripts
$ git init
Initialized empty Git repository in C:/Users/hjcer/Documents/temp/BatchScripts/.git/
$ git remote add origin https://github.com/HerCerM/BatchScripts
$ git pull origin master
remote: Enumerating objects: 81, done.
remote: Counting objects: 100% (81/81), done.
remote: Compressing objects: 100% (59/59), done.
remote: Total 81 (delta 31), reused 57 (delta 18), pack-reused 0
Unpacking objects: 100% (81/81), done.
From https://github.com/HerCerM/BatchScripts
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
```

### Operaciones de lectura y escritura (fetch, pull y push)

Obtener todos los cambios del remoto identificado por `<alias>` no existentes en el repo local (1). También puden solicitarse sólo los cambios de una rama (2).

```bnf
git fetch <alias>         (1)
git fetch <alias> <rama>  (2)
```

Es importante destacar que este comando solamente trae los cambios, mas no los integra a las ramas respectivas mediante un merge. Entonces, para incorporar las modificaciones hace falta un merge manual. Podemos ver que los cambios han sido traídos, pero no incorporados.

```shell
$ git fetch origin
$ git branch -a
* master
  remotes/origin/master
```

En la práctia, la mayoría de las veces se desea realizar un merge inmediatamente después de un fetch. `git pull` es un comando pensado para esto. **Es decir, `git pull` ejecuta dos comandos: primero un `git fetch`, seguido de `git merge`**.

```bnf
git pull <alias> <rama>
```

Observación acerca de la distinción entre `git fetch` y `git pull`.

![git fetch vs git pull](/images/fetch_vs_pull.png)

Actualizar la rama `<rama>` del repositorio remoto ubicado en `<alias>` con los cambios de la rama actual.

```bnf
git push <alias> <rama>
```

### Obtener información detallada de un repositorio remoto

Inspecciona el repositorio remoto bajo el alias `<alias>` y muestra información de relación respecto al repo local: ramas en el remoto que existen en el local (al igual que las que aún no han sido recuperadas) y la configuración de push y pull predeterminada por rama.

```bnf
git remote show <alias>
```

Continuando con el ejemplo del repositorio de scripts de Batch (iniciado por `git clone` en lugar de la forma alterna mostrada), el comando anteriormente mencionado muestra lo siguiente.

```shell
$ git remote show origin
* remote origin
  Fetch URL: https://github.com/HerCerM/BatchScripts
  Push  URL: https://github.com/HerCerM/BatchScripts
  HEAD branch: master
  Remote branch:
    master tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)
```

Podemos ver que en el repositorio remoto sólo existe una rama: `master`. En adición, vemos que en el repo local tenemos la versión de `master` (tracked). También vemos la configuración predeterminada para push y pull ubicándonoes en la rama `master`. Desde esa rama, basta con realizar `git pull` o `git push` y Git resuelve el alias y la rama.

```shell
$ git pull
Already up to date.
$ git push
Everything up-to-date
```

### Configurar upstreams

Retomemos el ejemplo en el que se inicia el repositorio de scripts de Batch mediante la forma alterna a `git clone` (es decir, mediante `git init` seguido de `git remote add`). En este caso, veamos qué ocurre al intentar hacer pull o push sin especificar ni el alias ni la rama.

```shell
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> master
$ git push
fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin master
```

En ambos casos la operación no se realiza con éxito, reportando Git que no hay información de tracking para la rama actual (en este caso, para `master`) o, en otras palabras como lo reporta el error de `git push`, que la rama `master` no tiene una rama upstream. Para resolver este problema de configuración es necesario asignar una rama upstream a `master`.

> **Rama upstream**. Rama de un repositorio remoto que es objeto de operaciones de lectura/escritura (fetch, pull y push) respecto a una rama en el repositorio local.

Es decir, cuando se realiza por ejemplo, git pull origin `master` ubicándose en la rama `master` del repo local, se dice que la rama `master` del repositorio ubicado en origin es upstream de la rama `master` del repositorio local para esa operación de pull.

```shell
$ git pull origin master
 From https://github.com/HerCerM/BatchScripts
 * branch            master     -> FETCH_HEAD
Already up to date.
```

Continuando con el ejemplo en mano, pudimos verificar que `git pull` arroja un error, pero eso no sucede con `git pull origin master` (tampoco ocurriría el error con `git push origin master`). En el último comando, se establece de forma explícita la rama upstream de esa operación pull respecto a `master` (el comando es ejecutado ubicado en `master` del repositorio local). Sin embargo, resulta muy extenso escribir todo ese comando; **sería ideal poder configurar la rama `master` de `origin` como el objeto de todas las operacioes de lectura/escritura respecto a `master` local**.

La configuración puede realizarse de dos formas. Sin realizar una operación de lectura/escritura (1) o bien al realizar un push (2). Tras ejecutar alguno de estos comandos, simplemente ejecutar `git pull` o `git push` ubicándose en la rama configurada basta para obtener el comportamiento deseado de Git.

```bnf
git branch -u <alias>/<rama-remota> <rama-local>  (1)
git push -u <alias> <rama>                        (2)
```

Los upstreams configurados pueden mostrarse utilizando `git branch -vv`.

```shell
$ git branch -vv
* master e1d8495 Update README.md
$ git branch -u origin/master master
Branch 'master' set up to track remote branch 'master' from 'origin'.
$ git branch -vv
* master e1d8495 [origin/master] Update README.md
```

Nótese el formato de la rama upstream configurada: `<alias>/<rama>`. Tras realizar al menos un fetch a esta rama, encontramos que `<alias>/<rama>` no sólo es notación para identificar la rama upstream, ¡sino también es una rama en el repositorio local!

```shell
$ git branch -a
* master
  remotes/origin/master
```

Recordemos el comando `git fetch`. Al ejecutar un fetch, los cambios no son integrados a alguna rama local, sino que sólo son traídos al repo local para inspección y, si se desea, para integración mediante `git merge`. ¿En dónde son esos cambios almacenados para inspección? Los cambios son almacenados en una rama de nombre `<alias>/<rama>` que sirve como rama de enlace entre una local y su upstream asociada (conocoda en inglés como tracking branch). **Entonces, en el proceso de enlace (objeto de operaciones fetch, pull y push) entre una rama local y una remota, intervienen tres ramas: la rama local, la rama remota y una intermediara local que permite traer cambios del repo remoto sin inmediatamente agregarlos al repositorio local**.

![Ramas remote tracking](/images/remote_tracking.png)
