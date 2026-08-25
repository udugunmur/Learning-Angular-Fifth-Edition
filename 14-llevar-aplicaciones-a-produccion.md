# Parte 4: Calidad, Rendimiento y Producción

## Capítulo 14: Llevar aplicaciones a producción

Por lo general, una aplicación web debería ejecutarse en la web y ser accesible por cualquier persona y desde cualquier lugar. Necesita dos ingredientes esenciales: un servidor web que aloje la aplicación y una compilación de producción (*production build*) para desplegarla en ese servidor. En este capítulo, nos centraremos en la segunda parte de la receta.

En pocas palabras, una compilación de producción de una aplicación web es una versión optimizada del código de la aplicación que es más pequeña, más rápida y de mayor rendimiento. Principalmente, es un proceso que toma todos los archivos de código de la aplicación, aplica técnicas de optimización y los convierte en un único archivo de paquete (*bundle*).

En los capítulos anteriores, analizamos las diversas partes involucradas en la creación de una aplicación Angular. Solo necesitamos una última pieza para conectar los puntos y hacer que nuestra aplicación esté disponible para que cualquiera la use, que es compilarla y desplegarla en un servidor web.

En este capítulo, aprenderemos sobre los siguientes conceptos:

- Compilación de una aplicación Angular
- Limitación del tamaño del paquete de la aplicación
- Optimización del paquete de la aplicación
- Despliegue de una aplicación Angular

---

### Sección 14.1: Requisitos técnicos

El capítulo contiene varios ejemplos de código para guiarte a través del concepto de llevar aplicaciones a producción. Puedes encontrar el código fuente correspondiente en la carpeta `ch14` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch14](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch14)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 14.2: Compilación de una aplicación Angular

Para compilar una aplicación Angular, usamos el siguiente comando de Angular CLI:

```bash
ng build
```

El proceso de compilación inicia el compilador de Angular, que principalmente recopila todos los archivos TypeScript y HTML del código de nuestra aplicación y los convierte en JavaScript. Los archivos de hojas de estilo CSS como SCSS se convierten en archivos CSS puros. El proceso de compilación garantiza el renderizado rápido y óptimo de nuestra aplicación en el navegador.

Una aplicación Angular contiene varios archivos TypeScript que generalmente no se usan durante el tiempo de ejecución, como pruebas unitarias o asistentes de herramientas. El compilador sabe qué archivos recopilar para el proceso de compilación leyendo la propiedad `files` del archivo `tsconfig.app.json`:

```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app",
    "types": []
  },
  "files": [
    "src/main.ts"
  ],
  "include": [
    "src/**/*.d.ts"
  ]
}
```

El archivo `src/main.ts` es el punto de entrada principal de la aplicación y ayuda a Angular a recorrer todos los componentes, servicios y otros artefactos de Angular que nuestra aplicación necesita.

La salida del comando `ng build` se parece a la siguiente:

```text
Initial chunk files   | Names         | Raw size  | Estimated transfer size
main-N4USDVTP.js      | main          | 206.91 kB |                55.87 kB
polyfills-SCHOHYNV.js | polyfills     |  34.52 kB |                11.29 kB
styles-5INURTSO.css   | styles        |   0 bytes |                 0 bytes
                      | Initial total | 241.44 kB |                67.16 kB
```

Esta salida muestra los archivos JavaScript y CSS generados al compilar la aplicación Angular, a saber:

- **`main`:** El código de aplicación real que hemos escrito
- **`polyfills`:** Polyfills de funciones para navegadores más antiguos
- **`styles`:** Estilos CSS globales de nuestra aplicación

El compilador de Angular genera los archivos anteriores en una carpeta `dist\appName\browser`, donde `appName` es el nombre de la aplicación. También contiene los siguientes archivos:

- **`favicon.ico`:** El icono de la aplicación Angular
- **`index.html`:** El archivo HTML principal de la aplicación Angular

El comando `ng build` de Angular CLI se puede ejecutar en dos modos: desarrollo y producción. Por defecto, se ejecuta en modo de producción. Para ejecutarlo en modo de desarrollo, debemos ejecutar el siguiente comando de Angular CLI:

```bash
ng build --configuration=development
```

El comando anterior tendrá una salida similar a la siguiente:

```text
Initial chunk files | Names         | Raw size
main.js             | main          |  1.25 MB
polyfills.js        | polyfills     | 90.23 kB
styles.css          | styles        | 95 bytes
                    | Initial total |  1.35 MB
```

