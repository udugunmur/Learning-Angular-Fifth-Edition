# Parte 1: Fundamentos de Angular

## Capítulo 5: Gestión de tareas complejas con Servicios

Hemos llegado a un punto de nuestro recorrido en el que podemos desarrollar aplicaciones más complejas con éxito anidando componentes dentro de otros componentes en una especie de árbol de componentes. Sin embargo, empaquetar toda nuestra lógica de negocio en un solo componente no es el camino a seguir. Nuestra aplicación podría volverse inmantenible muy pronto a medida que se desarrolla.

En este capítulo, investigaremos las ventajas que el mecanismo de gestión de dependencias de Angular puede aportar para superar tales problemas. Aprenderemos a utilizar el mecanismo de Inyección de Dependencias (**DI**, por sus siglas en inglés: *Dependency Injection*) de Angular para declarar y consumir nuestras dependencias en toda la aplicación con el mínimo esfuerzo y resultados óptimos. Al final de este capítulo, podrás crear una aplicación Angular correctamente estructurada para aplicar el patrón de Separación de Responsabilidades (**SoC**, *Separation of Concerns*) mediante el uso de servicios.

Cubriremos los siguientes conceptos relacionados con los servicios de Angular:

- Introducción a la Inyección de Dependencias (DI) de Angular
- Creando nuestro primer servicio Angular
- Proveer dependencias en toda la aplicación
- Inyectar servicios en el árbol de componentes
- Sobrescribir proveedores en la jerarquía del inyector

---

### Sección 5.1: Requisitos técnicos

Este capítulo contiene varios ejemplos de código para guiarte a través del concepto de los servicios de Angular. Puedes encontrar el código fuente correspondiente en la carpeta `ch05` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch05](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch05)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 5.2: Introducción a la Inyección de Dependencias (DI) de Angular

La **Inyección de Dependencias (DI)** es un patrón de diseño de aplicaciones que también encontramos en otros lenguajes como C# y Java. A medida que nuestras aplicaciones crecen y evolucionan, cada entidad de código requerirá internamente instancias de otros objetos, más conocidos como **dependencias**. Pasar dichas dependencias a la entidad de código consumidora se conoce como **inyección**, y también implica la participación de otra entidad de código llamada **inyector** (*injector*). Un inyector es responsable de instanciar y arrancar las dependencias requeridas para que estén listas para su uso cuando se inyectan en un consumidor. El consumidor no sabe nada sobre cómo instanciar sus dependencias y solo conoce la interfaz que implementan para utilizarlas.

Angular incluye un mecanismo de DI de primer nivel para exponer las dependencias requeridas a cualquier artefacto de una aplicación Angular. Antes de profundizar en este tema, veamos el problema que la DI en Angular intenta solucionar.

En el *Capítulo 3: Estructuración de interfaces de usuario con componentes*, aprendimos a mostrar una lista de objetos utilizando el bloque `@for`. Usamos una lista estática de objetos `Product` que se declararon en el archivo `product-list.component.ts`, como se muestra aquí:

```typescript
products: Product[] = [
  {
    id: 1,
    title: 'Keyboard',
    price: 100,
    categories: {
      1: 'Computing',
      2: 'Peripherals'
    }
  },
  {
    id: 2,
    title: 'Microphone',
    price: 35,
    categories: {
      3: 'Multimedia'
    }
  },
  {
    id: 3,
    title: 'Web camera',
    price: 79,
    categories: {
      1: 'Computing',
      3: 'Multimedia'
    }
  },
  {
    id: 4,
    title: 'Tablet',
    price: 500,
    categories: {
      4: 'Entertainment'
    }
  }
];
```

Este enfoque anterior tiene dos inconvenientes principales:

- En aplicaciones del mundo real, rara vez trabajamos con datos estáticos. Por lo general, provienen de una API backend o de alguna otra fuente externa.
- La lista de productos está fuertemente acoplada con el componente. Los componentes de Angular son responsables de la lógica de presentación y no deberían preocuparse por cómo obtener los datos. Solo necesitan mostrarlos en la plantilla HTML. Por lo tanto, deberían delegar la lógica de negocio a servicios para que se encarguen de tales tareas.

En la siguiente sección, aprenderemos cómo evitar estos obstáculos utilizando servicios de Angular.

> Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 4: Enriquecimiento de aplicaciones usando Pipes y Directivas* para seguir el resto del capítulo.

Crearemos un servicio de Angular que devolverá la lista de productos. De este modo, delegaremos de manera efectiva las tareas de lógica de negocio fuera del componente. Recuerda: el componente solo debe ocuparse de la lógica de presentación.

---

### Sección 5.3: Creando nuestro primer servicio Angular

Para crear un nuevo servicio de Angular, usamos el comando `ng generate` de Angular CLI pasando el nombre del servicio como parámetro:

```bash
ng generate service products
```

La ejecución del comando anterior creará el servicio `products`, que consta del archivo `products.service.ts` y su archivo de pruebas unitarias asociado, `products.service.spec.ts`.

Por lo general, nombramos un servicio en función de la funcionalidad que representa. Cada servicio tiene un contexto o dominio de negocio dentro del cual opera. Cuando comienza a cruzar fronteras entre diferentes contextos, esto es una indicación de que debes dividirlo en diferentes servicios. Un servicio de productos debe ocuparse de los productos. Del mismo modo, los pedidos deben gestionarse mediante un servicio de pedidos independiente.

Un servicio de Angular es una clase de TypeScript marcada con el decorador `@Injectable`. El decorador identifica la clase como un servicio de Angular que se puede inyectar en otros artefactos de Angular, como componentes, directivas o incluso otros servicios. Acepta un objeto como parámetro con una sola propiedad llamada `providedIn`, que define qué inyector proporciona el servicio.

Un servicio de Angular, de forma predeterminada, se registra con un inyector: el inyector raíz (*root injector*) de la aplicación Angular, como se define en el archivo `products.service.ts`:

```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class ProductsService {
  constructor() { }
}
```

Nuestro servicio no contiene ninguna implementación. Agreguemos algo de lógica para que nuestro componente pueda usarlo:

1. Agrega la siguiente sentencia para importar la interfaz `Product`:

```typescript
import { Product } from './product';
```

2. Crea el siguiente método en la clase `ProductsService`:

```typescript
getProducts(): Product[] {
  return [
    {
      id: 1,
      title: 'Keyboard',
      price: 100,
      categories: {
        1: 'Computing',
        2: 'Peripherals'
      }
    },
    {
      id: 2,
      title: 'Microphone',
      price: 35,
      categories: {
        3: 'Multimedia'
      }
    },
    {
      id: 3,
      title: 'Web camera',
      price: 79,
      categories: {
        1: 'Computing',
        3: 'Multimedia'
      }
    },
    {
      id: 4,
      title: 'Tablet',
      price: 500,
      categories: {
        4: 'Entertainment'
      }
    }
  ];
}
```

En las siguientes secciones, aprenderemos a utilizar el servicio en nuestra aplicación.

#### Inyección de servicios en el constructor
La forma más común de utilizar un servicio en un componente de Angular es a través de su constructor:

1. Abre el archivo `product-list.component.ts` y modifica la propiedad `products` para que se inicialice como un array vacío:

```typescript
products: Product[] = [];
```

2. Agrega la siguiente sentencia para importar la clase `ProductsService`:

```typescript
import { ProductsService } from '../products.service';
```

3. Crea una propiedad de componente llamada `productService` y asígnale el tipo `ProductsService`:

```typescript
private productService: ProductsService;
```

4. Instancia la propiedad usando la palabra clave `new` en el constructor del componente:

```typescript
constructor() {
  this.productService = new ProductsService();
}
```

5. Importa la interfaz `OnInit` del paquete npm `@angular/core`:

```typescript
import { Component, OnInit } from '@angular/core';
```

6. Agrega la interfaz `OnInit` a la lista de interfaces implementadas de la clase `ProductListComponent`:

```typescript
export class ProductListComponent implements OnInit
```

7. Agrega el siguiente método `ngOnInit` que llama al método `getProducts` de la propiedad `productService` y asigna el valor devuelto a la propiedad `products`:

```typescript
ngOnInit(): void {
  this.products = this.productService.getProducts();
}
```

8. Ejecuta la aplicación usando el comando `ng serve` para verificar que la lista de productos todavía se muestra correctamente en la página:

*(Figura 5.1: Lista de productos)*

¡Genial! Hemos conectado nuestro componente con el servicio con éxito y nuestra aplicación se ve muy bien. Bueno, este parece ser el caso, pero en realidad no lo es. Hay algunos problemas con la implementación real. Si la clase `ProductsService` debe cambiar, tal vez para acomodar otra dependencia, `ProductListComponent` también debería cambiar la implementación de su constructor. Por lo tanto, es evidente que el componente de lista de productos está estrechamente acoplado a la implementación de `ProductsService`. Nos impide alterar, sobrescribir o probar limpiamente el servicio si es necesario. También implica que se crea un nuevo objeto `ProductsService` cada vez que renderizamos un componente de lista de productos, lo que podría no ser deseado en escenarios específicos, como cuando esperamos usar un servicio singleton real.

Los sistemas de DI intentan resolver estos problemas proponiendo varios patrones, y el patrón de **inyección de constructor** (*constructor injection*) es el aplicado por Angular. Podríamos eliminar la propiedad de componente `productService` e inyectar el servicio directamente en el constructor. La clase `ProductListComponent` resultante sería la siguiente:

```typescript
export class ProductListComponent implements OnInit {
  products: Product[] = [];
  selectedProduct: Product | undefined;

  constructor(private productService: ProductsService) {}

  onAdded() {
    alert(`${this.selectedProduct?.title} added to the cart!`);
  }

  ngOnInit(): void {
    this.products = this.productService.getProducts();
  }
}
```

> Considera declarar los servicios inyectados como `readonly` para proporcionar un código más estable y evitar la reasignación del servicio. En el fragmento anterior, el constructor podría reescribirse como `constructor(private readonly productService: ProductsService) {}`.

El componente no necesita saber cómo instanciar el servicio. Por otro lado, espera que dicha dependencia esté disponible antes de instanciarse para que pueda inyectarse a través de su constructor. Este enfoque es más fácil de probar, ya que nos permite sobrescribirlo o simularlo (*mocking*).

Sin embargo, usar un constructor no es la única forma de inyectar servicios en una aplicación Angular, como aprenderemos en la siguiente sección.

#### La palabra clave / función inject
El framework Angular contiene una función integrada `inject` que podemos usar para inyectar servicios sin usar el constructor. Hay algunos casos en los que nos gustaría utilizar el método `inject`:

- El constructor contiene muchos servicios inyectados, lo que hace que nuestro código sea difícil de leer.
- Los constructores no se pueden usar cuando se trabaja con funciones puras en el enrutador de Angular o el cliente HTTP, como aprenderemos en los siguientes capítulos.

Veamos cómo podríamos refactorizar el componente de lista de productos para usar el método `inject`:

1. Abre el archivo `product-list.component.ts` e importa el método `inject` del paquete npm `@angular/core`:

```typescript
import { Component, OnInit, inject } from '@angular/core';
```

2. Declara la siguiente propiedad en la clase `ProductListComponent`:

```typescript
private productService = inject(ProductsService);
```

3. Elimina el constructor de la clase `ProductListComponent`.

La aplicación debería seguir funcionando como se esperaba si ejecutamos el comando `ng serve`. La lista de productos debería mostrarse como en la sección anterior.

Exploraremos casos de uso adicionales para el método `inject` en el *Capítulo 8: Comunicación con servicios de datos a través de HTTP*, y en el *Capítulo 9: Navegación a través de aplicaciones con Enrutamiento*.

