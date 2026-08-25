# Parte 3: Enrutamiento, Datos y Formularios

## Capítulo 9: Navegación a través de aplicaciones con Enrutamiento

En los capítulos anteriores, hicimos un gran trabajo separando responsabilidades y agregando diferentes capas de abstracción para aumentar la mantenibilidad de una aplicación Angular. Sin embargo, apenas nos hemos centrado en la experiencia de usuario (UX) de la aplicación.

Nuestra interfaz de usuario está sobrecargada, con componentes dispersos en una sola pantalla. Debemos proporcionar una mejor experiencia de navegación para los usuarios y una forma lógica de cambiar la vista de la aplicación de manera intuitiva. Ahora es el momento adecuado para incorporar el enrutamiento y dividir las diferentes áreas de interés en páginas, conectadas por una cuadrícula de enlaces y URLs.

Entonces, ¿cómo implementamos un esquema de navegación entre los componentes de una aplicación Angular? Usamos el **enrutador de Angular** (*Angular router*) y creamos enlaces personalizados a los que nuestros componentes puedan reaccionar.

Este capítulo contiene las siguientes secciones:

- Introducción al enrutador de Angular
- Configuración de las rutas principales
- Organización de las rutas de la aplicación
- Paso de parámetros a las rutas
- Mejora de la navegación con funciones avanzadas

---

### Sección 9.1: Requisitos técnicos

Este capítulo contiene varios ejemplos de código para guiarte a través del enrutamiento en el framework Angular. Puedes encontrar el código fuente correspondiente en la carpeta `ch09` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch09](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch09)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 9.2: Introducción al enrutador de Angular

En las aplicaciones web tradicionales, cuando queríamos cambiar de una vista a otra, necesitábamos solicitar una nueva página al servidor. El navegador creaba una URL para la vista y la enviaba al servidor. Luego, el navegador recargaba la página tan pronto como el cliente recibía una respuesta. Era un proceso que daba como resultado retrasos en el tiempo de ida y vuelta (*round trip time*) y una mala experiencia de usuario para nuestras aplicaciones:

*(Figura 9.1: Enrutamiento en aplicaciones web tradicionales)*

Las aplicaciones web modernas que utilizan frameworks de JavaScript como Angular siguen un enfoque diferente. Manejan los cambios entre vistas o componentes en el lado del cliente sin molestar al servidor. Se comunican con el servidor una vez durante el arranque para obtener el archivo HTML principal. El enrutador en el cliente intercepta y maneja cualquier cambio de URL posterior. Estas aplicaciones se denominan **Aplicaciones de Página Única** (**SPA**, *Single-Page Applications*) porque no provocan una recarga completa de una página:

*(Figura 9.2: Arquitectura SPA)*

