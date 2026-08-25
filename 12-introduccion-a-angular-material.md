# Parte 4: Calidad, Rendimiento y Producción

## Capítulo 12: Introducción a Angular Material

Al desarrollar una aplicación web, debes decidir cómo crear tu interfaz de usuario (UI). Idealmente, debería utilizar colores contrastantes adecuados, tener una apariencia coherente, ser receptiva (*responsive*) y funcionar bien en diferentes dispositivos y navegadores. En resumen, hay muchas cosas a considerar con respecto a la UI y la UX. Muchos desarrolladores consideran que la creación de la UI/UX es una tarea abrumadora y recurren a frameworks de UI que hacen gran parte del trabajo pesado. Algunos frameworks se utilizan más que otros, a saber, Bootstrap y Tailwind CSS. Sin embargo, Angular Material, un framework basado en las técnicas de Material Design de Google, ha ganado popularidad. En este capítulo, explicaremos qué es Material Design y cómo Angular Material lo utiliza para proporcionar una librería de componentes de interfaz de usuario para el framework Angular. También aprenderemos a utilizar varios componentes de Angular Material aplicándolos en nuestra aplicación de tienda online.

En este capítulo, realizaremos lo siguiente:

- Introducción a Material Design
- Introducción a Angular Material
- Integración de componentes de UI

---

### Sección 12.1: Requisitos técnicos

El capítulo contiene varios ejemplos de código para guiarte a través del concepto de Angular Material. Puedes encontrar el código fuente correspondiente en la carpeta `ch12` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch12](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch12)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 12.2: Introducción a Material Design

Material Design es un lenguaje de diseño desarrollado por Google teniendo en cuenta los siguientes objetivos:

- Desarrollar un sistema subyacente único que permita una experiencia unificada en todas las plataformas y tamaños de dispositivos.
- Los preceptos móviles son fundamentales, pero el tacto, la voz, el ratón y el teclado son métodos de entrada de primer nivel.

El propósito de un lenguaje de diseño es hacer que el usuario experimente cómo debe verse y sentirse la interfaz de usuario y la interacción del usuario en todos los dispositivos. Material Design se basa en tres principios fundamentales:

- **El material es la metáfora (*Material is the metaphor*):** Se inspira en el mundo físico con diferentes texturas y medios, como el papel y la tinta.
- **Audaz, gráfico e intencional (*Bold, graphic, and intentional*):** Se guía por diferentes métodos de diseño de impresión, como tipografía, cuadrículas y color, para crear una experiencia inmersiva para el usuario.
- **El movimiento proporciona significado (*Motion provides meaning*):** Los elementos se muestran en la pantalla mediante la creación de animaciones e interacciones que reorganizan el entorno.

