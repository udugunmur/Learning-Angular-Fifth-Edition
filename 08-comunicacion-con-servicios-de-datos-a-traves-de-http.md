# Parte 3: Enrutamiento, Datos y Formularios

## Capítulo 8: Comunicación con servicios de datos a través de HTTP

Un escenario del mundo real para las aplicaciones empresariales de Angular es conectarse a servicios y API remotas para intercambiar datos. El cliente HTTP de Angular proporciona soporte integrado para comunicarse con servicios a través de HTTP. La interacción de una aplicación Angular con el cliente HTTP se basa en flujos de observables de RxJS, lo que brinda a los desarrolladores un amplio conjunto de capacidades para el acceso a datos.

Hay muchas formas de conectarse a las API a través de HTTP. En este libro, solo tocaremos la superficie. Aun así, los conceptos e ideas cubiertos en este capítulo te brindarán todo lo que necesitas para conectar tus aplicaciones de Angular a servicios HTTP en poco tiempo, dejando todo lo que puedas hacer con ellos a tu creatividad.

En este capítulo, exploraremos los siguientes conceptos:

- Comunicación de datos a través de HTTP
- Introducción al cliente HTTP de Angular
- Configuración de una API backend
- Manejo de datos CRUD en Angular
- Autenticación y autorización con HTTP

---

### Sección 8.1: Requisitos técnicos

Este capítulo contiene varios ejemplos de código para guiarte a través del concepto del cliente HTTP de Angular. Puedes encontrar el código fuente correspondiente en la carpeta `ch08` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch08](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch08)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 8.2: Comunicación de datos a través de HTTP

Antes de sumergirnos en la descripción del cliente HTTP de Angular y cómo usarlo para comunicarse con servidores, hablemos primero de las implementaciones nativas de HTTP. Actualmente, si queremos comunicarnos con un servidor a través de HTTP usando JavaScript, podemos usar la API nativa de JavaScript `fetch`. Contiene todos los métodos necesarios para conectarse con un servidor e intercambiar datos.

Puedes ver un ejemplo de cómo obtener datos en el siguiente código:

```javascript
fetch(url)
  .then(response => {
    return response.ok ? response.text() : '';
  })
  .then(result => {
    if (result) {
      console.log(result);
    } else {
      console.error('An error has occurred');
    }
  });
```

Aunque la API `fetch` se basa en promesas, la promesa que devuelve no se rechaza si hay un error. En su lugar, la solicitud no tiene éxito cuando la propiedad `ok` no está en el objeto de respuesta.

Si la solicitud a la URL remota se completa, podemos usar el método `text()` del objeto de respuesta para devolver el texto de respuesta dentro de una nueva promesa. Finalmente, en el segundo callback de `then`, mostramos el texto de respuesta o un mensaje de error específico en la consola del navegador.

