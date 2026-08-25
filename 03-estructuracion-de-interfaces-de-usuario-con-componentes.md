# Parte 1: Fundamentos de Angular

## Capítulo 3: Estructuración de interfaces de usuario con componentes

Hasta ahora, hemos tenido la oportunidad de obtener una vista panorámica del framework Angular. Aprendimos a crear una nueva aplicación Angular utilizando Angular CLI y a interactuar con un componente Angular mediante la sintaxis de plantillas. También exploramos TypeScript, lo que nos ayudará a comprender cómo escribir código en Angular. Tenemos todo lo necesario para explorar las posibilidades adicionales que Angular aporta en lo que respecta a la creación de componentes interactivos y cómo pueden comunicarse entre sí.

En este capítulo, aprenderemos sobre los siguientes conceptos:

- Creación de nuestro primer componente
- Interacción con la plantilla
- Comunicación entre componentes
- Encapsulación de estilos CSS
- Elección de una estrategia de detección de cambios
- Introducción al ciclo de vida del componente

---

### Sección 3.1: Requisitos técnicos

Este capítulo contiene varios ejemplos de código para guiarte a través de los componentes de Angular. Puedes encontrar el código fuente correspondiente en la carpeta `ch03` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch03](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch03)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)
- **Angular DevTools:** [https://angular.dev/tools/devtools](https://angular.dev/tools/devtools)

---

### Sección 3.2: Creación de nuestro primer componente

Los componentes son los bloques de construcción básicos de una aplicación Angular. Controlan diferentes partes de la página web llamadas vistas, como una lista de productos o un formulario de pago de pedidos. Son responsables de la lógica de presentación de una aplicación Angular y están organizados en un árbol jerárquico de componentes que pueden interactuar entre sí:

*(Figura 3.1: Arquitectura de componentes)*

La arquitectura de una aplicación Angular se basa en los componentes de Angular. Cada componente de Angular puede comunicarse e interactuar con uno o más componentes en el árbol de componentes. Como podemos ver en la Figura 3.1, un componente puede ser simultáneamente padre de algunos componentes hijos y a su vez hijo de otro componente padre.

En esta sección, exploraremos los siguientes temas sobre los componentes de Angular:

- La estructura de un componente Angular
- Creación de componentes con Angular CLI

Comenzaremos nuestro recorrido investigando el funcionamiento interno de los componentes de Angular.

#### La estructura de un componente Angular
Como aprendimos en el *Capítulo 1: Construyendo tu primera aplicación Angular*, una aplicación Angular típica contiene al menos un componente principal que consta de varios archivos. La clase TypeScript del componente se define en el archivo `app.component.ts`:

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
  title = 'World';
}
```

`@Component` es un decorador de Angular que define las propiedades del componente Angular. Un decorador de Angular es un método que acepta un objeto con metadatos como parámetro. Los metadatos se utilizan para configurar una clase TypeScript como un componente Angular utilizando las siguientes propiedades:

- **`selector`:** Un selector CSS que indica a Angular que cargue el componente en la ubicación donde encuentre la etiqueta correspondiente en una plantilla HTML. Angular CLI agrega el prefijo `app` por defecto, pero puedes personalizarlo usando la opción `--prefix` al crear el proyecto de Angular.
- **`imports`:** Define una lista de artefactos de Angular que el componente necesita para cargarse correctamente, como otros componentes de Angular. Angular CLI agrega `RouterOutlet` en el componente principal de la aplicación de forma predeterminada. `RouterOutlet` se usa cuando necesitamos capacidades de enrutamiento en una aplicación Angular. Aprenderemos a configurar el enrutamiento en el *Capítulo 9: Navegación a través de aplicaciones con Enrutamiento*.
- **`templateUrl`:** Define la ruta de un archivo HTML externo que contiene la plantilla HTML del componente. Alternativamente, puedes proporcionar la plantilla en línea mediante la propiedad `template`.
- **`styleUrl`:** Define la ruta de un archivo de hoja de estilos CSS externo que contiene los estilos CSS del componente. Alternativamente, puedes proporcionar los estilos en línea mediante la propiedad `styles`.

En aplicaciones construidas con versiones anteriores de Angular, puedes notar que falta la propiedad `imports` en el decorador `@Component`. Esto se debe a que dichos componentes dependen de los módulos de Angular para proporcionar la funcionalidad necesaria.

Sin embargo, a partir de Angular v16, se introdujo la propiedad `standalone` como alternativa a los módulos de Angular. Con Angular v19, los componentes independientes (*standalone components*) son ahora los predeterminados y se imponen en toda la estructura del proyecto. Este cambio significa que las aplicaciones creadas con Angular v19 utilizarán el array `imports` en componentes independientes por defecto, marcando una desviación significativa de la arquitectura basada en módulos de versiones anteriores.

Ahora que hemos explorado la estructura de un componente Angular, aprenderemos a usar Angular CLI y a crear componentes por nosotros mismos.

#### Creación de componentes con Angular CLI
Además del componente principal de la aplicación, podemos crear otros componentes de Angular que proporcionen funciones específicas a la aplicación.

Necesitarás una aplicación Angular para seguir el resto del capítulo. Una opción es crear una nueva aplicación Angular ejecutando el comando `ng new` que aprendiste en el *Capítulo 1: Construyendo tu primera aplicación Angular*. Alternativamente, puedes obtener el código fuente del repositorio de GitHub mencionado en la sección de requisitos técnicos del mismo capítulo.

Para crear un nuevo componente en una aplicación Angular, usamos el comando `ng generate` de Angular CLI, pasando el nombre del componente como parámetro. Ejecuta el siguiente comando dentro de la carpeta raíz del espacio de trabajo actual de Angular CLI:

```bash
ng generate component product-list
```

El comando anterior crea una carpeta dedicada para el componente llamada `product-list` que contiene todos los archivos necesarios:

- El archivo `product-list.component.css`, que aún no contiene ningún estilo CSS.
- El archivo `product-list.component.html`, que contiene un elemento de párrafo que muestra texto estático:

```html
<p>product-list works!</p>
```

- El archivo `product-list.component.spec.ts`, que contiene una prueba unitaria que comprueba si el componente se puede crear con éxito:

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { ProductListComponent } from './product-list.component';

describe('ProductListComponent', () => {
  let component: ProductListComponent;
  let fixture: ComponentFixture<ProductListComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [ProductListComponent]
    })
    .compileComponents();

    fixture = TestBed.createComponent(ProductListComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

Aprenderemos más sobre las pruebas unitarias y su sintaxis en el *Capítulo 13: Pruebas unitarias en aplicaciones Angular*.

- El archivo `product-list.component.ts`, que contiene la lógica de presentación de nuestro componente:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-product-list',
  imports: [],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
export class ProductListComponent {
}
```

En esta sección nos enfocamos en la clase TypeScript de los componentes de Angular, pero ¿cómo interactúan con su plantilla HTML?

En la siguiente sección, aprenderemos a mostrar la plantilla HTML de un componente Angular en una página. También veremos cómo usar la sintaxis de plantillas de Angular para interactuar entre la clase TypeScript del componente y su plantilla HTML.

---

### Sección 3.3: Interactuando con la plantilla

Como hemos aprendido, crear un componente de Angular con Angular CLI implica generar un conjunto de archivos asociados. Uno de estos archivos es la plantilla del componente, que contiene el contenido HTML que se muestra en la página. En esta sección, exploraremos cómo mostrar e interactuar con la plantilla a través de los siguientes temas:

- Carga de la plantilla del componente
- Visualización de datos desde la clase del componente
- Estilizado del componente
- Obtención de datos desde la plantilla

Comenzaremos nuestro recorrido en la plantilla del componente explorando cómo renderizamos un componente en la página web.

#### Carga de la plantilla del componente
Aprendimos que Angular utiliza la propiedad `selector` para cargar el componente en una plantilla HTML. Una aplicación Angular típica carga la plantilla del componente principal al inicio de la aplicación. La etiqueta `<app-root>` que vimos en el *Capítulo 1: Construyendo tu primera aplicación Angular* es el selector del componente principal de la aplicación.

Para cargar un componente que hayamos creado, como el componente de lista de productos, debemos agregar su selector dentro de una plantilla HTML. Para este escenario, lo cargaremos en la plantilla del componente principal de la aplicación:

1. Abre el archivo `app.component.html` y mueve el contenido de la etiqueta `<style>` al archivo `app.component.css`.

> Es más fácil de mantener y se considera una buena práctica tener todos los estilos CSS en un archivo separado.

2. Modifica el archivo `app.component.html` agregando la etiqueta `<app-product-list>` dentro de la etiqueta `<div>` con la clase `content`:

```html
<div class="content">
  <app-product-list></app-product-list>
</div>
```

> También podemos usar etiquetas de autocierre, similares a los elementos HTML `<input>` e `<img>`, para agregar el componente de lista de productos como `<app-product-list />`.

3. Ejecuta el comando `ng serve` en una ventana de terminal para iniciar la aplicación Angular. El comando fallará indicando el siguiente error:

```text
[ERROR] NG8001: 'app-product-list' is not a known element
```

Este error se debe a que el componente principal de la aplicación aún no reconoce el componente de lista de productos.

4. Abre el archivo `app.component.ts` e importa la clase `ProductListComponent`:

```typescript
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { ProductListComponent } from './product-list/product-list.component';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet, ProductListComponent],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'World';
}
```

Después de que la aplicación se haya compilado con éxito, navega a `http://localhost:4200` para previsualizarla. La página web mostrará el texto estático de la plantilla del componente de lista de productos.

En las siguientes secciones, veremos cómo usar la sintaxis de plantillas de Angular e interactuar con la plantilla a través de la clase TypeScript. Comenzaremos explorando cómo mostrar datos dinámicos definidos en la clase TypeScript del componente.

#### Visualización de datos desde la clase del componente
Ya nos hemos encontrado con la interpolación para mostrar el valor de una propiedad como texto desde la clase del componente a la plantilla:

```html
<h1>Hello, {{ title }}</h1>
```

Angular convierte la propiedad del componente `title` en texto y la muestra en la pantalla.

Una forma alternativa de realizar la interpolación es vincular la propiedad `title` a la propiedad `innerText` del elemento HTML `<h1>`, un método llamado **enlace de propiedad** (*property binding*):

```html
<h1 [innerText]="title"></h1>
```

En el fragmento anterior, nos vinculamos a la propiedad DOM de un elemento y no a su atributo HTML, como parece a primera vista. La propiedad entre corchetes se llama **propiedad de destino** (*target property*) y es la propiedad del elemento DOM a la que queremos vincularnos. La variable de la derecha se denomina **expresión de plantilla** (*template expression*) y corresponde a la propiedad `title` del componente.

Cuando abrimos una página web, el navegador analiza el contenido HTML de la página y lo convierte en una estructura de árbol, el DOM. Cada elemento HTML de la página se convierte en un objeto llamado nodo, que representa parte del DOM. Un nodo define un conjunto de propiedades y métodos que representan la API del objeto. `innerText` es una de esas propiedades y se utiliza para establecer el texto dentro de un elemento HTML.

Para comprender mejor cómo funciona el mecanismo de plantillas de Angular, primero debemos comprender cómo interactúa Angular con los atributos y las propiedades. Define atributos HTML para inicializar una propiedad del DOM y luego utiliza el enlace de datos para interactuar directamente con la propiedad.

Para establecer el atributo de un elemento HTML, utilizamos la sintaxis `attr.` a través del enlace de propiedades seguido del nombre del atributo. Por ejemplo, para establecer el atributo de accesibilidad `aria-label` de un elemento HTML, escribiríamos lo siguiente:

```html
<p [attr.aria-label]="myText"></p>
```

En el fragmento anterior, `myText` es una propiedad de un componente de Angular. Recuerda que el enlace de propiedades interactúa con las propiedades de un componente de Angular. Por lo tanto, si quisiéramos establecer el valor de la propiedad `innerText` directamente en el HTML, escribiríamos el valor del texto entre comillas simples:

```html
<h1 [innerText]="'My title'"></h1>
```

En este caso, el valor pasado a la propiedad `innerText` es texto estático, no una propiedad del componente.

El enlace de propiedades en el framework Angular vincula los valores de las propiedades de la clase TypeScript del componente con la plantilla. Como veremos a continuación, la sintaxis del flujo de control es adecuada para coordinar cómo se mostrarán esos valores en la plantilla.

#### Control de la representación de datos
La nueva sintaxis de flujo de control (*control flow syntax*) introducida en las últimas versiones del framework Angular nos permite manipular cómo se representarán los datos en la plantilla del componente. Cuenta con un conjunto de bloques integrados que agregan las siguientes capacidades a la sintaxis de plantillas de Angular:

- Visualización condicional de datos
- Iteración a través de datos
- Conmutación entre plantillas

En las siguientes secciones, exploraremos las capacidades anteriores, comenzando por mostrar datos del componente basados en una sentencia condicional.

##### Visualización condicional de datos
El bloque `@if` agrega o elimina un elemento HTML en el DOM en función de la evaluación de una expresión. Si la expresión se evalúa como `true`, el elemento se inserta en el DOM. De lo contrario, el elemento se elimina del DOM. Ilustraremos el uso del bloque `@if` con un ejemplo:

1. Ejecuta el siguiente comando para crear una interfaz para productos:

```bash
ng generate interface product
```

2. Abre el archivo `product.ts` y agrega las siguientes propiedades:

```typescript
export interface Product {
  id: number;
  title: string;
}
```

La interfaz `Product` define la estructura de un objeto `Product`.

3. Abre el archivo `app.component.css` y mueve los estilos CSS que contienen los selectores `h1` y `p` al archivo `product-list.component.css`.
4. Abre el archivo `product-list.component.ts` y crea un array `products` vacío:

```typescript
import { Component } from '@angular/core';
import { Product } from '../product';

@Component({
  selector: 'app-product-list',
  imports: [],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
export class ProductListComponent {
  products: Product[] = [];
}
```

El array `products` se utilizará para almacenar una lista de objetos `Product`.

5. Abre el archivo `product-list.component.html` y reemplaza su contenido con el siguiente fragmento:

```html
@if (products.length > 0) {
  <h1>Products ({{products.length}})</h1>
}
```

El elemento `<h1>` en la plantilla HTML anterior se renderiza en la pantalla cuando el array `products` no está vacío. De lo contrario, se elimina por completo.

El bloque `@if` se comporta de manera similar a una sentencia `if` de JavaScript. Por lo tanto, podemos agregar una sección `@else` en la plantilla del componente para ejecutar lógica personalizada cuando aún no haya productos:

```html
@if (products.length > 0) {
  <h1>Products ({{products.length}})</h1>
} @else {
  <p>No products found!</p>
}
```

Si tuviéramos una condición adicional que quisiéramos evaluar, podríamos usar una sección `@else if`:

```html
@if (products.length > 0) {
  <h1>Products ({{products.length}})</h1>
} @else if (products.length === 100) {
  <span>
    Click <a>Load More</a> to see more products
  </span>
} @else {
  <p>No products found!</p>
}
```

Ejecuta el comando `ng serve` para previsualizar la aplicación hasta ahora:

*(Figura 3.2: Salida de la aplicación)*

En aplicaciones creadas con versiones anteriores de Angular donde la sintaxis de flujo de control no está disponible, puedes notar que se utilizó la sintaxis `*ngIf` para mostrar datos condicionales:

```html
<h1 *ngIf="products.length > 0">
  Products ({{products.length}})
</h1>
```

`*ngIf` es una directiva de Angular con el mismo comportamiento que el bloque `@if`. Aprenderemos cómo crear directivas personalizadas de Angular en el próximo capítulo.

Sin embargo, se recomienda encarecidamente utilizar el bloque `@if` por las siguientes razones:

- Hace que las plantillas sean mucho más legibles.
- La sintaxis es más cercana a JavaScript y es más fácil de recordar.
- Está integrado en el framework y disponible de inmediato, lo que se traduce en paquetes de menor tamaño.