Material Design tiene mucha teoría detrás y hay documentación disponible si deseas profundizar más. Puedes encontrar más información en el sitio de documentación oficial: [https://material.io](https://material.io/).

Un lenguaje de diseño por sí solo no es tan interesante si no eres diseñador. En la siguiente sección, aprenderemos cómo los desarrolladores de Angular pueden beneficiarse de Material Design utilizando la librería Angular Material.

---

### Sección 12.3: Introducción a Angular Material

La librería Angular Material fue desarrollada para implementar Material Design para el framework Angular. Se basa en los siguientes conceptos:

- **Acelera desde cero hasta la app (*Sprint from zero to app*):** La intención es facilitarte, como desarrollador de aplicaciones, ponerte manos a la obra de inmediato. El esfuerzo necesario para configurarlo debe ser mínimo.
- **Rápido y consistente (*Fast and consistent*):** El rendimiento ha sido un punto de enfoque importante, y se garantiza que Angular Material funcione bien en todos los navegadores principales.
- **Versátil (*Versatile*):** Muchos temas deberían ser fácilmente personalizables y también hay un gran soporte para la localización e internacionalización.
- **Optimizado para Angular (*Optimized for Angular*):** El hecho de que el equipo de Angular lo haya construido significa que el soporte para Angular es una gran prioridad.

La librería se divide en las siguientes partes principales:

- **Componentes (*Components*):** Hay muchos componentes de UI disponibles, como diferentes tipos de entradas (*inputs*), botones, diseño (*layout*), navegación, modales y otras formas de mostrar datos tabulares, para ayudarte a tener éxito.
- **Temas (*Themes*):** La librería viene con temas preinstalados, pero también hay una guía de temas si deseas crear el tuyo propio en [https://material.angular.io/guide/theming](https://material.angular.io/guide/theming).

> Cada parte y componente de la librería Angular Material encapsula las mejores técnicas de accesibilidad web de fábrica.

El núcleo de la librería Angular Material es el **Angular CDK**, que es una colección de herramientas que implementan patrones de interacción similares no relacionados con ningún estilo de presentación. El comportamiento de los componentes de Angular Material se ha diseñado utilizando el Angular CDK. El Angular CDK es tan abstracto que puedes usarlo para crear componentes personalizados. Deberías considerarlo seriamente si eres autor de una librería de UI.

Hemos cubierto toda la teoría básica sobre Angular Material, así que pongámosla en práctica en la siguiente sección integrándola con una aplicación Angular.

#### Instalación de Angular Material
La librería Angular Material es un paquete npm. Para instalarlo, debemos ejecutar manualmente el comando `npm install` e importar varios artefactos de Angular en nuestra aplicación Angular. El equipo de Angular ha automatizado estas interacciones creando los esquemáticos (*schematics*) necesarios para instalarlo utilizando Angular CLI.

> Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 11: Manejo de errores de la aplicación* para seguir el resto del capítulo.

Podemos usar el comando `ng add` de Angular CLI para instalar Angular Material en nuestra aplicación de tienda online:

1. Ejecuta el siguiente comando en el espacio de trabajo actual de Angular CLI:

```bash
ng add @angular/material
```

> **Consejo rápido:** Mejora tu experiencia de programación con las funciones AI Code Explainer y Quick Copy en el lector de última generación de Packt: [https://www.packtpub.com/unlock/9781835087480](https://www.packtpub.com/unlock/9781835087480).

Angular CLI encontrará la última versión estable de la librería Angular Material y nos pedirá que la descarguemos.

> En este libro, trabajamos con Angular Material 19, que es compatible con Angular 19. Si la versión que te solicita es diferente, debes ejecutar el comando `ng add @angular/material@19` para instalar el último Angular Material 19 en tu sistema.

2. Una vez completada la descarga, nos preguntará si queremos usar un tema prediseñado para nuestra aplicación Angular o uno personalizado:

```text
Choose a prebuilt theme name, or "custom" for a custom theme: (Use arrow keys)
```

Acepta el valor predeterminado, `Azure/Blue`, presionando Enter.

3. Después de seleccionar un tema, Angular CLI nos preguntará si queremos configurar estilos tipográficos globales en nuestra aplicación. La tipografía se refiere a cómo se organiza el texto en nuestra aplicación:

```text
Set up global Angular Material typography styles? (y/N)
```

Queremos mantener nuestra aplicación lo más simple posible, así que acepta el valor predeterminado, `No`, presionando Enter.

> La tipografía de Angular Material se basa en las directrices de Material Design y utiliza la fuente de Google Roboto para el estilo.

4. La siguiente pregunta es sobre animaciones. Las animaciones no son estrictamente necesarias, pero queremos que nuestra aplicación muestre una animación agradable cuando hacemos clic en un botón o abrimos un diálogo modal:

```text
Include the Angular animations module? (Use arrow keys)
```

Acepta el valor predeterminado, `Include and enable animations`, presionando Enter.

Angular CLI comenzará a instalar y configurar Angular Material en nuestra aplicación. Creará la estructura e importará todos los artefactos necesarios para que podamos comenzar a trabajar con Angular Material de inmediato:

- **`angular.json`:** Agrega el archivo de hoja de estilos del tema en el archivo de configuración del espacio de trabajo de Angular CLI:

```json
"styles": [
  "@angular/material/prebuilt-themes/azure-blue.css",
  "src/styles.css"
]
```

- **`package.json`:** Agrega los paquetes npm `@angular/cdk` y `@angular/material`.
- **`index.html`:** Agrega los archivos de hojas de estilo de las fuentes Roboto y los iconos de Material en el archivo HTML principal.
- **`styles.css`:** Agrega los estilos CSS globales necesarios para las etiquetas `<html>` y `<body>`:

```css
html, body { height: 100%; }
body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
```

- **`app.config.ts`:** Habilita las animaciones en el archivo de configuración de la aplicación:

```typescript
import { provideHttpClient } from '@angular/common/http';
import { ApplicationConfig, ErrorHandler, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';
import { APP_SETTINGS, appSettings } from './app.settings';
import { AppErrorHandler } from './app-error-handler';
import { provideAnimationsAsync } from '@angular/platform-browser/animations/async';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient(),
    { provide: APP_SETTINGS, useValue: appSettings },
    { provide: ErrorHandler, useClass: AppErrorHandler },
    provideAnimationsAsync()
  ]
};
```

Una vez finalizado el proceso, podemos comenzar a agregar componentes de UI de la librería Angular Material a nuestra aplicación.

#### Adición de componentes de UI
El componente de botón es uno de los componentes más utilizados de la librería Angular Material. Como ejemplo, aprenderemos lo fácil que es agregar un componente de botón a nuestra aplicación de tienda online. Antes de que podamos usarlo en nuestra aplicación Angular, debemos eliminar todos los estilos CSS para la etiqueta nativa `<button>` que hemos usado hasta ahora:

1. Abre el archivo `styles.css` y elimina los estilos CSS `button`, `button:hover` y `button:disabled`.
2. Abre el archivo `product-detail.component.css` y elimina la variable `--button-accent` de los estilos `button.secondary` y `button.delete`.
3. Elimina el estilo CSS `.button-group` por completo.
4. Agrega un color en el estilo `button.delete`:

```css
button.delete {
  display: inline;
  margin-left: 5px;
  color: brown;
}
```

Para comenzar a usar un componente de UI de la librería Angular Material, debemos importar su componente Angular correspondiente. Veamos cómo se hace esto agregando un componente de botón en el componente de autenticación de la aplicación Angular:

5. Abre el archivo `auth.component.ts` y agrega la siguiente sentencia de importación para usar los botones de Angular Material:

```typescript
import { MatButton } from '@angular/material/button';
```

> No importamos directamente desde el paquete `@angular/material` porque cada componente tiene un espacio de nombres dedicado. El componente de botón se puede encontrar en el espacio de nombres `@angular/material/button`.
>
> Los componentes de Angular Material también se pueden usar importando su módulo respectivo, como `MatButtonModule` para botones. Sin embargo, se recomienda importar los componentes directamente, ya que nos ayuda a mantenernos coherentes con los patrones modernos de Angular. No obstante, veremos que algunas funciones requieren demasiados componentes para importar. En esos casos, es aceptable importar el módulo directamente.

6. Agrega la clase `MatButton` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-auth',
  imports: [MatButton],
  templateUrl: './auth.component.html',
  styleUrl: './auth.component.css'
})
```

7. Abre el archivo `auth.component.html` y agrega la directiva `mat-button` en los elementos HTML `<button>`:

```html
@if (!authService.isLoggedIn()) {
  <button mat-button (click)="login()">Login</button>
} @else {
  <button mat-button (click)="logout()">Logout</button>
}
```

En la plantilla anterior, la directiva `mat-button`, en esencia, modifica el elemento `<button>` para que aparezca y se comporte como un botón de Material Design.

Si ejecutamos el comando `ng serve` y navegamos a `http://localhost:4200`, notaremos que el estilo del botón es diferente al de antes. Se parece más a un enlace, que es la apariencia predeterminada de un botón de Material. En la siguiente sección, aprenderemos sobre la tematización y las variaciones del componente de botón.

#### Tematización de componentes de UI
La librería Angular Material viene con cuatro temas prediseñados:

- Azure/Blue
- Rose/Red
- Magenta/Violet
- Cyan/Orange

Cuando agregamos Angular Material a una aplicación Angular, podemos elegir cuál de los temas anteriores queremos aplicar. Siempre podemos cambiarlo modificando el archivo de hoja de estilos CSS incluido en el archivo de configuración `angular.json`. He aquí un ejemplo:

```json
"styles": [
  "/@angular/material/prebuilt-themes/azure-blue.css",
  "src/styles.css"
]
```

Como vimos en la sección anterior, el componente de botón se muestra como un enlace. La directiva `mat-button` muestra un color de fondo solo cuando pasamos el cursor sobre el botón. Para establecer el color de fondo de forma permanente, debemos utilizar la directiva `mat-flat-button` de la siguiente manera:

```html
@if (!authService.isLoggedIn()) {
  <button mat-flat-button (click)="login()">
    Login
  </button>
} @else {
  <button mat-flat-button (click)="logout()">
    Logout
  </button>
}
```

Ahora que sabemos cómo interactuar con el componente de botón en una aplicación Angular, aprendamos algunas de sus variaciones:

1. Abre el archivo `product-create.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { MatButton } from '@angular/material/button';
```

2. Agrega la clase `MatButton` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-create',
  imports: [ReactiveFormsModule, MatButton],
  templateUrl: './product-create.component.html',
  styleUrl: './product-create.component.css'
})
```

3. Abre el archivo `product-create.component.html` y agrega la directiva `mat-raised-button` en el elemento HTML `<button>`:

```html
<button mat-raised-button type="submit" [disabled]="productForm.invalid">
  Create
</button>
```

La directiva `mat-raised-button` agregará una sombra al elemento del botón:

*(Figura 12.1: Botón elevado)*

4. Abre el archivo `product-detail.component.ts` y repite los pasos 1 y 2.
5. Abre el archivo `product-detail.component.html` y agrega la directiva `mat-stroked-button` en el botón *Change*:

```html
<button mat-stroked-button class="secondary" type="submit" [disabled]="priceForm.invalid">
  Change
</button>
```

La directiva `mat-stroked-button` agrega un borde alrededor del elemento del botón:

*(Figura 12.2: Botón con borde)*

6. Elimina el elemento HTML `<div>` con la clase `button-group` y agrega la directiva `mat-raised-button` en ambos elementos HTML `<button>`:

```html
@if (authService.isLoggedIn()) {
  <button mat-raised-button (click)="addToCart(product.id)">
    Add to cart
  </button>
}
<button mat-raised-button class="delete" (click)="remove(product)">
  Delete