> Para obtener más información sobre la API `fetch`, consulta la documentación oficial en [https://developer.mozilla.org/docs/Web/API/fetch](https://developer.mozilla.org/docs/Web/API/fetch).

Ya hemos aprendido que los observables son flexibles para gestionar operaciones asíncronas. Probablemente te estés preguntando cómo podemos aplicar este patrón al consumir información de un servicio HTTP. Hasta ahora, te habrás acostumbrado a enviar solicitudes asíncronas a servicios AJAX y luego pasar la respuesta a un callback o una promesa. Ahora, manejaremos la llamada devolviendo un observable. El observable emitirá la respuesta del servidor como un evento en el contexto de un flujo (*stream*), que se puede canalizar a través de operadores de RxJS para procesar mejor la respuesta.

Convirtamos el ejemplo anterior con la API `fetch` a un observable. Usamos la clase `Observable` para envolver la llamada `fetch` en un flujo observable y reemplazamos los métodos de consola con los métodos del objeto observador apropiados:

```typescript
const request$ = new Observable(observer => {
  fetch(url)
    .then(response => {
      return response.ok ? response.text() : '';
    })
    .then(result => {
      if (result) {
        observer.next(result);
        observer.complete();
      } else {
        observer.error('An error has occurred');
      }
    });
});
```

> **Consejo rápido:** Mejora tu experiencia de programación con las funciones AI Code Explainer y Quick Copy en el lector de nueva generación de Packt: [https://www.packtpub.com/unlock/9781835087480](https://www.packtpub.com/unlock/9781835087480).

En el fragmento anterior, usamos los siguientes métodos del observador:

- **`next`:** Devuelve los datos de respuesta a los suscriptores cuando llegan.
- **`complete`:** Notifica a los suscriptores que no habrá otros datos disponibles en el flujo.
- **`error`:** Alerta a los suscriptores de que ha ocurrido un error.

¡Eso es todo! Ahora hemos construido un cliente HTTP personalizado. Por supuesto, esto no es mucho: nuestro cliente HTTP personalizado solo maneja una operación GET para obtener datos de un punto de conexión remoto. No estamos manejando muchas otras operaciones del protocolo HTTP, como POST, PUT y DELETE. Sin embargo, era esencial darse cuenta de todo el trabajo pesado que el cliente HTTP en Angular está haciendo por nosotros. Otra lección importante es lo fácil que es convertir una API asíncrona en una API observable que encaja perfectamente con el resto de nuestros conceptos asíncronos. Por lo tanto, continuemos con la implementación de Angular de un servicio HTTP.

---

### Sección 8.3: Introducción al cliente HTTP de Angular

El cliente HTTP del framework Angular es una librería de Angular independiente que reside en el paquete npm `@angular/common` bajo el espacio de nombres `http`. Angular CLI instala este paquete de forma predeterminada al crear un nuevo proyecto de Angular.

> Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 6: Patrones reactivos en Angular* para seguir el resto del capítulo. Después de obtener el código, te sugerimos eliminar la carpeta `key-logger` por simplicidad.

Para comenzar a utilizar el cliente HTTP de Angular, debemos importar el método `provideHttpClient` en el archivo `app.config.ts`:

```typescript
import { provideHttpClient } from '@angular/common/http';
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient()
  ]
};
```

> Si queremos usar el cliente HTTP en aplicaciones construidas con versiones anteriores de Angular, necesitamos importar un módulo de Angular, llamado `HttpClientModule`, del espacio de nombres `@angular/common/http` en uno de los módulos de nuestra aplicación.

El método `provideHttpClient` expone varios servicios de Angular que podemos usar para manejar la comunicación HTTP asíncrona. El más básico es el servicio `HttpClient`, que proporciona una API robusta y abstrae todas las operaciones requeridas para manejar conexiones asíncronas a través de los siguientes métodos HTTP:

- **`get`:** Realiza una operación GET para obtener datos.
- **`post`:** Realiza una operación POST para agregar nuevos datos.
- **`put`/`patch`:** Realiza una operación PUT/PATCH para actualizar datos existentes.
- **`delete`:** Realiza una operación DELETE para eliminar datos existentes.

Los métodos HTTP anteriores constituyen las operaciones principales para las aplicaciones Create, Read, Update, Delete (CRUD). Todos los métodos anteriores del cliente HTTP de Angular devuelven un flujo de datos observable. Los componentes de Angular pueden usar la librería RxJS para suscribirse a esos métodos e interactuar con una API remota.

> El equipo de Angular está investigando y experimentando actualmente para ver si pueden hacer que el uso de RxJS sea opcional en el framework. En ese caso, podríamos ver una implementación de HTTP basada en señales. Para el resto de este capítulo, nos quedaremos con observables porque el cliente HTTP de Angular no admite señales de fábrica.

En la siguiente sección, exploraremos cómo usar estos métodos y comunicarnos con una API remota.

---

### Sección 8.4: Configuración de una API backend

Una aplicación web CRUD generalmente se conecta a un servidor y utiliza una API backend HTTP para realizar operaciones sobre los datos: obtiene datos existentes, los actualiza, crea nuevos datos o los elimina.

En un escenario del mundo real, lo más probable es que interactúes con un servicio de API backend real a través de HTTP. En este libro, utilizaremos una API simulada llamada **Fake Store API**.

> La documentación oficial de Fake Store API se puede encontrar en [https://fakestoreapi.com](https://fakestoreapi.com/).

Fake Store API es una API REST backend disponible en línea que puedes utilizar cuando necesitas datos simulados para una aplicación web de comercio electrónico o tienda online. Puede gestionar productos, carritos de compras y usuarios disponibles en formato JSON. Expone los siguientes puntos de conexión principales:

- **`products`:** Gestiona un conjunto de artículos de productos.
- **`cart`:** Gestiona el carrito de compras de un usuario.
- **`user`:** Gestiona una colección de usuarios de la aplicación.
- **`login`:** Maneja la autenticación de usuarios.

En este capítulo, trabajaremos solo con los puntos de conexión `products` y `login`. Sin embargo, volveremos a visitar el punto de conexión `cart` más adelante en el libro.

> Todas las operaciones que modifican datos no los persisten físicamente en una base de datos. Sin embargo, devuelven una indicación de si la operación fue exitosa. Todas las operaciones que obtienen datos devuelven una colección predefinida de elementos.

---

### Sección 8.5: Manejo de datos CRUD en Angular

Las aplicaciones CRUD se utilizan ampliamente en el mundo de Angular. Difícilmente encontrarás alguna aplicación web que no siga este patrón. Angular hace un gran trabajo admitiendo este tipo de aplicación al proporcionar el servicio `HttpClient`. En esta sección, exploraremos el cliente HTTP de Angular interactuando con el punto de conexión `products` de la Fake Store API.

#### Obtención de datos a través de HTTP
La clase `ProductListComponent` utiliza la clase `ProductsService` para obtener y mostrar datos de productos. Los datos están actualmente codificados de forma rígida (*hardcoded*) en la propiedad `products` de la clase `ProductsService`. En esta sección, modificaremos nuestra aplicación Angular para trabajar con datos en vivo de la Fake Store API:

1. Abre el archivo `app.component.ts` y elimina la propiedad `providers` del decorador `@Component`. Proporcionaremos `APP_SETTINGS` directamente a través del archivo de configuración de la aplicación.

En este punto, también podemos eliminar la propiedad `title`, el observable `title$`, la propiedad `setTitle` y el constructor de la clase del componente:

```typescript
export class AppComponent {
  settings = inject(APP_SETTINGS);
}
```

2. Abre el archivo `app.component.html` y modifica el elemento HTML `<header>` para que utilice directamente el objeto `settings`:

```html
<header>{{ settings.title }}</header>
```

3. Abre el archivo `app.config.ts` y agrega el proveedor `APP_SETTINGS` de la siguiente manera:

```typescript
import { provideHttpClient } from '@angular/common/http';
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';
import { APP_SETTINGS, appSettings } from './app.settings';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient(),
    { provide: APP_SETTINGS, useValue: appSettings }
  ]
};
```

Proporcionamos `APP_SETTINGS` desde el archivo de configuración de la aplicación porque queremos que sea accesible globalmente en la aplicación.

4. Abre el archivo `app.settings.ts` y agrega una nueva propiedad en la interfaz `AppSettings` que represente la URL de la Fake Store API:

```typescript
import { InjectionToken } from '@angular/core';

export interface AppSettings {
  title: string;
  version: string;
  apiUrl: string;
}

export const appSettings: AppSettings = {
  title: 'My e-shop',
  version: '1.0',
  apiUrl: 'https://fakestoreapi.com'
};

export const APP_SETTINGS = new InjectionToken<AppSettings>('app.settings');
```

> La URL de una API backend también se puede agregar en archivos de entorno (*environment files*), como aprenderemos en el *Capítulo 14: Llevando aplicaciones a producción*.

5. Abre el archivo `products.service.ts` y modifica las sentencias de importación correspondientes:

```typescript
import { HttpClient } from '@angular/common/http';
import { Injectable, inject } from '@angular/core';
import { Product } from './product';
import { Observable, of } from 'rxjs';
import { APP_SETTINGS } from './app.settings';
```

6. Crea la siguiente propiedad en la clase `ProductsService` que represente el punto de conexión de productos de la API:

```typescript
private productsUrl = inject(APP_SETTINGS).apiUrl + '/products';
```

7. Modifica el constructor para inyectar el servicio `HttpClient`:

```typescript
constructor(private http: HttpClient) { }
```

8. Modifica el método `getProducts` para que utilice el servicio `HttpClient` para obtener la lista de productos:

```typescript
getProducts(): Observable<Product[]> {
  return this.http.get<Product[]>(this.productsUrl);
}
```

En el método anterior, usamos el método `get` de la clase `HttpClient` y pasamos el punto de conexión de productos de la API como parámetro. También definimos `Product[]` como un tipo genérico en el método `get` para indicar que la respuesta de la API contiene una lista de objetos `Product`.

9. Convierte la propiedad `products` a un array vacío:

```typescript
private products: Product[] = [];
```

Usaremos esto para propósitos de caché local más adelante, en la sección *Modificación de datos a través de HTTP*.

10. Abre el archivo `product-list.component.html` y modifica el bloque `@if` para que verifique si existe la variable de plantilla `products`:

```html
@if (products) {
  <h1>Products ({{products.length}})</h1>
}
```

Necesitamos verificar si la variable existe porque los datos ahora se obtienen de la Fake Store API y habrá un retraso de red antes de que la variable tenga un valor.

Si ejecutamos la aplicación con el comando `ng serve`, deberíamos ver una lista extendida de productos de la API similar a la siguiente:

*(Figura 8.1: Lista de productos de Fake Store API)*

El punto de conexión de productos admite pasar un parámetro de solicitud para limitar los resultados devueltos por la API. Como se indica en [https://fakestoreapi.com/docs#p-limit](https://fakestoreapi.com/docs#p-limit), podemos usar un parámetro de consulta llamado `limit` para lograr esa tarea. Veamos cómo podemos pasar parámetros de consulta en el cliente HTTP de Angular:

11. Abre el archivo `products.service.ts` e importa la clase `HttpParams` del espacio de nombres `@angular/common/http`:

```typescript
import { HttpClient, HttpParams } from '@angular/common/http';
```

La clase `HttpParams` se utiliza para pasar parámetros de consulta (*query parameters*) en una solicitud HTTP.

12. Crea la siguiente variable dentro del método `getProducts`:

```typescript
const options = new HttpParams().set('limit', 10);
```

> La clase `HttpParams` es inmutable. Lo siguiente no funcionaría porque cada operación devuelve una nueva instancia:
>
> ```typescript
> const options = new HttpParams();
> options.set('limit', 10);
> ```
>
> El método `set` de la clase `HttpParams` crea un nuevo parámetro de consulta. Si quisiéramos pasar parámetros adicionales, deberíamos encadenar más métodos `set`, como por ejemplo:
>
> ```typescript
> const options = new HttpParams()
>   .set('limit', 10)
>   .set('page', 1);
> ```

13. Usamos el segundo parámetro del método `get` para pasar parámetros de consulta utilizando la propiedad `params`:

```typescript
return this.http.get<Product[]>(this.productsUrl, { params: options });
```

Guarda los cambios, espera a que la aplicación se recargue y observa la salida de la aplicación:

*(Figura 8.2: Lista de productos)*

En la lista anterior, todos los productos se muestran con el mismo icono de etiqueta, que es el predeterminado según el bloque `@switch` en el archivo `product-list.component.html`:

```html
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
```

El bloque `@switch` depende de la propiedad del título del producto. Lo cambiaremos para que se base en la propiedad `category`, que proviene del punto de conexión de productos de la API.

14. Abre el archivo `product.ts` y reemplaza la propiedad `categories` con la siguiente propiedad:

```typescript
category: string;
```

15. Abre el archivo `product-list.component.html` y modifica el bloque `@switch` de la siguiente manera:

```html
@switch (product.category) {
  @case ('electronics') {
  }
  @case ('jewelery') {
  }
  @default {
  }
}
```

También necesitamos modificar el archivo `product-detail.component.html` porque reemplazamos la propiedad `categories`:

```html
@if (product()) {
  <p>You selected: <strong>{{product()!.title}}</strong>
  </p>
  <p>{{product()!.price | currency:'EUR'}}</p>
  <div class="pill-group">
    <p class="pill">{{ product()!.category }}</p>
  </div>
  <button (click)="addToCart()">Add to cart</button>
}
```

Guarda los cambios, espera a que la aplicación se recargue y observa la salida de la aplicación:

*(Figura 8.3: Lista de productos con categorías)*

Si haces clic en un producto de la lista, notarás que los detalles del producto se muestran correctamente:

*(Figura 8.4: Detalles del producto)*

El componente de detalles del producto continúa funcionando como se esperaba porque pasamos el producto seleccionado como una propiedad de entrada desde la lista de productos:

```html
<app-product-detail
  [product]="selectedProduct"
  (added)="onAdded()"
></app-product-detail>
```

Cambiaremos el comportamiento anterior y obtendremos los detalles del producto directamente de la API mediante una solicitud HTTP GET. Fake Store API contiene un método de punto de conexión que podemos usar para obtener los detalles de un producto específico según su ID:

16. Abre el archivo `products.service.ts` y crea un nuevo método `getProduct` que acepte el `id` del producto como parámetro e inicie una solicitud GET a la API según ese `id`:

```typescript
getProduct(id: number): Observable<Product> {
  return this.http.get<Product>(`${this.productsUrl}/${id}`);
}
```

El método anterior utiliza el método `get` del servicio `HttpClient`. Acepta la URL del punto de conexión de productos seguida del ID del producto como parámetro.

17. Abre el archivo `product-detail.component.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { CommonModule } from '@angular/common';
import { Component, input, output, OnChanges } from '@angular/core';
import { Product } from '../product';
import { Observable } from 'rxjs';
import { ProductsService } from '../products.service';
```

18. Agrega la siguiente propiedad en la clase `ProductDetailComponent`:

```typescript
id = input<number>();
```

La propiedad de componente `id` se utilizará para pasar el ID del producto seleccionado de la lista.

19. Reemplaza la propiedad de entrada `product` con el siguiente observable:

```typescript
product$: Observable<Product> | undefined;
```

La propiedad `product$` se utilizará para llamar al método `getProduct` del servicio.

20. Agrega un constructor en la clase `ProductDetailComponent` e inyecta `ProductsService`:

```typescript
constructor(private productService: ProductsService) { }
```

21. Agrega `OnChanges` a la lista de interfaces implementadas:

```typescript
export class ProductDetailComponent implements OnChanges
```

22. Implementa el método `ngOnChanges` de la siguiente manera:

```typescript
ngOnChanges(): void {
  this.product$ = this.productService.getProduct(this.id()!);
}
```

En el método anterior, asignamos el valor del método `getProduct` de `ProductsService` a la propiedad de componente `product$` cada vez que se pasa un nuevo `id` mediante el enlace de entrada.

23. Abre el archivo `product-detail.component.html` y modifica su contenido para que utilice el observable `product$`:

```html
@let product = (product$ | async);
@if (product) {
  <p>You selected: <strong>{{product.title}}</strong>
  </p>
  <p>{{product.price | currency:'EUR'}}</p>
  <div class="pill-group">
    <p class="pill">{{ product.category }}</p>
  </div>
  <button (click)="addToCart()">Add to cart</button>
}
```

24. Finalmente, abre el archivo `product-list.component.html` y vincula el `id` de la propiedad `selectedProduct` al enlace de entrada `id` del componente `<app-product-detail>`:

```html
<app-product-detail
  [id]="selectedProduct?.id"
  (added)="onAdded()"
></app-product-detail>
```

Si ejecutamos la aplicación con el comando `ng serve` y seleccionamos un producto de la lista, verificaremos que el detalle del producto se muestra correctamente.

Hemos aprendido cómo obtener una lista de elementos y un solo elemento de una API backend y cubrimos la parte **Read** (Lectura) de una operación CRUD. En la siguiente sección, cubriremos las partes restantes de una operación CRUD, que se ocupan principalmente de modificar datos.

#### Modificación de datos a través de HTTP
La modificación de datos en una aplicación CRUD generalmente se refiere a agregar nuevos datos y actualizar o eliminar datos existentes. Para demostrar cómo implementar dicha funcionalidad en una aplicación Angular utilizando el cliente HTTP, realizaremos los siguientes cambios en nuestra aplicación:

- Crear un componente de Angular para agregar nuevos productos.
- Modificar el componente de detalles del producto para cambiar el precio de un producto existente.
- Agregar un botón en el componente de detalles del producto para eliminar un producto existente.

Ya hemos mencionado que ninguna operación HTTP persiste datos físicamente en la Fake Store API, por lo que necesitamos implementar un mecanismo de caché local para los datos de nuestros productos e interactuar con él directamente en el servicio de productos:

1. Abre el archivo `products.service.ts` e importa el operador `map` de RxJS:

```typescript
import { Observable, map, of } from 'rxjs';
```

2. Modifica el método `getProducts` de la siguiente manera:

```typescript
getProducts(): Observable<Product[]> {
  const options = new HttpParams().set('limit', 10);
  return this.http.get<Product[]>(this.productsUrl, { params: options }).pipe(map(products => {
    this.products = products;
    return products;
  }));
}
```

El método anterior llena el array `products` con datos de la API y devuelve los datos de los productos como un observable.

3. Modifica el método `getProduct` para que utilice el array `products` para devolver un objeto de producto en lugar de consultar la Fake Store API:

```typescript
getProduct(id: number): Observable<Product> {
  const product = this.products.find(p => p.id === id);
  return of(product!);
}
```

Ahora tenemos nuestro servicio de productos listo y podemos comenzar a construir el componente para agregar nuevos productos.

#### Adición de nuevos productos
Para agregar un nuevo producto a través de nuestra aplicación, debemos enviar sus detalles a la Fake Store API:

1. Abre el archivo `products.service.ts` y agrega el siguiente método:

```typescript
addProduct(newProduct: Partial<Product>): Observable<Product> {
  return this.http.post<Product>(this.productsUrl, newProduct).pipe(
    map(product => {
      this.products.push(product);
      return product;
    })
  );
}
```

En el fragmento anterior, usamos el método `post` de la clase `HttpClient` y pasamos el punto de conexión de productos de la API junto con un nuevo objeto de producto como parámetros.

> Definimos el nuevo producto como `Partial<Product>` porque los nuevos productos no tienen un ID asignado todavía.

El tipo genérico definido en el método `post` indica que el producto devuelto por la API es un objeto `Product`. También agregamos el nuevo producto a la caché local y lo devolvemos.

2. Ejecuta el siguiente comando de Angular CLI para crear un nuevo componente:

```bash
ng generate component product-create
```

3. Abre el archivo `product-create.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { ProductsService } from '../products.service';
```

4. Crea un constructor e inyecta la clase `ProductsService`:

```typescript
constructor(private productsService: ProductsService) {}
```

5. Agrega el siguiente método a la clase del componente:

```typescript
createProduct(title: string, price: string, category: string) {
  this.productsService.addProduct({ title, price: Number(price), category }).subscribe();
}
```

> No necesitamos cancelar la suscripción al interactuar con el cliente HTTP de Angular porque el framework lo hará automáticamente por nosotros.

El método anterior acepta los detalles del producto como parámetros y llama al método `addProduct` de la clase `ProductsService`. Usamos la función nativa `Number` para convertir el valor del precio a un número porque se pasará como una cadena desde la plantilla.

6. Abre el archivo `product-create.component.html` y reemplaza su contenido con la siguiente plantilla HTML:

```html
<h1>Add new product</h1>
<div>
  <label for="title">Title</label>
  <input id="title" #title />
</div>
<div>
  <label for="price">Price</label>
  <input id="price" #price type="number" />
</div>
<div>
  <label for="category">Category</label>
  <select id="category" #category>
    <option>Select a category</option>
    <option value="electronics">Electronics</option>
    <option value="jewelery">Jewelery</option>
    <option>Other</option>
  </select>
</div>
<div>
  <button (click)="createProduct(title.value, price.value, category.value)">Create</button>
</div>
```

En la plantilla anterior, vinculamos el método `createProduct` al evento `click` del botón `Create` y pasamos el valor de los elementos HTML `<input>` y `<select>` utilizando las variables de referencia de plantilla respectivas.

7. Abre el archivo global `styles.css` y agrega el siguiente estilo CSS:

```css
input {
  border-radius: 4px;
  padding: 8px;
  margin-bottom: 16px;
  border: 1px solid #BDBDBD;
}
```

Además, mueve los estilos relacionados con los botones del archivo `product-detail.component.css` al archivo de estilos CSS global.

8. Abre el archivo `product-create.component.css` y agrega los siguientes estilos CSS para dar una apariencia agradable a nuestro nuevo componente:

```css
input {
  width: 200px;
}

select {
  border-radius: 4px;
  padding: 8px;
  margin-bottom: 16px;
  border: 1px solid #BDBDBD;
  width: 220px;
}

label {
  margin-bottom: 4px;
  display: block;
}
```

9. Abre el archivo `product-list.component.ts` e importa la clase `ProductCreateComponent`:

```typescript
import { AsyncPipe } from '@angular/common';
import { Component, OnInit } from '@angular/core';
import { Observable } from 'rxjs';
import { Product } from '../product';
import { ProductDetailComponent } from '../product-detail/product-detail.component';
import { SortPipe } from '../sort.pipe';
import { ProductsService } from '../products.service';
import { ProductCreateComponent } from '../product-create/product-create.component';

@Component({
  selector: 'app-product-list',
  imports: [
    ProductDetailComponent,
    SortPipe,
    AsyncPipe,
    ProductCreateComponent
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

10. Finalmente, abre el archivo `product-list.component.html` y agrega el siguiente fragmento al final de la plantilla:

```html
<app-product-create></app-product-create>
```

Si ahora ejecutamos nuestra aplicación Angular con el comando `ng serve`, deberíamos ver el componente para agregar nuevos productos al final de la página:

*(Figura 8.5: Crear un producto)*

Para experimentar, intenta agregar un nuevo producto completando sus detalles, haciendo clic en el botón *Create* y verificando que el nuevo producto se haya agregado a la lista.

La siguiente característica que agregaremos a nuestra aplicación es modificar datos cambiando el precio de un producto existente.

#### Actualización del precio del producto
El precio de un producto en una aplicación de comercio electrónico puede necesitar cambiar en algún momento. Debemos proporcionar una forma para que nuestros usuarios actualicen ese precio a través de nuestra aplicación:

1. Abre el archivo `products.service.ts` y agrega un nuevo método para actualizar un producto:

```typescript
updateProduct(id: number, price: number): Observable<Product> {
  return this.http.patch<Product>(`${this.productsUrl}/${id}`, { price }).pipe(
    map(product => {
      const index = this.products.findIndex(p => p.id === id);
      this.products[index].price = price;
      return product;
    })
  );
}
```

En el método anterior, usamos el método `patch` de la clase `HttpClient` para enviar los detalles del producto que queremos modificar a la API. También actualizamos el precio del producto seleccionado en la caché local de productos y lo devolvemos.

> Alternativamente, podríamos haber usado el método `put` del cliente HTTP. El método `patch` debe usarse cuando queremos actualizar solo un subconjunto de un objeto, mientras que el método `put` interactúa con todas las propiedades del objeto. En este caso, no queremos actualizar el título del producto, por lo que usamos el método `patch`. Ambos métodos aceptan el punto de conexión de la API y el objeto que queremos actualizar como parámetros.

2. Agrega el siguiente método a la clase `ProductDetailComponent`:

```typescript
changePrice(product: Product, price: string) {
  this.productService.updateProduct(product.id, Number(price)).subscribe();
}
```

El método anterior acepta un producto existente y su nuevo precio como parámetros y llama al método `updateProduct` de la clase `ProductsService`.

3. Abre el archivo `product-detail.component.html` y agrega un elemento `<input>` y un elemento `<button>` después del elemento de párrafo del precio:

```html
@let product = (product$ | async);
@if (product) {
  <p>You selected: <strong>{{product.title}}</strong>
  </p>
  <p>{{product.price | currency:'EUR'}}</p>
  <input placeholder="New price" #price type="number" />
  <button class="secondary" (click)="changePrice(product, price.value)">
    Change
  </button>
  <div class="pill-group">
    <p class="pill">{{ product.category }}</p>
  </div>
  <button (click)="addToCart()">Add to cart</button>
}
```

El elemento `<input>` se usa para ingresar el nuevo precio del producto y define la variable de referencia de plantilla `price`. El evento `click` del elemento `<button>` está vinculado al método `changePrice` que pasa el objeto de producto actual y el valor de la variable `price`.

4. Finalmente, abre el archivo `product-detail.component.css` y agrega los siguientes estilos CSS:

```css
button.secondary {
  display: inline;
  margin-left: 5px;
  --button-accent: var(--vivid-pink);
}
```

Ejecuta el comando `ng serve` para iniciar la aplicación Angular y selecciona un producto de la lista. Los detalles del producto deberían verse así:

*(Figura 8.6: Detalles del producto)*

Ingresa un precio en el cuadro de entrada *New price* y haz clic en el botón *Change*. El precio existente debe actualizarse para reflejar el cambio, por ejemplo:

*(Figura 8.7: Detalles del producto con el precio modificado)*

Ahora podemos modificar un producto cambiando su precio.

> Recuerda que los cambios en los productos que provienen de Fake Store API no se persisten físicamente. Si cambias el precio y actualizas el navegador, se restaurará el precio inicial.

El siguiente y último paso de nuestra aplicación CRUD será eliminar un producto existente.

#### Eliminación de un producto
Eliminar un producto de una aplicación de tienda online no es muy común. Sin embargo, debemos proporcionar funcionalidad para ello en caso de que los usuarios ingresen datos incorrectos o no válidos y deseen eliminarlos más adelante. En nuestra aplicación, la eliminación de un producto existente se realizará con el componente de detalles del producto:

1. Abre el archivo `products.service.ts` e importa el operador `tap` del paquete `rxjs`:

```typescript
import { Observable, map, of, tap } from 'rxjs';
```

2. Agrega el siguiente método a la clase `ProductsService`:

```typescript
deleteProduct(id: number): Observable<void> {
  return this.http.delete<void>(`${this.productsUrl}/${id}`).pipe(
    tap(() => {
      const index = this.products.findIndex(p => p.id === id);
      this.products.splice(index, 1);
    })
  );
}
```

En el método anterior, usamos el método `delete` de la clase `HttpClient`, pasando el punto de conexión de productos y el `id` del producto que queremos eliminar en la API. También usamos el método `splice` del array `products` para eliminar el producto de la caché local.

El tipo de retorno del método se establece en `Observable<void>` porque actualmente no estamos interesados en el resultado de la solicitud HTTP. Solo necesitamos saber si fue exitosa o no. También usamos el operador de RxJS `tap` porque no estamos alterando el valor devuelto por el observable.

3. Abre el archivo `product-detail.component.ts` y crea una nueva propiedad de salida en la clase `ProductDetailComponent`:

```typescript
deleted = output();
```

La propiedad anterior notificará a `ProductListComponent` que el producto seleccionado ha sido eliminado.

4. Crea el siguiente método, que llama al método `deleteProduct` de la clase `ProductsService` y desencadena el evento de salida `deleted`:

```typescript
remove(product: Product) {
  this.productService.deleteProduct(product.id).subscribe(() => {
    this.deleted.emit();
  });
}
```

5. Abre el archivo `product-detail.component.html`, crea un elemento `<button>` y vincula su evento `click` al método `emit` de la salida `deleted`:

```html
@let product = (product$ | async);
@if (product) {
  <p>You selected: <strong>{{product.title}}</strong>
  </p>
  <p>{{product.price | currency:'EUR'}}</p>
  <input placeholder="New price" #price type="number" />
  <button class="secondary" (click)="changePrice(product, price.value)">
    Change
  </button>
  <div class="pill-group">
    <p class="pill">{{ product.category }}</p>
  </div>
  <div class="button-group">
    <button (click)="addToCart()">Add to cart</button>
    <button class="delete" (click)="remove(product)">Delete</button>
  </div>
}
```

En el fragmento anterior, agrupamos los dos botones en un elemento HTML `<div>` para que aparezcan uno al lado del otro.

6. Agrega un estilo apropiado para el nuevo botón y el grupo de botones en el archivo `product-detail.component.css`:

```css
button.delete {
  display: inline;
  margin-left: 5px;
  --button-accent: var(--hot-red);
}

.button-group {
  display: flex;
  flex-direction: row;
  align-items: start;
  flex-wrap: wrap;
}
```

7. Abre el archivo `product-list.component.html` y agrega un enlace al evento `deleted` del componente `<app-product-detail>`:

```html
<app-product-detail
  [id]="selectedProduct?.id"
  (added)="onAdded()"
  (deleted)="selectedProduct = undefined"
></app-product-detail>
```

Si ejecutamos la aplicación mediante el comando `ng serve` y seleccionamos un producto de la lista, deberíamos ver algo como lo siguiente:

*(Figura 8.8: Detalles del producto)*

El componente de detalles del producto ahora tiene un botón *Delete* que elimina el producto y lo quita de la lista cuando se hace clic en él.

> Recuerda que los cambios en los productos que provienen de Fake Store API no se persisten físicamente. Si eliminas un producto y actualizas el navegador, el producto volverá a aparecer en la lista.

La aplicación de comercio electrónico que hemos creado hasta ahora tiene un botón *Add to cart* que podemos usar para agregar un producto a un carrito de compras. El botón aún no hace mucho, pero implementaremos la funcionalidad completa del carrito en los siguientes capítulos. De acuerdo con la documentación de la Fake Store API, los carritos de compras solo están disponibles para usuarios autenticados, por lo que debemos asegurarnos de que el botón *Add to cart* solo esté disponible para ellos en nuestra aplicación.

En una aplicación empresarial de Angular, la función de gestión de productos también debe protegerse de usuarios no autorizados. En este caso, implementaríamos un esquema de autorización más granular con roles de usuario, permitiendo que solo los administradores cambien y agreguen productos. No implementaremos esta característica, pero te animamos a experimentar.

En la siguiente sección, aprenderemos sobre autenticación y autorización en Angular.

---

### Sección 8.6: Autenticación y autorización con HTTP

La Fake Store API proporciona un punto de conexión para autenticar usuarios. Contiene un método de inicio de sesión que acepta un nombre de usuario y una contraseña como parámetros y devuelve un token de autenticación. Utilizaremos el token de autenticación en nuestra aplicación para diferenciar entre un usuario que ha iniciado sesión y un invitado.

> Un grupo predefinido del punto de conexión de usuarios en [https://fakestoreapi.com/users](https://fakestoreapi.com/users) proporciona el nombre de usuario y la contraseña.

Exploraremos los siguientes temas de autenticación y autorización en esta sección:

- Autenticación con una API backend
- Autorización de usuarios para ciertas funciones
- Autorización de solicitudes HTTP mediante interceptores

Comencemos con el tema de la autenticación con la Fake Store API.

#### Autenticación con una API backend
En las aplicaciones de Angular del mundo real, generalmente creamos un componente de Angular que permite a los usuarios iniciar y cerrar sesión en la aplicación. Un servicio de Angular se comunicará con la API y manejará todas las tareas de autenticación.

Comencemos creando el servicio de autenticación:

1. Ejecuta el siguiente comando para crear un nuevo servicio de Angular:

```bash
ng generate service auth
```

2. Abre el archivo `auth.service.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { Injectable, computed, inject, signal } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, tap } from 'rxjs';
import { APP_SETTINGS } from './app.settings';
```

3. Crea las siguientes propiedades en la clase `AuthService`:

```typescript
private accessToken = signal('');
private authUrl = inject(APP_SETTINGS).apiUrl + '/auth';
isLoggedIn = computed(() => this.accessToken() !== '');
```

En el fragmento anterior, la señal `accessToken` almacenará el token de autenticación de la API y la señal `isLoggedIn` indica si el usuario ha iniciado sesión. El estado de inicio de sesión del usuario depende de si la propiedad `accessToken` tiene un valor.

> Las señales se pueden usar no solo en componentes de Angular sino también dentro de servicios.

La propiedad `authUrl` apunta a la URL del punto de conexión de autenticación de la Fake Store API.

4. Inyecta la clase `HttpClient` en el constructor:

```typescript
constructor(private http: HttpClient) { }
```

5. Crea un método `login` para permitir a los usuarios iniciar sesión en la Fake Store API:

```typescript
login(username: string, password: string): Observable<string> {
  return this.http.post<string>(this.authUrl + '/login', { username, password }).pipe(
    tap(token => this.accessToken.set(token))
  );
}
```

El método anterior inicia una solicitud POST a la API, utilizando el punto de conexión de inicio de sesión y pasando el nombre de usuario y la contraseña en el cuerpo de la solicitud. El observable devuelto por la solicitud POST se pasa al operador `tap`, que actualiza la señal `accessToken`.

6. Crea un método `logout` que restablezca la señal `accessToken`:

```typescript
logout() {
  this.accessToken.set('');
}
```

Ya hemos configurado la lógica de negocio para autenticar usuarios en nuestra aplicación Angular. En la siguiente sección, aprenderemos cómo usarla para controlar la autorización en la aplicación.

#### Autorización del acceso de usuarios
Primero, crearemos un componente de autenticación que permitirá a nuestros usuarios iniciar y cerrar sesión en la aplicación:

1. Ejecuta el siguiente comando para crear un nuevo componente de Angular:

```bash
ng generate component auth
```

2. Abre el archivo `auth.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { AuthService } from '../auth.service';
```

3. Inyecta `AuthService` en el constructor del componente:

```typescript
constructor(public authService: AuthService) {}
```

En el fragmento anterior, usamos el modificador de acceso `public` para inyectar `AuthService` porque queremos que sea accesible desde la plantilla del componente.

4. Crea los siguientes métodos en la clase `AuthComponent`:

```typescript
login() {
  this.authService.login('david_r', '3478*#54').subscribe();
}

logout() {
  this.authService.logout();
}
```

En el fragmento anterior, el método `login` utiliza credenciales predefinidas del punto de conexión de usuarios.

5. Abre el archivo `auth.component.html` y reemplaza su contenido con la siguiente plantilla HTML:

```html
@if (!authService.isLoggedIn()) {
  <button (click)="login()">Login</button>
} @else {
  <button (click)="logout()">Logout</button>
}
```

La plantilla anterior contiene dos elementos HTML `<button>` para iniciar y cerrar sesión. Cada botón se muestra condicionalmente según el valor de la señal `isLoggedIn` de la clase `AuthService`.

Ahora podemos aprovechar la señal `isLoggedIn` en el componente de detalles del producto y alternar la visibilidad del botón *Add to cart*:

6. Abre el archivo `product-detail.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { AuthService } from '../auth.service';
```

7. Inyecta `AuthService` en el constructor de la clase `ProductDetailComponent`:

```typescript
constructor(private productService: ProductsService, public authService: AuthService) { }
```

8. Abre el archivo `product-detail.component.html` y usa un bloque `@if` para mostrar el botón *Add to cart* condicionalmente:

```html
@if (authService.isLoggedIn()) {
  <button (click)="addToCart()">Add to cart</button>
}
```

9. Abre el archivo `app.component.ts` e importa la clase `AuthComponent`:

```typescript
import { Component, inject } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { ProductListComponent } from './product-list/product-list.component';
import { CopyrightDirective } from './copyright.directive';
import { APP_SETTINGS } from './app.settings';
import { AuthComponent } from './auth/auth.component';

@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    ProductListComponent,
    CopyrightDirective,
    AuthComponent
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
```

10. Abre el archivo `app.component.html` y agrega la etiqueta `<app-auth>` dentro del elemento HTML `<header>`:

```html
<header>
  {{ settings.title }}
  <app-auth></app-auth>
</header>
```

Para probar la función de autenticación en la aplicación, sigue estos pasos:

1. Ejecuta el comando `ng serve` para iniciar la aplicación y navega a `http://localhost:4200`.
2. Selecciona un producto de la lista y verifica que el botón *Add to cart* no esté visible.
3. Haz clic en el botón *Login* en la esquina superior izquierda de la página. El texto debería cambiar a *Logout* después de haber iniciado sesión con éxito en la Fake Store API, y debería aparecer el botón *Add to cart*.

¡Felicitaciones! Has agregado patrones básicos de autenticación y autorización a tu aplicación de Angular.

Es común en aplicaciones empresariales realizar la autorización en la capa de lógica de negocio mientras se comunica con la API backend. La API backend a menudo requiere ciertas llamadas a métodos para pasar el token de autenticación en cada solicitud a través de encabezados (*headers*). Aprenderemos a trabajar con encabezados HTTP en la siguiente sección.

#### Autorización de solicitudes HTTP
La Fake Store API no requiere autorización mientras se comunica con sus puntos de conexión. Sin embargo, supongamos que estamos trabajando con una API backend que espera que todas las solicitudes HTTP contengan un token de autenticación mediante encabezados HTTP. Un patrón común en las aplicaciones web es incluir el token en un encabezado `Authorization`. Podemos usar encabezados HTTP en una aplicación de Angular importando la clase `HttpHeaders` del espacio de nombres `@angular/common/http` y modificando nuestros métodos en consecuencia. Aquí hay un ejemplo de cómo debería verse el método `getProducts`:

```typescript
getProducts(): Observable<Product[]> {
  const options = {
    params: new HttpParams().set('limit', 10),
    headers: new HttpHeaders({
      Authorization: 'myToken'
    })
  };
  return this.http.get<Product[]>(this.productsUrl, options).pipe(map(products => {
    this.products = products;
    return products;
  }));
}
```

> Por simplicidad, estamos usando un valor fijo para el token de autenticación. En un escenario del mundo real, podemos obtenerlo del almacenamiento local (*local storage*) del navegador o por algún otro medio.

Todos los métodos de `HttpClient` aceptan un objeto opcional como parámetro para pasar opciones adicionales a una solicitud HTTP, incluidos los encabezados HTTP. Para establecer encabezados, usamos la propiedad `headers` del objeto de opciones y creamos una nueva instancia de la clase `HttpHeaders` como valor. El objeto `HttpHeaders` es un par clave-valor que define encabezados HTTP personalizados.

Ahora, imagina lo que sucederá si necesitamos pasar el token de autenticación en todos los métodos de la clase `ProductsService`. Deberíamos ir a cada uno de ellos y escribir el mismo código repetidamente. Nuestro código podría saturarse rápidamente y volverse difícil de probar. Afortunadamente, el cliente HTTP de Angular tiene otra característica que podemos usar para ayudarnos en tal situación llamada **interceptores** (*interceptors*).

Un **interceptor HTTP** es un servicio de Angular que intercepta las solicitudes y respuestas HTTP que pasan a través del cliente HTTP de Angular. Se puede utilizar en los siguientes escenarios:

- Cuando queremos pasar encabezados HTTP personalizados en cada solicitud, como un token de autenticación.
- Cuando queremos mostrar un indicador de carga mientras esperamos una respuesta del servidor.
- Cuando queremos proporcionar un mecanismo de registro (*logging*) para cada comunicación HTTP.

En nuestro caso, podemos crear un interceptor para pasar el token de autenticación a cada solicitud HTTP:

1. Ejecuta el siguiente comando para crear un nuevo interceptor:

```bash
ng generate interceptor auth
```

2. Abre el archivo `app.config.ts` e importa la función `withInterceptors` del espacio de nombres `@angular/common/http`:

```typescript
import { provideHttpClient, withInterceptors } from '@angular/common/http';
```

La función `withInterceptors` se utiliza para registrar un interceptor con el cliente HTTP.

3. Importa el interceptor que creamos en el paso anterior usando la siguiente sentencia:

```typescript
import { authInterceptor } from './auth.interceptor';
```

4. Modifica el método `provideHttpClient` para registrar el `authInterceptor`:

```typescript
export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient(withInterceptors([authInterceptor])),
    { provide: APP_SETTINGS, useValue: appSettings }
  ]
};
```

La función `withInterceptors` acepta una lista de interceptores registrados, y su orden importa. En el siguiente diagrama, puedes ver cómo los interceptores procesan las solicitudes y respuestas HTTP según su orden:

*(Figura 8.9: Orden de ejecución de los interceptores de Angular)*

> De forma predeterminada, el último interceptor antes de enviar la solicitud al servidor es un servicio integrado de Angular llamado `HttpBackend`.

5. Abre el archivo `auth.interceptor.ts` y modifica la función flecha de la función `authInterceptor` de la siguiente manera:

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authReq = req.clone({
    setHeaders: {
      Authorization: 'myToken'
    }
  });
  return next(authReq);
};
```

La función flecha acepta los siguientes parámetros: `req`, que indica la solicitud actual, y `next`, que es el siguiente interceptor en la cadena. En el fragmento anterior, usamos el método `clone` para modificar la solicitud existente porque las solicitudes HTTP son inmutables por defecto. Del mismo modo, debido a la naturaleza inmutable de los encabezados HTTP, usamos el método `setHeaders` para actualizarlos. Finalmente, delegamos la solicitud al siguiente interceptor usando el método `next`.

Los interceptores pueden usar el método `inject` para obtener las dependencias que puedan necesitar del mecanismo de DI de Angular. Por ejemplo, si quisiéramos usar la clase `AuthService` dentro del interceptor, podríamos modificarlo de la siguiente manera:

```typescript
import { inject } from '@angular/core';
import { HttpInterceptorFn } from '@angular/common/http';
import { AuthService } from './auth.service';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);
  const authReq = req.clone({
    setHeaders: {
      Authorization: 'myToken'
    }
  });
  return next(authReq);
};
```

> En aplicaciones construidas con versiones anteriores del framework Angular, notarás que los interceptores son clases de TypeScript en lugar de funciones puras. Para registrar un interceptor con el cliente HTTP, necesitamos agregar el siguiente literal de objeto `provide` en el array `providers` del módulo, que también proporciona el `HttpClientModule`:
>
> ```typescript
> { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true }
> ```
>
> En el fragmento anterior, `HTTP_INTERCEPTORS` es un token de inyección que se puede proporcionar varias veces, como lo indica la propiedad `multi`.

Los interceptores de Angular tienen muchos usos, y la autorización es uno de los más básicos. Pasar tokens de autenticación durante las solicitudes HTTP es un escenario común en las aplicaciones web empresariales.

---

### Sección 8.7: Resumen

Las aplicaciones web empresariales deben intercambiar información con una API backend casi a diario. El framework Angular permite que las aplicaciones se comuniquen con una API a través de HTTP mediante el cliente HTTP de Angular. En este capítulo, exploramos las partes esenciales del cliente HTTP de Angular.

Aprendimos a alejarnos de la API tradicional `fetch` y a utilizar observables para comunicarnos a través de HTTP. Exploramos las partes básicas de una aplicación CRUD utilizando la Fake Store API como nuestro backend. Investigamos cómo implementar autenticación y autorización en aplicaciones de Angular. Finalmente, aprendimos qué son los interceptores de Angular y cómo usarlos para autorizar llamadas HTTP.

Ahora que sabemos cómo consumir datos de una API backend en nuestros componentes, podemos mejorar aún más la experiencia del usuario de nuestra aplicación. En el próximo capítulo, aprenderemos cómo cargar nuestros componentes a través de la navegación utilizando el enrutador de Angular.