Puedes encontrar más información sobre `*ngIf` en [https://angular.dev/guide/directives#adding-or-removing-an-element-with-ngif](https://angular.dev/guide/directives#adding-or-removing-an-element-with-ngif).

La aplicación que hemos creado no muestra ningún dato porque el array `products` está vacío. En la siguiente sección, aprenderemos cómo agregar y mostrar datos de productos en el componente de lista de productos.

##### Iteración a través de datos
El bloque `@for` nos permite recorrer en bucle una colección de elementos y renderizar una plantilla para cada uno, donde podemos definir marcadores de posición convenientes para interpolar los datos de los elementos. Cada plantilla renderizada tiene como alcance el contexto exterior donde se coloca la directiva de bucle, de modo que podemos acceder a otros enlaces. Podemos pensar en el bloque `@for` como el bucle `for` de JavaScript pero para plantillas HTML.

Podemos usar el bloque `@for` para mostrar la lista de productos en nuestro componente de la siguiente manera:

1. Abre el archivo `app.component.css` y mueve los estilos CSS que contienen los selectores `.pill-group`, `.pill` y `.pill:hover` al archivo `product-list.component.css`.
2. Modifica el array `products` en la clase `ProductListComponent` del archivo `product-list.component.ts` para que contenga los siguientes datos:

```typescript
export class ProductListComponent {
  products: Product[] = [
    { id: 1, title: 'Keyboard' },
    { id: 2, title: 'Microphone' },
    { id: 3, title: 'Web camera' },
    { id: 4, title: 'Tablet' }
  ];
}
```

3. Abre el archivo `product-list.component.html` y agrega el siguiente fragmento después del bloque `@if`:

```html
<ul class="pill-group">
  @for (product of products; track product.id) {
    <li class="pill">{{product.title}}</li>
  }
</ul>
```

En el código anterior, usamos el bloque `@for` y convertimos cada elemento obtenido del array `products` en una variable `product` llamada **variable de entrada de plantilla** (*template input variable*). Hacemos referencia a la variable de plantilla en nuestro HTML vinculando su propiedad `title` mediante la sintaxis de interpolación de Angular.

Durante la ejecución del bloque `@for`, los datos pueden cambiar, los elementos HTML se pueden agregar, mover o eliminar, e incluso toda la lista se puede reemplazar. Angular debe sincronizar los cambios de datos con el árbol DOM conectando el array iterado y su elemento DOM correspondiente. Es un proceso que puede volverse muy lento y costoso, y eventualmente puede resultar en un rendimiento deficiente. Para ese propósito, Angular se basa en la propiedad `track`, que realiza un seguimiento de los cambios en los datos. En nuestro caso, la propiedad `track` define el nombre de la propiedad de la variable `product` que se utilizará para realizar un seguimiento de cada elemento en el array `products`.

Ejecuta el comando `ng serve` para previsualizar la aplicación:

*(Figura 3.3: Lista de productos)*

El bloque `@for` admite la adición de una sección `@empty`, que se ejecuta cuando el array de elementos está vacío. Podemos refactorizar nuestro código eliminando la sección `@else` del bloque `@if` y agregando una sección `@empty` de la siguiente manera:

```html
@if (products.length > 0) {
  <h1>Products ({{products.length}})</h1>
}
<ul class="pill-group">
  @for (product of products; track product.id) {
    <li class="pill">{{product.title}}</li>
  } @empty {
    <p>No products found!</p>
  }
</ul>
```

El bloque `@for` puede observar cambios en la colección subyacente y agregar, eliminar u ordenar las plantillas renderizadas a medida que los elementos se agregan, eliminan o reordenan en la colección. También es posible realizar un seguimiento de otras propiedades útiles. Podemos utilizar la versión extendida del bloque `@for` utilizando la siguiente sintaxis:

```html
@for (product of products; track product.id; let variable=property) {}
```

La `variable` es una variable de entrada de plantilla a la que podemos hacer referencia más adelante en nuestra plantilla. La `property` puede tener los siguientes valores:

- `$count`: Indica el número de elementos en el array.
- `$index`: Indica el índice del elemento en el array.
- `$first`/`$last`: Indica si el elemento actual es el primero o el último del array.
- `$even`/`$odd`: Indica si el índice del elemento en el array es par o impar.

Podemos usar las propiedades anteriores directamente o declarando un alias, como se ve en el siguiente ejemplo.

En el siguiente fragmento, Angular asigna el valor de la propiedad `$index` a la variable de entrada `i`. La variable `i` se usa luego en la plantilla para mostrar cada producto como una lista numerada:

```html
@for (product of products; track product.id; let i = $index) {
  <li class="pill">{{i+1}}. {{product.title}}</li>
}
```

> Usa la propiedad `$index` en la variable `track` cuando no estés seguro de cuál deberías elegir de los datos de tu objeto. Además, se recomienda usarla cuando no tengas ninguna propiedad única en tu objeto y no estés modificando el orden de la lista eliminando, agregando o moviendo elementos.

En aplicaciones creadas con versiones anteriores de Angular, puedes notar la siguiente sintaxis para iterar sobre colecciones:

```html
<ul class="pill-group">
  <li class="pill" *ngFor="let product of products">
    {{product.title}}
  </li>
</ul>
```

`*ngFor` es una directiva de Angular que funciona de manera similar al bloque `@for`. Sin embargo, se recomienda encarecidamente utilizar `@for` por las mismas razones mencionadas sobre el bloque `@if` en la sección anterior.

Puedes encontrar más información sobre `*ngFor` en [https://angular.dev/guide/directives#listing-items-with-ngfor](https://angular.dev/guide/directives#listing-items-with-ngfor).

El último bloque de la sintaxis de flujo de control que cubriremos es el bloque `@switch` en la siguiente sección.

##### Conmutación entre plantillas
El bloque `@switch` alterna entre partes de la plantilla del componente y muestra cada una en función de un valor definido.

Puedes pensar en `@switch` como la sentencia `switch` de JavaScript. Consta de las siguientes secciones:

- `@switch`: Define la propiedad que queremos comprobar al aplicar el bloque.
- `@case`: Agrega o elimina una plantilla del árbol DOM según el valor de la propiedad definida en el bloque `@switch`.
- `@default`: Agrega una plantilla al árbol DOM si el valor de la propiedad definida en el bloque `@switch` no cumple ninguna sentencia `@case`.

Aprenderemos a usar el bloque `@switch` mostrando un emoji diferente según el título del producto. Abre el archivo `product-list.component.html` y modifica el bloque `@for` para que incluya el siguiente bloque `@switch`:

```html
<ul class="pill-group">
  @for (product of products; track product.id) {
    <li class="pill">
      @switch (product.title) {
        @case ('Keyboard') {
        }
        @case ('Microphone') {
        }
        @default {
        }
      }
      {{product.title}}
    </li>
  } @empty {
    <p>No products found!</p>
  }
</ul>
```

El bloque `@switch` evalúa la propiedad `title` de cada producto. Cuando encuentra una coincidencia, activa la sección `@case` correspondiente. Si el valor de la propiedad `title` no coincide con ninguna sección `@case`, se activa la sección `@default`.

En aplicaciones construidas con versiones anteriores de Angular, puedes notar la siguiente sintaxis para alternar partes de la plantilla:

```html
<div [ngSwitch]="product.title">
  <p *ngSwitchCase="'Keyboard'"></p>
  <p *ngSwitchCase="'Microphone'"></p>
  <p *ngSwitchDefault></p>
</div>
```

`[ngSwitch]` es una directiva de Angular con el mismo comportamiento que el bloque `@switch`. Sin embargo, se recomienda encarecidamente utilizar `@switch` por las mismas razones mencionadas sobre el bloque `@if` en la sección anterior.

Puedes encontrar más información sobre `[ngSwitch]` en [https://angular.dev/guide/directives#switching-cases-with-ngswitch](https://angular.dev/guide/directives#switching-cases-with-ngswitch).

La simplicidad y la ergonomía mejorada de la sintaxis de flujo de control han permitido la introducción del bloque `@defer` en el framework Angular. El bloque `@defer` ayuda a mejorar la UX y a optimizar el rendimiento de la aplicación cargando partes de la plantilla del componente de forma asíncrona. Aprenderemos más en el *Capítulo 15: Optimización del rendimiento de la aplicación*.

En esta sección, aprendimos a aprovechar la sintaxis de flujo de control y a coordinar cómo se mostrarán los datos en la plantilla del componente.

> Si deseas utilizar esta sintaxis en aplicaciones que ya utilizan el enfoque de directivas antiguo, puedes ejecutar la migración de Angular CLI descrita en [https://angular.dev/reference/migrations/control-flow](https://angular.dev/reference/migrations/control-flow).

Como aprenderemos en la siguiente sección, el enlace de propiedades en el framework Angular aplica estilos y clases CSS en las plantillas de Angular.

#### Estilizado del componente
Los estilos en una aplicación web se pueden aplicar mediante el atributo `class` o `style`, o ambos, de un elemento HTML:

```html
<p class="star"></p>
<p style="color: greenyellow"></p>
```

El framework Angular proporciona dos tipos de enlace de propiedades:

- Enlace de clases (*Class binding*)
- Enlace de estilos (*Style binding*)

Comencemos nuestro recorrido sobre el estilizado de componentes con el enlace de clases en la siguiente sección.

##### Enlace de clases (Class binding)
Podemos aplicar una sola clase a un elemento HTML usando la siguiente sintaxis:

```html
<p [class.star]="isLiked"></p>
```

En el fragmento anterior, la clase `star` se agregará al elemento de párrafo cuando la expresión `isLiked` sea verdadera. De lo contrario, se eliminará del elemento. Si queremos aplicar múltiples clases CSS simultáneamente, podemos usar la siguiente sintaxis:

```html
<p [class]="currentClasses"></p>
```

La variable `currentClasses` es una propiedad del componente. El valor de una expresión que se utiliza en un enlace de clase puede ser uno de los siguientes:

- Una cadena delimitada por espacios de nombres de clases, como `'star active'`.
- Un objeto con claves como los nombres de clase y valores como condiciones booleanas para cada clave. Se agrega una clase al elemento cuando el valor de la clave, con su nombre, se evalúa como verdadero. De lo contrario, la clase se elimina del elemento:

```typescript
currentClasses = {
  star: true,
  active: false
};
```

En lugar de aplicar estilo a nuestros elementos mediante clases CSS, podemos establecer estilos directamente con el enlace de estilos.

##### Enlace de estilos (Style binding)
Al igual que el enlace de clases, podemos aplicar estilos individuales o múltiples simultáneamente mediante un enlace de estilos. Se puede establecer un solo estilo en un elemento HTML utilizando la siguiente sintaxis:

```html
<p [style.color]="'greenyellow'"></p>
```

En el fragmento anterior, el elemento de párrafo tendrá un color `greenyellow`. Algunos estilos se pueden ampliar aún más en el enlace, como el ancho del elemento de párrafo, que podemos definir junto con la unidad de medida:

```html
<p [style.width.px]="100"></p>
```

El elemento de párrafo tendrá 100 píxeles de ancho. Si necesitamos alternar varios estilos a la vez, podemos usar la sintaxis de objeto:

```html
<p [style]="currentStyles"></p>
```

La variable `currentStyles` es una propiedad del componente. El valor de una expresión que se utiliza en un enlace de estilo puede ser uno de los siguientes:

- Una cadena con estilos separados por punto y coma, como `'color: greenyellow; width: 100px'`.
- Un objeto donde sus claves son los nombres de los estilos y los valores son los valores de estilo reales:

```typescript
currentStyles = {
  color: 'greenyellow',
  width: '100px'
};
```

Los enlaces de clases y estilos son características poderosas que Angular proporciona de forma predeterminada. Junto con la configuración de estilos CSS que podemos definir en el decorador `@Component`, brinda infinitas oportunidades para aplicar estilos a los componentes de Angular. Una característica igualmente atractiva es la capacidad de leer datos de una plantilla en la clase del componente, que veremos a continuación.

#### Obtención de datos desde la plantilla
En la sección anterior, aprendimos cómo usar el enlace de propiedades para mostrar datos de la clase del componente. Los escenarios del mundo real suelen implicar un flujo de datos bidireccional a través de los componentes. Para obtener datos de la plantilla de vuelta a la clase del componente, utilizamos una técnica llamada **enlace de eventos** (*event binding*). Aprenderemos a utilizar el enlace de eventos notificando a la clase del componente cuando se haya seleccionado un producto de la lista:

1. Abre el archivo `product-list.component.ts` y agrega una propiedad `selectedProduct`:

```typescript
selectedProduct: Product | undefined;
```

2. Abre el archivo `product-list.component.html` y utiliza la sintaxis de interpolación para mostrar el producto seleccionado si existe:

```html
@if (selectedProduct) {
  <p>
    You selected: <strong>{{selectedProduct.title}}</strong>
  </p>
}
```

3. Agrega un enlace de evento `click` en la etiqueta `<li>` para establecer `selectedProduct` en la variable de producto actual del bloque `@for`:

```html
@for (product of products; track product.id) {
  <li class="pill" (click)="selectedProduct = product">
    @switch (product.title) {
      @case ('Keyboard') {
      }
      @case ('Microphone') {
      }
      @default {
      }
    }
    {{product.title}}
  </li>
}
```

4. Ejecuta `ng serve` para iniciar la aplicación y haz clic en un producto de la lista:

*(Figura 3.4: Selección de producto)*

Un enlace de evento escucha eventos del DOM en el elemento HTML de destino y responde a esos eventos interactuando con los miembros de la clase del componente. El evento entre paréntesis se denomina **evento de destino** (*target event*) y es el evento que estamos escuchando actualmente. La expresión de la derecha se llama **sentencia de plantilla** (*template statement*) e interactúa con la clase del componente. El enlace de eventos en Angular admite todos los eventos nativos del DOM que se encuentran en [https://developer.mozilla.org/docs/Web/Events](https://developer.mozilla.org/docs/Web/Events).

La interacción de una plantilla de componente con su correspondiente clase TypeScript se resume en el siguiente diagrama:

*(Figura 3.5: Interacción componente-plantilla)*

El mismo principio que seguimos para interactuar con la plantilla del componente y la clase se puede utilizar al comunicarse entre componentes.

---

### Sección 3.4: Comunicación entre componentes

Los componentes de Angular exponen una API pública que les permite comunicarse con otros componentes. Esta API abarca propiedades de entrada, que utilizamos para alimentar al componente con datos. También expone propiedades de salida a las que podemos vincular detectores de eventos (*event listeners*), obteniendo así información oportuna sobre los cambios en el estado del componente.

En esta sección, aprenderemos cómo Angular resuelve el problema de inyectar datos y extraer datos de los componentes a través de ejemplos rápidos y fáciles.

#### Paso de datos mediante un enlace de entrada (Input binding)
La aplicación actualmente muestra la lista de productos y los detalles del producto seleccionado en el mismo componente. Para aprender a pasar datos entre diferentes componentes, crearemos un nuevo componente de Angular que mostrará los detalles del producto seleccionado. Los datos que representan los detalles del producto específico se pasarán dinámicamente desde el componente de lista de productos.

Comenzaremos creando y configurando el componente para mostrar los detalles del producto:

1. Ejecuta el siguiente comando de Angular CLI para crear el nuevo componente de Angular:

```bash
ng generate component product-detail
```

2. Abre el archivo `product-detail.component.ts` y modifica las sentencias de importación correspondientes:

```typescript
import { Component, input } from '@angular/core';
import { Product } from '../product';
```

La función `input` es parte de la API de Signals y se usa cuando queremos pasar datos de un componente hacia abajo a otro componente.

> Aprenderemos más sobre la API de Signals en el *Capítulo 7: Seguimiento del estado de la aplicación con Signals*.

3. Define una propiedad `product` en la clase `ProductDetailComponent` que use la función `input`:

```typescript
export class ProductDetailComponent {
  product = input<Product>();
}
```

> En versiones anteriores de Angular, usamos el decorador `@Input` para pasar datos entre componentes. Puedes obtener más información en [https://angular.dev/guide/components/inputs](https://angular.dev/guide/components/inputs).

4. Abre el archivo `product-detail.component.html` y agrega el siguiente contenido:

```html
@if (product()) {
  <p>
    You selected: <strong>{{product()!.title}}</strong>
  </p>
}
```

En el fragmento anterior, usamos un bloque `@if` para verificar si la propiedad de entrada `product` se ha establecido antes de mostrar su título.

5. Abre el archivo `product-list.component.ts` e importa la clase `ProductDetailComponent`:

```typescript
import { Component } from '@angular/core';
import { Product } from '../product';
import { ProductDetailComponent } from '../product-detail/product-detail.component';

@Component({
  selector: 'app-product-list',
  imports: [ProductDetailComponent],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

6. Finalmente, reemplaza el último bloque `@if` en el archivo `product-list.component.html` con el siguiente fragmento:

```html
<app-product-detail [product]="selectedProduct"></app-product-detail>
```

En el fragmento anterior, usamos el enlace de propiedades para vincular el valor de la propiedad `selectedProduct` a la propiedad de entrada `product` del componente de detalle del producto. Este enfoque se llama **enlace de entrada** (*input binding*).

Si ejecutamos la aplicación y hacemos clic en un producto de la lista, veremos que la selección de productos continúa funcionando como se esperaba.

El bloque `@if` en la plantilla del componente de detalle del producto implica que la propiedad de entrada `product` es obligatoria; de lo contrario, no muestra su título. Angular no sabe si el componente de lista de productos pasa un valor para el enlace de entrada `product` durante el tiempo de compilación. Si queremos hacer cumplir esa regla durante el tiempo de compilación, podemos definir una propiedad de entrada como requerida en consecuencia:

```typescript
product = input.required<Product>();
```

Según el fragmento anterior, si el componente de lista de productos no pasa un valor para la propiedad de entrada `product`, el compilador de Angular arrojará el siguiente error:

```text
[ERROR] NG8008: Required input 'product' from component ProductDetailComponent must be specified.
```

¡Eso es todo! Hemos pasado con éxito datos de un componente a otro. En la siguiente sección, aprenderemos a escuchar eventos en un componente y responder a ellos.

#### Escucha de eventos mediante un enlace de salida (Output binding)
Aprendimos que el enlace de entrada se usa cuando queremos pasar datos entre componentes. Este método es aplicable en escenarios donde tenemos dos componentes, uno que actúa como componente padre y el otro como hijo. ¿Qué pasa si queremos comunicarnos al revés, del componente hijo al padre? ¿Cómo notificamos al componente padre sobre acciones específicas en el componente hijo?

Considera un escenario en el que el componente de detalle del producto debe tener un botón para agregar el producto actual a un carrito de compras. El carrito de compras sería una propiedad del componente de lista de productos. ¿Cómo notificaría el componente de detalle del producto al componente de lista de productos que se hizo clic en el botón? Veamos cómo implementaríamos esta funcionalidad en nuestra aplicación:

1. Abre el archivo `product-detail.component.ts` e importa la función `output` del paquete npm `@angular/core`:

```typescript
import { Component, input, output } from '@angular/core';
```

La función `output` se utiliza cuando queremos crear eventos que se activarán desde un componente hacia otro componente superior.

2. Define una nueva propiedad de componente dentro de la clase `ProductDetailComponent` que use la función `output`:

```typescript
added = output();
```

> En versiones anteriores de Angular, usamos el decorador `@Output` para activar eventos entre componentes. Puedes obtener más información en [https://angular.dev/guide/components/outputs](https://angular.dev/guide/components/outputs).

3. En la misma clase TypeScript, crea el siguiente método:

```typescript
addToCart() {
  this.added.emit();
}
```

El método `addToCart` llama al método `emit` en el evento de salida `added` que creamos en el paso anterior. El método `emit` desencadena un evento y notifica a cualquier componente que esté escuchando ese evento actualmente.

4. Ahora, agrega un elemento `<button>` en la plantilla del componente y vincula su evento `click` al método `addToCart`:

```html
@if (product()) {
  <p>
    You selected: <strong>{{product()!.title}}</strong>
  </p>
  <button (click)="addToCart()">Add to cart</button>
}
```

5. Abre el archivo `product-detail.component.css` y agrega los siguientes estilos CSS que se aplicarán al elemento `<button>`:

```css
button {
  display: flex;
  align-items: center;
  --button-accent: var(--bright-blue);
  background: color-mix(in srgb, var(--button-accent) 65%, transparent);
  color: white;
  padding-inline: 0.75rem;
  padding-block: 0.375rem;
  border-radius: 0.5rem;
  border: 0;
  transition: background 0.3s ease;
  font-family: var(--inter-font);
  font-size: 0.875rem;
  font-style: normal;
  font-weight: 500;
  line-height: 1.4rem;
  letter-spacing: -0.00875rem;
  cursor: pointer;
}

button:hover {
  background: color-mix(in srgb, var(--button-accent) 50%, transparent);
}
```

6. ¡Ya casi estamos! Ahora debemos conectar el enlace en el componente de lista de productos para que los dos componentes puedan comunicarse. Abre el archivo `product-list.component.ts` y crea el siguiente método:

```typescript
onAdded() {
  alert(`${this.selectedProduct?.title} added to the cart!`);
}
```

En el fragmento anterior, usamos el método nativo `alert` del navegador para mostrar un cuadro de diálogo al usuario.

7. Finalmente, modifica la etiqueta `<app-product-detail>` en el archivo `product-list.component.html` de la siguiente manera:

```html
<app-product-detail
  [product]="selectedProduct"
  (added)="onAdded()"
></app-product-detail>
```

En el fragmento anterior, usamos el enlace de eventos para vincular el método `onAdded` a la propiedad de salida `added` del componente de detalle del producto. Este enfoque se llama **enlace de salida** (*output binding*).

Si seleccionamos un producto de la lista y hacemos clic en el botón *Add to cart*, un cuadro de diálogo mostrará un mensaje como el siguiente:

```text
Web camera added to the cart!
```

Puedes ver una descripción general del mecanismo de comunicación de componentes que hemos discutido en el siguiente diagrama:

*(Figura 3.6: Comunicación entre componentes)*

El evento de salida del componente de detalle del producto no hace más ni menos que emitir un evento al componente padre. Sin embargo, podemos usarlo para pasar datos arbitrarios a través del método `emit`, como aprenderemos en la siguiente sección.

#### Emisión de datos a través de eventos personalizados
El método `emit` de un evento de salida puede aceptar cualquier dato para pasarlo al componente padre. Es mejor definir el tipo de datos que se pueden pasar para hacer cumplir la comprobación estática de tipos.

Actualmente, el componente de lista de productos ya conoce el producto seleccionado. Supongamos que el componente de lista de productos solo pudiera enterarse de ello después de que el usuario hace clic en el botón *Add to cart*:

1. Abre el archivo `product-detail.component.ts` y usa genéricos para declarar el tipo de datos que se pasarán al componente de lista de productos:

```typescript
added = output<Product>();
```

2. Modifica el método `addToCart` para que el método `emit` pase el producto seleccionado actualmente:

```typescript
addToCart() {
  this.added.emit(this.product()!);
}
```

3. Abre el archivo `product-list.component.html` y pasa la variable `$event` en el método `onAdded`:

```html
<app-product-detail
  [product]="selectedProduct"
  (added)="onAdded($event)"
></app-product-detail>
```

El objeto `$event` es una palabra clave reservada en Angular que contiene los datos de carga útil (*payload*) de un emisor de eventos de un enlace de salida, en nuestro caso, un objeto `Product`.

4. Abre el archivo `product-list.component.ts` y cambia la firma del método `onAdded` en consecuencia:

```typescript
onAdded(product: Product) {
  alert(`${product.title} added to the cart!`);
}
```

Como vimos, los enlaces de eventos de salida son una excelente manera de notificar a un componente padre sobre un cambio en el estado del componente o enviar cualquier dato.

Además de usar los enlaces de entrada y salida para comunicarse con los componentes, podemos acceder a sus propiedades y métodos directamente mediante variables de referencia local de plantilla.

#### Variables de referencia local en plantillas
Hemos visto cómo vincular datos a nuestras plantillas usando la interpolación con la sintaxis de dobles llaves. Además de esto, a menudo encontramos identificadores con nombre precedidos por un símbolo de almohadilla (`#`) en los elementos que pertenecen a nuestros componentes o incluso a elementos HTML normales. Estos identificadores de referencia, a saber, **variables de referencia de plantilla**, hacen referencia a los componentes marcados con ellos en nuestras vistas de plantilla y luego acceden a ellos mediante programación. Los componentes también pueden utilizarlos para hacer referencia a otros elementos del DOM y acceder a sus propiedades.

Hemos aprendido cómo se comunican los componentes escuchando los eventos emitidos mediante el enlace de salida o pasando datos a través del enlace de entrada. Pero ¿y si pudiéramos inspeccionar el componente en profundidad, o al menos sus propiedades y métodos expuestos, y acceder a ellos sin pasar por los enlaces de entrada y salida? Establecer una referencia local en el componente abre la puerta a su API pública.

> La API pública de un componente consta de todos los miembros públicos de la clase TypeScript.

Podemos declarar una variable de referencia de plantilla para el componente de detalle del producto en el archivo `product-list.component.html` de la siguiente manera:

```html
<app-product-detail
  #productDetail
  [product]="selectedProduct"
  (added)="onAdded()"
></app-product-detail>
```

A partir de ese momento, podemos acceder directamente a los miembros del componente e incluso vincularlos en otras ubicaciones de la plantilla, como mostrar el título del producto:

```html
<span>{{productDetail.product()!.title}}</span>
```

De esta manera, no necesitamos depender de las propiedades de entrada y salida y podemos manipular el valor de dichas propiedades.

> El enfoque de variables de referencia local es particularmente útil cuando se utilizan librerías donde no podemos controlar los componentes secundarios para agregar propiedades de enlace de entrada o salida.

Principalmente hemos explicado cómo la clase del componente interactúa con su plantilla o con otros componentes, pero apenas nos hemos preocupado por su estilo. Exploraremos eso con más detalle a continuación.

---

### Sección 3.5: Encapsulación de estilos CSS

Podemos definir estilos CSS dentro de nuestros componentes para encapsular mejor nuestro código y hacerlo más reutilizable. En la sección *Creación de nuestro primer componente*, aprendimos a definir estilos CSS para un componente utilizando un archivo CSS externo a través de la propiedad `styleUrl` o definiendo estilos CSS dentro del archivo del componente TypeScript con la propiedad `styles`.

Las reglas habituales de especificidad de CSS gobiernan ambas formas: [https://developer.mozilla.org/docs/Web/CSS/Specificity](https://developer.mozilla.org/docs/Web/CSS/Specificity).

Gracias a los estilos con alcance (*scoped styling*), la gestión de CSS y la especificidad se vuelven muy sencillas en los navegadores que admiten Shadow DOM. Los estilos CSS se aplican a los elementos contenidos en el componente, pero no se extienden más allá de sus límites.

Puedes encontrar más detalles sobre Shadow DOM en [https://developer.mozilla.org/docs/Web/API/Web_components/Using_shadow_DOM](https://developer.mozilla.org/docs/Web/API/Web_components/Using_shadow_DOM).

Además de eso, Angular inserta hojas de estilo en el elemento `<head>` de una página web para que puedan afectar a otros elementos de nuestra aplicación. Podemos configurar diferentes niveles de encapsulación de vistas para evitar que esto suceda.

La **encapsulación de vista** (*view encapsulation*) es la forma en que Angular gestiona el alcance de CSS dentro del componente. Podemos cambiarlo configurando la propiedad `encapsulation` del decorador `@Component` en uno de los siguientes valores de la enumeración `ViewEncapsulation`:

- **`Emulated`:** Implica una emulación del alcance nativo en Shadow DOM mediante el aislamiento (*sandboxing*) de las reglas CSS bajo un selector específico que apunta a un componente. Se prefiere esta opción para garantizar que los estilos de los componentes no se filtren fuera del componente y no se vean afectados por otros estilos externos. Es el comportamiento predeterminado en los proyectos de Angular CLI.
- **`Native` (o `ShadowDom`):** Utiliza el mecanismo nativo de encapsulación Shadow DOM del renderizador que funciona solo en navegadores que admiten Shadow DOM.
- **`None`:** No se proporciona encapsulación de plantilla o estilo. Los estilos se inyectan como si se hubieran agregado al elemento `<head>` del documento. Es la única opción si no están involucrados navegadores compatibles con Shadow DOM.

Exploraremos las opciones `Emulated` y `None` debido a su amplio soporte mediante un ejemplo:

1. Abre el archivo `product-detail.component.html` y encierra el contenido del bloque `@if` en un elemento `<div>`:

```html
@if (product()) {
  <div>
    <p>
      You selected: <strong>{{product()!.title}}</strong>
    </p>
    <button (click)="addToCart()">Add to cart</button>
  </div>
}
```

2. Abre el archivo `product-detail.component.css` y agrega un estilo CSS para cambiar el borde de un elemento `<div>`:

```css
div {
  padding-inline: 0.75rem;
  padding-block: 0.375rem;
  border: 2px dashed;
}
```

3. Ejecuta la aplicación mediante el comando `ng serve` y observa que el componente de detalle del producto tiene un borde discontinuo a su alrededor cuando seleccionas un producto:

*(Figura 3.7: Detalles del producto)*

El estilo no afectó al elemento `<div>` en el archivo `app.component.html` porque la encapsulación predeterminada asigna el alcance de todos los estilos CSS definidos al componente específico.

> La encapsulación de vista predeterminada es `Emulated` si no especificamos una explícitamente.

4. Abre el archivo `product-detail.component.ts` y establece la encapsulación del componente en `ViewEncapsulation.None`:

```typescript
import { Component, input, output, ViewEncapsulation } from '@angular/core';
import { Product } from '../product';

@Component({
  selector: 'app-product-detail',
  imports: [],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css',
  encapsulation: ViewEncapsulation.None
})
```

La salida de la aplicación debería verse de la siguiente manera:

*(Figura 3.8: Sin encapsulación de vista)*

En la imagen anterior, el estilo CSS se filtró al árbol de componentes y afectó al elemento `<div>` del componente principal de la aplicación.

La encapsulación de vistas puede resolver muchos problemas al dar estilo a nuestros componentes. Sin embargo, debe usarse con precaución porque, como ya aprendimos, los estilos CSS pueden filtrarse en partes de la aplicación y producir efectos no deseados.

La estrategia de detección de cambios es otra propiedad muy potente del decorador `@Component`. Examinemos esto a continuación.

---

### Sección 3.6: Decidir una estrategia de detección de cambios

La **detección de cambios** (*change detection*) es el mecanismo que utiliza Angular internamente para detectar los cambios que se producen en las propiedades del componente y reflejarlos en la vista. Se desencadena en eventos específicos, como cuando el usuario hace clic en un botón, se completa una solicitud asíncrona o se ejecuta un método `setTimeout` o `setInterval`. Angular utiliza un proceso llamado *monkey patching* para modificar dichos eventos sobrescribiendo su comportamiento predeterminado mediante una librería llamada **Zone.js**.

Cada componente tiene un detector de cambios que detecta si se ha producido un cambio en sus propiedades comparando el valor actual de una propiedad con el anterior. Si hay diferencias, aplica el cambio a la plantilla del componente. En el componente de detalle del producto, cuando la propiedad de entrada `product` cambia como resultado de un evento que mencionamos anteriormente, el mecanismo de detección de cambios se ejecuta para este componente y actualiza la plantilla en consecuencia.

Sin embargo, hay casos en los que este comportamiento no es deseado, como componentes que representan una gran cantidad de datos. En ese escenario, el mecanismo de detección de cambios predeterminado es insuficiente porque puede introducir cuellos de botella en el rendimiento de la aplicación. Alternativamente, podríamos usar la propiedad `changeDetection` del decorador `@Component`, que dicta la estrategia seleccionada que seguirá el componente para la detección de cambios.

Aprenderemos a usar un mecanismo de detección de cambios perfilando nuestra aplicación Angular con Angular DevTools:

1. Abre el archivo `product-detail.component.ts` y crea una propiedad getter que devuelva el título del producto actual:

```typescript
get productTitle() {
  return this.product()!.title;
}
```

2. Abre el archivo `product-detail.component.html` y reemplaza la expresión `product.title` dentro de la etiqueta `<strong>` con `productTitle`:

```html
@if (product()) {
  <p>
    You selected: <strong>{{productTitle}}</strong>
  </p>
  <button (click)="addToCart()">Add to cart</button>
}
```

3. Ejecuta la aplicación con el comando `ng serve` y previsualízala en `http://localhost:4200`.
4. Inicia **Angular DevTools**, selecciona la pestaña **Profiler** y haz clic en el botón *Start recording* para comenzar a perfilar la aplicación Angular.
5. Haz clic en el producto `Keyboard` de la lista de productos y selecciona la primera barra en el gráfico de barras para revisar la detección de cambios:

*(Figura 3.9: Gráfico de barras de detección de cambios)*

En la imagen anterior, podemos ver que la detección de cambios se activa para cada componente en el árbol de componentes de la aplicación.

6. Haz clic en el botón *Add to cart* y selecciona la segunda barra en el gráfico de barras:

*(Figura 3.10: Gráfico de barras de detección de cambios)*

Angular ejecutó la detección de cambios en el componente de detalle del producto a pesar de que no cambiamos sus propiedades.

7. Modifica el decorador `@Component` del archivo `product-detail.component.ts` configurando la propiedad `changeDetection` en `ChangeDetectionStrategy.OnPush`:

```typescript
import { ChangeDetectionStrategy, Component, input, output } from '@angular/core';
import { Product } from '../product';

@Component({
  selector: 'app-product-detail',
  imports: [],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css',
  changeDetection: ChangeDetectionStrategy.OnPush
})
```

8. Repite los pasos 4 a 6 y observa el resultado de la segunda barra en el gráfico de barras de detección de cambios:

*(Figura 3.11: Gráfico de barras de detección de cambios)*

La detección de cambios no se ejecutó para el componente de detalle del producto esta vez.

9. Haz clic en el producto `Microphone` de la lista y observa la nueva barra en el gráfico de barras:

*(Figura 3.12: Gráfico de barras de detección de cambios)*

La detección de cambios se ejecutó esta vez porque cambiamos la referencia de la propiedad de entrada `product`. Si solo hubiéramos cambiado una propiedad interna usando la estrategia de detección de cambios `OnPush`, el mecanismo de detección de cambios no se habría activado. Puedes obtener más información sobre escenarios de detección de cambios en [https://angular.dev/best-practices/skipping-subtrees](https://angular.dev/best-practices/skipping-subtrees).

La estrategia de detección de cambios es un mecanismo que nos permite modificar la forma en que nuestros componentes detectan cambios en sus datos, mejorando significativamente el rendimiento en aplicaciones a gran escala. Con esto concluye nuestro recorrido por la configuración de un componente, pero el framework Angular no se detiene ahí. Como aprenderemos en la siguiente sección, podemos engancharnos (*hook*) a momentos específicos en el ciclo de vida del componente.

---

### Sección 3.7: Introducción al ciclo de vida del componente

Los eventos del ciclo de vida son ganchos (*lifecycle hooks*) que nos permiten ingresar a etapas específicas en el ciclo de vida de un componente y aplicar lógica personalizada. Su uso es opcional, pero puede ser muy valioso si comprendes cómo usarlos.

Algunos hooks se consideran mejores prácticas, mientras que otros ayudan a depurar y comprender qué sucede en una aplicación Angular. Un hook tiene una interfaz que define un método que debemos implementar. El framework Angular se asegura de que se llame al hook, siempre que hayamos implementado este método en el componente.

> Definir la interfaz en el componente no es obligatorio, pero se considera una buena práctica. A Angular solo le importa si hemos implementado el método real o no.

Los ganchos de ciclo de vida más básicos de un componente de Angular son:

- **`ngOnInit`:** Se llama cuando se inicializa un componente.
- **`ngOnDestroy`:** Se llama cuando se destruye un componente.
- **`ngOnChanges`:** Se llama cuando cambian los valores de las propiedades de enlace de entrada en el componente.
- **`ngAfterViewInit`:** Se llama cuando Angular inicializa la vista del componente actual y sus componentes secundarios.

Todos estos ganchos de ciclo de vida están disponibles en el paquete npm `@angular/core` del framework Angular.

Una lista completa de todos los ganchos de ciclo de vida compatibles está disponible en la documentación oficial de Angular en [https://angular.dev/guide/components/lifecycle](https://angular.dev/guide/components/lifecycle).

Exploraremos cada uno a través de un ejemplo en las siguientes secciones. Comencemos con el hook `ngOnInit`, que es el evento de ciclo de vida más básico de un componente.

#### Realización de la inicialización del componente
El hook de ciclo de vida `ngOnInit` es un método que se llama durante la inicialización del componente. Todos los enlaces de entrada y las propiedades vinculadas a datos se han establecido adecuadamente en esta etapa y podemos usarlos de manera segura. Puede ser tentador usar el constructor del componente para acceder a ellos, pero sus valores no se habrían establecido en ese momento. Aprenderemos a usar el hook de ciclo de vida `ngOnInit` a través del siguiente ejemplo:

1. Abre el archivo `product-detail.component.ts` y agrega un constructor que registre el valor de la propiedad `product` en la consola del navegador:

```typescript
constructor() {
  console.log('Product:', this.product());
}
```

2. Importa la interfaz `OnInit` del paquete npm `@angular/core`:

```typescript
import { Component, input, OnInit, output } from '@angular/core';
```

3. Agrega la interfaz `OnInit` a la lista de interfaces implementadas de la clase `ProductDetailComponent`:

```typescript
export class ProductDetailComponent implements OnInit
```

4. Agrega el siguiente método en la clase `ProductDetailComponent` para registrar la misma información que en el paso 1:

```typescript
ngOnInit(): void {
  console.log('Product:', this.product());
}
```

5. Abre el archivo `product-list.component.ts` y establece un valor inicial para la propiedad `selectedProduct`:

```typescript
selectedProduct: Product | undefined = this.products[0];
```

6. Ejecuta la aplicación usando el comando `ng serve` e inspecciona la salida de la consola del navegador:

*(Figura 3.13: Salida de la consola)*

El primer mensaje del constructor contiene un valor `undefined`, pero en el segundo mensaje, el valor de la propiedad `product` se muestra correctamente.

> Los constructores deben estar relativamente vacíos y desprovistos de lógica aparte de establecer variables iniciales. Agregar lógica de negocios dentro de un constructor dificulta su simulación (*mocking*) en escenarios de prueba.

Otro buen uso del hook `ngOnInit` es cuando necesitamos inicializar un componente con datos de una fuente externa, como un servicio de Angular, como aprenderemos en el *Capítulo 5: Gestión de tareas complejas con Servicios*.

El framework Angular proporciona ganchos para todas las etapas del ciclo de vida del componente, desde la inicialización hasta la destrucción.

#### Limpieza de recursos del componente
La interfaz que usamos para conectarnos al evento de destrucción de un componente es el hook de ciclo de vida `ngOnDestroy`. Necesitamos importar la interfaz `OnDestroy` e implementar el método `ngOnDestroy` para comenzar a usarla:

```typescript
import { Component, input, OnDestroy, output } from '@angular/core';
import { Product } from '../product';

@Component({
  selector: 'app-product-detail',
  imports: [],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
export class ProductDetailComponent implements OnDestroy {
  product = input<Product>();
  added = output();

  addToCart() {
    this.added.emit();
  }

  ngOnDestroy(): void {
  }
}
```

En el fragmento anterior, agregamos la interfaz `OnDestroy` e implementamos su método `ngOnDestroy`. Luego podemos agregar cualquier lógica personalizada en el método `ngOnDestroy` para ejecutar código cuando el componente se destruye.

Un componente se destruye cuando se elimina del árbol DOM de una página web debido a las siguientes razones:

- Uso del bloque `@if` de la sintaxis de flujo de control.
- Navegar fuera de un componente usando el enrutador de Angular, del cual aprenderemos en el *Capítulo 9: Navegación a través de aplicaciones con Enrutamiento*.

Por lo general, realizamos una limpieza de los recursos del componente dentro del método `ngOnDestroy`, tales como:

- Restablecimiento de temporizadores e intervalos (`setTimeout`, `setInterval`).
- Cancelación de la suscripción a flujos de observables, de los cuales aprenderemos en el *Capítulo 6: Patrones reactivos en Angular*.

Un método alternativo al hook de ciclo de vida `ngOnDestroy` es utilizar un servicio integrado de Angular como `DestroyRef`:

```typescript
import { Component, DestroyRef, input, output } from '@angular/core';
import { Product } from '../product';

@Component({
  selector: 'app-product-detail',
  imports: [],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
export class ProductDetailComponent {
  product = input<Product>();
  added = output();

  constructor(destroyRef: DestroyRef) {
    destroyRef.onDestroy(() => {
    });
  }

  addToCart() {
    this.added.emit();
  }
}
```

Como aprenderemos en el *Capítulo 5: Gestión de tareas complejas con Servicios*, el uso de un constructor es una forma de inyectar servicios de Angular en otros artefactos de Angular. En este caso, el servicio `destroyRef` expone el método `onDestroy`, que acepta una función de devolución de llamada (*callback*) como parámetro. Se llamará a la función callback cuando se destruya el componente.

Ya hemos aprendido a pasar datos a un componente mediante un enlace de entrada. El framework Angular proporciona el hook de ciclo de vida `ngOnChanges`, que podemos usar para inspeccionar cuándo ha cambiado el valor de dicho enlace.

#### Detección de cambios en los enlaces de entrada
El hook de ciclo de vida `ngOnChanges` se llama cuando Angular detecta que el valor de un enlace de datos de entrada ha cambiado. Lo usaremos en el componente de detalle del producto para ver cómo se comporta cuando seleccionamos un producto diferente de la lista:

1. Importa las interfaces `OnChanges` y `SimpleChanges` en el archivo `product-detail.component.ts`:

```typescript
import { Component, input, OnChanges, output, SimpleChanges } from '@angular/core';
```

2. Modifica la definición de la clase `ProductDetailComponent` para que implemente la interfaz `OnChanges`:

```typescript
export class ProductDetailComponent implements OnChanges
```

3. Implementa el método `ngOnChanges` que se define en la interfaz `OnChanges`. Acepta un objeto del tipo `SimpleChanges` como parámetro que contiene una clave para cada propiedad de entrada que cambia. Cada clave apunta a otro objeto con las propiedades `currentValue` y `previousValue`, que denotan el valor nuevo y el valor antiguo de la propiedad de entrada, respectivamente:

```typescript
ngOnChanges(changes: SimpleChanges): void {
  const product = changes['product'];
  const oldValue = product.previousValue;
  const newValue = product.currentValue;
  console.log('Old value', oldValue);
  console.log('New value', newValue);
}
```

El fragmento anterior realiza un seguimiento de la propiedad de entrada `product` para detectar cambios y registra los valores antiguos y nuevos en la consola del navegador.

4. Para inspeccionar la aplicación, ejecuta el comando `ng serve`, selecciona un producto de la lista y observa la salida en la consola. Deberías obtener algo como lo siguiente:

*(Figura 3.14: Salida de la consola)*

En la imagen anterior, las dos primeras líneas indican que el valor de `product` cambió de `undefined` a `undefined`. Es el momento real en que se inicializa el componente de detalle del producto y la propiedad `product` aún no tiene ningún valor. El evento de ciclo de vida `OnChanges` se activa una vez que el valor se establece por primera vez y en todos los cambios posteriores que se producen a través del mecanismo de enlace.

5. Para eliminar los mensajes de registro innecesarios, podemos comprobar si es la primera vez que se modifica la propiedad `product` utilizando el método `isFirstChange`:

```typescript
ngOnChanges(changes: SimpleChanges): void {
  const product = changes['product'];
  if (!product.isFirstChange()) {
    const oldValue = product.previousValue;
    const newValue = product.currentValue;
    console.log('Old value', oldValue);
    console.log('New value', newValue);
  }
}
```

Si actualizamos el navegador, podemos ver el mensaje correcto en la ventana de la consola.

El hook de ciclo de vida `ngOnChanges` es una excelente manera de detectar cuándo cambia el valor de una propiedad de entrada. Con la llegada de la API de Signals, disponemos de métodos mucho mejores para detectar y reaccionar a estos cambios, como aprenderemos en el *Capítulo 7: Seguimiento del estado de la aplicación con Signals*. Sin embargo, para versiones anteriores de Angular, el hook sigue siendo la solución preferida.

El último evento de ciclo de vida de un componente Angular que exploraremos es el hook `ngAfterViewInit`.

#### Acceso a componentes hijos
El hook de ciclo de vida `ngAfterViewInit` de un componente de Angular se llama cuando:

- La plantilla HTML del componente ha sido inicializada.
- Las plantillas HTML de todos los componentes secundarios han sido inicializadas.

Podemos explorar cómo funciona el evento `ngAfterViewInit` utilizando los componentes de lista de productos y detalle de productos:

1. Abre el archivo `product-list.component.ts` e importa los artefactos `AfterViewInit` y `viewChild` del paquete npm `@angular/core`:

```typescript
import { AfterViewInit, Component, viewChild } from '@angular/core';
```

2. Crea la siguiente propiedad en la clase `ProductListComponent`:

```typescript
productDetail = viewChild(ProductDetailComponent);
```

Ya hemos aprendido a consultar una clase de componente desde una plantilla HTML utilizando variables de referencia local. Alternativamente, podemos usar la función `viewChild` para consultar un componente secundario desde la clase del componente padre.

> En versiones anteriores de Angular, usamos el decorador `@ViewChild` para consultar componentes secundarios. Puedes obtener más información en [https://angular.dev/guide/components/queries](https://angular.dev/guide/components/queries).

La función `viewChild` acepta el tipo de componente que queremos consultar como parámetro.

3. Modifica la definición de la clase `ProductListComponent` para que implemente la interfaz `AfterViewInit`:

```typescript
export class ProductListComponent implements AfterViewInit
```

4. La interfaz `AfterViewInit` implementa el método `ngAfterViewInit`, que podemos usar para acceder a la propiedad `productDetail`:

```typescript
ngAfterViewInit(): void {
  console.log(this.productDetail()!.product());
}
```

Cuando consultamos la propiedad `productDetail`, obtenemos una instancia de la clase `ProductDetailComponent`. Luego podemos acceder a cualquier miembro de su API pública, como la propiedad `product`.

Ejecutar el código anterior mostrará un valor `undefined` para la propiedad `product` porque no establecemos un valor inicial cuando se inicializa el componente de detalle del producto.

El evento de ciclo de vida `ngAfterViewInit` concluye nuestro recorrido por el ciclo de vida de los componentes de Angular. Los ganchos del ciclo de vida de los componentes son una característica útil del framework y los usarás mucho para desarrollar aplicaciones Angular.

---

### Sección 3.8: Resumen

En este capítulo exploramos los componentes de Angular. Vimos su estructura y cómo crearlos, y discutimos cómo aislar la plantilla HTML de un componente en un archivo externo para facilitar su mantenimiento futuro. Además, vimos cómo hacer lo mismo con cualquier hoja de estilos que quisiéramos vincular al componente en caso de que no quisiéramos incluir los estilos del componente en línea. También aprendimos a usar la sintaxis de plantillas de Angular e interactuar con la plantilla del componente. Del mismo modo, revisamos cómo los componentes se comunican bidireccionalmente mediante enlaces de propiedades y eventos.

Revisamos las opciones disponibles en Angular para crear APIs potentes para nuestros componentes, de modo que pudiéramos proporcionar altos niveles de interoperabilidad entre componentes, configurando sus propiedades asignando valores estáticos o enlaces administrados. También vimos cómo un componente puede actuar como un componente anfitrión para otro componente secundario, instanciando el elemento personalizado de este último en su plantilla y sentando las bases para árboles de componentes más grandes en nuestras aplicaciones. Los parámetros de salida nos brindan la capa de interactividad que necesitamos al convertir nuestros componentes en emisores de eventos para que puedan comunicarse adecuadamente con cualquier componente padre que eventualmente los aloje.

Las referencias de plantilla allanaron el camino para que creemos referencias en nuestros elementos personalizados, que podemos usar como descriptores de acceso a sus propiedades y métodos desde dentro de la plantilla de manera declarativa. Una descripción general de las características integradas para el manejo de la encapsulación de vistas CSS en Angular nos brindó información adicional sobre cómo podemos beneficiarnos del alcance de CSS por componente de Shadow DOM. Finalmente, aprendimos lo importante que es la detección de cambios en una aplicación Angular y cómo podemos personalizarla para mejorar aún más su rendimiento.

También estudiamos el ciclo de vida del componente y aprendimos a ejecutar lógica personalizada utilizando los ganchos de ciclo de vida integrados de Angular. Todavía tenemos mucho más que aprender sobre la gestión de plantillas en Angular, principalmente sobre dos conceptos que utilizarás en tu viaje con Angular: directivas y pipes, que cubriremos en el próximo capítulo.
