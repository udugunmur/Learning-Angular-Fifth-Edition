# Parte 4: Calidad, Rendimiento y Producción

## Capítulo 15: Optimización del rendimiento de la aplicación

Como desarrolladores y profesionales técnicos, desempeñamos un papel crucial en la creación y el despliegue de aplicaciones Angular, garantizando su rendimiento continuo y brindando una experiencia de usuario superior. Nuestros esfuerzos son fundamentales para el éxito de nuestras aplicaciones.

El comportamiento de una aplicación web y su rendimiento durante el tiempo de ejecución son consideraciones clave para el monitoreo y la optimización. Debemos monitorear y medir el rendimiento de la aplicación en caso de que comience a degradarse. Una de las métricas más populares para identificar problemas en aplicaciones web es **Core Web Vitals (CWV)**.

Después de determinar las causas de la degradación, podemos aplicar varias técnicas de optimización. El framework Angular proporciona varias herramientas para optimizar aplicaciones Angular, incluido el renderizado del lado del servidor (*Server-Side Rendering* o SSR), la optimización de imágenes y la carga de vistas diferidas (*deferrable views*). Si sabemos de antemano que la aplicación requerirá un uso intensivo del rendimiento, también se recomienda encarecidamente el uso de cualquiera de las herramientas anteriores en las primeras etapas del desarrollo.

En este capítulo, exploraremos los siguientes conceptos de Angular con respecto a la optimización:

- Introducción a Core Web Vitals
- Renderizado de aplicaciones SSR
- Optimización de la carga de imágenes
- Diferimiento de componentes
- Prerrenderizado de aplicaciones SSG

---

### Sección 15.1: Requisitos técnicos

El capítulo contiene varios ejemplos de código para guiarte a través del concepto de optimización de aplicaciones Angular. Puedes encontrar el código fuente correspondiente en la carpeta `ch15` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch15](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch15)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 15.2: Introducción a Core Web Vitals

CWV es un conjunto de métricas que nos ayuda a medir el rendimiento de una aplicación web. Es parte de Web Vitals, una iniciativa liderada por Google que unifica varias guías y herramientas para medir el rendimiento en páginas web. Cada métrica se centra en un aspecto específico de la experiencia del usuario, incluida la carga, la interactividad y la estabilidad visual de una página web:

- **Largest Contentful Paint (LCP):** Mide la velocidad de carga de una página web calculando cuánto tiempo tarda en renderizarse el elemento más grande de la página. Un valor de LCP rápido indica que la página está disponible para el usuario rápidamente.
- **Interaction to Next Paint (INP):** Mide la capacidad de respuesta de una página web calculando cuánto tiempo tarda en responder a las interacciones del usuario y proporcionar retroalimentación visual. Un valor de INP bajo indica que la página responde al usuario rápidamente.
- **Cumulative Layout Shift (CLS):** Mide la estabilidad de la interfaz de usuario en una página web calculando con qué frecuencia ocurren cambios de diseño no deseados. Un cambio de diseño generalmente ocurre cuando los elementos HTML se mueven en el DOM debido a una carga dinámica o asíncrona. Un valor de CLS bajo indica que la página es visualmente estable.

> Web Vitals contiene métricas adicionales que contribuyen al conjunto de CWV existente al medir un área más amplia o más específica de UX, como First Contentful Paint (FCP) y Time to First Byte (TTFB).

El valor de cada métrica de CWV se divide en las siguientes categorías:

- **GOOD** (verde / bueno)
- **NEEDS IMPROVEMENT** (naranja / necesita mejora)
- **POOR** (rojo / pobre)

