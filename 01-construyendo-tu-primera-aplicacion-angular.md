# Parte 1: Fundamentos de Angular

## Capítulo 1: Construyendo tu primera aplicación Angular

El desarrollo web ha experimentado un enorme crecimiento durante la última década. Han surgido frameworks, librerías y herramientas que permiten a los desarrolladores crear excelentes aplicaciones web. **Angular** ha allanado el camino creando un framework centrado en el rendimiento de las aplicaciones, la ergonomía del desarrollo y las técnicas web modernas.

Antes de desarrollar aplicaciones en Angular, necesitamos aprender algunos conceptos básicos pero esenciales para tener una gran experiencia con el framework. Una de las primeras cosas que debemos saber es qué es Angular y por qué deberíamos usarlo para el desarrollo web. También haremos un recorrido en este capítulo por la historia de Angular para comprender cómo ha evolucionado el framework.

Otro tema introductorio importante, y a veces desafiante, es la configuración de nuestro entorno de desarrollo. Debe realizarse al comienzo de un proyecto, y hacerlo bien desde el principio puede reducir la fricción a medida que nuestra aplicación crece. Por lo tanto, una gran parte de este capítulo está dedicada a **Angular CLI**, una herramienta desarrollada por el equipo de Angular que proporciona tareas de scaffolding y automatización en una aplicación Angular, eliminando el código repetitivo de configuración y permitiendo a los desarrolladores concentrarse en el proceso de programación. Utilizaremos Angular CLI para crear nuestra primera aplicación desde cero, familiarizarnos con la anatomía de una aplicación Angular y echar un vistazo a cómo funciona Angular por debajo.

Trabajar en un proyecto de Angular sin la ayuda de herramientas de desarrollo, como un Entorno de Desarrollo Integrado (**IDE**), puede resultar tedioso. Nuestro editor de código favorito puede proporcionar un flujo de trabajo ágil que incluye compilación en tiempo de ejecución, comprobación estática de tipos, introspección, autocompletado de código y asistencia visual para depurar y compilar nuestra aplicación. En este capítulo destacaremos algunas de las herramientas más populares en el ecosistema de Angular, como **Angular DevTools** y **Visual Studio Code (VSCode)**.

En resumen, estos son los temas principales que exploraremos en este capítulo:

- ¿Qué es Angular?
- ¿Por qué elegir Angular?
- Configuración del espacio de trabajo con Angular CLI
- La estructura de una aplicación Angular
- Herramientas de Angular

---

### Sección 1.1: Aprovecha al máximo este libro: conoce tus beneficios gratuitos

Desbloquea los beneficios gratuitos exclusivos que vienen con tu compra, cuidadosamente diseñados para potenciar tu proceso de aprendizaje y ayudarte a aprender sin límites.

A continuación, se presenta una descripción general de lo que obtienes con este libro:

#### Lector de nueva generación
*(Figura 1.1: Ilustración de las características del lector de nueva generación de Packt)*

Nuestro lector basado en web, diseñado para ayudarte a aprender de manera efectiva, incluye las siguientes características:

- **Sincronización de progreso multidispositivo:** Aprende desde cualquier dispositivo con una sincronización de progreso perfecta.
- **Resaltado y toma de notas:** Convierte tu lectura en conocimiento duradero.
- **Marcadores:** Vuelve a consultar tus aprendizajes más importantes en cualquier momento.
- **Modo oscuro:** Concéntrate con la mínima fatiga visual cambiando al modo oscuro o sepia.

#### Asistente de IA interactivo (beta)
*(Figura 1.2: Ilustración del asistente de IA de Packt)*

Nuestro asistente interactivo de IA ha sido entrenado con el contenido de este libro, por lo que puede ayudarte si encuentras algún problema. Viene con las siguientes características:

- **Resúmelo:** Resume secciones clave o un capítulo entero.
- **Explicadores de código por IA:** En el lector de nueva generación de Packt, haz clic en el botón *Explain* sobre cada bloque de código para obtener explicaciones impulsadas por IA.

> **Nota:** El asistente de IA es parte del lector de nueva generación de Packt y todavía se encuentra en fase beta.

#### Versión en PDF o ePub sin DRM
*(Figura 1.3: PDF y ePub gratuitos)*

Aprende sin límites con las siguientes ventajas incluidas con tu compra:

- Aprende desde cualquier lugar con una copia en PDF sin DRM de este libro.
- Usa tu lector electrónico favorito para aprender utilizando una versión ePub sin DRM de este libro.