</button>
```

Los dos botones aparecen de la siguiente manera cuando ejecutamos la aplicación:

*(Figura 12.3: Botones de acción de detalles del producto)*

7. Abre el archivo `product-list.component.ts` y agrega las siguientes sentencias de importación:

```typescript
import { MatMiniFabButton } from '@angular/material/button';
import { MatIcon } from '@angular/material/icon';
```

8. Agrega las clases importadas anteriores en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-list',
  imports: [
    SortPipe,
    AsyncPipe,
    RouterLink,
    MatMiniFabButton,
    MatIcon
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

9. Abre el archivo `product-list.component.html` y reemplaza el elemento de anclaje que navega al componente de creación de productos con el siguiente fragmento HTML:

```html
<button mat-mini-fab routerLink="new">
  <mat-icon>add</mat-icon>
</button>
```

La directiva `mat-mini-fab` muestra un botón circular/cuadrado con esquinas redondeadas y un icono indicado por el elemento HTML `<mat-icon>`. El texto del elemento `<mat-icon>` corresponde al nombre del icono `add` de la colección de iconos de Material Design:

*(Figura 12.4: Botón FAB)*

> La tematización en Angular Material es tan amplia que podemos usar variables CSS existentes para crear temas personalizados, un tema que está fuera del alcance de este libro.

Para continuar nuestro viaje a través del mundo del estilo con Angular Material, aprenderemos a integrar varios componentes de UI en la siguiente sección.

---

### Sección 12.4: Integración de componentes de UI

Angular Material contiene una gran cantidad de componentes de UI organizados en categorías en [https://material.angular.io/components/categories](https://material.angular.io/components/categories). En este capítulo, exploraremos un subconjunto de la colección anterior que se puede agrupar en las siguientes categorías:

- **Controles de formulario (*Form controls*):** Se pueden usar dentro de un formulario de Angular, como autocompletar, entrada y lista desplegable.
- **Navegación (*Navigation*):** Proporcionan capacidades de navegación, como un encabezado y un pie de página.
- **Diseño (*Layout*):** Definen cómo se representan los datos, como una tarjeta o una tabla.
- **Ventanas emergentes y superposiciones (*Popups and overlays*):** Son ventanas superpuestas que muestran información y pueden bloquear cualquier interacción del usuario hasta que se descarten.

En las siguientes secciones, exploraremos cada categoría con más detalle.

#### Controles de formulario
Aprendimos en el *Capítulo 10: Recopilación de datos del usuario con Formularios* que los controles de formulario se encargan de recopilar datos de entrada de diferentes maneras y tomar acciones adicionales, como enviar datos a una API backend a través de HTTP.

Hay bastantes controles de formulario en la librería Angular Material de varios tipos, a saber, los siguientes:

- **`Autocomplete`:** Permite al usuario comenzar a escribir en un campo de entrada y recibir sugerencias mientras escribe. Ayuda a reducir los posibles valores que puede tomar la entrada.
- **`Checkbox`:** Una casilla de verificación clásica que representa un estado marcado o desmarcado.
- **`Date picker`:** Permite al usuario seleccionar una fecha en un calendario.
- **`Input`:** Un control de entrada clásico mejorado con una animación significativa al escribir.
- **`Radio button`:** Un botón de radio clásico mejorado con animaciones y transiciones durante la edición para crear una mejor experiencia de usuario.
- **`Select`:** Un control desplegable que solicita al usuario que seleccione uno o más elementos de una lista.
- **`Slider`:** Permite al usuario aumentar o disminuir un valor arrastrando un botón deslizante hacia la derecha o hacia la izquierda.
- **`Slide toggle`:** Un interruptor que el usuario puede deslizar para encenderlo o apagarlo.
- **`Chips`:** Una lista que muestra, selecciona y filtra elementos.

En las siguientes secciones, examinaremos algunos de estos controles de formulario con más detalle. Comencemos con el componente de entrada (*input*).

##### Input
El componente de entrada generalmente se adjunta a un elemento HTML `<input>`. También podemos agregar la capacidad de mostrar errores en el campo de entrada.

Antes de que podamos usar el componente de entrada en nuestra aplicación Angular, debemos eliminar todos los estilos CSS para la etiqueta nativa `<input>` que hemos usado hasta ahora:

1. Abre el archivo `styles.css` y elimina cualquier estilo CSS que haga referencia a la etiqueta `input`.
2. Elimina el estilo CSS `input` de los archivos `product-create.component.css` y `cart.component.css`.

Para aprender a usar el componente de entrada, lo integraremos en los componentes de nuestra aplicación:

3. Abre el archivo `product-create.component.ts` y agrega las siguientes sentencias de importación:

```typescript
import { MatInput } from '@angular/material/input';
import { MatFormField, MatError, MatLabel } from '@angular/material/form-field';
```

4. Agrega las clases importadas anteriores en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-create',
  imports: [
    ReactiveFormsModule,
    MatButton,
    MatInput,
    MatFormField,
    MatError,
    MatLabel
  ],
  templateUrl: './product-create.component.html',
  styleUrl: './product-create.component.css'
})
```

5. Abre el archivo `product-create.component.html` y reemplaza las etiquetas `<div>` de los elementos HTML `<input>` de la siguiente manera:

```html
<mat-form-field>
  <mat-label>Title</mat-label>
  <input formControlName="title" matInput required />
  <mat-error>Title is required</mat-error>
</mat-form-field>
<mat-form-field>
  <mat-label>Price</mat-label>
  <input formControlName="price" matInput type="number" required />
  @if (productForm.controls.price.touched && productForm.controls.price.hasError('required')) {
    <mat-error>Price is required</mat-error>
  }
  @if (productForm.controls.price.touched && productForm.controls.price.hasError('min')) {
    <mat-error>Price should be greater than 0</mat-error>
  }
  @if (productForm.controls.price.touched && productForm.controls.price.hasError('priceMaximum')) {
    <mat-error>Price must be smaller or equal to 1000</mat-error>
  }
</mat-form-field>
```

En el fragmento HTML anterior, usamos la directiva `matInput` para indicar que un elemento HTML `<input>` es un componente de entrada de Angular Material. Un control de formulario en Angular Material debe estar encerrado en un elemento `<mat-form-field>`.

Hemos reemplazado todos los elementos HTML `<label>` con elementos `<mat-label>`. Un elemento HTML `<mat-label>` es una etiqueta que se dirige a un control de formulario específico de Angular Material.

El elemento `<mat-error>` muestra mensajes de error en los controles de formulario cuando Angular desencadena errores de validación. Se muestra de forma predeterminada cuando el estado del control de formulario no es válido. En todos los demás casos, podemos usar un bloque `@if` para controlar cuándo se mostrará el elemento `<mat-error>`.

6. Abre el archivo global `styles.css` y agrega el siguiente estilo CSS:

```css
mat-form-field {
  width: 100%;
}
```

En el fragmento anterior, configuramos los elementos `mat-form-field` para que ocupen todo el ancho disponible.

7. Ejecuta el comando `ng serve` para iniciar la aplicación y navega a `http://localhost:4200/products/new`. Concéntrate en la apariencia de los campos de entrada:

*(Figura 12.5: Componente de entrada)*

En la figura anterior, la etiqueta de cada control de formulario tiene el sufijo de un asterisco. El asterisco es una indicación común de que el control de formulario debe tener un valor. Angular Material lo agrega automáticamente al reconocer el atributo `required` en el elemento HTML `<input>`.

8. Abre el archivo `cart.component.ts` y repite los pasos 3 y 4, pero no incluyas la clase `MatError`.
9. Abre el archivo `cart.component.html` y modifica el contenido del bloque `@for` de la siguiente manera:

```html
@for(product of cartForm.controls.products.controls; track $index) {
  <mat-form-field>
    <mat-label>{{products[$index].title}}</mat-label>
    <input [formControlName]="$index" placeholder="{{products[$index].title}}" type="number" matInput />
  </mat-form-field>
}
```

El componente restante de nuestra aplicación que contiene un elemento HTML `<input>` es el componente de detalles del producto. El componente de detalles del producto es un caso especial de una entrada de Angular Material porque debemos agruparlo con el botón que cambia el precio del producto:

10. Abre el archivo `product-detail.component.ts` y modifica la sentencia de importación del paquete npm de Angular Material de la siguiente manera:

```typescript
import { MatButton, MatIconButton } from '@angular/material/button';
import { MatInput } from '@angular/material/input';
import { MatFormField, MatError, MatSuffix } from '@angular/material/form-field';
import { MatIcon } from '@angular/material/icon';
```

11. Agrega las clases importadas anteriores en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-detail',
  imports: [
    CommonModule,
    FormsModule,
    PriceMaximumDirective,
    MatButton,
    MatInput,
    MatFormField,
    MatError,
    MatIcon,
    MatSuffix,
    MatIconButton
  ],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
```

12. Abre el archivo `product-detail.component.html` y modifica el elemento HTML `<form>` de la siguiente manera:

```html
<form (ngSubmit)="changePrice(product)" #priceForm="ngForm">
  <mat-form-field>
    <input placeholder="New price" type="number" name="price" required min="1" appPriceMaximum threshold="500" matInput #priceCtrl="ngModel" [(ngModel)]="price" />
    <button mat-icon-button matSuffix type="submit" [disabled]="priceForm.invalid">
      <mat-icon>edit</mat-icon>
    </button>
    @if (priceCtrl.dirty && (priceCtrl.invalid || priceCtrl.hasError('min'))) {
      <mat-error></mat-error>Please enter a valid price
    }
    @if (priceCtrl.dirty && priceCtrl.hasError('priceMaximum')) {
      <mat-error></mat-error>Price must be smaller or equal to 500
    }
  </mat-form-field>
</form>
```

En el fragmento anterior, modificamos el botón que cambia el precio para que muestre un icono de lápiz y se coloque en línea con el elemento HTML `<input>`.

> La directiva `mat-icon-button` indica que el botón no tendrá ningún texto. En su lugar, mostrará un icono definido por el elemento HTML `<mat-icon>`. La directiva `matSuffix` posiciona el botón en línea y al final del elemento HTML `<input>`.

Navega a la lista de productos en el navegador y selecciona un producto. La entrada para cambiar el precio del producto debe ser la siguiente:

*(Figura 12.6: Componente de entrada con botón en línea)*

En la siguiente sección, aprenderemos cómo usar un componente de selección de Angular Material para elegir una categoría en el componente de creación de productos.

##### Select
El componente de selección funciona de manera similar al elemento HTML nativo `<select>`. Muestra un elemento desplegable con una lista de opciones para los usuarios.

Agregaremos uno en el componente de creación de productos para seleccionar la categoría de un nuevo producto:

1. Abre el archivo `product-create.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { MatSelect, MatOption } from '@angular/material/select';
```

2. Agrega las clases importadas anteriores en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-create',
  imports: [
    ReactiveFormsModule,
    MatButton,
    MatInput,
    MatFormField,
    MatError,
    MatLabel,
    MatSelect,
    MatOption
  ],
  templateUrl: './product-create.component.html',
  styleUrl: './product-create.component.css'
})
```

3. Abre el archivo `product-create.component.html` y reemplaza el elemento HTML `<div>` que encierra el elemento `<select>` con el siguiente fragmento HTML:

```html
<mat-form-field>
  <mat-label>Category</mat-label>
  <mat-select formControlName="category">
    <mat-option value="electronics">Electronics</mat-option>
    <mat-option value="jewelery">Jewelery</mat-option>
    <mat-option>Other</mat-option>
  </mat-select>
</mat-form-field>
```

En el fragmento anterior, reemplazamos los elementos HTML `<select>` y `<option>` con los elementos `<mat-select>` y `<mat-option>`, respectivamente.

Navega a `http://localhost:4200/products/new` y haz clic en la lista desplegable *Category*:

*(Figura 12.7: Componente Select)*

El componente de detalles del producto muestra la categoría del producto como un elemento de párrafo con una clase CSS específica. En la siguiente sección, aprenderemos a representar la categoría del producto con el componente de chips de Angular Material.

##### Chips
El componente de chips a menudo se usa para mostrar información agrupada por una propiedad específica. También puede proporcionar capacidades de filtrado y selección de datos. Podemos usar chips en nuestra aplicación para mostrar la categoría en el componente de detalles del producto.

> Nuestros productos solo tienen una categoría, pero los chips tendrían más sentido si tuviéramos categorías adicionales asignadas a nuestros productos.

Comencemos:

1. Abre el archivo `product-detail.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { MatChipSet, MatChip } from '@angular/material/chips';
```

2. Agrega las clases importadas anteriores en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-detail',
  imports: [
    CommonModule,
    FormsModule,
    PriceMaximumDirective,
    MatButton,
    MatInput,
    MatFormField,
    MatError,
    MatIcon,
    MatSuffix,
    MatIconButton,
    MatChipSet,
    MatChip
  ],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
```

3. Abre el archivo `product-detail.component.html` y reemplaza el elemento HTML `<div>` que contiene la clase `pill-group` con el siguiente contenido:

```html
<mat-chip-set>
  <mat-chip>{{ product.category }}</mat-chip>
</mat-chip-set>
```

El elemento HTML `<mat-chip>` indica un componente de chip. Los chips siempre deben estar encerrados utilizando un elemento contenedor. La forma más simple de un contenedor de chips es el elemento `<mat-chip-set>`.

4. Abre el archivo `product-detail.component.css` y agrega el siguiente estilo CSS:

```css
mat-chip-set {
  margin-bottom: 1.375rem;
}
```

Ejecuta el comando `ng serve` para iniciar la aplicación y selecciona un producto de la lista. La categoría debería, por ejemplo, verse de la siguiente manera:

*(Figura 12.8: Componente Chips)*

El componente de chips completa nuestra exploración de los controles de formulario de Angular Material. En la siguiente sección, obtendremos experiencia práctica al diseñar el diseño de navegación de la aplicación.

#### Navegación
Existen diferentes formas de navegar en una aplicación Angular, como hacer clic en un enlace o en un elemento del menú. Angular Material ofrece los siguientes componentes para este tipo de interacción:

- **`Menu`:** Una lista emergente donde puedes elegir entre un conjunto predefinido de opciones.
- **`Sidenav`:** Un componente que actúa como un menú acoplado a la izquierda o a la derecha de la página. Se puede presentar como una superposición sobre la aplicación mientras atenúa el contenido de la misma.
- **`Toolbar`:** Una barra de herramientas estándar que permite al usuario acceder a acciones de uso común.

En esta sección, demostraremos cómo usar el componente de barra de herramientas (*toolbar*). Convertiremos los elementos HTML `<header>` y `<footer>` del componente principal de la aplicación en barras de herramientas de Angular Material.

Para crear una barra de herramientas, seguiremos los siguientes pasos:

1. Abre el archivo `app.component.ts` y agrega las siguientes sentencias de importación:

```typescript
import { MatToolbarRow, MatToolbar } from '@angular/material/toolbar';
import { MatButton } from '@angular/material/button';
```

2. Agrega las clases importadas anteriores en el array `imports` del decorador `@Component` y elimina la clase `RouterLinkActive`:

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
    MatButton
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
```