En la salida anterior, puedes notar que los nombres de los archivos de fragmentos iniciales (*Initial chunk files*) no contienen números hash, como en el caso de una compilación de producción. En el modo de producción, Angular CLI realiza varias técnicas de optimización en el código de la aplicación, como la optimización de imágenes y la compilación anticipada (*Ahead-of-Time* o AOT), de modo que la salida final sea adecuada para el alojamiento en un servidor web y un entorno de producción. El número hash agregado a cada archivo garantiza que la memoria caché de un navegador los invalide rápidamente al implementar una versión más nueva de la aplicación.

Cuando ejecutamos el comando `ng build` de Angular CLI en modo de desarrollo, usamos la opción `--configuration`. La opción `--configuration` nos permite ejecutar una aplicación Angular en diferentes entornos. Aprenderemos cómo definir entornos de Angular en la siguiente sección.

#### Compilación para diferentes entornos
Una organización puede desear compilar una aplicación Angular para múltiples entornos que requieren diferentes variables, como un endpoint de API backend y configuraciones locales de la aplicación. Un caso de uso común es un entorno de prueba (*staging*) para probar la aplicación antes de implementarla en producción.

Angular CLI nos permite definir diferentes configuraciones para cada entorno y compilar nuestra aplicación con cada una de ellas. Podemos ejecutar el comando `ng build` pasando el nombre de la configuración como parámetro usando la siguiente sintaxis:

```bash
ng build --configuration=name
```

> También podemos pasar una configuración en otros comandos de Angular CLI, como `ng serve` y `ng test`.

Podemos usar el siguiente comando de Angular CLI para comenzar a trabajar con entornos:

```bash
ng generate environments
```

Este comando creará una carpeta `src\environments` en el proyecto Angular que contiene los siguientes archivos:

- **`environment.ts`:** El entorno predeterminado de la aplicación, que se utiliza durante la producción
- **`environment.development.ts`:** El entorno de la aplicación utilizado durante el desarrollo

También agregará una sección `fileReplacements` en el archivo de configuración `angular.json` del proyecto Angular:

```json
"development": {
  "optimization": false,
  "extractLicenses": false,
  "sourceMap": true,
  "fileReplacements": [
    {
      "replace": "src/environments/environment.ts",
      "with": "src/environments/environment.development.ts"
    }
  ]
}
```

En el fragmento anterior, la propiedad `fileReplacements` define el archivo de entorno que reemplazará al predeterminado mientras se ejecuta el comando de compilación en el entorno de desarrollo. Si ejecutamos el comando `ng build --configuration=development`, Angular CLI reemplazará el archivo `environment.ts` con el archivo `environment.development.ts` en el paquete de la aplicación.

Cada archivo de entorno exporta un objeto `environment` donde podemos definir propiedades adicionales de la aplicación, como la URL de una API backend:

```typescript
export const environment = {
  apiUrl: 'https://my-default-url'
};
```

> Las mismas propiedades del objeto exportado deben definirse en todos los archivos de entorno.

Necesitamos importar el entorno predeterminado para acceder a una propiedad de entorno en una aplicación Angular. Por ejemplo, para usar la propiedad `apiUrl` en el componente principal de la aplicación, debemos hacer lo siguiente:

```typescript
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { environment } from '../environments/environment';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'my-app';
  apiUrl = environment.apiUrl;
}
```

No todas las librerías en una aplicación Angular se pueden importar como un módulo de JavaScript, como ocurre con la mayoría de las librerías oficiales de Angular. En la siguiente sección, aprenderemos a importar librerías que necesitan el objeto global `window`.

#### Compilación para el objeto window
Una aplicación Angular puede usar una librería como jQuery que debe adjuntarse al objeto `window`. Otras librerías, como Bootstrap, tienen fuentes, iconos y archivos CSS que deben incluirse en el paquete de la aplicación.

En todos estos casos, debemos informarle a Angular CLI sobre su existencia para que pueda incluirlos en el paquete final.

El archivo de configuración `angular.json` contiene un objeto `options` en la configuración de compilación que podemos usar para definir dichos archivos:

```json
"options": {
  "outputPath": "dist/my-app",
  "index": "src/index.html",
  "browser": "src/main.ts",
  "polyfills": [
    "zone.js"
  ],
  "tsConfig": "tsconfig.app.json",
  "assets": [
    {
      "glob": "**/*",
      "input": "public"
    }
  ],
  "styles": [
    "src/styles.css"
  ],
  "scripts": []
}
```