#### Desbloquea los beneficios exclusivos de este libro ahora
Tómate un momento para aprovechar al máximo tu compra y disfrutar de la experiencia de aprendizaje completa:  
[https://www.packtpub.com/unlock/9781835087480](https://www.packtpub.com/unlock/9781835087480)

> **Nota:** Ten lista tu factura de compra antes de comenzar.

---

### Sección 1.2: Requisitos técnicos

- **GitHub:** [https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch01](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch01)
- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)
- **Angular DevTools:** [https://angular.dev/tools/devtools](https://angular.dev/tools/devtools)

---

### Sección 1.3: ¿Qué es Angular?

**Angular** es un framework web escrito en el lenguaje **TypeScript** e incluye una interfaz de línea de comandos (**CLI**), un servicio de lenguaje (*language service*), una herramienta de depuración y una rica colección de librerías oficiales de primera mano (*first-party libraries*).

Las librerías incluidas en el framework de Angular proporcionadas de serie se denominan **librerías de primera mano** (*first-party libraries*).

Angular permite a los desarrolladores crear aplicaciones web escalables con TypeScript, un superconjunto sintáctico estricto de JavaScript, del cual aprenderemos en el *Capítulo 2: Introducción a TypeScript*.

La documentación oficial de Angular se encuentra en [https://angular.dev](https://angular.dev/).

> La documentación oficial de Angular es el recurso más actualizado para el desarrollo con Angular. Es preferible utilizarla sobre otros recursos externos mientras se desarrolla con Angular.

**Google** creó Angular. La primera versión, 1.0, fue lanzada en 2012 y se llamó **AngularJS**. AngularJS era un framework de JavaScript, y las aplicaciones web construidas con él estaban escritas en JavaScript.

En 2016, el equipo de Angular decidió hacer un cambio revolucionario en AngularJS. Colaboraron con el equipo de TypeScript en Microsoft e introdujeron el lenguaje TypeScript en el framework. La siguiente versión del framework, 2.0, fue escrita en TypeScript y pasó a llamarse **Angular**, con un logotipo diferente al de AngularJS.

En 2022, Angular entró en una nueva era de avances evolutivos conocida como el **Renacimiento de Angular** (*Angular Renaissance*). Durante ese período, el framework cobró un gran impulso en el desarrollo web al introducir importantes innovaciones centradas en mejorar la Experiencia de Desarrollo (**DX**) y optimizar el rendimiento de las aplicaciones, tales como:

- Un enfoque simple y moderno para la creación de aplicaciones Angular.
- Patrones de reactividad mejorados para gestionar el estado de la aplicación de manera eficiente.
- La integración de técnicas de Renderizado en el Servidor (**SSR**) para mejorar el rendimiento.

Un hito fundamental en la era del Renacimiento de Angular fue **Angular 17**, cuando el equipo de Angular decidió renovar la marca del framework con un nuevo logotipo y colores, reflejando los cambios recientes y estableciendo la visión para futuros avances.

En este libro, cubriremos **Angular 19**, la última versión principal estable del framework Angular. AngularJS llegó al final de su vida útil en 2022 y ya no cuenta con soporte ni mantenimiento por parte del equipo de Angular.

Angular se basa en los estándares web más modernos y es compatible con todos los navegadores modernos (*evergreen browsers*). Puedes encontrar más detalles sobre el soporte de versiones específicas de cada navegador en [https://angular.dev/reference/versions#browser-support](https://angular.dev/reference/versions#browser-support).

En la siguiente sección, aprenderemos los beneficios de elegir Angular para el desarrollo web.

---

### Sección 1.4: ¿Por qué elegir Angular?

El poder del framework Angular se basa en la combinación de las siguientes características:

- **Los pilares principales del framework:**
  - Multiplataforma
  - Herramientas increíbles
  - Fácil incorporación
- **El uso de Angular en todo el mundo:**
  - Una comunidad increíble
  - Probado en batalla con los productos de Google

En las siguientes secciones, examinaremos cada característica en mayor detalle.

#### Multiplataforma
Las aplicaciones Angular pueden ejecutarse en diferentes plataformas: web, servidor, escritorio y móvil. Angular puede ejecutarse de forma nativa solo en la web porque es un framework web; sin embargo, es de código abierto y está respaldado por herramientas increíbles que permiten que el framework se ejecute en las tres restantes mediante las siguientes herramientas:

- **Angular SSR:** Renderiza aplicaciones Angular en el lado del servidor.
- **Angular service worker:** Permite que las aplicaciones Angular se ejecuten como Aplicaciones Web Progresivas (**PWA**) que pueden ejecutarse en entornos de escritorio y móviles nativos.
- **Ionic/NativeScript:** Nos permite crear aplicaciones móviles utilizando Angular.

El siguiente pilar del framework describe las herramientas disponibles en el ecosistema de Angular.

#### Herramientas
El equipo de Angular ha creado dos excelentes herramientas que hacen que el desarrollo en Angular sea fácil y divertido:

- **Angular CLI:** Una interfaz de línea de comandos que nos permite trabajar con proyectos de Angular, desde el scaffolding hasta las pruebas y el despliegue.
- **Angular DevTools:** Una extensión de navegador que nos permite inspeccionar y perfilar aplicaciones Angular cómodamente desde nuestro navegador.

Angular CLI es la solución *de facto* para trabajar con aplicaciones Angular. Permite al desarrollador concentrarse en escribir código de aplicación, eliminando el código repetitivo de las tareas de configuración como el scaffolding, la compilación, las pruebas y el despliegue de una aplicación Angular.

#### Incorporación (Onboarding)
Es simple y fácil comenzar con el desarrollo en Angular porque, cuando instalamos Angular, también obtenemos una rica colección de librerías de primera mano listas para usar, que incluyen:

- Un **cliente HTTP de Angular** para comunicarse con recursos externos a través de HTTP.
- **Formularios de Angular** para crear formularios HTML y recopilar entradas y datos de los usuarios.
- Un **enrutador de Angular** para realizar navegaciones internas en la aplicación.

Las librerías anteriores se instalan por defecto cuando creamos una nueva aplicación Angular utilizando Angular CLI. Sin embargo, solo se utilizan en nuestra aplicación si las importamos explícitamente en nuestro proyecto.

#### El uso de Angular en todo el mundo
Muchas empresas utilizan Angular para sus sitios y aplicaciones web. El sitio web [https://www.madewithangular.com](https://www.madewithangular.com/) contiene una lista exhaustiva de esas empresas, incluidas algunas muy populares.

Además, Angular se utiliza en miles de proyectos dentro de Google y por millones de desarrolladores en todo el mundo. El hecho de que Angular ya se utilice internamente en Google es un factor crucial en la fiabilidad del framework. Cada nueva versión de Angular se prueba exhaustivamente en esos proyectos antes de estar disponible para el público. Este proceso de prueba ayuda al equipo de Angular a detectar errores de forma temprana y ofrece un framework de máxima calidad al resto de la comunidad de desarrolladores.

Angular está respaldado y soportado por una próspera comunidad de desarrolladores. Los desarrolladores pueden acceder a muchas comunidades disponibles en todo el mundo, tanto en línea como locales, para obtener ayuda y orientación sobre el framework Angular. Por otro lado, las comunidades ayudan a que el framework Angular progrese compartiendo comentarios sobre nuevas funciones, probando nuevas ideas y reportando problemas. Algunas de las comunidades en línea más populares son:

- **Tech Stack Nation:** El grupo de estudio de Angular más amigable del mundo, que reúne a desarrolladores de Angular apasionados por mejorar su confianza en la creación de increíbles aplicaciones Angular. Tech Stack Nation es una comunidad donde los desarrolladores de Angular pueden colaborar, aprender de la experiencia de los demás y ampliar los límites de lo que Angular puede lograr. Puedes unirte a Tech Stack Nation en [https://techstacknation.com](https://techstacknation.com/).
- **Angular Community Discord:** El servidor oficial de Discord de Angular que reúne a la increíble comunidad de Angular. Todos son bienvenidos a unirse a la comunidad con solo hacer clic en un botón. Es el lugar central para conectar a miembros del equipo de Angular, Google Developer Experts (**GDE**), autores de librerías, grupos de meetups y cualquier persona interesada en aprender el framework. Puedes unirte al servidor de Discord de la comunidad de Angular en [https://discord.gg/angular](https://discord.gg/angular).
- **Angular.love:** Una plataforma comunitaria para entusiastas de Angular, respaldada por House of Angular, para facilitar el crecimiento de los desarrolladores de Angular a través de iniciativas de intercambio de conocimientos. Comenzó como un blog donde los expertos publicaban artículos sobre novedades, características y mejores prácticas de Angular. Ahora, Angular.love también organiza meetups presenciales y online, con la participación frecuente de GDEs. Puedes unirte a Angular.love en [https://angular.love](https://angular.love/).

Ahora que hemos visto qué es Angular y por qué se debería elegir para el desarrollo web, aprenderemos a usarlo y a crear excelentes aplicaciones web.

---

### Sección 1.5: Configuración del espacio de trabajo con Angular CLI

Configurar un proyecto con Angular puede ser complicado. Es necesario saber qué librerías importar y asegurarse de que los archivos se procesen en el orden correcto, lo que nos lleva al tema del **scaffolding**. El scaffolding es una herramienta para automatizar tareas, como generar un proyecto desde cero, y se vuelve necesario a medida que la complejidad crece y donde cada hora cuenta para producir valor comercial, en lugar de gastarse luchando contra problemas de configuración.

La motivación principal detrás de la creación de Angular CLI fue ayudar a los desarrolladores a concentrarse en la creación de aplicaciones, eliminando el código repetitivo de configuración. Básicamente, con un comando simple, deberías poder inicializar una aplicación, agregar nuevos artefactos, ejecutar pruebas, actualizar aplicaciones y crear un paquete listo para producción. Angular CLI admite todo esto mediante comandos especiales llamados **schematics**.

#### Requisitos previos
Antes de comenzar, debemos asegurarnos de que nuestro entorno de desarrollo incluya las herramientas de software esenciales para el flujo de trabajo de desarrollo en Angular.

##### Node.js
Node.js es un entorno de ejecución de JavaScript construido sobre el motor de JavaScript V8 de Chrome. Angular requiere una versión activa o de soporte extendido (**LTS**). Si ya lo tienes instalado, puedes ejecutar `node -v` en la línea de comandos para verificar qué versión estás ejecutando.

Si necesitas trabajar con aplicaciones que usan diferentes versiones de Node.js o no puedes instalar el entorno de ejecución debido a permisos restringidos, usa **nvm**, un administrador de versiones para Node.js diseñado para instalarse por usuario. Puedes obtener más información en [https://github.com/nvm-sh/nvm](https://github.com/nvm-sh/nvm).

##### npm
npm es un administrador de paquetes de software que se incluye por defecto en Node.js. Puedes comprobarlo ejecutando `npm -v` en la línea de comandos. Una aplicación Angular consta de varias librerías, llamadas paquetes, que existen en un lugar central llamado registro de npm (*npm registry*). El cliente npm descarga e instala las librerías necesarias para ejecutar tu aplicación desde el registro de npm a tu ordenador local.

##### Git
Git es un cliente que nos permite conectarnos a sistemas de control de versiones distribuidos, como GitHub, Bitbucket y GitLab. Es opcional desde la perspectiva de Angular CLI. Deberías instalarlo si deseas subir tu proyecto Angular a un repositorio Git, lo cual es muy recomendable.

#### Instalación de Angular CLI
Angular CLI es parte del ecosistema de Angular y se puede descargar desde el registro de paquetes de npm. Dado que se utiliza para crear proyectos de Angular, debemos instalarlo globalmente en nuestro sistema. Abre una ventana de terminal y ejecuta el siguiente comando:

```bash
npm install -g @angular/cli
```

Es posible que necesites permisos elevados en algunos sistemas Windows, por lo que deberías ejecutar tu terminal como administrador. Ejecuta el comando anterior en sistemas Linux/macOS agregando la palabra clave `sudo` como prefijo para ejecutarlo con privilegios administrativos.

El comando que usamos para instalar Angular CLI utiliza el cliente npm, seguido de un conjunto de argumentos de tiempo de ejecución:

- `install` o `i`: Denota la instalación de un paquete.
- `-g` o `--global`: Indica que el paquete se instalará en el sistema globalmente.
- `@angular/cli`: El nombre del paquete a instalar.

Angular CLI sigue la misma versión que el framework Angular, que en este libro es la 19. El comando anterior instalará la última versión estable de Angular CLI. Puedes comprobar qué versión tienes instalada ejecutando `ng version` o `ng v` en la línea de comandos. Si tienes una versión diferente a la 19 después de instalarla, puedes ejecutar el siguiente comando:

```bash
npm install -g @angular/cli@19
```

> **Consejo rápido:** Mejora tu experiencia de programación con las funciones AI Code Explainer y Quick Copy. Abre este libro en el lector de nueva generación de Packt. Haz clic en el botón *Copy* para copiar rápidamente el código en tu entorno de desarrollo, o haz clic en el botón *Explain* para que el asistente de IA te explique un bloque de código.
> 
> El lector de nueva generación de Packt se incluye de forma gratuita con la compra de este libro. Desbloquéalo visitando [https://www.packtpub.com/unlock/9781835087480](https://www.packtpub.com/unlock/9781835087480).

El comando anterior descargará e instalará la última versión de Angular CLI 19.

#### Comandos de la CLI
Angular CLI es una herramienta de interfaz de línea de comandos que automatiza tareas específicas durante el desarrollo, como servir, compilar, empaquetar, actualizar y probar un proyecto de Angular. Como su nombre lo indica, utiliza la línea de comandos para invocar el archivo ejecutable `ng` y ejecutar comandos utilizando la siguiente sintaxis:

```bash
ng [command] [options]
```

Aquí, `[command]` es el nombre del comando que se va a ejecutar, y `[options]` denota parámetros adicionales que se pueden pasar a cada comando. Para ver todos los comandos disponibles, puedes ejecutar lo siguiente:

```bash
ng help
```

Algunos comandos se pueden invocar usando un alias en lugar del nombre completo. En este libro, cubrimos los más comunes (el alias de cada comando se muestra entre paréntesis):

- `new` (`n`): Crea un nuevo espacio de trabajo de Angular CLI desde cero.
- `build` (`b`): Compila una aplicación Angular y genera los archivos resultantes en una carpeta predefinida.
- `generate` (`g`): Crea nuevos archivos que componen una aplicación Angular.
- `serve` (`dev`): Compila una aplicación Angular y la sirve utilizando un servidor web preconfigurado.
- `test` (`t`): Ejecuta las pruebas unitarias de una aplicación Angular.
- `add`: Instala una librería de Angular en una aplicación Angular.
- `update`: Actualiza una aplicación Angular a la última versión de Angular.

Puedes encontrar más comandos de Angular CLI en [https://angular.dev/cli](https://angular.dev/cli).

Actualizar una aplicación Angular es una de las tareas más críticas de la lista anterior. Nos ayuda a mantenernos al día actualizando nuestras aplicaciones Angular a la última versión.

> Intenta mantener tus proyectos de Angular actualizados porque cada nueva versión de Angular viene repleta de muchas características nuevas y emocionantes, mejoras de rendimiento y correcciones de errores.

Además, puedes utilizar la guía de actualización de Angular, que contiene consejos e instrucciones paso a paso sobre cómo actualizar tus aplicaciones, en [https://angular.dev/update-guide](https://angular.dev/update-guide).

#### Creación de un nuevo proyecto
Ahora que hemos preparado nuestro entorno de desarrollo, podemos comenzar a crear nuestra primera aplicación Angular. Usaremos el comando `ng new` de Angular CLI y pasaremos el nombre de la aplicación que queremos crear como una opción:

Abre una ventana de terminal, navega hasta una carpeta de tu elección y ejecuta el comando `ng new my-app`. Crear una nueva aplicación Angular es un proceso sencillo. Angular CLI te pedirá detalles sobre la aplicación que deseas crear para poder generar el scaffolding del proyecto de la mejor manera posible.

Inicialmente, nos preguntará si queremos habilitar la analítica de Angular:

```text
Would you like to share pseudonymous usage data about this project with the Angular Team at Google under Google's Privacy Policy at https://policies.google.com/privacy. For more details and how to change this setting, see https://angular.dev/cli/analytics. (y/N)
```

Angular CLI hará esta pregunta una vez cuando creemos el primer proyecto de Angular y la aplicará globalmente en nuestro sistema. Sin embargo, podemos cambiar la configuración más adelante en un espacio de trabajo de Angular específico.

La siguiente pregunta está relacionada con el estilo de nuestra aplicación:

```text
Which stylesheet format would you like to use?
```

Es común usar CSS para dar estilo a las aplicaciones Angular. Sin embargo, podemos usar preprocesadores como SCSS o Less para agregar valor a nuestro flujo de trabajo de desarrollo. En este libro, trabajamos directamente con CSS, así que acepta la opción predeterminada, **CSS**, y presiona *Enter*.

Finalmente, Angular CLI nos preguntará si queremos habilitar SSR y la Generación de Sitios Estáticos (**SSG**) en nuestra aplicación:

```text
Do you want to enable Server-Side Rendering (SSR) and Static Site Generation (SSG/Prerendering)? (y/N)
```

SSR y SSG se ocupan de mejorar el rendimiento de inicio y carga de una aplicación Angular. Aprenderemos más sobre ellos en el *Capítulo 15: Optimización del rendimiento de la aplicación*. Por ahora, acepta la opción predeterminada, **No**, presionando *Enter*.

El proceso puede tardar algún tiempo, dependiendo de tu conexión a Internet. Durante este tiempo, Angular CLI descarga e instala todos los paquetes necesarios y crea los archivos predeterminados para tu aplicación Angular. Cuando termine, habrá creado una carpeta llamada `my-app`. La carpeta representa un espacio de trabajo de Angular CLI que contiene una única aplicación Angular llamada `my-app` en el nivel raíz.

El espacio de trabajo contiene varias carpetas y archivos de configuración que Angular CLI necesita para compilar y probar la aplicación Angular:

- `.vscode`: Incluye archivos de configuración de VSCode.
- `node_modules`: Incluye paquetes npm instalados necesarios para desarrollar y ejecutar la aplicación Angular.
- `public`: Contiene recursos estáticos como fuentes, imágenes e iconos.
- `src`: Contiene los archivos fuente de la aplicación.
- `.editorconfig`: Define estilos de codificación para el editor predeterminado.
- `.gitignore`: Especifica los archivos y carpetas que Git no debe rastrear.
- `angular.json`: El archivo de configuración principal del espacio de trabajo de Angular CLI.
- `package.json` y `package-lock.json`: Proporcionan definiciones de paquetes npm, junto con sus versiones exactas, necesarias para desarrollar, probar y ejecutar la aplicación Angular.
- `README.md`: Un archivo README generado automáticamente por Angular CLI.
- `tsconfig.app.json`: Una configuración de TypeScript específica para la aplicación Angular.
- `tsconfig.json`: Una configuración de TypeScript específica para el espacio de trabajo de Angular CLI.
- `tsconfig.spec.json`: Una configuración de TypeScript específica para las pruebas unitarias de la aplicación Angular.

Como desarrolladores, solo debemos preocuparnos por escribir el código fuente que implementa las funcionalidades de nuestra aplicación. No obstante, tener un conocimiento básico de cómo se organiza y configura la aplicación nos ayuda a comprender mejor su funcionamiento interno y las formas de intervenir si es necesario.

Navega a la carpeta recién creada e inicia tu aplicación con el siguiente comando:

```bash
ng serve
```

> Recuerda que cualquier comando de Angular CLI debe ejecutarse dentro de la carpeta del espacio de trabajo de Angular CLI.

Angular CLI compila el proyecto de Angular e inicia un servidor web que observa los cambios en los archivos del proyecto. De esta manera, cada vez que cambies el código de tu aplicación, el servidor web recompilará el proyecto para reflejar los nuevos cambios.

Una vez que la compilación se haya completado con éxito, puedes obtener una vista previa de la aplicación abriendo tu navegador y navegando a `http://localhost:4200`:

*(Figura 1.4: Página de inicio de la aplicación Angular)*

¡Felicidades! Has creado tu primer espacio de trabajo de Angular CLI. Angular CLI ha creado una página web de ejemplo que podemos usar como referencia para construir nuestro proyecto. En la siguiente sección, exploraremos las partes principales de nuestra aplicación y aprenderemos cómo modificar esta página.

---

### Sección 1.6: La estructura de una aplicación Angular

Daremos los primeros pasos para examinar nuestra aplicación Angular. Angular CLI ya ha generado el scaffolding de nuestro proyecto y ha hecho gran parte del trabajo pesado por nosotros. Todo lo que necesitamos hacer es abrir nuestro IDE favorito y comenzar a trabajar con el proyecto de Angular. Usaremos VSCode en este libro, pero siéntete libre de elegir cualquier editor con el que te sientas cómodo:

1. Abre VSCode y selecciona **File | Open Folder…** en el menú principal.
2. Navega hasta la carpeta `my-app` y selecciónala. VSCode cargará el espacio de trabajo de Angular CLI asociado.
3. Despliega la carpeta `src`.

Cuando desarrollamos una aplicación Angular, lo más probable es que interactuemos con la carpeta `src`. Es donde escribimos el código y las pruebas de nuestra aplicación. Contiene lo siguiente:

- `app`: Todos los archivos relacionados con Angular de la aplicación. Interactuarás con esta carpeta la mayor parte del tiempo durante el desarrollo.
- `index.html`: La página HTML principal de la aplicación Angular.
- `main.ts`: El punto de entrada principal de la aplicación Angular.
- `styles.css`: Estilos CSS que se aplican globalmente a la aplicación Angular. La extensión de este archivo depende del formato de hoja de estilos que elijas al crear la aplicación.

La carpeta `app` contiene el código fuente real que escribimos para nuestra aplicación. Los desarrolladores pasan la mayor parte de su tiempo dentro de esa carpeta. La aplicación Angular que se creó automáticamente desde Angular CLI contiene los siguientes archivos:

- `app.component.css`: Contiene estilos CSS específicos para la página de ejemplo. La extensión de este archivo depende del formato de hoja de estilos que elijas al crear la aplicación.
- `app.component.html`: Contiene el contenido HTML de la página de ejemplo.
- `app.component.spec.ts`: Contiene pruebas unitarias para la página de ejemplo.
- `app.component.ts`: Define la lógica de presentación de la página de ejemplo.
- `app.config.ts`: Define la configuración de la aplicación Angular.
- `app.routes.ts`: Define la configuración de enrutamiento de la aplicación Angular.

> La extensión de archivo `.ts` se refiere a archivos TypeScript.

En las siguientes secciones, aprenderemos cómo Angular organiza algunos de esos archivos para mostrar la página de ejemplo de la aplicación.

#### Componentes
Los archivos cuyos nombres comienzan con `app.component` constituyen un **componente de Angular**. Un componente en Angular controla parte de una página web orquestando la interacción de la lógica de presentación con el contenido HTML de la página, llamado **plantilla** (*template*).

Cada aplicación Angular tiene un archivo HTML principal, llamado `index.html`, que existe dentro de la carpeta `src` y contiene el siguiente elemento HTML `<body>`:

```html
<body>
  <app-root></app-root>
</body>
```

La etiqueta `<app-root>` se utiliza para identificar el componente principal de la aplicación y actúa como un contenedor para mostrar su contenido HTML. Le indica a Angular que renderice la plantilla del componente principal dentro de esa etiqueta. Aprenderemos cómo funciona en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*.

Cuando Angular CLI compila una aplicación Angular, analiza el archivo `index.html` e identifica las etiquetas HTML dentro del elemento `<body>`. Una aplicación Angular siempre se renderiza dentro del elemento `<body>` y comprende un árbol de componentes. Cuando Angular CLI encuentra una etiqueta que no es un elemento HTML conocido, como `<app-root>`, comienza a buscar a través de los componentes del árbol de la aplicación. Pero, ¿cómo sabe por dónde empezar?

#### Arranque (Bootstrapping)
El método de inicio de una aplicación Angular se llama **arranque** (*bootstrapping*), y se define en el archivo `main.ts` dentro de la carpeta `src`:

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { appConfig } from './app/app.config';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, appConfig)
  .catch((err) => console.error(err));
```

La tarea principal del archivo de arranque es definir el componente que se cargará al iniciar la aplicación. Llama al método `bootstrapApplication`, pasando `AppComponent` como parámetro para especificar el componente inicial de la aplicación. También pasa el objeto `appConfig` como segundo parámetro para especificar la configuración que se utilizará en el inicio de la aplicación. La configuración de la aplicación se describe en el archivo `app.config.ts`:

```typescript
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [provideZoneChangeDetection({ eventCoalescing: true }), provideRouter(routes)]
};
```

El objeto `appConfig` contiene una propiedad `providers` para definir los servicios proporcionados en toda la aplicación Angular. Aprenderemos más sobre los servicios en el *Capítulo 5: Gestión de tareas complejas con Servicios*.

Una nueva aplicación de Angular CLI proporciona servicios de enrutamiento por defecto. El enrutamiento está relacionado con la navegación dentro de la aplicación entre diferentes componentes utilizando la URL del navegador. Se activa mediante el método `provideRouter`, pasando un objeto de rutas, llamado configuración de rutas, como parámetro. La configuración de rutas de la aplicación se define en el archivo `app.routes.ts`:

```typescript
import { Routes } from '@angular/router';

export const routes: Routes = [];
```

Nuestra aplicación aún no tiene una configuración de rutas, como lo indica el array `routes` vacío. Aprenderemos a configurar el enrutamiento en el *Capítulo 9: Navegación a través de aplicaciones con Enrutamiento*.

#### Sintaxis de plantillas
Ahora que hemos realizado una breve descripción general de nuestra aplicación de ejemplo, es hora de comenzar a interactuar con el código fuente:

1. Ejecuta el siguiente comando en una ventana de terminal para iniciar la aplicación si aún no se está ejecutando:

```bash
ng serve
```

> Si estás trabajando con VSCode, es preferible utilizar su terminal integrado, al cual se puede acceder desde la opción **Terminal | New Terminal** en el menú principal.

2. Abre la aplicación con tu navegador en `http://localhost:4200` y observa el texto debajo del logotipo de Angular que dice *Hello, my-app*. La palabra *my-app*, que corresponde al nombre de la aplicación, proviene de una variable declarada en el archivo TypeScript del componente principal. Abre el archivo `app.component.ts` y localiza la variable `title`:

```typescript
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'my-app';
}
```

La variable `title` es una propiedad del componente que se utiliza en la plantilla del componente.

3. Abre el archivo `app.component.html` y ve a la línea 228:

```html
<h1>Hello, {{ title }}</h1>
```

La propiedad `title` está rodeada por una sintaxis de dobles llaves llamada **interpolación**, que forma parte de la sintaxis de plantillas de Angular. En pocas palabras, la interpolación convierte el valor de la propiedad `title` en texto y lo imprime en la página.

Angular utiliza una sintaxis de plantillas específica para extender y enriquecer la sintaxis HTML estándar en la plantilla de la aplicación. Aprenderemos más sobre la sintaxis de plantillas de Angular en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*.

4. Cambia el valor de la propiedad `title` en la clase `AppComponent` a `'World'`, guarda los cambios, espera a que la aplicación se recargue y examina la salida en el navegador:

*(Figura 1.5: Título de la página de destino)*

¡Felicidades! Has interactuado con éxito con el código fuente de tu aplicación.

A estas alturas, deberías tener una comprensión básica de cómo funciona Angular y cuáles son las partes básicas de una aplicación Angular. Como lector, has tenido que absorber mucha información hasta ahora. Sin embargo, tendrás la oportunidad de familiarizarte más con los componentes en los próximos capítulos. Por ahora, el objetivo es ponerte en marcha, brindándote una herramienta poderosa como Angular CLI y mostrándote cómo solo se necesitan unos pocos pasos para mostrar una aplicación en pantalla.

---

### Sección 1.7: Herramientas de Angular

Una de las razones por las que el framework Angular es popular entre los desarrolladores es el rico ecosistema de herramientas disponibles. La comunidad de Angular ha creado herramientas increíbles para completar y automatizar diversas tareas, como depurar, inspeccionar y crear aplicaciones Angular:

- Angular DevTools
- Depurador de VSCode
- Perfiles de VSCode (VSCode Profiles)

Aprenderemos a usar cada una en las siguientes secciones, comenzando con Angular DevTools.

#### Angular DevTools
**Angular DevTools** es una extensión de navegador creada y mantenida por el equipo de Angular. Nos permite inspeccionar y perfilar aplicaciones Angular directamente en el navegador. Actualmente es compatible con Google Chrome y Mozilla Firefox y se puede descargar desde las siguientes tiendas de navegadores:

- **Google Chrome:** [https://chrome.google.com/webstore/detail/angular-developer-tools/ienfalfjdbdpebioblfackkekamfmbnh](https://chrome.google.com/webstore/detail/angular-developer-tools/ienfalfjdbdpebioblfackkekamfmbnh)
- **Mozilla Firefox:** [https://addons.mozilla.org/firefox/addon/angular-devtools](https://addons.mozilla.org/firefox/addon/angular-devtools)

Para abrir la extensión, abre las herramientas de desarrollo del navegador y selecciona la pestaña **Angular**. Contiene tres pestañas adicionales:

- **Components:** Muestra el árbol de componentes de la aplicación Angular.
- **Profiler:** Nos permite perfilar e inspeccionar la aplicación Angular.
- **Injector Tree:** Muestra los servicios proporcionados por la aplicación Angular.

En este capítulo, exploraremos cómo usar la pestaña *Components*. Aprenderemos a usar la pestaña *Profiler* en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*, y la pestaña *Injector Tree* en el *Capítulo 5: Gestión de tareas complejas con Servicios*.

La pestaña *Components* nos permite previsualizar los componentes y directivas de una aplicación Angular e interactuar con ellos. Si seleccionamos un componente de la representación de árbol, podemos ver sus propiedades y metadatos:

*(Figura 1.6: Vista previa del componente)*

Desde la pestaña *Components*, también podemos buscar el elemento HTML correspondiente en el DOM o navegar hasta el código fuente real del componente o directiva. Al hacer clic en el botón `< >` iremos al archivo TypeScript del componente actual:

*(Figura 1.7: Archivo fuente de TypeScript)*

Hacer doble clic en un selector de la representación de árbol de la pestaña *Components* nos llevará al DOM de la página principal y resaltará el elemento HTML individual:

*(Figura 1.8: DOM de la página principal)*

Por último, una de las características más útiles del árbol de componentes es que podemos modificar el valor de una propiedad del componente e inspeccionar cómo se comporta la plantilla del componente:

*(Figura 1.9: Cambio de estado del componente)*

En la imagen anterior, puedes ver que cuando cambiamos el valor de la propiedad `title` a `Angular World`, el cambio también se reflejó en la plantilla del componente.

#### Depurador de VSCode
Podemos depurar una aplicación Angular utilizando técnicas estándar de depuración para aplicaciones web o las herramientas que VSCode proporciona de fábrica.

El objeto `console` es la API web más utilizada para depuración. Es una forma muy rápida de imprimir datos e inspeccionar valores en la consola del navegador. Para inspeccionar el valor de un objeto en un componente de Angular, podemos usar el método `debug` o `log`, pasando el objeto que queremos inspeccionar como parámetro. Sin embargo, se considera un enfoque anticuado, y una base de código con muchos métodos `console.log` es difícil de leer. Una forma alternativa es utilizar puntos de interrupción (*breakpoints*) dentro del código fuente utilizando el menú de depuración de VSCode.

**VSCode** es un editor de código abierto respaldado por Microsoft. Es muy popular en la comunidad de Angular, principalmente debido a su sólido soporte para TypeScript. TypeScript ha sido, en gran medida, un proyecto impulsado por Microsoft, por lo que tiene sentido que uno de sus editores populares fuera concebido con soporte integrado para este lenguaje. Contiene una rica colección de funciones útiles, que incluyen resaltado de sintaxis y errores, compilaciones automáticas y depuración.

VSCode contiene una herramienta de depuración integrada que utiliza puntos de interrupción para depurar aplicaciones Angular. Podemos agregar puntos de interrupción dentro del código fuente desde VSCode e inspeccionar el estado de una aplicación Angular. Cuando una aplicación Angular se ejecuta y alcanza un punto de interrupción, se pausará y esperará. Durante ese tiempo, podemos investigar e inspeccionar varios valores involucrados en el contexto de ejecución actual.

Veamos cómo agregar puntos de interrupción a nuestra aplicación de ejemplo:

1. Abre el archivo `app.component.ts` y haz clic a la izquierda de la línea 11 para agregar un punto de interrupción. Un punto rojo indica los puntos de interrupción:

*(Figura 1.10: Adición de un punto de interrupción)*

2. Haz clic en el botón **Run and Debug** en la barra lateral izquierda de VSCode.
3. Haz clic en el botón de reproducción (*play*) para iniciar la aplicación mediante el comando `ng serve`:

*(Figura 1.11: Menú de ejecución y depuración)*

4. VSCode compilará nuestra aplicación, abrirá el navegador web predeterminado y alcanzará el punto de interrupción dentro del editor:

*(Figura 1.12: Alcanzando un punto de interrupción)*

Ahora podemos inspeccionar varios aspectos de nuestro componente y usar los botones en la barra de herramientas del depurador para controlar la sesión de depuración.

Otra característica poderosa de VSCode son los perfiles de VSCode (**VSCode Profiles**), que ayudan a los desarrolladores a personalizar VSCode de acuerdo con sus necesidades de desarrollo.

#### Perfiles de VSCode (VSCode Profiles)
Los perfiles de VSCode nos permiten personalizar los siguientes aspectos del editor VSCode:

- **Configuración (Settings):** Las opciones de configuración de VSCode.
- **Atajos de teclado (Keyboard shortcuts):** Atajos para ejecutar comandos de VSCode con el teclado.
- **Fragmentos de código (Snippets):** Fragmentos de plantillas de código reutilizables.
- **Tareas (Tasks):** Tareas que automatizan la ejecución de scripts y herramientas directamente desde VSCode.
- **Extensiones (Extensions):** Herramientas que nos permiten agregar nuevas capacidades en VSCode, como lenguajes, depuradores y linters.

Los perfiles también se pueden compartir, lo que nos ayuda a mantener una configuración y un flujo de trabajo de desarrollo consistentes en todo nuestro equipo. VSCode contiene un conjunto de perfiles integrados, incluido uno para Angular, que podemos personalizar aún más según nuestras necesidades de desarrollo. Para instalar el perfil de Angular:

1. Haz clic en el botón **Manage** representado por el icono de engranaje en la parte inferior de la barra lateral izquierda en VSCode y selecciona la opción **Profiles**.
2. Haz clic en la flecha del botón **New Profile** y selecciona la opción **From Template | Angular**.
3. Haz clic en el botón de engranaje si deseas seleccionar un icono personalizado para tu perfil.
4. Haz clic en el botón **Create** para crear tu perfil.

VSCode aplicará automáticamente el nuevo perfil una vez que se haya creado con éxito.

En las siguientes secciones, exploraremos algunas de las extensiones en el perfil de Angular para VSCode.

#### Angular Language Service
La extensión **Angular Language Service** es desarrollada y mantenida por el equipo de Angular y proporciona autocompletado de código, navegación y detección de errores dentro de las plantillas de Angular. Enriquece a VSCode con las siguientes características:

- Autocompletado de código
- Ir a la definición (*Go-to definition*)
- Información rápida (*Quick info*)
- Mensajes de diagnóstico (*Diagnostic messages*)

Para tener una idea de sus potentes capacidades, veamos la función de autocompletado de código. Supongamos que queremos mostrar una nueva propiedad llamada `description` en la plantilla del componente principal. Podemos configurar esto siguiendo estos pasos:

1. Define la nueva propiedad en el archivo `app.component.ts`:

```typescript
export class AppComponent {
  title = 'my-app';
  description = 'Hello World';
}
```

2. Abre el archivo `app.component.html` y agrega el nombre de la propiedad en la plantilla usando la sintaxis de interpolación de Angular. Angular Language Service la encontrará y nos la sugerirá automáticamente:

*(Figura 1.13: Angular Language Service)*

La propiedad `description` es una propiedad pública. Podemos omitir la palabra clave `public` al usar propiedades y métodos públicos. El autocompletado de código no funciona para propiedades y métodos privados. Si la propiedad se hubiera declarado como privada, Angular Language Service y la plantilla no habrían podido reconocerla.

Es posible que hayas notado que apareció instantáneamente una línea roja debajo del elemento HTML mientras escribías. Angular Language Service no reconoció la propiedad hasta que la escribiste correctamente y te dio una indicación adecuada de esta falta de reconocimiento. Si pasas el cursor sobre la indicación roja, se muestra un mensaje informativo completo sobre lo que salió mal:

*(Figura 1.14: Manejo de errores en la plantilla)*

El mensaje informativo anterior proviene de la función de mensajes de diagnóstico. Angular Language Service admite varios mensajes según el caso de uso. Te encontrarás con más de estos mensajes a medida que trabajes más con Angular.

#### Material Icon Theme
VSCode tiene un conjunto integrado de iconos para mostrar diferentes tipos de archivos en un proyecto. La extensión **Material Icon Theme** proporciona iconos adicionales que se ajustan a las directrices de Material Design de Google; un subconjunto de esta colección está orientado a artefactos basados en Angular:

*(Figura 1.15: Material Icon Theme)*

Al usar esta extensión, puedes identificar fácilmente el tipo de archivos de Angular en un proyecto, como los componentes, y aumentar la productividad del desarrollador, especialmente en proyectos grandes con muchos archivos.

#### EditorConfig
La configuración del editor VSCode, como la sangría o el espaciado, se puede establecer a nivel de usuario o de proyecto. **EditorConfig** puede anular estas configuraciones utilizando el archivo de configuración `.editorconfig`, que se puede encontrar en la carpeta raíz de un proyecto de Angular CLI:

```ini
# Editor configuration, see https://editorconfig.org
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true

[*.ts]
quote_type = single
ij_typescript_use_double_quotes = false

[*.md]
max_line_length = off
trim_trailing_whitespace = false
```

Puedes definir configuraciones únicas en este archivo para garantizar la coherencia del estilo de codificación en todo tu equipo.

---

### Sección 1.8: Resumen

¡Eso es todo! Tu viaje en el mundo de Angular acaba de comenzar. Recapitulemos las características que has aprendido hasta ahora. Aprendimos qué es Angular, repasamos la breve historia del framework y examinamos los beneficios de usarlo para el desarrollo web.

Vimos cómo configurar nuestro espacio de trabajo de desarrollo y encontrar las herramientas para incorporar TypeScript al juego. Presentamos la herramienta Angular CLI, la navaja suiza para Angular, que automatiza tareas de desarrollo específicas. Utilizamos algunos de los comandos más comunes para generar el scaffolding de nuestra primera aplicación Angular. También examinamos la estructura de nuestra aplicación y aprendimos cómo interactuar con ella.

Nuestra primera aplicación nos brindó una comprensión básica de cómo funciona Angular internamente para renderizar nuestra aplicación en una página web. Nos embarcamos en nuestro viaje comenzando con el archivo HTML principal de una aplicación Angular. Vimos cómo Angular analiza ese archivo y comienza a buscar en el árbol de componentes para cargar el componente principal. Aprendimos el proceso de arranque de Angular (*bootstrapping*) y cómo se utiliza para cargar la configuración de la aplicación.

Finalmente, conocimos algunas de las herramientas de Angular más importantes que podrían empoderarte como desarrollador de software. Exploramos cómo usar Angular DevTools para inspeccionar aplicaciones Angular y el depurador de VSCode para la depuración. También examinamos los perfiles de VSCode y cómo pueden ayudarnos a mantener un entorno de desarrollo consistente en todo nuestro equipo.

En el próximo capítulo, aprenderás algunos de los conceptos básicos del lenguaje TypeScript. El capítulo cubrirá qué problemas se pueden resolver mediante la introducción de tipos y el lenguaje en sí. TypeScript, como superconjunto de JavaScript, contiene muchos conceptos poderosos y se combina a la perfección con el framework Angular, como estás a punto de descubrir.