3. Abre el archivo `app.component.html` y modifica el elemento HTML `<header>` de la siguiente manera:

```html
<header>
  <mat-toolbar>
    <mat-toolbar-row>
      <h2>{{ settings.title }}</h2>
      <span class="spacer"></span>
      <button mat-button routerLink="/products">Products</button>
      <button mat-button routerLink="/cart">My Cart</button>
      <button mat-button routerLink="/user">My Profile</button>
      <app-auth></app-auth>
    </mat-toolbar-row>
  </mat-toolbar>
</header>
```

En la plantilla anterior, agregamos los enlaces principales de la aplicación y el componente de autenticación dentro de un elemento `<mat-toolbar>`. El componente de la barra de herramientas consta de una sola fila indicada por el elemento HTML `<mat-toolbar-row>`.

4. Abre el archivo `app.component.css` y elimina el estilo CSS para la etiqueta `header` y los `menu-links`.

Si ejecutamos la aplicación utilizando el comando `ng serve`, veremos la nueva barra de herramientas de nuestra aplicación en la parte superior de la página:

*(Figura 12.9: Encabezado de la aplicación)*

5. Ahora, modifica el elemento HTML `<footer>` para convertirlo en un componente de barra de herramientas de Angular Material:

```html
<footer>
  <mat-toolbar>
    <mat-toolbar-row>
      <span appCopyright> - v{{ settings.version }}</span>
    </mat-toolbar-row>
  </mat-toolbar>
</footer>
```

Guarda los cambios, espera a que la aplicación se actualice y observa la barra de herramientas en la parte inferior de la aplicación:

*(Figura 12.10: Pie de página de la aplicación)*

La barra de herramientas es totalmente personalizable y podemos ajustarla según las necesidades de la aplicación. Podemos agregar iconos e incluso crear barras de herramientas con contenido en varias filas. Ahora que conoces los conceptos básicos para crear una barra de herramientas simple, puedes explorar más posibilidades.

En la siguiente sección, aprenderemos a organizar el contenido de manera diferente dentro de nuestra aplicación.

#### Diseño (Layout)
Cuando nos referimos al diseño (*layout*), discutimos cómo colocamos el contenido en nuestras plantillas. Angular Material nos brinda diferentes componentes para este propósito:

- **`List`:** Visualiza el contenido como una lista de elementos. Se puede enriquecer con enlaces e iconos e incluso en varias líneas.
- **`Grid list`:** Nos ayuda a organizar el contenido en bloques. Solo necesitamos definir el número de columnas; el componente llenará el espacio visual.
- **`Card`:** Envuelve el contenido y agrega una sombra de caja (*box shadow*). También podemos definir un encabezado para él.
- **`Tabs`:** Divide el contenido en diferentes pestañas.
- **`Stepper`:** Divide el contenido en pasos similares a los de un asistente (*wizard*).
- **`Expansion panel`:** Nos permite colocar el contenido en forma de lista con un título para cada elemento. Los elementos solo se pueden expandir uno a la vez.
- **`Table`:** Representa datos en un formato tabular con filas y columnas.

En este libro, cubriremos los componentes de tarjeta (*card*) y tabla (*table*).

##### Card
Aprenderemos a mostrar cada producto de la lista como una tarjeta:

1. Abre el archivo `product.ts` y agrega una propiedad `image` a la interfaz `Product`:

```typescript
export interface Product {
  id: number;
  title: string;
  price: number;
  category: string;
  image: string;
}
```

La propiedad `image` es una URL que apunta al archivo de imagen del producto en la Fake Store API.

2. Abre el archivo `product-list.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { MatCardModule } from '@angular/material/card';
```

