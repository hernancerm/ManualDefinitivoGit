# Fundamentos

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
