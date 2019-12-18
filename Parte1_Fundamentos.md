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

Para visualizar toda la configuración disponible (incluso en .git), ingresar:

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