3. Agrega la clase `MatCardModule` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-list',
  imports: [
    SortPipe,
    AsyncPipe,
    RouterLink,
    MatMiniFabButton,
    MatIcon,
    MatCardModule
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

> El componente de tarjeta de Angular Material consta de muchos otros componentes y directivas. Elegimos importar todo el módulo de Angular porque no sería conveniente importarlos todos individualmente.

4. Abre el archivo `product-list.component.html` y reemplaza el elemento de lista no ordenada con el siguiente fragmento HTML:

```html
@for (product of products | sort; track product.id) {
  <mat-card [routerLink]="[product.id]">
    <mat-card-header>
      <mat-card-title-group>
        <mat-card-title>{{ product.title }}</mat-card-title>
        <mat-card-subtitle>{{ product.category }}</mat-card-subtitle>
        <img mat-card-sm-image [src]="product.image" />
      </mat-card-title-group>
    </mat-card-header>
  </mat-card>
} @empty {
  <p>No products found!</p>
}
```

Un componente de tarjeta de Angular Material consta de un encabezado, indicado por el elemento HTML `<mat-card-header>`. El componente de encabezado contiene un elemento HTML `<mat-card-title-group>` que alinea el título, el subtítulo y la imagen de la tarjeta en una sola sección. El título de la tarjeta, indicado por el elemento HTML `<mat-card-title>`, muestra el título del producto. El subtítulo de la tarjeta, indicado por el elemento HTML `<mat-card-subtitle>`, muestra la categoría del producto. Finalmente, la imagen del producto se muestra adjuntando la directiva `mat-card-sm-image` a un elemento HTML `<img>`. La palabra clave `sm` en la directiva indica que queremos renderizar un tamaño pequeño de la imagen.

> Angular Material también admite `md` y `lg` para tamaños medianos y grandes, respectivamente.

5. Abre el archivo `product-list.component.css` y agrega el siguiente estilo CSS:

```css
mat-card {
  margin: 1.375rem;
  cursor: pointer;
}
```

6. Ejecuta la aplicación usando el comando `ng serve` y navega a `http://localhost:4200`:

*(Figura 12.11: Representación de tarjetas de la lista de productos)*

Puedes explorar más opciones para el componente de tarjeta navegando a [https://material.angular.io/components/card/overview](https://material.angular.io/components/card/overview).

En la siguiente sección, aprenderemos a cambiar la lista de productos a una vista tabular.

##### Tabla de datos (Data table)
El componente de tabla de la librería Angular Material nos permite mostrar nuestros datos en columnas y filas. Para crear una tabla, debemos importar la clase `MatTableModule` del espacio de nombres `@angular/material/table`.

> La tabla de datos de Angular Material consta de muchos otros componentes y directivas. Elegimos importar todo el módulo de Angular porque no sería conveniente importarlos todos individualmente.

Comencemos:

1. Abre el archivo `product-list.component.ts` e importa los artefactos `CurrencyPipe` y `MatTableModule`:

```typescript
import { AsyncPipe, CurrencyPipe } from '@angular/common';
import { MatTableModule } from '@angular/material/table';
```

2. Agrega las clases importadas anteriores al array `imports` del decorador `@Component`:

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
    MatTableModule
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

3. Crea la siguiente propiedad en la clase `ProductListComponent` para definir los nombres de las columnas de la tabla:

```typescript
columnNames = ['title', 'price'];
```

El nombre de cada columna coincide con una propiedad de la interfaz `Product`.

4. Abre el archivo `product-list.component.html` y agrega el siguiente fragmento después del bloque `@for`:

```html
<table mat-table [dataSource]="products"></table>
```

Una tabla de Angular Material es un elemento HTML estándar `<table>` con la directiva `mat-table` adjunta.

> La propiedad `dataSource` de la directiva `mat-table` define los datos que queremos mostrar en la tabla. Puede ser cualquier dato que se pueda enumerar, como un array. En nuestro caso, lo vinculamos a la variable de referencia de plantilla `products`.

5. Agrega un elemento `<ng-container>` para cada columna que queramos mostrar:

```html
<table mat-table [dataSource]="products">
  <ng-container matColumnDef="title">
    <th mat-header-cell *matHeaderCellDef>Title</th>
    <td mat-cell *matCellDef="let product">
      <a [routerLink]="[product.id]">{{ product.title }}</a>
    </td>
  </ng-container>
  <ng-container matColumnDef="price">
    <th mat-header-cell *matHeaderCellDef>Price</th>
    <td mat-cell *matCellDef="let product">{{ product.price | currency }}</td>
  </ng-container>
</table>
```

El elemento `<ng-container>` es un elemento de propósito especial que agrupa elementos con funcionalidad similar. No interfiere con el estilo de los elementos secundarios ni se representa en la pantalla.

El elemento `<ng-container>` utiliza la directiva `matColumnDef` para establecer el nombre de la columna específica.

> El valor de la directiva `matColumnDef` debe coincidir con un valor de la propiedad del componente `columnNames`; de lo contrario, la aplicación arrojará un error indicando que no puede encontrar el nombre de la columna definida.

Contiene un elemento HTML `<th>` con una directiva `mat-header-cell` que indica el encabezado de la celda y un elemento HTML `<td>` con una directiva `mat-cell` para los datos de la celda. El elemento HTML `<td>` utiliza la directiva `matCellDef` para crear una variable de plantilla local para los datos de la fila actual que podemos usar más adelante.

6. Agrega el siguiente fragmento después de los elementos `<ng-container>`:

```html
<tr mat-header-row *matHeaderRowDef="columnNames"></tr>
<tr mat-row *matRowDef="let row; columns: columnNames;"></tr>
```

En el fragmento anterior, definimos la fila de encabezado de la tabla que muestra los nombres de las columnas y las filas reales que contienen datos.

Si ejecutamos la aplicación, la salida debería ser la siguiente:

*(Figura 12.12: Componente Tabla)*

El componente de lista de productos muestra la representación en tarjeta y en tabla de los datos simultáneamente. Usaremos el componente de alternancia de botones (*button toggle*) de Angular Material para distinguir entre ellos.

El componente de alternancia de botones activa o desactiva los botones según una condición específica:

7. Abre el archivo `product-list.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { MatButtonToggle, MatButtonToggleGroup } from '@angular/material/button-toggle';
```

8. Agrega las clases importadas anteriores en el array `imports` del decorador `@Component`:

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
    MatButtonToggleGroup
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

9. Abre el archivo `product-list.component.html` y agrega el siguiente fragmento HTML dentro del elemento HTML `<div>` con la clase `caption`:

```html
<span class="spacer"></span>
<mat-button-toggle-group #group="matButtonToggleGroup">
  <mat-button-toggle value="card" checked>
    <mat-icon>list</mat-icon>
  </mat-button-toggle>
  <mat-button-toggle value="table">
    <mat-icon>grid_on</mat-icon>
  </mat-button-toggle>
</mat-button-toggle-group>
```

En el fragmento anterior, usamos el elemento `<mat-button-toggle-group>` para crear dos botones de alternancia uno al lado del otro. La instancia del grupo de alternancia de botones se asigna a la variable de referencia de plantilla `group` para que podamos acceder a ella más adelante.

Declaramos botones de alternancia utilizando el elemento `<mat-button-toggle>` y estableciendo un valor apropiado. La propiedad `value` se establecerá cuando hagamos clic en cualquiera de los botones. También tenemos un icono para cada botón de alternancia para mejorar la UX mientras los usuarios interactúan con la lista de productos.

10. Crea un nuevo bloque `@if` después del elemento HTML `<div>` con la clase `caption` y mueve el bloque `@for` dentro de él:

```html
@if (group.value === 'card') {
  @for (product of products | sort; track product.id) {
    <mat-card [routerLink]="[product.id]">
      <mat-card-header>
        <mat-card-title-group>
          <mat-card-title>{{ product.title }}</mat-card-title>
          <mat-card-subtitle>{{ product.category }}</mat-card-subtitle>
          <img mat-card-sm-image [src]="product.image" />
        </mat-card-title-group>
      </mat-card-header>
    </mat-card>
  } @empty {
    <p>No products found!</p>
  }
}
```

De acuerdo con el fragmento anterior, la representación de productos en tarjeta se mostrará cuando la propiedad `value` del grupo de alternancia de botones se establezca en `card`.

11. Agrega el siguiente bloque `@else` y mueve el componente de tabla de datos dentro de él para mostrar la lista de productos en formato tabular cuando se hace clic en el segundo botón de alternancia:

```html
@else {
  <table mat-table [dataSource]="products">
    <ng-container matColumnDef="title">
      <th mat-header-cell *matHeaderCellDef>Title</th>
      <td mat-cell *matCellDef="let product">
        <a [routerLink]="[product.id]">{{ product.title }}</a>
      </td>
    </ng-container>
    <ng-container matColumnDef="price">
      <th mat-header-cell *matHeaderCellDef>Price</th>
      <td mat-cell *matCellDef="let product">{{ product.price | currency }}</td>
    </ng-container>
    <tr mat-header-row *matHeaderRowDef="columnNames"></tr>
    <tr mat-row *matRowDef="let row; columns: columnNames;"></tr>
  </table>
}
```

12. Ejecuta el comando `ng serve` para iniciar la aplicación y verifica que la representación de tarjetas se muestre inicialmente.

*(Figura 12.13: Lista de productos)*

Haz clic en el segundo botón de alternancia y verifica que los productos ahora se muestren en formato tabular.

En esta sección, aprendimos a mostrar la lista de productos en un formato tabular. También usamos botones de alternancia para cambiar de la vista de tarjeta a la tabular.

En la siguiente sección, aprenderemos a utilizar ventanas emergentes y superposiciones para proporcionar información adicional a los usuarios.

#### Ventanas emergentes y superposiciones (Popups and overlays)
Hay diferentes formas de captar la atención del usuario en una aplicación web. Una de ellas es mostrar un cuadro de diálogo emergente sobre el contenido de la página y pedir al usuario que actúe en consecuencia. Otra forma es mostrar información como una notificación en diferentes partes de la página.

Angular Material ofrece tres componentes diferentes para manejar tales casos:

- **`Dialog`:** Un cuadro de diálogo emergente modal que se muestra encima del contenido de la página.
- **`Badge`:** Una pequeña indicación circular para actualizar el estado de un elemento de la interfaz de usuario.
- **`Snackbar`:** Un mensaje de información que se muestra en la parte inferior de una página y que está visible brevemente. Su propósito es notificar al usuario el resultado de una acción, como guardar un formulario.

Aprenderemos a utilizar los componentes anteriores en nuestra aplicación de tienda online, comenzando por cómo crear un diálogo modal simple.

##### Creación de un cuadro de diálogo de confirmación
El componente de diálogo es bastante potente y se puede personalizar y configurar fácilmente. Es un componente de Angular ordinario con directivas personalizadas que lo obligan a comportarse como un diálogo. Para explorar las capacidades del diálogo de Angular Material, utilizaremos un diálogo de confirmación en el guard de checkout para notificar a los usuarios sobre los artículos restantes en sus carritos de compras:

1. Ejecuta el siguiente comando de Angular CLI para crear un nuevo componente de Angular:

```bash
ng generate component checkout
```

El comando anterior creará un componente de Angular que alojará nuestro diálogo.

2. Abre el archivo `checkout.component.ts` y agrega las siguientes sentencias de importación:

```typescript
import { MatButton } from '@angular/material/button';
import { MatDialogModule } from '@angular/material/dialog';
```

> El componente de diálogo de Angular Material consta de muchos otros componentes y directivas. Elegimos importar todo el módulo de Angular porque no sería conveniente importarlos todos individualmente.

3. Agrega las clases importadas anteriores en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-checkout',
  imports: [MatButton, MatDialogModule],
  templateUrl: './checkout.component.html',
  styleUrl: './checkout.component.css'
})
```

4. Abre el archivo `checkout.component.html` y reemplaza su contenido con la siguiente plantilla HTML:

```html
<h1 mat-dialog-title>Cart Checkout</h1>
<mat-dialog-content>
  <span>You have pending items in your cart. Do you want to continue?</span>