> **Consejo rápido:** ¿Necesitas ver una versión de alta resolución de esta imagen? Abre este libro en el lector Packt de última generación o visualízalo en la copia en PDF/ePub: [https://www.packtpub.com/unlock/9781835087480](https://www.packtpub.com/unlock/9781835087480).

El framework Angular proporciona el paquete npm `@angular/router`, que podemos utilizar para navegar entre diferentes componentes en una aplicación Angular.

Agregar enrutamiento en una aplicación Angular implica los siguientes pasos:

1. Especificar la ruta base para la aplicación Angular.
2. Utilizar una directiva o servicio apropiado del paquete npm `@angular/router`.
3. Configurar diferentes rutas para la aplicación Angular.
4. Decidir dónde renderizar los componentes al navegar.

En las siguientes secciones, aprenderemos los conceptos básicos del enrutamiento de Angular antes de profundizar en ejemplos prácticos.

#### Especificar una ruta base
Como ya hemos visto, las aplicaciones web modernas y tradicionales reaccionan de manera diferente cuando cambia una URL dentro de la aplicación. La arquitectura de cada navegador juega un papel esencial en este comportamiento. Los navegadores más antiguos inician una nueva solicitud al servidor cuando cambia la URL. Los navegadores modernos, también conocidos como navegadores *evergreen*, pueden cambiar la URL y el historial del navegador al navegar en diferentes vistas sin enviar una solicitud al servidor, utilizando una técnica llamada **`pushState`**.

> `HTML5 pushState` permite la navegación dentro de la aplicación sin provocar una recarga completa de la página y es compatible con todos los navegadores modernos.

Una aplicación Angular debe establecer la etiqueta HTML `<base>` en el archivo `index.html` para habilitar el enrutamiento mediante `pushState`:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MyApp</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

El atributo `href` informa al navegador de la ruta que debe seguir al cargar los recursos de la aplicación. Angular CLI agrega automáticamente la etiqueta al crear una nueva aplicación y establece el valor de `href` en la raíz de la aplicación, `/`. Si tu aplicación reside en una carpeta diferente a la raíz, debes nombrarla según esa carpeta.

#### Habilitar el enrutamiento en aplicaciones de Angular
El enrutador de Angular está habilitado de forma predeterminada en las nuevas aplicaciones de Angular, como lo indica el método `provideRouter` en el archivo `app.config.ts`:

```typescript
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';
import { routes } from './app.routes';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes)
  ]
};
```

> En aplicaciones creadas con versiones anteriores del framework Angular, el enrutador se habilita importando la clase `RouterModule` en el módulo principal de la aplicación y usando su método `forRoot` para definir la configuración de enrutamiento.

El método `provideRouter` nos permite utilizar un conjunto de artefactos de Angular relacionados con el enrutamiento:

- Servicios para realizar tareas comunes de enrutamiento como la navegación.
- Directivas que podemos usar en nuestros componentes para enriquecerlos con lógica de navegación.

Acepta un único parámetro, que es la configuración de enrutamiento de la aplicación, y se define por defecto en el archivo `app.routes.ts`.

#### Configuración del enrutador
El archivo `app.routes.ts` contiene una lista de objetos `Routes` que especifican qué rutas existen en la aplicación y qué componentes deben responder a una ruta específica. Se ve de la siguiente manera:

```typescript
const routes: Routes = [
  { path: 'products', component: ProductListComponent },
  { path: '**', component: PageNotFoundComponent }
];
```

> En aplicaciones construidas con versiones anteriores del framework Angular, notarás que la configuración de rutas se define en un archivo dedicado `app-routing.module.ts`.

Cada objeto de definición de ruta contiene una propiedad `path`, que es la ruta URL de la ruta, y una propiedad `component` que define qué componente se cargará cuando la aplicación navegue a esa ruta.

> El valor de una propiedad `path` no debe contener una barra diagonal inicial `/`.

La navegación en una aplicación Angular puede ocurrir manualmente cambiando la URL del navegador o navegando mediante enlaces dentro de la aplicación (*in-app links*). El navegador provocará que la aplicación se recargue en el primer escenario, mientras que el segundo indicará al enrutador que navegue en tiempo de ejecución. En nuestro caso, cuando la URL del navegador contiene la ruta `products`, el enrutador muestra el componente de lista de productos en la página. Por el contrario, cuando la aplicación navega a `products` por código, el enrutador sigue el mismo procedimiento y actualiza la URL del navegador.

Si el usuario intenta navegar a una URL que no coincide con ninguna ruta, Angular activa un tipo personalizado de ruta llamado comodín (*wildcard*) o reserva (*fallback*). La ruta comodín tiene una propiedad `path` con dos asteriscos `**` y coincide con cualquier URL. La propiedad `component` para esto suele ser un `PageNotFoundComponent` específico de la aplicación o el componente principal de la aplicación.

#### Renderizado de componentes
La plantilla del componente principal de la aplicación contiene el elemento `<router-outlet>`, que es una de las principales directivas del enrutador de Angular. Reside dentro del archivo `app.component.html` y se utiliza como un marcador de posición para los componentes activados con enrutamiento. Estos componentes se representan como un elemento hermano del elemento `<router-outlet>`.

Hemos cubierto los conceptos básicos y proporcionado una configuración mínima del enrutador. En la siguiente sección, veremos un ejemplo más realista y ampliaremos nuestro conocimiento sobre el enrutamiento.

---

### Sección 9.3: Configuración de las rutas principales

Cuando comenzamos a diseñar la arquitectura de una aplicación Angular con enrutamiento, es más fácil pensar en sus características principales, como los enlaces de menú en los que los usuarios pueden hacer clic para acceder. Los productos y los carritos de compras son características básicas de la aplicación de tienda online que estamos construyendo actualmente. Agregar enlaces y configurarlos para activar ciertas funciones de una aplicación Angular es parte de la configuración de rutas de la aplicación.

> Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 8: Comunicación con servicios de datos a través de HTTP* para seguir el resto del capítulo. Después de obtener el código, te sugerimos realizar las siguientes acciones por simplicidad:
>
> - Eliminar el archivo `auth.interceptor.ts` y su archivo de pruebas unitarias. Las llamadas reales en la Fake Store API no necesitan autenticación.
> - Modificar el archivo `app.config.ts` para que el método `provideHttpClient` no use el interceptor.

Para configurar las rutas de nuestra aplicación, debemos seguir los siguientes pasos:

1. Ejecuta el siguiente comando para crear un nuevo componente de Angular para el carrito de compras:

```bash
ng generate component cart
```

2. Abre el archivo `app.routes.ts` y agrega las siguientes sentencias de importación:

```typescript
import { CartComponent } from './cart/cart.component';
import { ProductListComponent } from './product-list/product-list.component';
```

3. Agrega dos objetos de definición de ruta en la variable `routes`:

```typescript
export const routes: Routes = [
  { path: 'products', component: ProductListComponent },
  { path: 'cart', component: CartComponent }
];
```

En el fragmento anterior, la ruta `products` activará el `ProductListComponent`, y la ruta `cart` activará el `CartComponent`.

4. Abre el archivo `app.component.html` y modifica el elemento HTML `<header>` de la siguiente manera:

```html
<header>
  <h2>{{ settings.title }}</h2>
  <span class="spacer"></span>
  <div class="menu-links">
    <a routerLink="/products">Products</a>
    <a routerLink="/cart">My Cart</a>
  </div>
  <app-auth></app-auth>
</header>
```

En la plantilla anterior, aplicamos la directiva `routerLink` a los elementos de anclaje HTML (`<a>`) y asignamos la ruta a la que queremos navegar. Observa que la ruta debe comenzar con `/`, a diferencia de la propiedad `path` en el objeto de definición de ruta.

> La forma en que comienza la ruta depende de si queremos usar enrutamiento absoluto o relativo en nuestra aplicación, como aprenderemos más adelante en el capítulo.

5. Mueve el elemento HTML `<router-outlet>` dentro del elemento `<div>` con el selector de clase `content` y elimina el componente `<app-product-list>`:

```html
<main class="main">
  <div class="content">
    <router-outlet />
  </div>
</main>
```

6. Abre el archivo `app.component.ts`, elimina cualquier referencia a la clase `ProductListComponent` e importa la clase `RouterLink`:

```typescript
import { Component, inject } from '@angular/core';
import { RouterLink, RouterOutlet } from '@angular/router';
import { CopyrightDirective } from './copyright.directive';
import { APP_SETTINGS } from './app.settings';
import { AuthComponent } from './auth/auth.component';

@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    RouterLink,
    CopyrightDirective,
    AuthComponent
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
```

7. Abre el archivo `app.component.css` y reemplaza cada estilo CSS relacionado con el selector `.social-links` con los siguientes estilos:

```css
header {
  display: flex;
  flex-direction: row;
  gap: 0.73rem;
  justify-content: end;
  margin-top: 1.5rem;
}

.menu-links {
  display: flex;
  align-items: center;
  gap: 0.73rem;
}

.menu-links a {
  transition: fill 0.3s ease;
  color: var(--gray-400);
}

.menu-links a:hover {
  color: var(--gray-900);
}
```

8. Finalmente, abre el archivo global `styles.css` y agrega los siguientes estilos CSS:

```css
a {
  text-decoration: none;
}

.spacer {
  flex: 1 1 auto;
}
```

Ahora estamos listos para previsualizar nuestra aplicación Angular:

1. Ejecuta el comando `ng serve` y navega a `http://localhost:4200`. Inicialmente, la página de la aplicación muestra solo el encabezado de la aplicación y la información de derechos de autor.
2. Haz clic en el enlace **Products**. La aplicación debería mostrar la lista de productos y actualizar la URL del navegador para que coincida con la ruta `/products`.
3. Ahora navega a la ruta raíz en `http://localhost:4200` y añade la ruta `/cart` al final de la URL del navegador. La aplicación debería reemplazar la vista de la lista de productos con el componente del carrito:

```text
cart works!
```

> El enrutamiento en Angular funciona bidireccionalmente. Nos permite navegar a un componente de Angular usando los enlaces dentro de la aplicación o la barra de direcciones del navegador.

¡Felicitaciones! Tu aplicación Angular ahora admite la navegación interna.

Apenas hemos arañado la superficie del enrutamiento de Angular. Hay muchas funciones que investigaremos en las siguientes secciones. Por ahora, intentemos dividir nuestros componentes en más rutas para que podamos administrarlos fácilmente.

---

### Sección 9.4: Organización de las rutas de la aplicación

Nuestra aplicación muestra la lista de productos junto con los componentes de detalles del producto y de creación de productos. Necesitamos organizar la configuración de enrutamiento para que diferentes rutas activen cada componente.

En esta sección, agregaremos una nueva ruta para el componente de creación de productos. Más adelante, en la sección *Paso de parámetros a las rutas*, agregaremos una ruta separada para el componente de detalles del producto.

Comencemos con el componente de creación de productos:

1. Abre el archivo `app.routes.ts` y agrega la siguiente sentencia de importación:

```typescript
import { ProductCreateComponent } from './product-create/product-create.component';
```

2. Agrega el siguiente objeto de definición de ruta en la variable `routes`:

```typescript
{ path: 'products/new', component: ProductCreateComponent }
```

3. Abre el archivo `product-list.component.ts` y elimina cualquier referencia a la clase `ProductCreateComponent`.
4. Abre el archivo `product-list.component.html` y elimina el elemento `<app-product-create>`.
5. Ejecuta el comando `ng serve` para iniciar la aplicación, haz clic en el enlace *Products* y verifica que el formulario de creación de productos no se muestre.

Actualmente, el componente de creación de productos solo es accesible mediante la URL del navegador y no podemos acceder a él mediante la interfaz de usuario de la aplicación. En la siguiente sección, aprenderemos cómo lograr esa tarea y navegar imperativamente hacia una ruta.

#### Navegación imperativa hacia una ruta
El componente de creación de productos solo se puede activar ingresando la dirección `http://localhost:4200/products/new` en la barra de direcciones del navegador. Agreguemos un botón en la lista de productos que nos permita navegar desde la interfaz de usuario:

1. Abre el archivo `product-list.component.html` y modifica el segundo bloque `@if` de la siguiente manera:

> El elemento `<path>` a continuación puede ser complicado de escribir manualmente. Alternativamente, puedes encontrar el código en la carpeta `ch09` en el repositorio de GitHub del libro y copiarlo desde allí.

```html
@if (products) {
  <div class="caption">
    <h1>Products ({{products.length}})</h1>
    <a routerLink="new">
      <svg width="24" height="24" xmlns="http://www.w3.org/2000/svg" fill-rule="evenodd" clip-rule="evenodd">
        <path d="M11.5 0c6.347 0 11.5 5.153 11.5 11.5s-5.153 11.5-11.5 11.5-11.5-5.153-11.5-11.5 5.153-11.5 11.5-11.5zm0 1c5.795 0 10.5 4.705 10.5 10.5s-4.705 10.5-10.5 10.5-10.5-4.705-10.5-10.5 4.705-10.5 10.5-10.5zm.5 10h6v1h-6v6h-1v-6h-6v-1h6v-6h1v6z"/>
      </svg>
    </a>
  </div>
}
```

En el fragmento anterior, agregamos un elemento de anclaje que nos llevará al componente de creación de productos, según lo indicado por el valor de la directiva `routerLink`.

> El valor de la directiva `routerLink` es `new` y no `/products/new` como alguien esperaría. Este comportamiento se debe a que el botón reside en el componente de lista de productos, que ya está activado por la parte `products` de la ruta.
>
> El enrutador de Angular puede sintetizar la ruta de destino mediante todas las rutas activadas, pero si no deseas comenzar desde la raíz, puedes agregar una `/` antes de la ruta.

2. Abre el archivo `product-list.component.css` y agrega los siguientes estilos CSS:

```css
.caption {
  display: flex;
  align-items: center;
  gap: 1.25rem;
}

path {
  transition: fill 0.3s ease;
  fill: var(--gray-400);
}

a:hover svg path {
  fill: var(--gray-900);
}
```

3. Abre el archivo `product-list.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { RouterLink } from '@angular/router';
```

4. Agrega la clase `RouterLink` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-product-list',
  imports: [
    ProductDetailComponent,
    SortPipe,
    AsyncPipe,
    RouterLink
  ],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

5. Abre el archivo `product-create.component.css` y agrega el siguiente estilo CSS:

```css
:host {
  width: 400px;
}
```

En el estilo anterior, el selector `:host` apunta al elemento anfitrión del componente de creación de productos.

6. Ejecuta el comando `ng serve` para iniciar la aplicación y navega a `http://localhost:4200/products`:

*(Figura 9.3: Lista de productos)*

Haz clic en el botón con el signo más (`+`). La aplicación te redirige a la ruta `/products/new` y activa el componente de creación de productos:

*(Figura 9.4: Formulario de creación de productos)*

Aunque el componente de creación de productos sigue siendo funcional, nuestro cambio introdujo un defecto en la experiencia de usuario (UX) de la aplicación. El usuario no tiene una indicación visual cuando se crea un nuevo producto porque la lista de productos pertenece a una ruta diferente. Debemos modificar la lógica del botón *Create* para que redirija al usuario a la lista de productos tras la creación exitosa de un producto:

7. Abre el archivo `product-create.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { Router } from '@angular/router';
```

8. Inyecta el servicio `Router` en el constructor de la clase `ProductCreateComponent`:

```typescript
constructor(private productsService: ProductsService, private router: Router) {}
```

9. Modifica el método `createProduct` de la siguiente manera:

```typescript
createProduct(title: string, price: string, category: string) {
  this.productsService.addProduct({ title, price: Number(price), category }).subscribe(() => this.router.navigate(['/products']));
}
```

En el método anterior, llamamos al método `navigate` del servicio `Router` para navegar hacia la ruta `/products` de la aplicación.

> Usamos el carácter `/` porque estamos usando enrutamiento absoluto por defecto.
>
> Acepta un array de parámetros de enlace que contiene la ruta de destino a la que queremos navegar.

10. Abre el archivo `products.service.ts` y modifica el método `getProducts` para que utilice la Fake Store API cuando no haya datos de productos locales:

```typescript
getProducts(): Observable<Product[]> {
  if (this.products.length === 0) {
    const options = new HttpParams().set('limit', 10);
    return this.http.get<Product[]>(this.productsUrl, { params: options }).pipe(map(products => {
      this.products = products;
      return products;
    }));
  }
  return of(this.products);
}
```

Si no realizamos el cambio anterior, el componente de lista de productos siempre devolverá datos de la Fake Store API.

Nuestra aplicación ahora redirige a los usuarios a la lista de productos cada vez que crean un nuevo producto para que puedan verlo en la lista.

Hasta ahora, hemos configurado el enrutamiento de la aplicación para activar componentes según una ruta determinada. Sin embargo, nuestra aplicación no muestra ningún componente en las siguientes situaciones:

- Cuando navegamos a la ruta raíz de la aplicación.
- Cuando intentamos navegar a una ruta que no existe.

En la siguiente sección, aprenderemos cómo usar las rutas integradas que proporciona el enrutador de Angular y mejorar la UX de la aplicación.

#### Uso de rutas integradas
Cuando queremos definir un componente que se cargará cuando naveguemos a la ruta raíz, creamos un objeto de definición de ruta y establecemos la propiedad `path` en una cadena vacía. Una ruta con una propiedad `path` de cadena vacía se denomina **ruta predeterminada** (*default route*) de la aplicación Angular.

En nuestro caso, queremos que la ruta predeterminada muestre el componente de lista de productos. Abre el archivo `app.routes.ts` y agrega la siguiente ruta al final de la variable `routes`:

```typescript
{ path: '', redirectTo: 'products', pathMatch: 'full' }
```

En el fragmento anterior, le indicamos al enrutador que redirija a la ruta `products` cuando la aplicación navegue a la ruta predeterminada. La propiedad `pathMatch` le dice al enrutador cómo hacer coincidir la URL con la propiedad de ruta raíz. En este caso, el enrutador redirige a la ruta `products` solo cuando la URL coincide exactamente con la ruta raíz, que es la cadena vacía.

Si ejecutamos la aplicación, notaremos que cuando la URL del navegador apunta a la ruta raíz de nuestra aplicación, somos redirigidos a la ruta `products` y la lista de productos se muestra en la pantalla.

> Agregamos la ruta predeterminada después de todas las demás rutas porque el orden de las rutas es importante. El enrutador selecciona rutas con una estrategia en la que la primera coincidencia gana (*first-match-wins*). Las rutas más específicas deben definirse antes que las menos específicas.

Nos encontramos con el concepto de rutas desconocidas en la sección *Introducción al enrutador de Angular*. Vimos brevemente cómo configurar una ruta comodín para mostrar un `PageNotFoundComponent` cuando nuestra aplicación intenta navegar a una ruta que no existe. En aplicaciones del mundo real, es común crear un componente de este tipo, especialmente si deseas mostrar información adicional al usuario, como los siguientes pasos que puede seguir. En nuestro caso, que es más simple, redirigiremos a la ruta `products`.

Abre el archivo `app.routes.ts` y agrega la siguiente ruta al final de la variable `routes`:

```typescript
{ path: '**', redirectTo: 'products' }
```

> La ruta comodín debe ser la última entrada en la lista de rutas porque la aplicación solo debe alcanzarla si no hay rutas coincidentes.

Si ejecutamos nuestra aplicación usando el comando `ng serve` y navegamos a una ruta desconocida, nuestra aplicación mostrará la lista de productos.

Hasta ahora, hemos confiado en la barra de direcciones del navegador para indicar qué ruta está activa en un momento dado. Como aprenderemos en la siguiente sección, podríamos mejorar la experiencia del usuario mediante el uso de estilos CSS.

#### Estilizado de enlaces del enrutador
El encabezado de la aplicación contiene los enlaces *Products* y *My Cart*. Cuando navegamos a cada uno de ellos, no queda claro qué ruta se ha activado. El enrutador de Angular exporta la directiva `routerLinkActive`, que podemos utilizar para cambiar el estilo de un enlace cuando la ruta correspondiente está activa. Funciona de manera similar al enlace de clases que aprendimos en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*. Acepta una lista de nombres de clases o una sola clase que se agrega cuando el enlace está activo y se elimina cuando se vuelve inactivo.

Veamos cómo usarlo en nuestra aplicación:

1. Abre el archivo `app.component.css` y agrega el siguiente estilo CSS:

```css
.menu-links a.active {
  color: var(--electric-violet);
}
```

2. Abre el archivo `app.component.ts` e importa la clase `RouterLinkActive` del paquete npm `@angular/router`:

```typescript
import { RouterLink, RouterLinkActive, RouterOutlet } from '@angular/router';
```

3. Agrega la clase `RouterLinkActive` en el array `imports` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    RouterLink,
    RouterLinkActive,
    CopyrightDirective,
    AuthComponent
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
```

4. Abre el archivo `app.component.html` y agrega la directiva `routerLinkActive` a ambos enlaces:

```html
<div class="menu-links">
  <a routerLink="/products" routerLinkActive="active">Products</a>
  <a routerLink="/cart" routerLinkActive="active">My Cart</a>
</div>
```

Ahora, cuando hacemos clic en un enlace de la aplicación en el encabezado, su color cambia para indicar que el enlace está activo.

Hemos aprendido a usar el enrutamiento y activar componentes que no necesitan ningún parámetro. Sin embargo, el componente de detalles del producto acepta el ID del producto como parámetro. En la siguiente sección, aprenderemos cómo activar el componente utilizando parámetros de ruta dinámicos.

---

### Sección 9.5: Paso de parámetros a las rutas

Un escenario común en las aplicaciones web empresariales es tener una lista de elementos y, cuando haces clic en uno de ellos, la página cambia la vista actual y muestra los detalles del elemento seleccionado. El enfoque anterior se asemeja a una funcionalidad de navegación maestro-detalle (*master-detail*), donde cada URL generada en la página maestra contiene los identificadores necesarios para cargar cada elemento en la página de detalles.

Podemos representar el escenario anterior con dos rutas navegando a diferentes componentes. Un componente es la lista de elementos y el otro son los detalles del elemento. Por lo tanto, necesitamos encontrar una manera de crear y pasar datos dinámicos específicos del elemento de una ruta a la otra.

Aquí estamos abordando una doble tarea: crear URLs con parámetros dinámicos en tiempo de ejecución y analizar el valor de estos parámetros. No hay problema: el enrutador de Angular nos respalda y veremos cómo con un ejemplo real.

#### Creación de una página de detalle utilizando parámetros de ruta
La lista de productos en nuestra aplicación actualmente muestra una lista de productos. Cuando hacemos clic en un producto, los detalles del producto aparecen debajo de la lista. Necesitamos refactorizar el flujo de trabajo anterior para que el componente responsable de mostrar los detalles del producto se represente en una página diferente de la lista. Usaremos el enrutador de Angular para redirigir al usuario a la nueva página al hacer clic en un producto de la lista.

El componente de lista de productos actualmente pasa el ID del producto seleccionado mediante el enlace de entrada. Usaremos el enrutador de Angular para pasar el ID del producto como parámetro de ruta en su lugar:

1. Abre el archivo `app.routes.ts` y agrega la siguiente sentencia de importación:

```typescript
import { ProductDetailComponent } from './product-detail/product-detail.component';
```

2. Agrega la siguiente definición de ruta en la variable `routes` después de la ruta `products/new`:

```typescript
{ path: 'products/:id', component: ProductDetailComponent }
```

El carácter de dos puntos (`:`) denota `id` como un parámetro de ruta en el nuevo objeto de definición de ruta. Si una ruta tiene varios parámetros, los separamos con `/`. Como aprenderemos más adelante, el nombre del parámetro es importante cuando queremos consumir su valor en nuestros componentes.

3. Abre el archivo `product-list.component.html` y agrega un elemento de anclaje para el título del producto de modo que use la nueva definición de ruta:

```html
<ul class="pill-group">
  @for (product of products | sort; track product.id) {
    <li class="pill" (click)="selectedProduct = product">
      @switch (product.category) {
        @case ('electronics') {
        }
        @case ('jewelery') {
        }
        @default {
        }
      }
      <a [routerLink]="[product.id]">{{product.title}}</a>
    </li>
  } @empty {
    <p>No products found!</p>
  }
</ul>
```

En el fragmento anterior, la directiva `routerLink` utiliza el enlace de propiedades para establecer su valor en un array de parámetros de enlace. Pasamos el `id` de la variable de referencia de la plantilla del producto como parámetro en el array.

> No necesitamos prefijar el valor del array de parámetros de enlace con `/products` porque esa ruta ya activa la lista de productos.

4. Elimina el componente `<app-product-detail>` y el enlace del evento de clic de la etiqueta `<li>`.

Podemos refactorizar el archivo `product-list.component.ts` y eliminar cualquier código que use la propiedad `selectedProduct` y la clase `ProductDetailComponent`. La lista de productos no necesita mantener el producto seleccionado en su estado local porque estamos navegando fuera de la lista al elegir un producto.

Ahora podemos continuar modificando el componente de detalles del producto para que funcione con el enrutamiento:

5. Abre el archivo `product-detail.component.css` y agrega un estilo CSS para establecer el ancho del elemento anfitrión:

```css
:host {
  width: 450px;
}
```

6. Abre el archivo `product-detail.component.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { CommonModule } from '@angular/common';
import { Component, input, OnInit } from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { Product } from '../product';
import { Observable, switchMap } from 'rxjs';
import { ProductsService } from '../products.service';
import { AuthService } from '../auth.service';
```

El enrutador de Angular exporta el servicio `ActivatedRoute`, que podemos utilizar para recuperar información sobre la ruta actualmente activada, incluidos los parámetros.

7. Modifica el constructor del componente para inyectar los servicios `ActivatedRoute` y `Router`:

```typescript
constructor(
  private productService: ProductsService,
  public authService: AuthService,
  private route: ActivatedRoute,
  private router: Router
) { }
```

8. Modifica la lista de interfaces implementadas de la clase `ProductDetailComponent`:

```typescript
export class ProductDetailComponent implements OnInit
```

9. Crea el siguiente método `ngOnInit`:

```typescript
ngOnInit(): void {
  this.product$ = this.route.paramMap.pipe(
    switchMap(params => {
      return this.productService.getProduct(Number(params.get('id')));
    })
  );
}
```

El servicio `ActivatedRoute` contiene el observable `paramMap`, al que podemos suscribirnos para obtener los valores de los parámetros de ruta. El operador de RxJS `switchMap` se utiliza cuando queremos obtener un valor de un observable, completarlo y pasar el valor a otro observable. Lo usamos, en este caso, para canalizar el parámetro `id` del observable `paramMap` al método `getProduct` de la clase `ProductsService`.

10. Modifica los métodos `changePrice` y `remove` para que la aplicación redirija a la lista de productos al completar cada acción:

```typescript
changePrice(product: Product, price: string) {
  this.productService.updateProduct(product.id, Number(price)).subscribe(() => {
    this.router.navigate(['/products']);
  });
}

remove(product: Product) {
  this.productService.deleteProduct(product.id).subscribe(() => {
    this.router.navigate(['/products']);
  });
}
```

11. Elimina el método `ngOnChanges` porque el componente y sus enlaces se inicializan cada vez que se activa la ruta.
12. Elimina los emisores de eventos de salida (`output`) porque el componente de lista de productos ya no es un componente padre. Deja la propiedad de entrada `id` como está porque la usaremos más adelante en el capítulo.
13. Deja el método `addToCart` vacío por ahora. Lo utilizaremos más adelante en el *Capítulo 10: Recopilación de datos del usuario con Formularios*.

También vale la pena señalar lo siguiente:

- El observable `paramMap` devuelve un objeto del tipo `ParamMap`. Podemos usar el método `get` del objeto `ParamMap` para pasar el nombre del parámetro que definimos en la configuración de la ruta y acceder a su valor.
- Convertimos el valor del parámetro `id` a un número porque los valores de los parámetros de ruta son siempre cadenas de texto.

Si ejecutamos la aplicación usando el comando `ng serve` y hacemos clic en un producto de la lista, la aplicación nos navega al componente de detalles del producto:

*(Figura 9.5: Página de detalles del producto)*

> Si actualizas el navegador, la aplicación no mostrará el producto porque el método `getProduct` de la clase `ProductsService` funciona solo con la versión en caché de los datos del producto. Debes ir a la lista de productos nuevamente y seleccionar un producto porque la caché local se ha restablecido. Ten en cuenta que este comportamiento se basa en la implementación actual de la aplicación de tienda online y no está vinculado a la arquitectura del enrutador de Angular.

En el ejemplo anterior, usamos la propiedad `paramMap` para obtener parámetros de ruta como un observable. Por lo tanto, idealmente, nuestro componente podría recibir notificaciones de nuevos valores durante su ciclo de vida. Pero el componente se destruye cada vez que queremos seleccionar un producto diferente de la lista, al igual que la suscripción al observable `paramMap`.

Alternativamente, podemos evitar el uso de observables reutilizando la instancia de un componente siempre que permanezca renderizado en la pantalla durante navegaciones consecutivas. Podemos lograr este comportamiento mediante rutas secundarias (*child routes*), como aprenderemos en la siguiente sección.

#### Reutilización de componentes mediante rutas hijas
Las rutas secundarias o rutas hijas son una solución perfecta cuando queremos un componente de página de inicio (*landing page*) que proporcione enrutamiento a otros componentes. El componente debe contener un elemento `<router-outlet>` en el que se cargarán las rutas hijas.

Supongamos que queremos definir el diseño de nuestra aplicación Angular de la siguiente manera:

*(Figura 9.6: Diseño maestro-detalle)*

El escenario del diagrama anterior requiere que el componente de lista de productos contenga un elemento `<router-outlet>` para renderizar el componente de detalles del producto cuando se active la ruta correspondiente.

> El componente de detalles del producto se representará en el `<router-outlet>` del componente de lista de productos y no en el `<router-outlet>` del componente principal de la aplicación.

El componente de detalles del producto no se destruye cuando navegamos de un producto a otro. En su lugar, permanece en el árbol DOM y su método `ngOnInit` se llama una vez, la primera vez que seleccionamos un producto. Cuando elegimos un nuevo producto de la lista, el observable `paramMap` emite el `id` del nuevo producto. El nuevo producto se obtiene utilizando la clase `ProductsService` y la plantilla del componente se actualiza para reflejar los nuevos cambios.

La configuración de rutas de la aplicación, en este caso, sería la siguiente:

```typescript
export const routes: Routes = [
  {
    path: 'products',
    component: ProductListComponent,
    children: [
      { path: 'new', component: ProductCreateComponent },
      { path: ':id', component: ProductDetailComponent },
    ]
  },
  { path: 'cart', component: CartComponent },
  { path: '', redirectTo: 'products', pathMatch: 'full' },
  { path: '**', redirectTo: 'products' }
];
```

En el fragmento anterior, usamos la propiedad `children` del objeto de definición de ruta para definir rutas secundarias que contienen una lista de objetos de definición de ruta.

> Observa también que eliminamos la palabra `products` de la propiedad `path` de las rutas hijas porque la ruta principal la añadirá.

Una ruta principal también puede proporcionar servicios a sus elementos secundarios mediante la propiedad `providers` del objeto de definición de ruta. Proporcionar servicios en una ruta es muy útil cuando queremos limitar el acceso a un subconjunto de la configuración de enrutamiento.

Si quisiéramos restringir la clase `ProductsService` solo a los componentes relacionados con productos, deberíamos hacer lo siguiente:

```typescript
{
  path: 'products',
  component: ProductListComponent,
  children: [
    { path: 'new', component: ProductCreateComponent },
    { path: ':id', component: ProductDetailComponent },
  ],
  providers: [ProductsService]
}
```

Angular crea un inyector separado al proporcionar servicios en objetos de definición de rutas, que es un hijo directo del inyector raíz. Supongamos que el servicio también se proporciona en el inyector raíz y que el componente del carrito lo utiliza. En ese caso, la instancia creada por uno de los componentes relacionados con el producto diferirá de la del componente del carrito.

Hemos aprendido a usar el observable `paramMap` en el enrutamiento de Angular. En la siguiente sección, analizaremos un enfoque alternativo utilizando instantáneas (*snapshots*).

#### Tomar una instantánea de los parámetros de ruta
Cuando seleccionamos un producto de la lista, el componente de lista de productos se elimina del árbol DOM y se agrega el componente de detalles del producto. Para elegir un producto diferente, debemos hacer clic en el enlace *Products* o en el botón Atrás de nuestro navegador. En consecuencia, el componente de detalles del producto es reemplazado por el componente de lista de productos en el DOM. Por lo tanto, estamos en una situación en la que solo se muestra un componente en la pantalla en cualquier momento.

Cuando se destruye el componente de detalles del producto, también se destruye su método `ngOnInit` y la suscripción al observable `paramMap`. Por lo tanto, no nos beneficiamos del uso de observables en este punto. Alternativamente, podríamos usar la propiedad `snapshot` del servicio `ActivatedRoute` para obtener valores de los parámetros de ruta, de la siguiente manera:

```typescript
ngOnInit(): void {
  const id = this.route.snapshot.params['id'];
  this.product$ = this.productService.getProduct(id);
}
```

La propiedad `snapshot` representa el valor actual de un parámetro de ruta, que también resulta ser el valor inicial. Contiene la propiedad `params`, un objeto de pares clave-valor de parámetros de ruta a los que podemos acceder.

> Si estás seguro de que tu componente no se reutilizará, utiliza el enfoque de instantánea (*snapshot*).

Hasta ahora, hemos tratado con parámetros de enrutamiento en la forma `products/:id`. Usamos estos parámetros para navegar a un componente que requiere el parámetro. En nuestro caso, el componente de detalles del producto requiere el parámetro `id` para obtener detalles específicos del producto. Sin embargo, existe otro tipo de parámetro de ruta cuando necesitamos que sea opcional, como aprenderemos en la siguiente sección.

#### Filtrado de datos mediante parámetros de consulta (query parameters)
En el *Capítulo 8: Comunicación con servicios de datos a través de HTTP*, aprendimos cómo pasar parámetros de consulta a una solicitud utilizando la clase `HttpParams`. El enrutador de Angular también admite pasar parámetros de consulta a través de la URL de la aplicación.

El método `getProducts` en el archivo `products.service.ts` utiliza parámetros de consulta HTTP para limitar los resultados de productos devueltos por la Fake Store API:

```typescript
getProducts(): Observable<Product[]> {
  if (this.products.length === 0) {
    const options = new HttpParams().set('limit', 10);
    return this.http.get<Product[]>(this.productsUrl, { params: options }).pipe(map(products => {
      this.products = products;
      return products;
    }));
  }
  return of(this.products);
}
```

Utiliza un valor codificado para establecer el parámetro de consulta `limit`. Modificaremos la aplicación para que el componente de lista de productos pase el valor `limit` dinámicamente:

1. Abre el archivo `products.service.ts` y modifica el método `getProducts` para que el límite se pase como parámetro:

```typescript
getProducts(limit?: number): Observable<Product[]> {
  if (this.products.length === 0) {
    const options = new HttpParams().set('limit', limit || 10);
    return this.http.get<Product[]>(this.productsUrl, { params: options }).pipe(map(products => {
      this.products = products;
      return products;
    }));
  }
  return of(this.products);
}
```

En el método anterior, si el valor `limit` es *falsy*, pasamos un valor predeterminado de 10 al parámetro de consulta.

> Un valor *falsy* se evalúa como `false` en un contexto booleano y puede ser `null`, `undefined`, `0` o `false`. Puedes leer más en [https://developer.mozilla.org/docs/Glossary/Falsy](https://developer.mozilla.org/docs/Glossary/Falsy).

2. Abre el archivo `product-list.component.ts` e importa el servicio `ActivatedRoute` y el operador de RxJS `switchMap`:

```typescript
import { RouterLink, ActivatedRoute } from '@angular/router';
import { Observable, switchMap } from 'rxjs';
```

3. Inyecta el servicio `ActivatedRoute` en el constructor de la clase `ProductListComponent`:

```typescript
constructor(private productService: ProductsService, private route: ActivatedRoute) {}
```

El servicio `ActivatedRoute` contiene un observable `queryParamMap` al que podemos suscribirnos para obtener valores de parámetros de consulta. Devuelve un objeto `ParamMap`, similar al observable `paramMap` que vimos anteriormente, que podemos consultar para obtener valores de parámetros.

4. Modifica el método `getProducts` para usar el observable `queryParamMap`:

```typescript
private getProducts() {
  this.products$ = this.route.queryParamMap.pipe(
    switchMap(params => {
      return this.productService.getProducts(Number(params.get('limit')));
    })
  );
}
```

En el fragmento anterior, usamos el operador de RxJS `switchMap` para canalizar el parámetro `limit` del observable `queryParamMap` al método `getProducts` de la clase `ProductsService` como un número.

5. Ejecuta el comando `ng serve` para iniciar la aplicación y navega a `http://localhost:4200?limit=5`. Deberías ver una lista de 5 productos:

*(Figura 9.7: Lista de productos filtrada)*

Intenta experimentar con diferentes valores para el parámetro `limit` y observa la salida.

Los parámetros de consulta en el enrutamiento son potentes y se pueden utilizar para diversos casos de uso, como filtrar y ordenar datos. También se pueden utilizar cuando se trabaja con enrutamiento basado en instantáneas (*snapshots*).

En la siguiente sección, exploraremos una nueva e innovadora forma de pasar parámetros de ruta utilizando propiedades de entrada de componentes.

#### Vinculación de propiedades de entrada a rutas
Ya aprendimos, en el *Capítulo 3: Estructuración de interfaces de usuario con componentes*, que usamos enlaces de entrada y salida para intercomunicarnos entre componentes. Un enlace de entrada también puede pasar parámetros de ruta mientras se navega a un componente. Veremos un ejemplo usando el componente de detalles del producto:

1. El enlace de entrada con parámetros de ruta no está habilitado de forma predeterminada en el enrutador de Angular. Debemos activarlo desde el archivo de configuración de la aplicación. Abre el archivo `app.config.ts` e importa la función `withComponentInputBinding` del paquete npm `@angular/router`:

```typescript
import { provideRouter, withComponentInputBinding } from '@angular/router';
```

2. Pasa la función anterior como segundo parámetro en el método `provideRouter`:

```typescript
provideRouter(routes, withComponentInputBinding()),
```

3. Ahora, abre el archivo `product-detail.component.ts` y cambia el tipo de la propiedad de componente `id` a una cadena de texto:

```typescript
id = input<string>();
```

Debemos cambiar el tipo de propiedad porque los parámetros de enrutamiento se pasan como cadenas de texto (*strings*).

4. Modifica el método `ngOnInit` para usar el parámetro `id` para obtener un producto:

```typescript
ngOnInit(): void {
  this.product$ = this.productService.getProduct(Number(this.id()!));
}
```

5. Ejecuta el comando `ng serve` y verifica que los detalles del producto se muestren al seleccionar un producto de la lista.

Vincular parámetros de ruta a propiedades de entrada de componentes tiene las siguientes ventajas:

- La clase de componente TypeScript es más simple porque no tenemos llamadas asíncronas con observables.
- Podemos acceder a componentes existentes que funcionan con enlaces de entrada y salida mediante una ruta.

> El enlace de entrada funciona con componentes que se activan mediante enrutamiento. Si queremos acceder a cualquier parámetro de ruta desde otro componente, debemos utilizar el servicio `ActivatedRoute`.

Ahora que hemos aprendido todas las diferentes formas de pasar parámetros durante la navegación, hemos cubierto toda la información esencial que necesitamos para comenzar a construir aplicaciones Angular con enrutamiento. En las siguientes secciones, nos centraremos en prácticas avanzadas que mejoran la experiencia del usuario al utilizar la navegación dentro de la aplicación en aplicaciones Angular.

---

### Sección 9.6: Mejora de la navegación con funciones avanzadas

Hasta ahora, hemos cubierto el enrutamiento básico con parámetros de ruta y de consulta. Sin embargo, el enrutador de Angular es bastante capaz y puede hacer mucho más, como lo siguiente:

- Controlar el acceso a una ruta.
- Prevenir la navegación fuera de una ruta.
- Precarga de datos (*prefetching*) para mejorar la experiencia de usuario de la aplicación.
- Carga diferida (*lazy-loading*) de rutas para acelerar el tiempo de respuesta.

En las siguientes secciones, aprenderemos sobre todas estas técnicas con más detalle.

#### Control de acceso a rutas
Cuando queremos controlar el acceso a una ruta en particular, usamos un **guard** (guardia). Para crear un guard, usamos el comando `ng generate` de Angular CLI, pasando la palabra `guard` y su nombre como parámetros:

```bash
ng generate guard auth
```

Cuando ejecutamos el comando anterior, Angular CLI nos pregunta qué tipo de guard nos gustaría crear. Hay varios tipos de guards que podemos crear según la funcionalidad que brindan:

- **`CanActivate`:** Controla si se puede activar una ruta.
- **`CanActivateChild`:** Controla si se pueden activar las rutas secundarias.
- **`CanDeactivate`:** Controla si se puede desactivar una ruta (la desactivación ocurre cuando navegamos fuera de una ruta).
- **`CanMatch`:** Controla si se puede acceder a una ruta en absoluto.

Selecciona `CanActivate` y presiona *Enter*. Angular CLI crea el siguiente archivo `auth.guard.ts`:

```typescript
import { CanActivateFn } from '@angular/router';

export const authGuard: CanActivateFn = (route, state) => {
  return true;
};
```

El guard que creamos es una función de tipo `CanActivateFn`, que acepta dos parámetros:

- **`route`:** Indica la ruta que se activará.
- **`state`:** Contiene el estado del enrutador tras una navegación exitosa.

La función `CanActivateFn` puede devolver un valor booleano, ya sea de forma sincrónica o asincrónica. En este último caso, el enrutador esperará a que se resuelva el observable o la promesa antes de continuar. Si el evento asíncrono no se completa, la navegación no continuará. También puede devolver un objeto `UrlTree`, lo que provocará una nueva navegación a una ruta definida.

Nuestro guard devuelve `true` de inmediato, lo que permite el libre acceso a la ruta. Agreguemos lógica personalizada para controlar el acceso según si el usuario ha iniciado sesión:

1. Modifica las sentencias de importación de la siguiente manera:

```typescript
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from './auth.service';
```

2. Reemplaza el cuerpo de la función flecha con el siguiente fragmento:

```typescript
const authService = inject(AuthService);
const router = inject(Router);
if (authService.isLoggedIn()) {
  return true;
}
return router.parseUrl('/');
```

En el fragmento anterior, usamos el método `inject` para inyectar los servicios `AuthService` y `Router` en la función. Luego verificamos el valor de la señal `isLoggedIn`. Si es `true`, permitimos que la aplicación navegue a la ruta solicitada. De lo contrario, usamos el método `parseUrl` del servicio `Router` para navegar a la ruta raíz de la aplicación Angular.

> El método `parseUrl` devuelve un objeto `UrlTree`, que cancela efectivamente la navegación anterior y redirige al usuario a la URL pasada en el parámetro. Se recomienda usarlo en lugar del método `navigate`, que puede introducir un comportamiento inesperado y provocar problemas de navegación complejos.

3. Abre el archivo `app.routes.ts` y agrega la siguiente sentencia de importación:

```typescript
import { authGuard } from './auth.guard';
```

4. Agrega la función `authGuard` en el array `canActivate` de la ruta `cart`:

```typescript
{ path: 'cart', component: CartComponent, canActivate: [authGuard] }
```

> La propiedad `canActivate` es un array porque múltiples guards pueden controlar la activación de la ruta. El orden de los guards en el array es importante. Si uno de los guards no pasa, Angular impedirá el acceso a la ruta.

Ahora solo los usuarios autenticados pueden acceder al carrito de compras. Si ejecutas la aplicación usando el comando `ng serve` y haces clic en el enlace *My Cart*, notarás que no pasa nada.

> Cuando intentas acceder al carrito de compras desde la lista de productos, siempre permaneces en la misma página. Esto se debe a que la redirección que ocurre debido al guard de autenticación no tiene ningún efecto cuando ya te encuentras en la ruta redirigida.

Otro tipo de guard relacionado con la activación de una ruta es el guard `CanDeactivate`. En la siguiente sección, aprenderemos a utilizarlo para evitar que un usuario abandone una ruta.

#### Prevenir la navegación fuera de una ruta
Un guard que controla si se puede desactivar una ruta es una función del tipo `CanDeactivateFn`. Aprenderemos a utilizarlo implementando un guard que notifique al usuario sobre productos pendientes en el carrito cuando navegue fuera del componente del carrito:

1. Ejecuta el siguiente comando para generar un nuevo guard:

```bash
ng generate guard checkout
```

Selecciona el tipo `CanDeactivate` de la lista y presiona *Enter*.

2. Abre el archivo `checkout.guard.ts` y agrega la siguiente sentencia de importación:

```typescript
import { CartComponent } from './cart/cart.component';
```

3. Cambia el genérico de `CanDeactivateFn` a `CartComponent` y elimina los parámetros de la función flecha.

> En un escenario del mundo real, probablemente necesitaremos agregar más componentes en los genéricos para crear un guard genérico.

4. Reemplaza el cuerpo de la función flecha con el siguiente fragmento:

```typescript
const confirmation = confirm(
  'You have pending items in your cart. Do you want to continue?'
);
return confirmation;
```

En el fragmento anterior, usamos el método `confirm` del objeto global `window` para mostrar un cuadro de diálogo de confirmación antes de navegar fuera del componente del carrito. La ejecución de la aplicación esperará hasta que el cuadro de diálogo de confirmación se cierre mediante la interacción del usuario.

5. Abre el archivo `app.routes.ts` y agrega la siguiente sentencia de importación:

```typescript
import { checkoutGuard } from './checkout.guard';
```

Un objeto de definición de ruta contiene un array `canDeactivate` similar a `canActivate`.

6. Agrega la función `checkoutGuard` al array `canDeactivate` de la ruta `cart`:

```typescript
{ path: 'cart', component: CartComponent, canActivate: [authGuard], canDeactivate: [checkoutGuard] }
```

> La propiedad `canDeactivate` es un array porque múltiples guards pueden controlar la desactivación de rutas. El orden de los guards en el array es importante. Si uno de los guards no pasa, Angular evitará que el usuario abandone la ruta.
>
> Para un escenario tan simple, podríamos haber escrito la lógica de la función `checkoutGuard` en línea para evitar la creación del archivo `checkout.guard.ts`:
>
> ```typescript
> {
>   path: 'cart',
>   component: CartComponent,
>   canActivate: [authGuard],
>   canDeactivate: [() => confirm('You have pending items in your cart. Do you want to continue?')]
> }
> ```

Ejecuta la aplicación usando el comando `ng serve` y haz clic en el enlace *My Cart* después de haber iniciado sesión. Si luego haces clic en el enlace *Products* o presionas el botón Atrás del navegador, deberías ver un cuadro de diálogo con el siguiente mensaje:

```text
You have pending items in your cart. Do you want to continue?
```

Si haces clic en el botón *Cancel*, la navegación se cancela y la aplicación permanece en su estado actual. Si haces clic en el botón *OK*, serás redirigido a la lista de productos.

#### Precarga de datos de ruta (prefetching)
Es posible que hayas notado que cuando navegas a la ruta raíz de la aplicación por primera vez, hay un retraso en la visualización de la lista de productos. Es razonable ya que estamos haciendo una solicitud HTTP a la API backend. Sin embargo, el componente de lista de productos ya estaba inicializado en ese momento.

El comportamiento anterior puede provocar efectos no deseados si el componente contiene lógica que interactúa con los datos durante la inicialización. Para resolver este problema, podemos usar un **resolver** para precargar la lista de productos y cargar el componente cuando los datos estén disponibles.

> Un resolver puede ser útil al manejar posibles errores antes de activar una ruta. Sería más apropiado navegar a una página de error si la solicitud a la API no tiene éxito en lugar de mostrar una página en blanco.

Para crear un resolver, usamos el comando `ng generate` de Angular CLI, pasando la palabra `resolver` y su nombre como parámetros:

```bash
ng generate resolver products
```

El comando anterior crea el siguiente archivo `products.resolver.ts`:

```typescript
import { ResolveFn } from '@angular/router';

export const productsResolver: ResolveFn<boolean> = (route, state) => {
  return true;
};
```

El resolver que creamos es una función de tipo `ResolveFn`, que acepta dos parámetros:

- **`route`:** Indica la ruta que se activará.
- **`state`:** Contiene el estado de la ruta activada.

Una función `ResolveFn` puede devolver un observable o una promesa. El enrutador esperará a que se resuelva el observable o la promesa antes de continuar. Si el evento asíncrono no se completa, la navegación no continuará.

Actualmente, nuestro resolver devuelve un valor booleano. Agreguemos lógica personalizada para que devuelva un array de productos:

1. Agrega las siguientes sentencias de importación:

```typescript
import { inject } from '@angular/core';
import { Product } from './product';
import { ProductsService } from './products.service';
```

2. Modifica la función `productsResolver` para que devuelva un array de productos:

```typescript
export const productsResolver: ResolveFn<Product[]> = (route, state) => {
  return [];
};
```

3. Usa el método `inject` para inyectar `ProductsService` en el cuerpo de la función:

```typescript
const productService = inject(ProductsService);
```

4. Usa la propiedad `queryParamMap` para obtener el valor del parámetro `limit` de la ruta actual:

```typescript
const limit = Number(route.queryParamMap.get('limit'));
```

5. Reemplaza la sentencia `return` con lo siguiente:

```typescript
return productService.getProducts(limit);
```

La función resultante debería verse así:

```typescript
export const productsResolver: ResolveFn<Product[]> = (route, state) => {
  const productService = inject(ProductsService);
  const limit = Number(route.queryParamMap.get('limit'));
  return productService.getProducts(limit);
};
```

Ahora que hemos creado el resolver, podemos conectarlo con el componente de lista de productos:

6. Abre el archivo `app.routes.ts` y agrega la siguiente sentencia de importación:

```typescript
import { productsResolver } from './products.resolver';
```

7. Agrega la siguiente propiedad `resolve` a la ruta `products`:

```typescript
{
  path: 'products',
  component: ProductListComponent,
  resolve: {
    products: productsResolver
  }
}
```

La propiedad `resolve` es un objeto que contiene un nombre único como clave y la función resolver como valor. El nombre de la clave es importante porque lo usaremos en nuestros componentes para acceder a los datos resueltos.

8. Abre el archivo `product-list.component.ts` e importa el operador `of` del paquete npm `rxjs`:

```typescript
import { Observable, switchMap, of } from 'rxjs';
```

9. Modifica el método `getProducts` para que se suscriba a la propiedad `data` del servicio `ActivatedRoute`:

```typescript
private getProducts() {
  this.products$ = this.route.data.pipe(
    switchMap(data => of(data['products']))
  );
}
```

En el fragmento anterior, el observable `data` emite un objeto cuyo valor existe en la clave `products`. Observa que usamos el operador `switchMap` para devolver productos en un nuevo observable.

En este punto, también podemos eliminar cualquier referencia a la clase `ProductsService` porque ya no es necesaria.

10. Ejecuta el comando `ng serve` para iniciar la aplicación y verifica que la lista de productos se muestre al navegar a `http://localhost:4200`.

Los resolvers de Angular mejoran el rendimiento de la aplicación cuando existe una lógica de inicialización compleja en los componentes enrutados. Otra forma de mejorar el rendimiento de la aplicación es cargar componentes o rutas secundarias bajo demanda, como aprenderemos en la siguiente sección.

#### Carga diferida (lazy loading) de partes de la aplicación
Nuestra aplicación puede crecer en algún momento y la cantidad de datos que contiene también puede aumentar. La aplicación puede tardar mucho tiempo en iniciarse inicialmente, o ciertas partes pueden tardar mucho en cargarse. Para superar estos problemas, podemos utilizar una técnica llamada **carga diferida** (*lazy loading*).

Lazy loading significa que inicialmente no cargamos ciertas partes de la aplicación, como componentes o rutas de Angular. Hay muchas ventajas de la carga diferida en una aplicación Angular:

- Los componentes y rutas se pueden cargar a petición del usuario.
- Los usuarios que visitan ciertas áreas de su aplicación pueden beneficiarse significativamente de esta técnica.
- Podemos agregar más funciones en un área cargada de forma diferida sin afectar el tamaño total del paquete de la aplicación.

Para comprender cómo funciona la carga diferida en Angular, crearemos un nuevo componente que muestre el perfil del usuario actual.

> Una buena práctica es cargar de forma diferida partes de la aplicación que no se utilizan con frecuencia, como el perfil del usuario que ha iniciado sesión actualmente.

Comencemos:

1. Ejecuta el siguiente comando para crear un componente de Angular:

```bash
ng generate component user
```

2. Crea un archivo llamado `user.routes.ts` en la carpeta `src/app` y agrega el siguiente contenido:

```typescript
import { UserComponent } from './user/user.component';

export default [
  {
    path: '',
    component: UserComponent
  }
];
```

En el fragmento anterior, establecemos la propiedad `path` en una cadena vacía para activar la ruta de forma predeterminada. También usamos la palabra clave `default` para beneficiarnos de la característica de exportación predeterminada en la carga diferida.

3. Abre el archivo `app.routes.ts` y agrega la siguiente definición de ruta en la variable `routes`:

```typescript
{
  path: 'user',
  loadChildren: () => import('./user.routes')
}
```

La propiedad `loadChildren` de un objeto de definición de ruta se utiliza para cargar rutas de Angular de forma diferida. Devuelve una función flecha que utiliza una sentencia de importación dinámica para cargar el archivo de rutas de forma diferida. La función `import` acepta la ruta relativa del archivo de rutas que queremos importar.

4. Agrega un nuevo elemento de anclaje al elemento `<header>` del archivo `app.component.html` que enlace a la ruta recién creada:

```html
<div class="menu-links">
  <a routerLink="/products" routerLinkActive="active">Products</a>
  <a routerLink="/cart" routerLinkActive="active">My Cart</a>
  <a routerLink="/user" routerLinkActive="active">My Profile</a>
</div>
```

5. Ejecuta el comando `ng serve` y observa la salida en la ventana de la consola. Debería verse similar a lo siguiente:

```text
Initial chunk files | Names         |  Raw size
polyfills.js        | polyfills     |  82.71 kB |
main.js             | main          |  47.22 kB |
styles.css          | styles        |   1.14 kB |
                    | Initial total | 131.07 kB

Lazy chunk files    | Names         |  Raw size
chunk-D3RURZVV.js   | user-routes   |   1.26 kB |

Application bundle generation complete. [1.234 seconds]
```

En la salida anterior, podemos ver que Angular CLI ha creado un archivo de fragmento diferido (*lazy chunk file*) llamado `user-routes` además de los archivos de fragmentos iniciales de la aplicación.

6. Navega con tu navegador a `http://localhost:4200` y abre las herramientas para desarrolladores.
7. Haz clic en el enlace **My Profile** e inspecciona la pestaña de solicitudes de Red (*Network*):

*(Figura 9.8: Ruta cargada de forma diferida)*

La aplicación inicia una nueva solicitud al archivo chunk, que es el paquete de la ruta del usuario. El framework Angular crea un nuevo paquete para cada artefacto cargado de forma diferida y no lo incluye en el paquete principal de la aplicación.

> Si navegas a otra parte y vuelves a hacer clic en el enlace *My Profile*, notarás que la aplicación no realiza una nueva solicitud para cargar el archivo de paquete. Tan pronto como se solicita una ruta cargada de forma diferida, se mantiene en memoria y se puede utilizar para solicitudes posteriores.

La carga diferida funciona no solo con rutas sino también con componentes. Podríamos haber cargado de forma diferida el componente de usuario en lugar de toda la ruta modificando la ruta de usuario de la siguiente manera:

```typescript
{
  path: 'user',
  loadComponent: () => import('./user/user.component').then(c => c.UserComponent),
}
```

En el fragmento anterior, usamos la propiedad `loadComponent` para importar el archivo `user.component.ts` dinámicamente. La función `import` devuelve una promesa que encadenamos con el método `then` para cargar la clase `UserComponent`.

La ruta del usuario es actualmente accesible para todos los usuarios, incluso si no están autenticados. En la siguiente sección, aprenderemos a protegerla usando guards.

#### Protección de una ruta con carga diferida
Podemos controlar el acceso no autorizado a una ruta cargada de forma diferida de manera similar a como lo hacemos en las rutas normales. Sin embargo, nuestros guards deben admitir un tipo de función llamado `CanMatchFn`.

Ampliaremos nuestro guard de autenticación para su uso con rutas cargadas de forma diferida:

1. Abre el archivo `auth.guard.ts` e importa el tipo `CanMatchFn` del paquete npm `@angular/router`:

```typescript
import { CanActivateFn, CanMatchFn, Router } from '@angular/router';
```

2. Modifica la firma de la función `authGuard` de la siguiente manera:

```typescript
export const authGuard: CanActivateFn | CanMatchFn = () => {
  const authService = inject(AuthService);
  const router = inject(Router);
  if (authService.isLoggedIn()) {
    return true;
  }
  return router.parseUrl('/');
};
```

3. Abre el archivo `app.routes.ts` y agrega la función `authGuard` en el array `canMatch` de la ruta `user`:

```typescript
{
  path: 'user',
  loadChildren: () => import('./user.routes'),
  canMatch: [authGuard]
}
```

La propiedad `canMatch` es un array porque múltiples guards pueden controlar la coincidencia de rutas. El orden de los guards en el array es importante. Si uno de los guards no coincide con una ruta, Angular impedirá el acceso a la ruta.

Si ahora ejecutamos la aplicación y hacemos clic en el enlace *My Profile*, notaremos que no podemos navegar al componente respectivo a menos que estemos autenticados.

> La carga diferida es una técnica preferida cuando el rendimiento de la aplicación es crítico. Angular también ha introducido una característica más eficaz para retrasar la carga de partes de una aplicación Angular llamada **vistas diferibles** (*deferrable views*). Las vistas diferibles brindan a los desarrolladores un control más granular sobre las condiciones bajo las cuales se cargará una parte de la aplicación. Exploraremos las vistas diferibles en el *Capítulo 15: Optimización del rendimiento de la aplicación*.

---

### Sección 9.7: Resumen

Ahora hemos descubierto el poder del enrutador de Angular y esperamos que hayas disfrutado de este viaje por los entresijos de esta librería. Una de las cosas que brilla en el enrutador de Angular es la gran cantidad de opciones y escenarios que podemos cubrir con una implementación tan simple pero potente.

Aprendimos los conceptos básicos para configurar el enrutamiento y manejar diferentes tipos de parámetros. También aprendimos sobre funciones más avanzadas, como el enrutamiento secundario (*child routing*). Además, aprendimos cómo proteger nuestras rutas del acceso no autorizado. Finalmente, hemos mostrado todo el poder del enrutamiento y cómo puedes mejorar el tiempo de respuesta con la carga diferida (*lazy loading*) y la precarga (*prefetching*).

En el próximo capítulo, reforzaremos los componentes de nuestra aplicación para mostrar los mecanismos subyacentes a los formularios web en Angular y las mejores estrategias para capturar la entrada del usuario con controles de formulario.