> En comparación con el enfoque del constructor, el método `inject` proporciona tipos más precisos, lo que refuerza las aplicaciones Angular fuertemente tipadas.
>
> Angular CLI proporciona un *schematic* que podemos ejecutar para migrar al nuevo método `inject`. Puedes encontrar más detalles sobre cómo ejecutarlo en [https://angular.dev/reference/migrations/inject-function](https://angular.dev/reference/migrations/inject-function).

En este libro, utilizamos tanto el método `inject` como el enfoque del constructor, de acuerdo con el contexto de ejecución del código de la aplicación.

Como aprendimos, cuando creamos un nuevo servicio de Angular, Angular CLI registra este servicio con el inyector raíz de la aplicación por defecto. En la siguiente sección, aprenderemos sobre el funcionamiento interno del mecanismo de DI y cómo funciona el inyector raíz.

---

### Sección 5.4: Proveer dependencias en toda la aplicación

El framework Angular ofrece un mecanismo de DI para proporcionar dependencias en artefactos de Angular como componentes, directivas, pipes y servicios. La DI de Angular se basa en una jerarquía de inyectores donde en la parte superior se encuentra el inyector raíz (*root injector*) de una aplicación Angular.

Los inyectores en Angular pueden examinar las dependencias en el constructor de un artefacto de Angular y devolver una instancia del tipo representado por cada dependencia, para que podamos usarla de inmediato en la implementación de nuestra clase de Angular. El inyector mantiene una lista de todas las dependencias que necesita una aplicación Angular. Cuando un componente u otro artefacto quiere usar una dependencia, el inyector primero verifica si ya ha creado una instancia de esta dependencia. Si no es así, crea una nueva, la devuelve al componente y guarda una copia para su uso posterior. La próxima vez que se solicite la misma dependencia, devolverá la copia creada previamente. Pero, ¿cómo sabe el inyector qué dependencias necesita una aplicación Angular?

Cuando creamos un servicio de Angular, usamos la propiedad `providedIn` del decorador `@Injectable` para definir cómo se proporciona a la aplicación. Es decir, creamos un **proveedor** (*provider*) para este servicio. Un proveedor es una receta que contiene directrices sobre cómo crear un servicio específico. Durante el inicio de la aplicación, el framework es responsable de configurar el inyector con proveedores de servicios para que sepa cómo crear uno previa solicitud. Un servicio de Angular se configura con el inyector raíz de forma predeterminada cuando se crea con la CLI. El inyector raíz crea servicios **singleton** que están disponibles globalmente en toda la aplicación.

En el *Capítulo 1: Construyendo tu primera aplicación Angular*, aprendimos que el objeto de configuración de la aplicación definido en el archivo `app.config.ts` tiene una propiedad `providers` donde podemos registrar los servicios de la aplicación. Podríamos eliminar la propiedad `providedIn` del decorador `@Injectable` del archivo `products.service.ts` y agregarla directamente en ese array. Registrar un servicio de esta manera es lo mismo que configurar el servicio con `providedIn: 'root'`. La principal diferencia entre ellos es que la sintaxis `providedIn` es **tree-shakable**.

> **Tree shaking** es el proceso de encontrar dependencias que no se usan en una aplicación y eliminarlas del paquete final (*bundle*). En el contexto de Angular, el compilador de Angular puede detectar y eliminar los servicios de Angular que no se utilizan, lo que resulta en un paquete más pequeño.

Cuando proporcionas un servicio a través del objeto de configuración de la aplicación, el compilador de Angular no puede determinar si el servicio se utiliza en algún lugar de la aplicación. Por lo tanto, incluye el servicio en el paquete final a priori. Así, es preferible utilizar el decorador `@Injectable` sobre el array `providers` de la configuración de la aplicación.

> Siempre debes registrar los servicios singleton con el inyector raíz.

El inyector raíz no es el único inyector en una aplicación Angular. Los componentes también tienen sus inyectores. Los inyectores de Angular también son jerárquicos. Siempre que un componente de Angular define un token en su constructor, el inyector busca un tipo que coincida con ese token en el grupo de proveedores registrados. Si no se encuentra ninguna coincidencia, delega la búsqueda al proveedor del componente padre y continúa ascendiendo por el árbol de inyectores de componentes hasta llegar al inyector raíz. Si no se encuentra ninguna coincidencia, Angular lanza una excepción.

Exploremos la jerarquía de inyectores del componente de lista de productos usando Angular DevTools:

1. Ejecuta la aplicación usando el comando `ng serve` y previsualízala en `http://localhost:4200`.
2. Inicia **Angular DevTools** y selecciona la pestaña **Components**.
3. Selecciona el componente `app-product-list` del árbol de componentes:

*(Figura 5.2: Pestaña Components)*

En la imagen anterior, la sección *Injected Services* contiene los servicios inyectados en el componente.

4. Haz clic en la flecha hacia abajo junto a la etiqueta `ProductsService` y verás el siguiente diagrama:

*(Figura 5.3: Jerarquía del inyector de la lista de productos)*

> El diagrama de jerarquía del inyector en Angular DevTools tiene una orientación horizontal. Aquí lo mostramos verticalmente para facilitar su lectura.

El diagrama anterior muestra la jerarquía de inyectores del componente de lista de productos. Contiene dos tipos principales de jerarquía de inyectores comunes a una aplicación Angular: inyectores de entorno (*environment injectors*) e inyectores de elemento (*element injectors*).

- Los **inyectores de entorno** se configuran mediante la propiedad `providedIn` y el array `providers` en el objeto de configuración de la aplicación. En nuestro caso, vemos los inyectores `Root` y `Standalone[_AppComponent]` porque el servicio de productos se proporciona desde el inyector raíz mediante la propiedad `providedIn`.
- Angular crea un **inyector de elemento** para cada componente que se puede configurar desde el array `providers` del decorador `@Component`, como veremos en la siguiente sección. En nuestro caso, vemos los inyectores `AppComponent` y `ProductListComponent` porque estos componentes están directamente relacionados con la lista de productos.

Puedes seleccionar la pestaña *Injector Tree* de Angular DevTools para un análisis más detallado de la jerarquía de inyectores de la aplicación por tipo. También puedes obtener más información sobre los diferentes tipos de inyectores en [https://angular.dev/guide/di/hierarchical-dependency-injection#types-of-injector-hierarchies](https://angular.dev/guide/di/hierarchical-dependency-injection#types-of-injector-hierarchies).

Los componentes crean inyectores, por lo que están disponibles de inmediato para sus componentes secundarios. Aprenderemos sobre esto en detalle en la siguiente sección.

---

### Sección 5.5: Inyectar servicios en el árbol de componentes

Como aprendimos en la sección anterior, Angular utiliza un inyector de elemento para proporcionar servicios en los componentes a través de la propiedad `providers` del decorador `@Component`. Un servicio que se registra con el inyector de elemento puede servir para dos propósitos:

- Se puede compartir con sus componentes secundarios.
- Puede crear múltiples copias del servicio cada vez que se renderiza el componente que proporciona el servicio.

En las siguientes secciones, aprenderemos a aplicar cada enfoque.

#### Compartir dependencias a través de componentes
Un servicio proporcionado a través de un componente se puede compartir entre los componentes hijos del componente padre y está disponible de inmediato para su inyección en sus constructores. Los componentes secundarios reutilizan la misma instancia del servicio que el componente padre. Veamos un ejemplo para entender esto mejor:

1. Crea un nuevo componente de Angular llamado `favorites`:

```bash
ng generate component favorites
```

2. Abre el archivo `favorites.component.ts` y modifica las sentencias de importación en consecuencia:

```typescript
import { Component, OnInit } from '@angular/core';
import { Product } from '../product';
import { ProductsService } from '../products.service';
```

3. Modifica la clase `FavoritesComponent` para usar la clase `ProductsService` y obtener la lista de productos en una propiedad de componente `products`:

```typescript
export class FavoritesComponent implements OnInit {
  products: Product[] = [];

  constructor(private productService: ProductsService) {}

  ngOnInit(): void {
    this.products = this.productService.getProducts();
  }
}
```

4. Abre el archivo `favorites.component.html` y reemplaza su contenido con el siguiente código HTML:

```html
<ul class="pill-group">
  @for (product of products | slice:1:3; track product.id) {
    <li class="pill">
      ⭐ {{product.title}}
    </li>
  }
</ul>
```

En el fragmento anterior, iteramos sobre el array `products` y usamos el pipe `slice` para mostrar solo dos productos.

5. Modifica el archivo `favorites.component.ts` para que importe la clase `CommonModule` necesaria para el pipe `slice`:

```typescript
import { CommonModule } from '@angular/common';
import { Component, OnInit } from '@angular/core';
import { Product } from '../product';
import { ProductsService } from '../products.service';

@Component({
  selector: 'app-favorites',
  imports: [CommonModule],
  templateUrl: './favorites.component.html',
  styleUrl: './favorites.component.css'
})
```

6. Abre el archivo `favorites.component.css` para agregar algunos estilos CSS a nuestros productos favoritos:

```css
.pill-group {
  display: flex;
  flex-direction: column;
  align-items: start;
  flex-wrap: wrap;
  gap: 1.25rem;
}

.pill {
  display: flex;
  align-items: center;
  --pill-accent: var(--hot-red);
  background: color-mix(in srgb, var(--hot-red) 5%, transparent);
  color: var(--pill-accent);
  padding-inline: 0.75rem;
  padding-block: 0.375rem;
  border-radius: 2.75rem;
  border: 0;
  transition: background 0.3s ease;
  font-family: var(--inter-font);
  font-size: 0.875rem;
  font-style: normal;
  font-weight: 500;
  line-height: 1.4rem;
  letter-spacing: -0.00875rem;
  text-decoration: none;
}
```

7. Abre el archivo `product-list.component.ts`, importa la clase `FavoritesComponent` y agrega la clase `ProductsService` al array `providers` del decorador `@Component`:

```typescript
import { Component, OnInit } from '@angular/core';
import { Product } from '../product';
import { ProductDetailComponent } from '../product-detail/product-detail.component';
import { SortPipe } from '../sort.pipe';
import { ProductsService } from '../products.service';
import { FavoritesComponent } from '../favorites/favorites.component';

@Component({
  selector: 'app-product-list',
  imports: [ProductDetailComponent, SortPipe, FavoritesComponent],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css',
  providers: [ProductsService]
})
```

8. Abre el archivo `products.service.ts` y elimina la propiedad `providedIn` del decorador `@Injectable`, ya que el inyector de elemento del componente de lista de productos la proporcionará.
9. Finalmente, abre el archivo `product-list.component.html` y agrega el siguiente fragmento HTML para mostrar el contenido del componente de favoritos:

```html
<h1>Favorites</h1>
<app-favorites></app-favorites>
```

Al ejecutar la aplicación con `ng serve`, deberías ver la siguiente salida:

*(Figura 5.4: Lista de productos con favoritos)*

Expliquemos lo que hicimos en el ejemplo anterior con más detalle. Inyectamos `ProductsService` en `FavoritesComponent` pero no lo proporcionamos a través de su propio inyector. Entonces, ¿cómo supo el componente cómo crear una instancia de la clase `ProductsService` y usarla? No lo sabía. Cuando agregamos el componente de favoritos a la plantilla `ProductListComponent`, lo convertimos en un hijo directo de este componente, dándole acceso a todos sus servicios provistos. En pocas palabras, `FavoritesComponent` puede usar `ProductsService` de inmediato porque ya se proporciona a través del inyector de elemento de su componente padre, `ProductListComponent`.

Por lo tanto, incluso si `ProductsService` se registró inicialmente con el inyector raíz del entorno, también podríamos registrarlo con el inyector de elemento de `ProductListComponent`. En la siguiente sección, investigaremos cómo es posible lograr dicho comportamiento.

#### Inyectores raíz y de componente
Ya hemos aprendido que cuando creamos un servicio de Angular usando la CLI de Angular, el servicio se proporciona en el inyector raíz de la aplicación por defecto. ¿En qué se diferencia esto de proporcionar un servicio a través del inyector de elemento de un componente?

Los servicios proporcionados con el inyector raíz de la aplicación están disponibles en toda la aplicación. Cuando un componente quiere usar dicho servicio, solo necesita inyectarlo, nada más. Ahora bien, si el componente proporciona el mismo servicio a través de su inyector, obtendrá una instancia del servicio completamente diferente a la del inyector raíz. Esta técnica se llama **limitación del alcance del servicio** (*service scope limiting*) porque limitamos el alcance del servicio a una parte específica del árbol de componentes:

*(Figura 5.5: Limitación del alcance del servicio)*

El diagrama anterior muestra que `ProductsService` se puede proporcionar a través de dos inyectores: el inyector raíz de la aplicación y el inyector de elemento del componente de lista de productos. La clase `FavoritesComponent` inyecta `ProductsService` para usarlo. Como ya hemos visto, `FavoritesComponent` es un componente secundario de `ProductListComponent`.

De acuerdo con la jerarquía del inyector, primero le preguntará al inyector de su componente padre, `ProductListComponent`, si proporciona el servicio. La clase `ProductListComponent` efectivamente proporciona `ProductsService`, por lo que crea una nueva instancia del servicio y la devuelve a `FavoritesComponent`.

Ahora, considera que otro componente de nuestra aplicación, `CmpA`, quiere usar `ProductsService`. Dado que no es un componente secundario de `ProductListComponent` y no contiene ningún componente principal que proporcione el servicio requerido, finalmente llegará al inyector raíz de la aplicación. El inyector raíz que proporciona `ProductsService` verifica si ya ha creado una instancia para ese servicio. Si no es así, crea uno nuevo, llamado `productService`, y lo devuelve a `CmpA`. También mantiene `productService` en el grupo local de servicios para su uso posterior.

Supongamos que otro componente, `CmpB`, quiere usar `ProductsService` y le pide al inyector raíz de la aplicación. El inyector raíz sabe que ya creó la instancia de `productService` cuando `CmpA` la solicitó y la devuelve inmediatamente al componente `CmpB`.

#### Aislamiento de componentes con múltiples instancias
Cuando proporcionamos un servicio a través del inyector de elemento y lo inyectamos en el constructor del componente, se crea una nueva instancia cada vez que el componente se renderiza en la página. Puede ser útil en casos como cuando queremos tener un servicio de caché local para cada componente. Exploraremos este escenario transformando nuestra aplicación Angular para que la lista de productos muestre una vista rápida de cada producto mediante un servicio de Angular:

1. Ejecuta el siguiente comando para crear un nuevo componente de Angular para la vista del producto:

```bash
ng generate component product-view
```

2. Abre el archivo `product-view.component.ts` y declara una propiedad de entrada llamada `id` para que podamos pasar un identificador único del producto que queremos mostrar:

```typescript
import { Component, input } from '@angular/core';

@Component({
  selector: 'app-product-view',
  imports: [],
  templateUrl: './product-view.component.html',
  styleUrl: './product-view.component.css'
})
export class ProductViewComponent {
  id = input<number>();
}
```

3. Ejecuta el siguiente comando de Angular CLI dentro de la carpeta `product-view` para crear un servicio de Angular que estará dedicado al componente de vista de producto:

```bash
ng generate service product-view
```

4. Abre el archivo `product-view.service.ts` y elimina la propiedad `providedIn` del decorador `@Injectable` porque la proporcionaremos más adelante en el componente de vista de producto.
5. Inyecta `ProductsService` en el constructor de la clase `ProductViewService`:

```typescript
import { Injectable } from '@angular/core';
import { ProductsService } from '../products.service';

@Injectable()
export class ProductViewService {
  constructor(private productService: ProductsService) { }
}
```

> La técnica anterior se denomina **servicio dentro de un servicio** (*service-in-a-service*) porque inyectamos un servicio de Angular en otro.

6. Crea un método llamado `getProduct` que tome una propiedad `id` como parámetro. El método llamará al método `getProducts` de la clase `ProductsService` y buscará en la lista de productos en función del `id`. Si encuentra el producto, lo mantendrá en una variable local llamada `product`:

```typescript
import { Injectable } from '@angular/core';
import { ProductsService } from '../products.service';
import { Product } from '../product';

@Injectable()
export class ProductViewService {
  private product: Product | undefined;

  constructor(private productService: ProductsService) { }

  getProduct(id: number): Product | undefined {
    const products = this.productService.getProducts();
    if (!this.product) {
      this.product = products.find(product => product.id === id);
    }
    return this.product;
  }
}
```

Ya hemos creado los artefactos esenciales de Angular para trabajar con el componente de vista de producto. Todo lo que tenemos que hacer ahora es conectarlos y vincularlos a la lista de productos:

7. Inyecta `ProductViewService` en el constructor de `ProductViewComponent` e implementa el método `ngOnInit`:

```typescript
import { Component, input, OnInit } from '@angular/core';
import { ProductViewService } from './product-view.service';

@Component({
  selector: 'app-product-view',
  imports: [],
  templateUrl: './product-view.component.html',
  styleUrl: './product-view.component.css',
  providers: [ProductViewService]
})
export class ProductViewComponent implements OnInit {
  id = input<number>();

  constructor(private productViewService: ProductViewService) {}

  ngOnInit(): void {
  }
}
```

8. Crea una propiedad de componente para almacenar el producto que obtendremos de la clase `ProductViewService`:

```typescript
import { Component, input, OnInit } from '@angular/core';
import { ProductViewService } from './product-view.service';
import { Product } from '../product';

@Component({
  selector: 'app-product-view',
  imports: [],
  templateUrl: './product-view.component.html',
  styleUrl: './product-view.component.css',
  providers: [ProductViewService]
})
export class ProductViewComponent implements OnInit {
  id = input<number>();
  product: Product | undefined;

  constructor(private productViewService: ProductViewService) {}

  ngOnInit(): void {
  }
}
```

9. Modifica el método `ngOnInit` para que llame al método `getProduct` de la clase `ProductViewService` de la siguiente manera:

```typescript
ngOnInit(): void {
  this.product = this.productViewService.getProduct(this.id()!);
}
```

En el fragmento anterior, pasamos la propiedad del componente `id` al método `getProduct` como parámetro y asignamos el valor devuelto a la propiedad `product`.

10. Abre el archivo `product-view.component.html` y reemplaza su contenido con la siguiente plantilla HTML:

```html
@switch (product?.title) {
  @case ('Keyboard') {
  }
  @case ('Microphone') {
  }
  @default {
  }
}
{{product?.title}}
```

11. Abre el archivo `product-list.component.ts` e importa la clase `ProductViewComponent`:

```typescript
import { Component, OnInit } from '@angular/core';
import { Product } from '../product';
import { ProductDetailComponent } from '../product-detail/product-detail.component';
import { SortPipe } from '../sort.pipe';
import { ProductsService } from '../products.service';
import { ProductViewComponent } from '../product-view/product-view.component';

@Component({
  selector: 'app-product-list',
  imports: [ProductDetailComponent, SortPipe, ProductViewComponent],
  templateUrl: './product-list.component.html',
  styleUrl: './product-list.component.css'
})
```

12. Finalmente, abre el archivo `product-list.component.html` y modifica el bloque `@for` para usar el componente de vista de producto:

```html
<ul class="pill-group">
  @for (product of products | sort; track product.id) {
    <li class="pill" (click)="selectedProduct = product">
      <app-product-view [id]="product.id"></app-product-view>
    </li>
  } @empty {
    <p>No products found!</p>
  }
</ul>
```

Si ejecutamos nuestra aplicación con el comando `ng serve`, veremos que la lista de productos aún se muestra correctamente.

Cada componente de vista de producto renderizado crea una instancia dedicada y aislada (*sandboxed*) de `ProductViewService` para su propósito. Ningún otro componente puede compartir la instancia ni ser modificado, excepto por el componente que lo proporciona.

Intenta proporcionar `ProductViewService` en `ProductListComponent` en lugar de en `ProductViewComponent`; verás que solo un producto se renderiza varias veces:

*(Figura 5.6: Lista de productos)*

En este caso, solo se comparte una instancia de servicio entre los componentes secundarios. ¿Por qué ocurre esto? Recuerda la lógica de negocio del método `getProduct` de la clase `ProductViewService`:

```typescript
getProduct(id: number): Product | undefined {
  const products = this.productService.getProducts();
  if (!this.product) {
    this.product = products.find(product => product.id === id);
  }
  return this.product;
}
```

En el método anterior, la propiedad `product` se establece inicialmente cuando proporcionamos el servicio dentro de `ProductListComponent`. Dado que solo tenemos una instancia del servicio, el valor de la propiedad seguirá siendo el mismo mientras renderizamos el componente de vista de producto varias veces.

Hemos aprendido cómo se inyectan las dependencias en la jerarquía de componentes y cómo se realiza la búsqueda de proveedores haciendo ascender la solicitud por el árbol de componentes. Sin embargo, ¿qué pasa si queremos restringir dichas acciones de inyección o búsqueda? Veremos cómo hacerlo en la siguiente sección.

#### Restricción de búsqueda de proveedores
Solo podemos restringir la búsqueda de dependencias al siguiente nivel superior. Para hacerlo, debemos aplicar el decorador `@Host` a aquellos parámetros de dependencia cuya búsqueda de proveedores queremos restringir:

```typescript
import { CommonModule } from '@angular/common';
import { Component, Host, OnInit } from '@angular/core';
import { Product } from '../product';
import { ProductsService } from '../products.service';

@Component({
  selector: 'app-favorites',
  imports: [CommonModule],
  templateUrl: './favorites.component.html',
  styleUrl: './favorites.component.css'
})
export class FavoritesComponent implements OnInit {
  products: Product[] = [];

  constructor(@Host() private productService: ProductsService) {}

  ngOnInit(): void {
    this.products = this.productService.getProducts();
  }
}
```

En el ejemplo anterior, el inyector de elemento de `FavoritesComponent` buscará la clase `ProductsService` en sus propios proveedores. Si no proporciona el servicio, no ascenderá en la jerarquía del inyector; en su lugar, se detendrá y lanzará una excepción en la consola del navegador:

```text
Error: NG0201: No provider for _ProductsService found in NodeInjector.
```

Podemos configurar el inyector para que no arroje un error si decoramos el servicio con el decorador `@Optional`:

```typescript
import { CommonModule } from '@angular/common';
import { Component, Host, OnInit, Optional } from '@angular/core';
import { Product } from '../product';
import { ProductsService } from '../products.service';

@Component({
  selector: 'app-favorites',
  imports: [CommonModule],
  templateUrl: './favorites.component.html',
  styleUrl: './favorites.component.css'
})
export class FavoritesComponent implements OnInit {
  products: Product[] = [];

  constructor(@Optional() @Host() private productService: ProductsService) {}

  ngOnInit(): void {
    this.products = this.productService.getProducts();
  }
}
```

Sin embargo, el uso del decorador `@Optional` no resuelve el problema real. El fragmento anterior seguirá arrojando un error, diferente al anterior, porque todavía usamos el decorador `@Host` que limita la búsqueda de la clase `ProductsService` en la jerarquía del inyector. Necesitamos refactorizar el evento del ciclo de vida `ngOnInit` para que se encargue de no encontrar la instancia del servicio.

Los decoradores `@Host` y `@Optional` definen el nivel en el que el inyector busca dependencias. Hay otros dos decoradores llamados `@Self` y `@SkipSelf`. Al usar el decorador `@Self`, el inyector busca dependencias solo en el inyector del componente actual. Por el contrario, el decorador `@SkipSelf` le indica al inyector que omita el inyector local y busque más arriba en la jerarquía de inyectores.

> Los decoradores `@Host` y `@Self` funcionan de manera similar. Para obtener más información sobre cuándo usar cada uno, consulta [https://angular.dev/guide/di/hierarchical-dependency-injection#self](https://angular.dev/guide/di/hierarchical-dependency-injection#self) y [https://angular.dev/guide/di/hierarchical-dependency-injection#host](https://angular.dev/guide/di/hierarchical-dependency-injection#host).

Hasta ahora, hemos aprendido cómo el framework de DI de Angular usa clases como tokens de dependencia para determinar el tipo requerido y devolverlo desde cualquier proveedor disponible en la jerarquía del inyector. Sin embargo, hay casos en los que podríamos necesitar sobrescribir la instancia de una clase o proporcionar tipos que no son clases reales, como los tipos primitivos.

---

### Sección 5.6: Sobrescribir proveedores en la jerarquía del inyector

Ya aprendimos cómo usar el array `providers` del decorador `@Component` en la sección *Compartir dependencias a través de componentes*:

```typescript
providers: [ProductsService]
```

La sintaxis anterior se llama **sintaxis de proveedor de clase** (*class provider syntax*) y es una abreviatura de la sintaxis literal de objeto `provide` que se muestra a continuación:

```typescript
providers: [
  { provide: ProductsService, useClass: ProductsService }
]
```

La sintaxis anterior utiliza un objeto con las siguientes propiedades:

- **`provide`:** Este es el token utilizado para configurar el inyector. Es la clase real que los consumidores de la dependencia inyectan en sus constructores.
- **`useClass`:** Esta es la implementación real que el inyector proporcionará a los consumidores. El nombre de la propiedad diferirá según el tipo de implementación proporcionado. El tipo puede ser una clase, un valor o una función de fábrica. En este caso, usamos `useClass` porque estamos proporcionando una clase.

Veamos algunos ejemplos para obtener una visión general de cómo usar la sintaxis literal del objeto `provide`.

#### Sobrescribir la implementación del servicio
Ya hemos aprendido que un componente puede compartir sus dependencias con sus componentes secundarios. Considera el `FavoritesComponent`, donde usamos el pipe `slice` para mostrar una lista de productos favoritos en su plantilla. ¿Qué pasa si necesita obtener datos a través de una versión recortada de `ProductsService` y no directamente de la instancia de servicio de `ProductListComponent`? Podríamos crear un nuevo servicio extendiendo la clase `ProductsService` y filtrando datos usando el método nativo `Array.slice`. Creemos el nuevo servicio y aprendamos a usarlo:

1. Ejecuta el siguiente comando para generar el servicio:

```bash
ng generate service favorites
```

2. Abre el archivo `favorites.service.ts` y agrega las siguientes sentencias de importación:

```typescript
import { Product } from './product';
import { ProductsService } from './products.service';
```

3. Usa la palabra clave `extends` en la definición de la clase para indicar que `ProductsService` es la clase base de `FavoritesService`:

```typescript
export class FavoritesService extends ProductsService {
  constructor() { }
}
```

4. Modifica el constructor para llamar al método `super` y ejecutar cualquier lógica de negocio dentro del constructor de la clase base:

```typescript
constructor() {
  super();
}
```

5. Crea el siguiente método de servicio que usa el método `slice` para devolver solo los dos primeros productos de la lista:

```typescript
override getProducts(): Product[] {
  return super.getProducts().slice(1, 3);
}
```

El método anterior está marcado con la palabra clave `override` para indicar que la implementación del método reemplaza el método correspondiente de la clase base.

6. Abre el archivo `favorites.component.ts` y agrega la siguiente sentencia de importación:

```typescript
import { FavoritesService } from '../favorites.service';
```

7. Agrega la clase `FavoritesService` en el array `providers` del decorador `@Component` de la siguiente manera:

```typescript
@Component({
  selector: 'app-favorites',
  imports: [],
  templateUrl: './favorites.component.html',
  styleUrl: './favorites.component.css',
  providers: [
    { provide: ProductsService, useClass: FavoritesService }
  ]
})
```

En el fragmento anterior, eliminamos `CommonModule` del array `imports` porque ya no necesitamos el pipe `slice`.

8. Finalmente, abre el archivo `favorites.component.html` y elimina el pipe `slice` del bloque `@for`.

Si ejecutamos la aplicación con el comando `ng serve`, veremos que la sección de Favoritos todavía se muestra correctamente:

*(Figura 5.7: Lista de productos favoritos)*

> La salida anterior asume que ya has importado y agregado el componente de favoritos en el componente de lista de productos.

La propiedad `useClass` esencialmente sobrescribió la implementación inicial de la clase `ProductsService` para el componente de favoritos. Alternativamente, podemos ir un paso más allá y usar una función para devolver una instancia de objeto específica que necesitemos, como aprenderemos en la siguiente sección.

#### Provisión condicional de servicios
En el ejemplo de la sección anterior, usamos la sintaxis `useClass` para reemplazar la implementación de la clase `ProductsService` inyectada. Alternativamente, podríamos crear una función de fábrica (*factory function*) que decida si devolverá una instancia de la clase `FavoritesService` o `ProductsService` según una condición. La función residiría en un archivo TypeScript simple llamado `favorites.ts`:

```typescript
import { FavoritesService } from './favorites.service';
import { ProductsService } from './products.service';

export function favoritesFactory(isFavorite: boolean) {
  return () => {
    if (isFavorite) {
      return new FavoritesService();
    }
    return new ProductsService();
  };
}
```

Luego podríamos modificar el array `providers` en el archivo `favorites.component.ts` de la siguiente manera:

```typescript
import { CommonModule } from '@angular/common';
import { Component, OnInit } from '@angular/core';
import { Product } from '../product';
import { ProductsService } from '../products.service';
import { favoritesFactory } from '../favorites';

@Component({
  selector: 'app-favorites',
  imports: [CommonModule],
  templateUrl: './favorites.component.html',
  styleUrl: './favorites.component.css',
  providers: [
    { provide: ProductsService, useFactory: favoritesFactory(true) }
  ]
})
```

Vale la pena señalar que si uno de los servicios también inyectara otras dependencias, la sintaxis anterior no sería suficiente. Por ejemplo, si la clase `FavoritesService` dependiera de la clase `ProductViewService`, la agregaríamos a la propiedad `deps` de la sintaxis literal de objeto `provide`:

```typescript
providers: [
  {
    provide: ProductsService,
    useFactory: favoritesFactory(true),
    deps: [ProductViewService]
  }
]
```

Luego podríamos usarlo en la función de fábrica del archivo `favorites.ts` de la siguiente manera:

```typescript
export function favoritesFactory(isFavorite: boolean) {
  return (productViewService: ProductViewService) => {
    if (isFavorite) {
      return new FavoritesService();
    }
    return new ProductsService();
  };
}
```

Ya hemos aprendido cómo proporcionar una implementación de clase alternativa para un servicio de Angular. ¿Qué pasa si la dependencia que queremos proporcionar no es una clase sino una cadena de texto o un objeto? Podemos usar la sintaxis `useValue` para realizar esta tarea.

#### Transformación de objetos en servicios de Angular
Es común mantener la configuración de la aplicación en un objeto constante en aplicaciones del mundo real. ¿Cómo podríamos usar la sintaxis `useValue` para proporcionar estas configuraciones en nuestros componentes? Aprenderemos más creando configuraciones para nuestra aplicación, como el número de versión y el título:

1. Crea un archivo `app.settings.ts` en la carpeta `src/app` del espacio de trabajo de Angular CLI y agrega el siguiente contenido:

```typescript
export interface AppSettings {
  title: string;
  version: string;
}

export const appSettings: AppSettings = {
  title: 'My e-shop',
  version: '1.0'
};
```

Podrías pensar que podríamos proporcionar estas configuraciones como `{ provide: AppSettings, useValue: appSettings }`, pero esto generaría un error porque `AppSettings` es una interfaz, no una clase. Las interfaces son azúcar sintáctico en TypeScript que se descartan durante la compilación. En su lugar, debemos proporcionar un objeto `InjectionToken`.

2. Agrega la siguiente sentencia para importar la clase `InjectionToken` del paquete npm `@angular/core`:

```typescript
import { InjectionToken } from '@angular/core';
```

3. Declara la siguiente variable constante que utiliza el tipo `InjectionToken`:

```typescript
export const APP_SETTINGS = new InjectionToken<AppSettings>('app.settings');
```

4. Abre el archivo `app.component.ts` y modifica las sentencias de importación de la siguiente manera:

```typescript
import { Component, inject } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { ProductListComponent } from './product-list/product-list.component';
import { CopyrightDirective } from './copyright.directive';
import { APP_SETTINGS, appSettings } from './app.settings';
```

5. Agrega el token de configuración de la aplicación en el array `providers` del decorador `@Component`:

```typescript
@Component({
  selector: 'app-root',
  imports: [
    RouterOutlet,
    ProductListComponent,
    CopyrightDirective
  ],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css',
  providers: [
    { provide: APP_SETTINGS, useValue: appSettings }
  ]
})
```

> La sintaxis `useValue` es particularmente útil al probar aplicaciones Angular. La utilizaremos ampliamente cuando aprendamos sobre pruebas unitarias en el *Capítulo 13: Pruebas unitarias en aplicaciones Angular*.

6. Agrega la siguiente propiedad en la clase `AppComponent`:

```typescript
settings = inject(APP_SETTINGS);
```

7. Abre el archivo `app.component.html` y modifica la etiqueta `<footer>` para incluir la versión de la aplicación:

```html
<footer appCopyright> - v{{ settings.version }}</footer>
```

8. Ejecuta la aplicación mediante el comando `ng serve` y observa el pie de página en la salida de la aplicación:

```text
Copyright ©2024 All Rights Reserved - v1.0
```

Ten en cuenta que, aunque la interfaz `AppSettings` no desempeñó un papel directo en el proceso de inyección, la necesitamos para proporcionar tipado en el objeto de configuración.

La DI de Angular es un mecanismo potente y robusto que nos permite gestionar las dependencias de nuestras aplicaciones de manera eficiente. El equipo de Angular se ha esforzado mucho para que sea fácil de usar y ha eliminado la carga del lado del desarrollador. Como hemos visto, las combinaciones son abundantes y la forma en que las usemos dependerá del caso de uso.

---

### Sección 5.7: Resumen

La implementación de la DI de Angular es la columna vertebral del framework Angular. Los componentes de Angular delegan tareas complejas a los servicios de Angular, basándose en la DI de Angular.

En este capítulo aprendimos qué es la DI de Angular y cómo aprovecharla creando servicios de Angular. Exploramos diferentes formas de inyectar servicios de Angular en componentes. Vimos cómo compartir servicios entre componentes, aislar servicios en componentes y definir el acceso a dependencias a través del árbol de componentes.

Finalmente, investigamos cómo sobrescribir servicios de Angular reemplazando la implementación del servicio o transformando objetos existentes en servicios.

En el próximo capítulo, aprenderemos qué es la programación reactiva y cómo podemos usar observables en el contexto de las aplicaciones Angular.