Puedes obtener más información sobre las categorías de CWV y sus umbrales en [https://web.dev/articles/vitals#core-web-vitals](https://web.dev/articles/vitals#core-web-vitals).

Podemos medir CWV de las siguientes maneras:

- **En el campo (*In the field*):** Podemos usar herramientas como PageSpeed Insights y Chrome User Experience Report mientras la aplicación web se ejecuta en producción.
- **Programáticamente en JavaScript:** Podemos usar APIs web estándar o librerías de terceros como `web-vitals`.
- **En el laboratorio (*In the lab*):** Podemos usar herramientas como Chrome DevTools y Lighthouse mientras creamos la aplicación web durante el desarrollo.

En este capítulo, aprenderemos a usar Chrome DevTools para medir el rendimiento de nuestra aplicación de tienda online:

1. Copia el código fuente del *Capítulo 12: Introducción a Angular Material* en una nueva carpeta.
2. Ejecuta el siguiente comando dentro de la nueva carpeta para instalar las dependencias del paquete:

```bash
npm install
```

3. Ejecuta el siguiente comando para iniciar la aplicación Angular:

```bash
ng serve
```

4. Abre Google Chrome y navega a `http://localhost:4200`.
5. Abre las herramientas de desarrollo y selecciona la pestaña **Lighthouse**. Lighthouse es una herramienta para medir varios aspectos del rendimiento de una página web, incluido CWV. Google Chrome tiene una versión integrada de Lighthouse que podemos usar para evaluar nuestra aplicación:

*(Figura 15.1: Pestaña Lighthouse)*

En la pantalla que se muestra en la imagen anterior, podemos generar un informe de rendimiento de Lighthouse seleccionando varias opciones, incluidas las secciones *Device* y *Categories*. La sección *Device* nos permite especificar el entorno en el que queremos medir nuestra aplicación. La sección *Categories* nos permite evaluar diferentes métricas, incluido *Performance*, relacionadas con CWV.

6. Selecciona la opción **Desktop** en la sección *Device*, marca solo la opción **Performance** en la sección *Categories* y haz clic en el botón **Analyze page load**:

*(Figura 15.2: Informe de Lighthouse)*

En la imagen anterior, podemos ver la puntuación individual de las métricas de CWV y la puntuación de rendimiento general.

> La puntuación de rendimiento general es una estimación y puede variar según las capacidades de tu ordenador o cualquier extensión de navegador instalada. Es preferible ejecutar la prueba comparativa en modo incógnito o privado para simular un entorno más cercano a un escenario del mundo real.

En las siguientes secciones, exploraremos formas de mejorar la puntuación de rendimiento aplicando las mejores prácticas de Angular. Comenzaremos con SSR.

---

### Sección 15.3: Renderizado de aplicaciones SSR

SSR es una técnica en el desarrollo web que mejora el rendimiento y la seguridad de las aplicaciones de las siguientes maneras:

- **Mejora el rendimiento de carga:** Renderiza la aplicación en el servidor y entrega el contenido HTML inicial al cliente. El servidor entrega el HTML inicial al cliente, que puede analizarse y cargarse mientras espera a que se descargue el contenido de JavaScript.
- **Mejora la optimización para motores de búsqueda (SEO):** Hace que la aplicación sea detectable e indexable por rastreadores web (*web crawlers*). El SEO proporciona contenido significativo cuando se comparte en aplicaciones de terceros, como plataformas de redes sociales.
- **Mejora las métricas de CWV:** Beneficia las métricas relacionadas con la velocidad de carga y la estabilidad de la interfaz de usuario, como LCP, FCP y CLS.
- **Mejora la seguridad:** Añade nonces CSP a las aplicaciones Angular.

Como vimos en el *Capítulo 1: Creación de tu primera aplicación Angular*, cuando creamos una nueva aplicación utilizando Angular CLI, nos solicitó habilitar SSR:

```text
Do you want to enable Server-Side Rendering (SSR) and Static Site Generation (SSG/Prerendering)? (y/N)
```

En nuestro caso, ya hemos creado una aplicación Angular utilizando Angular CLI. Para agregar SSR en una aplicación Angular existente, ejecuta el siguiente comando en una ventana de terminal dentro del espacio de trabajo de Angular CLI:

```bash
ng add @angular/ssr
```

El comando anterior nos hará la siguiente pregunta:

```text
Would you like to use the Server Routing and App Engine APIs (Developer Preview) for this server application? (y/N)
```

Acepta el valor predeterminado, `No`, presionando Enter y Angular CLI nos pedirá que instalemos el paquete npm `@angular/ssr`.

> Una función en Developer Preview significa que aún no está lista para producción, pero puedes probarla en tu entorno de desarrollo.

Una vez finalizada la instalación, Angular CLI crea los siguientes archivos:

- **`main.server.ts`:** Se utiliza para inicializar la aplicación en el servidor mediante una configuración específica.
- **`app.config.server.ts`:** Contiene la configuración para la aplicación renderizada en el servidor. Exporta una variable `config`, que contiene una versión combinada de los archivos de configuración de la aplicación de cliente y de servidor.
- **`server.ts`:** Configura e inicia un servidor Node.js Express que renderiza la aplicación Angular en el servidor. Utiliza la clase `CommonEngine` del paquete `@angular/ssr` para iniciar la aplicación Angular.

Además, el comando realizará las siguientes modificaciones en el espacio de trabajo de Angular CLI:

- Agregará las opciones necesarias en la sección `build` del archivo `angular.json` para ejecutar la aplicación Angular en SSR y SSG.
- Agregará las entradas necesarias en las propiedades `files` y `types` del archivo `tsconfig.app.json` para que el compilador de TypeScript pueda identificar los archivos creados para el servidor.
- Agregará los scripts y dependencias necesarios en el archivo `package.json`.
- Agregará `provideClientHydration` en el archivo `src\app\app.config.ts` para habilitar la hidratación en la aplicación Angular. La hidratación es el proceso de restaurar la aplicación renderizada del lado del servidor en el cliente. Aprenderemos más sobre la hidratación más adelante en el capítulo.

Ahora que hemos instalado Angular SSR en nuestra aplicación, veamos cómo usarlo:

1. Abre el archivo `app.config.ts` y modifica la sentencia de importación del espacio de nombres `@angular/common/http` de la siguiente manera:

```typescript
import { provideHttpClient, withFetch } from '@angular/common/http';
```

El método `withFetch` se utiliza para configurar el cliente HTTP de Angular para que utilice la API nativa `fetch` para realizar solicitudes.

> Se recomienda encarecidamente habilitar `fetch` para aplicaciones que usan SSR para un mejor rendimiento y compatibilidad.

2. Pasa el método `withFetch` como parámetro en el método `provideHttpClient`:

```typescript
provideHttpClient(withFetch())
```

3. Ejecuta el siguiente comando para compilar la aplicación Angular:

```bash
ng build
```

El comando anterior genera paquetes de navegador y de servidor dentro de la carpeta `dist\my-app` y prerrenderiza rutas estáticas. Aprenderemos más sobre el prerrenderizado en la sección *Prerrenderizado de aplicaciones SSG*.

4. Ejecuta el siguiente comando para ejecutar la aplicación SSR:

```bash
npm run serve:ssr:my-app
```

El comando anterior iniciará el servidor Express localmente en el puerto 4000 y servirá la aplicación SSR.

5. Abre Google Chrome y navega a `http://localhost:4000`. Deberías ver la aplicación de la tienda online en la página web.
6. Repite el proceso que aprendimos en la sección anterior para ejecutar una evaluación de rendimiento usando Lighthouse. La puntuación general y las métricas de CWV deberían haber mejorado drásticamente:

*(Figura 15.3: Informe de Lighthouse (SSR))*

¡El rendimiento de nuestra aplicación ha mejorado en más del 20 % simplemente instalando SSR en nuestra aplicación Angular! Como aprenderemos más adelante en el capítulo, podemos aplicar varias técnicas de Angular para mejorar aún más el rendimiento.

> Angular SSR es una buena opción cuando necesitamos obtener datos del servidor y mostrarlos estáticamente en un sitio web. Sin embargo, hay casos en los que SSR no es beneficioso, como cuando una aplicación se basa en la entrada de datos y tiene muchas entradas de usuario.

En la siguiente sección, aprenderemos cómo anular SSR o omitirlo por completo para ciertas partes de una aplicación Angular.

#### Anulación de SSR en aplicaciones Angular
La hidratación (*hydration*) es una característica importante habilitada de forma predeterminada en las aplicaciones Angular SSR. Mejora el rendimiento general de la aplicación al manejar la creación del DOM en el cliente de manera eficiente. El cliente puede reutilizar la estructura DOM de la aplicación renderizada en el servidor en lugar de crearla desde cero y forzar un parpadeo en la interfaz de usuario (*UI flicker*), lo que afecta a métricas de CWV como LCP y CLS. El proceso de hidratación fallará en los siguientes casos:

- Cuando intentamos manipular el DOM a través de una API de navegador nativa como `window` o `document`, ya sea directamente o usando una librería de terceros.
- Cuando nuestras plantillas de componentes no tienen una sintaxis HTML válida.

Podemos superar los problemas anteriores aplicando las siguientes mejores prácticas:

1. Usar APIs de Angular para detectar la plataforma en la que se ejecuta nuestra aplicación antes de interactuar con el DOM.
2. Omitir la hidratación para componentes específicos de Angular.

Veamos cómo usar ambos con un ejemplo:

1. Ejecuta la versión SSR de la aplicación Angular, como se muestra en la sección anterior.
2. Observa el texto que se muestra en el pie de página de la aplicación:

```text
- v1.0
```

La información de derechos de autor no se muestra correctamente.

3. Abre el archivo `copyright.directive.ts` y enfócate en el código del constructor:

```typescript
constructor(el: ElementRef) {
  const currentYear = new Date().getFullYear();
  const targetEl: HTMLElement = el.nativeElement;
  targetEl.classList.add('copyright');
  targetEl.textContent = `Copyright ©${currentYear} All Rights Reserved`;
}
```

El código anterior utiliza la propiedad `nativeElement` para manipular el DOM agregando una clase CSS y estableciendo el `textContent` del elemento HTML. Sin embargo, como se mencionó, el código rompe nuestra aplicación porque no hay DOM en el servidor. ¡Arreglemos eso!

4. Abre el archivo `app.component.html` y agrega el atributo `ngSkipHydration` en el elemento `<mat-toolbar>` de la etiqueta HTML `<footer>`:

```html
<footer>
  <mat-toolbar ngSkipHydration>
    <mat-toolbar-row>
      <span appCopyright> - v{{ settings.version }}</span>
    </mat-toolbar-row>
  </mat-toolbar>
</footer>
```

> `ngSkipHydration` es un atributo HTML, no una directiva de Angular. Solo se puede utilizar en otros componentes de Angular, no en elementos HTML nativos. No funcionaría si lo hubiéramos agregado en la etiqueta `<footer>` en su lugar.

En el fragmento anterior, el componente `<mat-toolbar>` y sus componentes secundarios no se hidratarán. Esto significa efectivamente que Angular los creará desde cero cuando la versión SSR de la aplicación esté lista.

5. Vuelve a ejecutar el paso 1 y observa la salida en el pie de página de la aplicación:

```text
Copyright ©2024 All Rights Reserved - v1.0
```

> Omitir la hidratación debe considerarse una solución temporal (*workaround*). La usamos temporalmente en los casos en que la hidratación no se puede habilitar. Se recomienda refactorizar tu código para que tu aplicación pueda beneficiarse de las capacidades de hidratación.

Un enfoque alternativo y mejor es refactorizar nuestro código para que ejecute el código del cliente condicionalmente:

6. Modifica las sentencias de importación en el archivo `copyright.directive.ts` de la siguiente manera:

```typescript
import { isPlatformBrowser } from '@angular/common';
import { Directive, ElementRef, inject, OnInit, PLATFORM_ID } from '@angular/core';
```

`PLATFORM_ID` es un `InjectionToken` que indica el tipo de plataforma en la que se ejecuta actualmente nuestra aplicación. La función `isPlatformBrowser` comprueba si un ID de plataforma determinado es el navegador.

7. Agrega la interfaz `OnInit` a la lista de interfaces implementadas de la clase `CopyrightDirective`:

```typescript
export class CopyrightDirective implements OnInit
```

8. Agrega las siguientes propiedades de clase:

```typescript
private platform = inject(PLATFORM_ID);
private el = inject(ElementRef);
```

9. Elimina el constructor y agrega el siguiente método `ngOnInit`:

```typescript
ngOnInit(): void {
  if (isPlatformBrowser(this.platform)) {
    const currentYear = new Date().getFullYear();
    const targetEl: HTMLElement = this.el.nativeElement;
    targetEl.classList.add('copyright');
    targetEl.textContent = `Copyright ©${currentYear} All Rights Reserved ${targetEl.textContent}`;
  }
}
```

La función `isPlatformBrowser` acepta el ID de plataforma como parámetro.

> Angular también proporciona la función `isPlatformServer`, una contraparte de la función `isPlatformBrowser`, que verifica si la plataforma actual es el servidor.

10. Compila y ejecuta la aplicación en modo del lado del servidor para verificar que el mensaje de copyright siga siendo visible.

En resumen, se recomienda que utilices Angular SSR en toda tu aplicación y refactorices las partes del código de la aplicación que deben ejecutarse en el navegador. Esto te permitirá aprovechar todos los beneficios de una aplicación renderizada en el servidor.

En la sección anterior, demostramos que agregar SSR a una aplicación Angular mejora drásticamente su puntaje de rendimiento general. Como aprenderemos en la siguiente sección, podemos hacerlo aún mejor aplicando técnicas de optimización a las imágenes de los productos.

---

### Sección 15.4: Optimización de la carga de imágenes

La lista de productos, que es el componente de aterrizaje de nuestra aplicación, muestra una imagen de cada producto en la lista. La forma en que se cargan las imágenes en una aplicación Angular puede afectar a métricas de CWV como LCP y CLS. Nuestra aplicación actualmente carga imágenes tal como se reciben de la Fake Store API. Sin embargo, podemos usar artefactos específicos de Angular para hacer cumplir las mejores prácticas al cargar imágenes.

El framework Angular nos proporciona la directiva `NgOptimizedImage`, que podemos adjuntar a los elementos HTML `<img>`:

1. Abre el archivo `product-list.component.ts` e importa la clase `NgOptimizedImage` del paquete npm `@angular/common`:

```typescript
import { AsyncPipe, CurrencyPipe, NgOptimizedImage } from '@angular/common';
```

2. Agrega la clase `NgOptimizedImage` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-list',
  imports: [
    SortPipe,
    AsyncPipe,
    CurrencyPipe,
    RouterLink,
    MatMiniFabButton,
    MatIcon,
    MatCardModule,
    MatTableModule,
    MatButtonToggle,
    MatButtonToggleGroup,
    NgOptimizedImage
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

3. Abre el archivo `product-list.component.html` y reemplaza el enlace de la propiedad `src` con la directiva `ngSrc`:

```html
<mat-card-title-group>
  <mat-card-title>{{ product.title }}</mat-card-title>
  <mat-card-subtitle>{{ product.category }}</mat-card-subtitle>
  <img mat-card-sm-image [ngSrc]="product.image" />
</mat-card-title-group>
```

La directiva `ngSrc` no es suficiente para evitar cambios de diseño (*layout shifts*) al cargar la imagen. También debemos establecer el tamaño de la imagen definiendo los atributos `width`, `height` o `fill`. En este caso, usaremos este último porque el tamaño de cada imagen no es el mismo para todos los productos:

```html
<img mat-card-sm-image [ngSrc]="product.image" fill />
```

4. Abre el archivo `product-list.component.css` y agrega los siguientes estilos CSS para colocar la imagen en la parte superior derecha del contenedor:

```css
img {
  object-fit: contain;
  object-position: right 5px top 0;
}
```

5. Ejecuta el siguiente comando para iniciar la aplicación:

```bash
ng serve
```

6. Navega a `http://localhost:4200` y verifica que la lista de productos se muestre correctamente.

Los beneficios adquiridos al utilizar la directiva `NgOptimizedImage` no se notan de inmediato en la interfaz de usuario. La directiva funciona en segundo plano y mejora automáticamente la métrica LCP de CWV mediante:

- Establecer la prioridad de obtención (*fetch priority*) en el elemento HTML `<img>`.
- Carga diferida de imágenes (*lazy loading*).
- Establecer etiquetas de enlace de preconexión (*preconnect*) y sugerencias de precarga (*preload*) en el caso de SSR.
- Generar atributos `srcset` para imágenes receptivas.

Además, ayuda a los desarrolladores a seguir las mejores prácticas con respecto a la carga de imágenes, tales como:

- Establecer el tamaño de la imagen si se conoce de antemano.
- Cargar imágenes a través de una CDN.
- Mostrar advertencias apropiadas en la ventana de la consola para diferentes métricas.

> La directiva `NgOptimizedImage` contiene muchas otras características que podemos habilitar para lograr mejoras de rendimiento potentes, como configurar cargadores de imágenes (*image loaders*), usar marcadores de posición (*placeholders*) y definir imágenes prioritarias para cargar. Puedes encontrar más información en [https://angular.dev/guide/image-optimization](https://angular.dev/guide/image-optimization).

Ya hemos aprendido acerca de varias herramientas para mejorar el rendimiento de las aplicaciones. Una de las herramientas de mayor rendimiento son las vistas diferibles (*deferrable views*), que conoceremos en la siguiente sección.

---

### Sección 15.5: Diferimiento de componentes

La introducción de la nueva sintaxis de flujo de control permitió a Angular integrar nuevas primitivas en el framework, mejorando la ergonomía, la DX y el rendimiento de las aplicaciones Angular. Una de estas primitivas son las vistas diferibles (*deferrable views*), que permiten la carga diferida (*lazy loading*) de un componente de Angular y sus dependencias.

#### Introducción a las vistas diferibles
Ya hemos aprendido a utilizar el enrutador de Angular para cargar de forma diferida un componente según una ruta específica. Las vistas diferibles proporcionan una nueva API que complementa a la anterior. Combinarlo con el enrutamiento de carga diferida garantiza el desarrollo de aplicaciones web potentes y de alto rendimiento. Las vistas diferibles nos permiten cargar de forma diferida un componente en función de un evento o del estado del componente y tienen las siguientes características:

- Son fáciles de usar y es fácil razonar sobre el código que encierran.
- Las definimos de forma declarativa.
- Minimizan la carga inicial de la aplicación y el tamaño del paquete final, mejorando las métricas de CWV como LCP y TTFB.

Cada vista diferible se divide en un fragmento (*chunk*) independiente, similar a los archivos de fragmentos individuales generados por rutas de carga diferida. Consisten en los siguientes bloques HTML:

- **`@defer`:** Indica el contenido HTML que se cargará de forma diferida.
- **`@placeholder`:** Indica el contenido HTML que se muestra antes de que el bloque `@defer` comience a cargarse. Es particularmente útil cuando la aplicación se carga en una red lenta o cuando queremos evitar el parpadeo de la interfaz de usuario.
- **`@loading`:** Indica el contenido HTML que será visible mientras se carga el bloque `@defer`.
- **`@error`:** Indica el contenido HTML que se muestra si se produce un error mientras se carga el bloque `@defer`.

Aprenderemos a usar cada bloque en la siguiente sección.

#### Uso de bloques diferibles
Integraremos vistas diferibles en nuestra aplicación de tienda online creando un componente que muestre un producto destacado de la Fake Store API que no se encuentre actualmente en la lista de productos. Empecemos:

1. Ejecuta el siguiente comando para crear el nuevo componente:

```bash
ng generate component featured
```

2. Abre el archivo `products.service.ts` y agrega el siguiente método, que obtiene un producto específico con ID 20 de la Fake Store API:

```typescript
getFeatured(): Observable<Product> {
  return this.http.get<Product>(this.productsUrl + '/20');
}
```

3. Abre el archivo `featured.component.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { Component, OnInit } from '@angular/core';
import { CommonModule } from '@angular/common';
import { MatButton } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';
import { Observable } from 'rxjs';
import { Product } from '../product';
import { ProductsService } from '../products.service';
```

4. Modifica el array `imports` del decorador `@Component` de la siguiente manera:

```typescript
@Component({
  selector: 'app-featured',
  imports: [CommonModule, MatButton, MatCardModule],
  templateUrl: './featured.component.html',
  styleUrl: './featured.component.css'
})
```

5. Modifica la clase `FeaturedComponent` de la siguiente manera:

```typescript
export class FeaturedComponent implements OnInit {
  product$: Observable<Product> | undefined;
  constructor(private productService: ProductsService) {}

  ngOnInit() {
    this.product$ = this.productService.getFeatured();
  }
}
```

En la clase TypeScript anterior, declaramos el observable `product$` y lo asignamos al valor devuelto del método `getFeatured` de la clase `ProductsService`.

6. Abre el archivo `featured.component.html` y reemplaza su contenido con el siguiente código HTML:

```html
@if (product$ | async; as product) {
  <mat-card>
    <mat-card-header>
      <mat-card-title>MEGA DEAL</mat-card-title>
      <mat-card-subtitle>{{ product.title }}</mat-card-subtitle>
    </mat-card-header>
    <img mat-card-image [src]="product.image" />
    <mat-card-actions>
      <button mat-flat-button color="primary">Buy now</button>
    </mat-card-actions>
  </mat-card>
}
```

En el fragmento anterior, usamos el pipe `async` para suscribirnos al observable `product$` dentro del bloque `@if`. El contenido HTML del bloque muestra los detalles del producto como un componente de tarjeta de Angular Material.

7. Abre el archivo `featured.component.css` y agrega los siguientes estilos CSS para los componentes de tarjeta y botón:

```css
mat-card {
  max-width: 350px;
}

button {
  width: 100%;
}
```

El nuevo componente de Angular está en su lugar. Debemos agregarlo al componente principal de la aplicación y usar un bloque `@defer` para cargarlo:

8. Abre el archivo `app.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { FeaturedComponent } from './featured/featured.component';
```

9. Agrega la clase `FeaturedComponent` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    RouterLink,
    CopyrightDirective,
    AuthComponent,
    MatToolbarRow,
    MatToolbar,
    MatButton,
    MatBadge,
    FeaturedComponent
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
```

10. Abre el archivo `app.component.html` y agrega el componente `<app-featured>` dentro de la etiqueta HTML `<main>`:

```html
<main class="main">
  <div class="content">
    <router-outlet />
  </div>
  @defer() {
    <app-featured />
  }
</main>
```

En el fragmento anterior, usamos el bloque `@defer` para declarar el componente `<app-featured>` usando la sintaxis de etiqueta de autocierre (*self-closing tag*).

11. Ejecuta el comando `ng serve` para iniciar la aplicación y observa la sección *Lazy chunk files* en la ventana de la terminal:

```text
Lazy chunk files   | Names              | Raw size
chunk-OP24QI45.mjs | featured-component |  2.88 kB |
chunk-4T4L5V7V.mjs | user-routes        |  1.19 kB |
```

El código fuente del componente destacado se divide en un archivo de fragmento (*chunk*).

12. Navega a `http://localhost:4200` y observa el nuevo componente en el lado derecho de la lista de productos:

*(Figura 15.4: Producto destacado)*

Intenta recargar el navegador y notarás un parpadeo en la interfaz de usuario mientras se carga el producto destacado. Usaremos el bloque `@placeholder` para mostrar una imagen de contorno antes de que el componente destacado comience a cargarse:

13. Copia la imagen `placeholder.png` de la carpeta `public` del repositorio de GitHub descrito en la sección *Requisitos técnicos* a la carpeta respectiva de tu espacio de trabajo.
14. Agrega un bloque `@placeholder` después del bloque `@defer` de la siguiente manera:

```html
@defer() {
  <app-featured />
} @placeholder(minimum 1s) {
  <img src="placeholder.png" />
}
```

El bloque `@placeholder` acepta un parámetro opcional que define el tiempo mínimo que estará visible el marcador de posición. En este caso, hemos definido el tiempo mínimo como 1 segundo.

15. Ejecuta la aplicación usando el comando `ng serve` y verifica que la siguiente imagen de marcador de posición sea visible durante 1 segundo antes de que se cargue el contenido real:

*(Figura 15.5: Imagen de marcador de posición)*

Un enfoque alternativo sería usar el bloque `@loading` y mostrar un indicador de carga, como un spinner, mientras se carga el componente destacado:

16. Abre el archivo `app.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { MatProgressSpinner } from '@angular/material/progress-spinner';
```

La clase `MatProgressSpinner` es un componente de spinner de la librería Angular Material.

17. Agrega la clase `MatProgressSpinner` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    RouterLink,
    CopyrightDirective,
    AuthComponent,
    MatToolbarRow,
    MatToolbar,
    MatButton,
    MatBadge,
    FeaturedComponent,
    MatProgressSpinner
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
```

18. Agrega el bloque `@loading` en el archivo `app.component.html` de la siguiente manera:

```html
@defer() {
  <app-featured />
} @loading(minimum 1s) {
  <mat-spinner ngSkipHydration></mat-spinner>
}
```

El bloque `@loading` acepta los mismos parámetros opcionales que el bloque `@placeholder`. En este caso, mostramos el componente spinner durante 1 segundo como mínimo.

> Agregamos el atributo `ngSkipHydration` porque el componente spinner interactúa con el DOM del navegador y no se puede hidratar.

Si ejecutamos la aplicación usando el comando `ng serve`, deberíamos ver una indicación de spinner durante 1 segundo mientras se carga el componente destacado.

El bloque `@error` en vistas diferibles funciona de manera similar a los bloques `@placeholder` y `@loading`. El contenido HTML dentro de él será visible cuando ocurra un error al cargar el contenido del bloque `@defer`:

```html
@defer() {
  <app-featured />
} @placeholder(minimum 1s) {
  <img src="placeholder.png" />
} @error() {
  <span>An error occurred while loading the featured product</span>
}
```

Como hemos visto, el contenido de un bloque `@defer` comienza a cargarse inmediatamente cuando se renderiza el componente al que pertenece. Sin embargo, la API de vistas diferibles nos proporciona herramientas ergonómicas para controlar cuándo se cargará el bloque, como veremos en la siguiente sección.

#### Patrones de carga en bloques @defer
Mediante el uso de disparadores (*triggers*) y mecanismos de precarga (*prefetch*), podemos controlar cómo y cuándo se cargará un bloque `@defer`:

- Los **disparadores (*triggers*)** definen cuándo comienza a cargarse el contenido del bloque.
- La **precarga (*prefetch*)** define si Angular recuperará el contenido de antemano para que esté disponible cuando sea necesario.

Podemos definir un disparador como un parámetro opcional dentro del bloque `@defer` usando la palabra clave `on` y el nombre del disparador:

```html
@defer(on viewport) {
  <app-featured />
} @placeholder(minimum 1s) {
  <img src="placeholder.png" />
} @error() {
  <span>An error occurred while loading the featured product</span>
}
```

El framework Angular contiene los siguientes disparadores integrados:

- **`viewport`:** Activará el bloque cuando el contenido ingrese a la ventana gráfica del navegador (*viewport*), que es la parte del navegador que está actualmente visible. Puedes obtener más información sobre el viewport en [https://developer.mozilla.org/docs/Glossary/Viewport](https://developer.mozilla.org/docs/Glossary/Viewport).
- **`interaction`:** Activará el bloque cuando el usuario interactúe con el contenido.
- **`hover`:** Activará el bloque cuando los usuarios pasen el cursor sobre el área cubierta por el contenido con el ratón.
- **`idle`:** Activará el bloque cuando el navegador haya entrado en un estado de inactividad, que es el comportamiento predeterminado de las vistas diferibles. El estado inactivo del navegador se activa mediante la API nativa `requestIdleCallback`. Puedes obtener más información sobre el estado inactivo en [https://developer.mozilla.org/docs/Web/API/Window/requestIdleCallback](https://developer.mozilla.org/docs/Web/API/Window/requestIdleCallback).
- **`immediate`:** Activará el bloque cuando el cliente renderice la página. La diferencia entre no usar el bloque y usarlo con el disparador `immediate` es que nos beneficiamos de las funciones de división de código de las vistas diferibles y entregamos menos JavaScript al cliente.
- **`timer`:** Activará el bloque después de una duración específica. La duración es un parámetro requerido de la función `timer`:

```html
@defer(on timer(2s)) {
  <app-featured />
}
```

El fragmento anterior comenzará a cargar el componente destacado después de 2 segundos.

Podemos lograr una mejor granularidad de carga combinando disparadores:

```html
@defer(on timer(2s); on idle) {
  <app-featured />
}
```

El fragmento anterior cargará el componente destacado cuando el navegador esté inactivo o después de 2 segundos.

Además de los disparadores integrados, podemos crear disparadores personalizados nosotros mismos usando la palabra clave `when`. La palabra clave `when` va seguida de una expresión que se evalúa como booleana:

```html
@defer(when isActive === true) {
  <app-featured />
}
```

En el fragmento anterior, el componente destacado se cargará cuando la propiedad del componente `isActive` sea `true`.

Los disparadores en vistas diferibles son herramientas potentes y ergonómicas que pueden dar resultados sorprendentes en velocidad y rendimiento. Cuando se combinan con la precarga (*prefetching*), pueden lograr grandes mejoras de rendimiento en las aplicaciones Angular. La precarga nos permite especificar la condición en la que podemos precargar una vista diferible para que esté lista cuando sea necesaria. La precarga es compatible con todos los disparadores integrados de vistas diferibles:

```html
@defer(on timer(2s); prefetch on idle) {
  <app-featured />
}
```

El fragmento anterior precargará el contenido cuando el navegador esté inactivo y lo cargará después de 2 segundos. También puede definir cuándo precargará el contenido utilizando la palabra clave `when` o crear disparadores personalizados.

Los disparadores y la precarga nos permiten crear escenarios sofisticados y complejos para cargar vistas diferibles. La versatilidad que proporciona la API de vistas diferibles la convierte en una herramienta muy útil para desarrollar aplicaciones Angular altamente sofisticadas y de alto rendimiento.

> Las vistas diferibles no deben usarse para contenido que deba renderizarse de inmediato.

En la siguiente sección, concluiremos nuestro viaje para optimizar el rendimiento de la aplicación con Angular SSG.

---

### Sección 15.6: Prerrenderizado de aplicaciones SSG

SSG o prerrenderizado en tiempo de compilación (*build-time prerendering*) es el proceso de creación de archivos HTML generados estáticos para una aplicación Angular. Ocurre de forma predeterminada cuando creamos una aplicación Angular SSR mediante el comando de Angular CLI `ng build`.

El principal beneficio de una aplicación SSG es que no requiere tiempos de ida y vuelta (*round-trip times*) entre el servidor y el cliente para cada solicitud. En cambio, cada página se sirve como contenido estático, lo que elimina el tiempo que lleva cargar la aplicación, según lo medido por la métrica de CWV TTFB.

En la sección *Renderizado de aplicaciones SSR*, la salida del comando de compilación de Angular CLI incluyó el siguiente mensaje:

```text
Prerendered 4 static routes.
```

Veamos cómo funciona SSG y qué significa la salida anterior:

1. Ejecuta el siguiente comando para compilar la aplicación Angular:

```bash
ng build
```

El comando `ng build` creará la carpeta `dist\my-app\browser`.

> La carpeta anterior no debe confundirse con la carpeta `browser` generada al compilar una aplicación Angular sin SSR.

2. Navega a la carpeta `dist\my-app` y abre el archivo `prerendered-routes.json`:

```json
{
  "routes": [
    "/cart",
    "/products",
    "/products/new",
    "/user"
  ]
}
```

Enumera las rutas de la aplicación que Angular SSG prerrenderizó. También ha creado una carpeta y un archivo `index.html` para cada ruta dentro de la carpeta `browser`.

3. Abre el archivo `products\index.html` y verás que Angular ha agregado todos los archivos CSS y HTML, e incluso ha renderizado los datos del producto obtenidos de la Fake Store API.

Para obtener una vista previa de cómo funciona SSG, ejecuta el comando `ng serve` para iniciar la aplicación y navega a `http://localhost:4200/products`. La lista de productos se carga instantáneamente sin esperar a que la aplicación obtenga datos de la Fake Store API.

> El comando `ng serve` sirve la versión SSG de nuestra aplicación porque ejecuta el comando `ng build` entre bastidores. Para deshabilitar SSG, abre el archivo `angular.json` y establece la propiedad `prerender` en `false` dentro de la sección `build`.

SSG está habilitado de forma predeterminada en las aplicaciones Angular SSR y puede mejorar drásticamente su tiempo de carga y rendimiento en tiempo de ejecución. Puede ser particularmente útil para dispositivos de gama baja con bajo rendimiento.

---

### Sección 15.7: Resumen

En este capítulo, aprendimos diferentes formas de optimizar y mejorar el rendimiento de una aplicación Angular. Presentamos el concepto de CWV y cómo puede afectar a una aplicación web. Exploramos cómo medir y mejorar las métricas de CWV utilizando SSR e hidratación en aplicaciones Angular. También investigamos diferentes aspectos de las optimizaciones de rendimiento, como la directiva `NgOptimizedImage` y las vistas diferibles (*deferrable views*). Finalmente, vimos una descripción general de SSG en aplicaciones Angular.

Nuestro viaje con el framework Angular termina con este capítulo. Sin embargo, las posibilidades de lo que podemos hacer son infinitas. El framework Angular se actualiza con nuevas características en cada lanzamiento, brindando a los desarrolladores web una poderosa herramienta para el desarrollo diario. ¡Estuvimos encantados de tenerte a bordo y esperamos que este libro te haya ayudado a ampliar tus ideas sobre lo que puedes lograr con una herramienta tan excelente!