</mat-dialog-content>
<mat-dialog-actions>
  <button mat-raised-button>Yes</button>
  <button mat-button>No</button>
</mat-dialog-actions>
```

La plantilla del componente contiene varias directivas y elementos que pertenecen al componente de diálogo de Angular Material. La directiva `mat-dialog-title` define el título del diálogo y `<mat-dialog-content>` es el contenido real. El elemento `<mat-dialog-actions>` define las acciones que puede realizar el diálogo y generalmente envuelve elementos de botón.

Se debe activar un diálogo para que se muestre en una página:

5. Abre el archivo `checkout.guard.ts` y agrega las siguientes sentencias de importación:

```typescript
import { MatDialog } from '@angular/material/dialog';
import { CheckoutComponent } from './checkout/checkout.component';
```

6. Inyecta el servicio `MatDialog` en el cuerpo de la función `checkoutGuard`:

```typescript
const dialog = inject(MatDialog);
```

7. Modifica la asignación de la variable `confirmation` de la siguiente manera:

```typescript
if (cartService.cart) {
  const confirmation = dialog.open(CheckoutComponent).afterClosed();
  return confirmation;
}
```

En el fragmento anterior, usamos el servicio `MatDialog` para mostrar el componente de checkout. El servicio `MatDialog` acepta el tipo de clase de componente que representa el diálogo como parámetro.

El método `open` del servicio `MatDialog` devuelve una propiedad observable `afterClosed`, que nos notificará cuando se cierre el diálogo. El observable emite cualquier valor que se devuelva desde el diálogo.

> Más adelante en el capítulo, aprenderemos cómo devolver un valor booleano desde el componente de diálogo que coincida con el tipo devuelto por la función `CanDeactivateFn`.

Ahora podemos verificar que el componente de diálogo funcione como se espera ejecutando los siguientes pasos:

1. Ejecuta la aplicación mediante el comando `ng serve` y navega a `http://localhost:4200`.
2. Inicia sesión en la aplicación.
3. Selecciona un producto de la lista y agrégalo al carrito de compras.
4. Repite el paso anterior para agregar más productos al carrito.
5. Navega al carrito de compras y luego haz clic en el botón Atrás del navegador o en cualquiera de los enlaces de la aplicación para salir del carrito. El siguiente diálogo se mostrará en la pantalla:

*(Figura 12.14: Componente de diálogo de Checkout)*

Podríamos mejorar aún más la UX de la aplicación mostrando información en el diálogo sobre la cantidad de artículos que hemos agregado al carrito de compras. En la siguiente sección, aprenderemos cómo pasar datos al diálogo y mostrar la cantidad de artículos del carrito de compras.

##### Configuración de diálogos
En un escenario del mundo real, probablemente necesitarás crear un componente reutilizable para mostrar un diálogo en un proyecto de Angular. El componente puede terminar en una librería de Angular como un paquete. Por lo tanto, debes configurar el componente de diálogo para que acepte datos dinámicamente.

En el proyecto actual de Angular, nos gustaría mostrar la cantidad de productos que hemos agregado al carrito de compras:

1. Abre el archivo `checkout.component.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { Component, inject } from '@angular/core';
import { MatButton } from '@angular/material/button';
import { MatDialogModule, MAT_DIALOG_DATA } from '@angular/material/dialog';
```

2. Inyecta `MAT_DIALOG_DATA` en la clase `CheckoutComponent` de la siguiente manera:

```typescript
export class CheckoutComponent {
  data = inject(MAT_DIALOG_DATA);
}
```

`MAT_DIALOG_DATA` es un token de inyección que nos permite pasar datos arbitrarios al componente de diálogo. La variable `data` contendrá cualquier dato que le pasemos al diálogo cuando llamemos a su método `open`.

3. Abre el archivo `checkout.component.html` y agrega la propiedad `data` al texto interno del elemento HTML `<span>`:

```html
<span>
  You have {{ data }} pending items in your cart. Do you want to continue?
</span>
```

4. Abre el archivo `checkout.guard.ts` y establece la propiedad `data` en el objeto de configuración del diálogo, que es el segundo parámetro del método `open`:

```typescript
const confirmation = dialog.open(
  CheckoutComponent,
  { data: cartService.cart.products.length }
).afterClosed();
```

Si intentamos salir de la página del carrito mientras ejecutamos la aplicación, obtendremos un diálogo similar al siguiente:

*(Figura 12.15: Componente de diálogo de Checkout con datos personalizados)*

Los botones del componente de diálogo aún no hacen nada específico. En la siguiente sección, aprenderemos a configurarlos y devolver datos al guard.

##### Obtención de datos de los diálogos
El módulo de diálogo de Angular Material expone la directiva `mat-dialog-close` que podemos usar para configurar qué botón cerrará el diálogo. Abre el archivo `checkout.component.html` y agrega la directiva `mat-dialog-close` a ambos botones:

```html
<mat-dialog-actions>
  <button mat-raised-button mat-dialog-close>Yes</button>
  <button mat-button [mat-dialog-close]="false">No</button>
</mat-dialog-actions>
```

En el fragmento anterior, usamos la directiva `mat-dialog-close` de dos maneras:

- Sin pasar un valor en el botón *Yes*, el diálogo devolverá `true` como valor predeterminado, lo que permitirá al guard navegar fuera de la página del carrito de compras.
- Con el enlace de propiedades en el botón *No*, pasamos `false` como valor para cancelar la navegación desde el guard.

Ejecuta los siguientes pasos para verificar que el comportamiento del diálogo sea correcto:

1. Ejecuta el comando `ng serve` para iniciar la aplicación y navega a `http://localhost:4200`.
2. Inicia sesión en la aplicación.
3. Selecciona un producto de la lista y agrégalo al carrito.
4. Haz clic en el enlace *My Cart* para navegar al carrito de compras.
5. Haz clic en el enlace *Products*, selecciona *No* en el diálogo de checkout y verifica que la aplicación permanezca en la página del carrito de compras.
6. Haz clic en el enlace *Products* nuevamente, selecciona *Yes* en el diálogo y deberías navegar a la lista de productos.

Los diálogos son una gran característica de Angular Material que puede brindar a tus aplicaciones capacidades potentes. En la siguiente sección, exploraremos los componentes de insignia (*badge*) y barra de notificaciones (*snackbar*) para notificar al usuario cuando se agrega un producto al carrito de compras.

##### Visualización de notificaciones de usuario
La librería Angular Material impone patrones y comportamientos que mejoran la UX de la aplicación. Un aspecto de la UX de la aplicación se refiere a proporcionar notificaciones a los usuarios ante acciones específicas. Angular Material nos brinda los componentes de insignia (*badge*) y snackbar que podemos usar en este caso.

###### Aplicación de insignias (badges)
El componente de insignia es un círculo ubicado en la parte superior de otro elemento y generalmente muestra un número. Aprenderemos cómo aplicar insignias mostrando la cantidad de artículos del carrito de compras en el enlace de la aplicación *My Cart*:

1. Abre el archivo `app.component.ts` y agrega las siguientes sentencias de importación:

```typescript
import { MatBadge } from '@angular/material/badge';
import { CartService } from './cart.service';
```

La clase `MatBadge` exporta el componente de insignia. La clase `CartService` nos proporcionará la cantidad de artículos en el carrito de compras.

2. Agrega la clase `MatBadge` en el array `imports` del decorador `@Component`:

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
    MatBadge
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
```

3. Inyecta la clase `CartService` en la clase `AppComponent`:

```typescript
cartService = inject(CartService);
```

4. Abre el archivo `app.component.html` y agrega la directiva `matBadge` al botón *My Cart*:

```html
<button mat-button routerLink="/cart" [matBadge]="cartService.cart?.products?.length">
  My Cart
</button>
```

En el fragmento anterior, la directiva `matBadge` indica el número que se muestra en la insignia. En este caso, lo vinculamos con la longitud del array `products` que existe en el carrito de compras actual.

5. Abre el archivo `app.component.css` y agrega el siguiente estilo CSS:

```css
button {
  margin: 5px;
}
```

El estilo anterior agregará espacio alrededor de cada enlace de la aplicación para que los botones no se superpongan con el componente de insignia.

6. Ejecuta el comando `ng serve` para iniciar la aplicación y agrega algunos productos al carrito de compras. Observa que el icono de la insignia actualiza su valor cuando se agregan productos al carrito:

*(Figura 12.16: Componente Badge)*

###### Aplicación de un snackbar
Otro buen patrón de UX cuando trabajamos con aplicaciones CRUD es mostrar una notificación cuando se ha completado una acción. Podemos aplicar dicho patrón mostrando una notificación cuando se agrega un producto al carrito de compras. Usaremos el componente snackbar de Angular Material para mostrar la notificación:

1. Abre el archivo `product-detail.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { MatSnackBarModule, MatSnackBar } from '@angular/material/snack-bar';
```

> El snackbar no es un componente de Angular real como todos los componentes de Angular Material que hemos visto. Es un servicio de Angular llamado `MatSnackBar` y se puede usar importando la clase `MatSnackBarModule` en nuestros componentes.

2. Agrega la clase `MatSnackBarModule` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-detail',
  imports: [
    CommonModule,
    FormsModule,
    PriceMaximumDirective,
    MatButton,
    MatInput,
    MatFormField,
    MatError,
    MatIcon,
    MatSuffix,
    MatIconButton,
    MatChipSet,
    MatChip,
    MatSnackBarModule
  ],
  templateUrl: './product-detail.component.html',
  styleUrl: './product-detail.component.css'
})
```

3. Inyecta el servicio `MatSnackBar` en el constructor de la clase `ProductDetailComponent`:

```typescript
constructor(
  private productService: ProductsService,
  public authService: AuthService,
  private route: ActivatedRoute,
  private router: Router,
  private cartService: CartService,
  private snackbar: MatSnackBar
) { }
```

4. Modifica el método `addToCart` para mostrar un snackbar cuando el producto se agregue al carrito:

```typescript
addToCart(id: number) {
  this.cartService.addProduct(id).subscribe(() => {
    this.snackbar.open('Product added to cart!', undefined, {
      duration: 1000
    });
  });
}
```

En el método anterior, usamos el método `open` del servicio `MatSnackBar` para mostrar un snackbar. El método `open` acepta tres parámetros: el mensaje que queremos mostrar, cualquier acción que queramos realizar cuando se cierre el snackbar y un objeto de configuración. El objeto de configuración nos permite establecer varias opciones, como la duración durante la cual el snackbar estará visible en milisegundos.

> No pasamos un parámetro para la acción porque no queremos reaccionar cuando se descarte el snackbar.

5. Ejecuta el comando `ng serve` para iniciar la aplicación y selecciona un producto de la lista.
6. Asegúrate de haber iniciado sesión y haz clic en el botón *Add to cart*. El siguiente mensaje de notificación se mostrará en la parte inferior de la página:

*(Figura 12.17: Componente Snackbar)*

> La posición del snackbar se puede cambiar desde las opciones de configuración. Lee más en [https://material.angular.io/components/snack-bar/overview](https://material.angular.io/components/snack-bar/overview).

En esta sección, aprendimos a usar modelos emergentes y superposiciones de notificaciones para mejorar la UX de la aplicación y proporcionar un flujo de trabajo excelente a nuestros usuarios.

---

### Sección 12.5: Resumen

En este capítulo, analizamos los conceptos básicos del sistema Material Design. Nos centramos principalmente en Angular Material, la implementación de Material Design diseñada para Angular, y cómo se compone de diferentes componentes. Analizamos una explicación práctica de cómo instalarlo, configurarlo y utilizar algunos de sus componentes y temas principales.

Esperamos que hayas leído este capítulo y hayas comprobado que ahora comprendes Material Design en general y Angular Material en particular, y que puedas determinar si es una buena opción para tu próxima aplicación Angular.

Las aplicaciones web deben ser testeables para garantizar que sean funcionales y cumplan con los requisitos de la aplicación. En el próximo capítulo, aprenderemos cómo aplicar diferentes técnicas de prueba en aplicaciones Angular.