El objeto `options` contiene las siguientes propiedades que podemos usar:

- **`assets`:** Contiene archivos estáticos de la carpeta pública (`public`), como iconos, fuentes y traducciones.
- **`styles`:** Contiene archivos de hojas de estilo CSS externas. El archivo de hoja de estilo CSS global de la aplicación se incluye de forma predeterminada.
- **`scripts`:** Contiene archivos JavaScript externos.

A medida que agregamos más y más funciones a una aplicación Angular, el paquete final crecerá en algún momento. En la siguiente sección, aprenderemos cómo mitigar dicho efecto utilizando presupuestos (*budgets*).

---

### Sección 14.3: Limitación del tamaño del paquete de la aplicación

Como desarrolladores, siempre queremos crear aplicaciones impresionantes con características atractivas para el usuario final. Como tal, terminamos agregando más y más características a nuestra aplicación Angular, a veces de acuerdo con las especificaciones y otras veces para proporcionar valor adicional a los usuarios. Sin embargo, agregar nueva funcionalidad a una aplicación Angular hará que crezca en tamaño, lo que puede no ser aceptable en algún momento. Para superar este problema, podemos utilizar presupuestos (*budgets*).

Los presupuestos son umbrales que podemos definir en el archivo de configuración `angular.json` y podemos asegurarnos de que el tamaño de nuestra aplicación no exceda esos umbrales. Para establecer presupuestos, podemos usar la propiedad `budgets` de la configuración de producción en el comando de compilación:

```json
"budgets": [
  {
    "type": "initial",
    "maximumWarning": "500kB",
    "maximumError": "1MB"
  },
  {
    "type": "anyComponentStyle",
    "maximumWarning": "4kB",
    "maximumError": "8kB"
  }
]
```

Angular CLI define los presupuestos predeterminados anteriores al crear un nuevo proyecto de Angular CLI.

Podemos definir un presupuesto para diferentes tipos, como toda la aplicación Angular o algunas partes de ella. El umbral de un presupuesto se puede definir como bytes, kilobytes, megabytes o un porcentaje de los mismos. Angular CLI muestra una advertencia o genera un error cuando se alcanza el tamaño o se supera el valor definido del umbral.

Para entenderlo mejor, describamos el ejemplo predeterminado anterior:

- Se muestra una advertencia cuando el tamaño de la aplicación Angular supera los 500 KB y un error cuando supera 1 MB.
- Se muestra una advertencia cuando el tamaño del estilo de cualquier componente supera los 4 KB y un error cuando supera los 8 KB.

Para ver todas las opciones disponibles que puedes definir al configurar presupuestos en una aplicación Angular, consulta la guía en el sitio web de documentación oficial en [https://angular.dev/tools/cli/build/#configuring-size-budgets](https://angular.dev/tools/cli/build/#configuring-size-budgets).

Los presupuestos son excelentes para usar cuando queremos proporcionar un mecanismo de alerta en caso de que nuestra aplicación Angular crezca significativamente. Sin embargo, son solo un nivel de información y precaución. En la siguiente sección, aprenderemos cómo minimizar el tamaño de nuestro paquete.

---

### Sección 14.4: Optimización del paquete de la aplicación

Como aprendimos en la sección *Compilación de una aplicación Angular*, Angular CLI realiza técnicas de optimización cuando compilamos una aplicación Angular. El proceso de optimización que se realiza en el código de la aplicación incluye herramientas y técnicas web modernas, incluidas las siguientes:

- **Minificación (*Minification*):** Convierte archivos fuente de varias líneas en una sola línea, eliminando espacios en blanco y comentarios. Es un proceso que permite a los navegadores analizarlos más rápido más adelante.
- **Ofuscación (*Uglification*):** Cambia el nombre de propiedades y métodos a una forma no legible por humanos para que sean difíciles de entender y utilizar con fines maliciosos.
- **Empaquetado (*Bundling*):** Concatena todos los archivos fuente de la aplicación en un solo archivo, llamado paquete (*bundle*).
- **Eliminación de código no utilizado (*Tree-shaking*):** Elimina archivos y artefactos de Angular no utilizados, como componentes y servicios, lo que da como resultado un paquete más pequeño.
- **Optimización de fuentes (*Font optimization*):** Inserta archivos de fuentes externos directamente (*inline*) en el archivo HTML principal de la aplicación sin bloquear las solicitudes de procesamiento. Actualmente es compatible con Google Fonts y Adobe Fonts y requiere una conexión a Internet para descargarlas.
- **Caché de compilación (*Build cache*):** Almacena en caché el estado de compilación anterior y lo restaura cuando ejecutamos la misma compilación, lo que reduce el tiempo necesario para compilar la aplicación.

Si el paquete final de una aplicación Angular sigue siendo grande después de todas las técnicas de optimización anteriores, podemos usar una herramienta externa llamada `source-map-explorer` para investigar la causa. Quizás hayamos importado una librería de JavaScript dos veces o hayamos incluido un archivo no utilizado. La herramienta analiza el paquete de nuestra aplicación y muestra todos los artefactos y librerías de Angular que usamos en una representación visual. Para comenzar a usarlo, haz lo siguiente:

1. Instala el paquete npm `source-map-explorer` desde la terminal:

```bash
npm install source-map-explorer --save-dev
```

2. Compila tu aplicación Angular y habilita los mapas de origen (*source maps*):

```bash
ng build --source-map
```

3. Agrega el siguiente script en el archivo `package.json`:

```json
"scripts": {
  "ng": "ng",
  "start": "ng serve",
  "build": "ng build",
  "watch": "ng build --watch --configuration development",
  "test": "ng test",
  "analyze": "source-map-explorer"
}
```

4. Ejecuta el siguiente comando contra el archivo del paquete principal:

```bash
npm run analyze dist/my-app/browser/main*.js
```

Se abrirá una representación visual del paquete de la aplicación en el navegador:

*(Figura 14.1: Salida de source-map-explorer)*

> **Consejo rápido:** ¿Necesitas ver una versión de alta resolución de esta imagen? Abre este libro en el lector Packt de última generación o consúltalo en la copia PDF/ePub: [https://www.packtpub.com/unlock/9781835087480](https://www.packtpub.com/unlock/9781835087480).

Luego podemos interactuar con él e inspeccionarlo para comprender por qué nuestro paquete todavía es demasiado grande. Algunas causas pueden ser las siguientes:

- Una librería se incluye dos veces en el paquete.
- Una librería que no admite tree-shaking está incluida pero no se utiliza actualmente.

El último paso después de compilar nuestra aplicación Angular es desplegarla en un servidor web, como aprenderemos en la siguiente sección.

---

### Sección 14.5: Despliegue de una aplicación Angular

Si ya tienes un servidor web que deseas utilizar para tu aplicación Angular, puedes copiar el contenido de la carpeta de salida a una ruta en ese servidor. Si deseas desplegarla en otra carpeta que no sea la raíz, puedes cambiar el atributo `href` de la etiqueta `<base>` en el archivo HTML principal de las siguientes maneras:

1. Pasando la opción `--base-href` en el comando `ng build`:

```bash
ng build --base-href=/mypath/
```

2. Estableciendo la propiedad `baseHref` en el comando de compilación del archivo de configuración `angular.json`:

```json
"options": {
  "outputPath": "dist/my-app",
  "index": "src/index.html",
  "browser": "src/main.ts",
  "baseHref": "/mypath/",
  "polyfills": [
    "zone.js"
  ],
  "tsConfig": "tsconfig.app.json",
  "assets": [
    {
      "glob": "**/*",
      "input": "public"
    }
  ],
  "styles": [
    "src/styles.css"
  ],
  "scripts": []
}
```

Si no deseas realizar el despliegue en un servidor personalizado, puedes utilizar las herramientas de Angular CLI para desplegarlo en un proveedor de alojamiento compatible, que puedes encontrar en [https://angular.dev/tools/cli/deployment#automatic-deployment-with-the-cli](https://angular.dev/tools/cli/deployment#automatic-deployment-with-the-cli).

---

### Sección 14.6: Resumen

El despliegue de una aplicación Angular es la parte más simple y crucial porque finalmente hace que tu increíble aplicación esté disponible para el usuario final. Al final del día, las aplicaciones web tienen como objetivo brindar experiencias al usuario final.

En este capítulo, aprendimos cómo compilar una aplicación Angular y prepararla para producción. También investigamos diferentes formas de optimizar el paquete final y aprendimos cómo implementar una aplicación Angular en un servidor personalizado, de forma manual y automática, para otros proveedores de alojamiento.

En el próximo capítulo, que también es el capítulo final del libro, aprenderemos cómo mejorar el rendimiento de una aplicación Angular.
