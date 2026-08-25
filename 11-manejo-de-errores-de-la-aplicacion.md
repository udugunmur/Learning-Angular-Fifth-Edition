# Parte 4: Calidad, Rendimiento y Producción

## Capítulo 11: Manejo de errores de la aplicación

Los errores de aplicación son una parte integral del ciclo de vida de una aplicación web. Pueden ocurrir durante el tiempo de ejecución (*runtime*) o mientras se desarrolla la aplicación. Las causas posibles de un error en tiempo de ejecución son una solicitud HTTP fallida o un formulario HTML incompleto. Una aplicación web debe manejar los errores en tiempo de ejecución y mitigar los efectos no deseados para garantizar una experiencia de usuario fluida.

Los errores de desarrollo suelen ocurrir cuando no utilizamos correctamente un lenguaje de programación o framework según su semántica. En este caso, los errores pueden eludir al compilador y manifestarse en la aplicación mientras se ejecuta. Los errores de desarrollo se pueden mitigar siguiendo las mejores prácticas y las técnicas de codificación recomendadas.

En este capítulo, aprenderemos cómo manejar diferentes tipos de errores en una aplicación Angular y a comprender los errores del propio framework. Exploraremos los siguientes conceptos con más detalle:

- Manejo de errores en tiempo de ejecución
- Desmitificando los errores del framework

---

### Sección 11.1: Requisitos técnicos

El código de ejemplo descrito en este capítulo se puede encontrar en la carpeta `ch11` del siguiente repositorio de GitHub:  
[https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch11](https://github.com/PacktPublishing/Learning-Angular-Fifth-Edition/tree/main/ch11)

- **Node.js:** [https://nodejs.org](https://nodejs.org)
- **Git:** [https://git-scm.com](https://git-scm.com)
- **VSCode:** [https://code.visualstudio.com](https://code.visualstudio.com)

---

### Sección 11.2: Manejo de errores en tiempo de ejecución

Los errores en tiempo de ejecución más comunes en una aplicación Angular provienen de la interacción con una API HTTP. Ingresar credenciales de inicio de sesión incorrectas o enviar datos en un formato incorrecto puede resultar en un error HTTP. Una aplicación Angular puede manejar errores HTTP de las siguientes maneras:

- Explícitamente durante la ejecución de una solicitud HTTP particular.
- Globalmente en el manejador de errores global de la aplicación.
- Centralmente usando un interceptor HTTP.

En la siguiente sección, exploraremos cómo manejar un error HTTP en una solicitud HTTP específica.

#### Captura de errores de solicitudes HTTP
El manejo de errores en solicitudes HTTP generalmente requiere inspeccionar manualmente la información devuelta en el objeto de respuesta de error. RxJS proporciona el operador `catchError` para simplificar esto. Puede capturar posibles errores al iniciar una solicitud HTTP con el operador `pipe`.

> Necesitarás el código fuente de la aplicación Angular que creamos en el *Capítulo 10: Recopilación de datos del usuario con Formularios* para seguir el resto del capítulo.

Veamos cómo podríamos usar el operador `catchError` para capturar errores HTTP al obtener la lista de productos en nuestra aplicación:

1. Abre el archivo `products.service.ts` e importa los operadores `catchError` y `throwError` del paquete npm `rxjs`:

```typescript
import { Observable, map, of, tap, catchError, throwError } from 'rxjs';
```

2. Importa la interfaz `HttpErrorResponse` del espacio de nombres `@angular/common/http`:

```typescript
import { HttpClient, HttpParams, HttpErrorResponse } from '@angular/common/http';
```

3. Modifica el método `getProducts` en consecuencia:

```typescript
getProducts(limit?: number): Observable<Product[]> {
  if (this.products.length === 0) {
    const options = new HttpParams().set('limit', limit || 10);
    return this.http.get<Product[]>(this.productsUrl, { params: options }).pipe(
      map(products => {
        this.products = products;
        return products;
      }),
      catchError((error: HttpErrorResponse) => {
        console.error(error);
        return throwError(() => error);
      })
    );
  }
  return of(this.products);
}
```

La firma del operador `catchError` contiene el objeto `HttpErrorResponse` real que se devuelve desde el servidor. Después de capturar el error, usamos el operador `throwError`, que vuelve a lanzar el error como un observable.

> Alternativamente, podríamos haber usado la palabra clave `throw` de los métodos estándar de la API web para lanzar el error. Sin embargo, el método `throwError` es, la mayoría de las veces, excesivo (*overkill*). Úsalo en consecuencia.

De esta manera, nos aseguramos de que la ejecución de la aplicación continúe y se complete sin causar una posible fuga de memoria (*memory leak*).

En un escenario del mundo real, probablemente crearíamos un método auxiliar para registrar el error en un sistema de seguimiento más sólido y devolver algo significativo según la causa del error:

4. En el mismo archivo, `products.service.ts`, importa la enumeración `HttpStatusCode` del espacio de nombres `@angular/common/http`:

```typescript
import { HttpClient, HttpParams, HttpErrorResponse, HttpStatusCode } from '@angular/common/http';
```

`HttpStatusCode` es una enumeración que contiene una lista de todos los códigos de estado de respuesta HTTP.

5. Crea el siguiente método en la clase `ProductsService`:

```typescript
private handleError(error: HttpErrorResponse) {
  let message = '';
  switch(error.status) {
    case HttpStatusCode.InternalServerError:
      message = 'Server error';
      break;
    case HttpStatusCode.BadRequest:
      message = 'Request error';
      break;
    default:
      message = 'Unknown error';
  }
  console.error(message, error.error);
  return throwError(() => error);
}
```

El método anterior registra un mensaje diferente en la consola del navegador según el estado del error. Utiliza una sentencia `switch` para diferenciar entre errores internos del servidor (*internal server errors*) y solicitudes incorrectas (*bad requests*). Para cualquier otro error, recurre a la sentencia `default`, que registra un mensaje genérico en la consola.

6. Refactoriza el método `getProducts` para usar el método `handleError` para capturar errores:

```typescript
getProducts(limit?: number): Observable<Product[]> {
  if (this.products.length === 0) {
    const options = new HttpParams().set('limit', limit || 10);
    return this.http.get<Product[]>(this.productsUrl, { params: options }).pipe(
      map(products => {
        this.products = products;
        return products;
      }),
      catchError(this.handleError)
    );
  }
  return of(this.products);
}
```

El método `handleError` gestiona actualmente los errores HTTP que se originan únicamente en la respuesta HTTP. Sin embargo, pueden ocurrir otros errores en una aplicación Angular desde el lado del cliente, como una solicitud que no llegó al servidor debido a un error de red o una excepción lanzada en un operador de RxJS. Para manejar cualquiera de los errores anteriores, debemos agregar una nueva sentencia `case` en el método `handleError`:

```typescript
private handleError(error: HttpErrorResponse) {
  let message = '';
  switch(error.status) {
    case 0:
      message = 'Client error';
      break;
    case HttpStatusCode.InternalServerError:
      message = 'Server error';
      break;
    case HttpStatusCode.BadRequest:
      message = 'Request error';
      break;
    default:
      message = 'Unknown error';
  }
  console.error(message, error.error);
  return throwError(() => error);
}
```

En el fragmento anterior, un error con un estado de `0` indica que es un error que ocurrió en el lado del cliente de la aplicación.

El manejo de errores en solicitudes HTTP se puede combinar con un mecanismo que reintente una llamada HTTP determinada un número específico de veces antes de manejar el error. Hay un operador de RxJS para casi todo, incluso uno para reintentar solicitudes HTTP. Acepta la cantidad de reintentos en los que se debe ejecutar la solicitud en particular hasta que se complete con éxito:

```typescript
getProducts(limit?: number): Observable<Product[]> {
  if (this.products.length === 0) {
    const options = new HttpParams().set('limit', limit || 10);
    return this.http.get<Product[]>(this.productsUrl, { params: options }).pipe(
      map(products => {
        this.products = products;
        return products;
      }),
      retry(2),
      catchError(this.handleError)
    );
  }
  return of(this.products);
}
```

Aprendimos que usamos el operador de RxJS `catchError` para capturar errores. La forma en que lo manejamos depende del escenario. En nuestro caso, creamos un método `handleError` para todas las llamadas HTTP en un servicio. En un escenario del mundo real, seguiríamos el mismo enfoque de manejo de errores en otros servicios de Angular de una aplicación. Crear un método para cada servicio no sería conveniente y no escala bien.

Alternativamente, podríamos utilizar el manejador de errores global que proporciona Angular para manejar errores en un lugar central. Aprenderemos cómo crear un manejador de errores global en la siguiente sección.

#### Creación de un manejador de errores global
El framework Angular proporciona la clase `ErrorHandler` para manejar errores globalmente en una aplicación Angular. La implementación predeterminada de la clase `ErrorHandler` imprime mensajes de error en la ventana de la consola del navegador.

Para crear un manejador de errores personalizado para nuestra aplicación, necesitamos crear una subclase de la clase `ErrorHandler` y proporcionar nuestra implementación personalizada para el registro de errores:

1. Crea un archivo llamado `app-error-handler.ts` en la carpeta `src/app` del espacio de trabajo de Angular CLI.
2. Abre el archivo y agrega las siguientes sentencias de importación:

```typescript
import { HttpErrorResponse, HttpStatusCode } from '@angular/common/http';
import { ErrorHandler, Injectable } from '@angular/core';
```

3. Crea una clase TypeScript que implemente la interfaz `ErrorHandler`:

```typescript
@Injectable()
export class AppErrorHandler implements ErrorHandler {}
```

> La clase `AppErrorHandler` debe estar decorada con el decorador `@Injectable()` porque la proporcionaremos más adelante en el archivo de configuración de la aplicación.

4. Implementa el método `handleError` de la interfaz `ErrorHandler` de la siguiente manera:

```typescript
handleError(error: any): void {
  const err = error.rejection || error;
  let message = '';
  if (err instanceof HttpErrorResponse) {
    switch(err.status) {
      case 0:
        message = 'Client error';
        break;
      case HttpStatusCode.InternalServerError:
        message = 'Server error';
        break;
      case HttpStatusCode.BadRequest:
        message = 'Request error';
        break;
      default:
        message = 'Unknown error';
    }
  } else {
    message = 'Application error';
  }
  console.error(message, err);
}
```

En el método anterior, verificamos si el objeto de error contiene una propiedad `rejection`. Los errores que se originan en la librería Zone.js, que es responsable de la detección de cambios en Angular, encapsulan el error real dentro de esa propiedad.

Después de extraer el error en la variable `err`, verificamos si es un error HTTP usando el tipo `HttpErrorResponse`. Esta verificación eventualmente capturará cualquier error de las llamadas HTTP utilizando el operador de RxJS `throwError`. Todos los demás errores se tratan como errores de aplicación que ocurren en el lado del cliente.

5. Abre el archivo `app.config.ts` e importa la clase `ErrorHandler` del paquete npm `@angular/core`:

```typescript
import { ApplicationConfig, ErrorHandler, provideZoneChangeDetection } from '@angular/core';
```

6. Importa el manejador de errores personalizado que creamos en el archivo `app-error-handler.ts`:

```typescript
import { AppErrorHandler } from './app-error-handler';
```

7. Registra la clase `AppErrorHandler` como el manejador de errores global de la aplicación agregándola al array `providers` de la variable `appConfig`:

```typescript
export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }),
    provideRouter(routes),
    provideHttpClient(),
    { provide: APP_SETTINGS, useValue: appSettings },
    { provide: ErrorHandler, useClass: AppErrorHandler }
  ]
};
```

Para investigar el comportamiento del manejador de errores global de la aplicación, ejecuta los siguientes pasos:

1. Ejecuta el comando `ng serve` para iniciar la aplicación.
2. Desconecta tu computadora de Internet.
3. Navega a `http://localhost:4200`.
4. Abre las herramientas para desarrolladores del navegador e inspecciona la salida de la ventana de la consola:

*(Figura 11.1: Error de aplicación)*

Uno de los errores HTTP más comunes en una aplicación empresarial web es el error de respuesta `401 Unauthorized`. Aprenderemos cómo manejar este error específico en la siguiente sección.

#### Respuesta al error 401 Unauthorized
El error `401 Unauthorized` en una aplicación Angular puede ocurrir en los siguientes casos:

- El usuario no proporciona las credenciales correctas al iniciar sesión en la aplicación.
- El token de autenticación proporcionado cuando el usuario inició sesión en la aplicación ha caducado.

Un buen lugar para manejar el error `401 Unauthorized` es dentro de un interceptor HTTP responsable de la autenticación. En el *Capítulo 8: Comunicación con servicios de datos a través de HTTP*, aprendimos cómo crear un interceptor de autenticación para pasar el token de autorización a cada solicitud HTTP. Para manejar el error `401 Unauthorized`, el archivo `auth.interceptor.ts` podría modificarse de la siguiente manera:

```typescript
import { HttpErrorResponse, HttpInterceptorFn, HttpStatusCode } from '@angular/common/http';
import { inject } from '@angular/core';
import { AuthService } from './auth.service';
import { catchError, EMPTY, throwError } from 'rxjs';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);
  const authReq = req.clone({
    setHeaders: {
      Authorization: 'myToken'
    }
  });
  return next(authReq).pipe(
    catchError((error: HttpErrorResponse) => {
      if (error.status === HttpStatusCode.Unauthorized) {
        authService.logout();
        return EMPTY;
      } else {
        return throwError(() => error);
      }
    })
  );
};
```

El interceptor llamará al método `logout` de la clase `AuthService` cuando ocurra un error `401 Unauthorized` y devolverá un observable `EMPTY` para dejar de emitir datos. Utilizará el operador `throwError` para propagar el error al manejador de errores global en todos los demás errores. Como ya hemos visto, el manejador de errores global examinará el error devuelto y tomará medidas según el código de estado.

Como vimos en el manejador de errores global que creamos en la sección anterior, algunos errores no están relacionados con la interacción con el cliente HTTP. Hay errores de aplicación que ocurren en el lado del cliente y aprenderemos a entenderlos en la siguiente sección.

---

### Sección 11.3: Desmitificando los errores del framework

Los errores de aplicación que se originan en el lado del cliente en una aplicación Angular pueden tener muchas causas. Uno de ellos es la interacción de nuestro código fuente con el framework Angular. A los desarrolladores les gusta probar cosas y enfoques nuevos mientras crean aplicaciones. A veces, las cosas saldrán bien, pero otras veces pueden provocar errores en una aplicación.

El framework Angular proporciona un mecanismo para reportar algunos de estos errores comunes con el siguiente formato:

```text
NGWXYZ: {Error message}.<Link>
```

Analicemos el formato de error anterior:

- **`NG`:** Indica que es un error de Angular para diferenciarlo de otros errores originados en TypeScript y el navegador.
- **`W`:** Un número de un solo dígito que indica el tipo de error. `0` representa un error en tiempo de ejecución y todos los demás números del 1 al 9 representan un error del compilador.
- **`X`:** Un número de un solo dígito que indica la categoría del área de tiempo de ejecución del framework, como detección de cambios, inyección de dependencias y plantillas.
- **`YZ`:** Un código de dos dígitos utilizado para indexar el error específico.
- **`{Error message}`:** El mensaje de error real.
- **`<Link>`:** Un enlace a la documentación de Angular que proporciona más información sobre el error especificado.

Los mensajes de error que se ajustan al formato anterior se muestran en la consola del navegador a medida que ocurren. Veamos un ejemplo de error utilizando el error `ExpressionChangedAfterChecked`, el error más famoso en las aplicaciones Angular:

1. Abre el archivo `app.component.ts` e importa el artefacto `AfterViewInit` del paquete npm `@angular/core`:

```typescript
import { AfterViewInit, Component, inject } from '@angular/core';
```

2. Agrega `AfterViewInit` en la lista de interfaces implementadas:

```typescript
export class AppComponent implements AfterViewInit
```

3. Crea la siguiente propiedad `title` en la clase `AppComponent`:

```typescript
title = '';
```

4. Implementa el método `ngAfterViewInit` y cambia la propiedad `title` dentro del cuerpo del método:

```typescript
ngAfterViewInit(): void {
  this.title = this.settings.title;
}
```

5. Abre el archivo `app.component.html` y vincula la propiedad `title` al elemento HTML `<h2>`:

```html
<h2>{{ title }}</h2>
```

6. Ejecuta el comando `ng serve` y navega a `http://localhost:4200`.

Inicialmente, todo parece funcionar correctamente. El valor de la propiedad `title` se muestra en la página correctamente.

7. Abre las herramientas para desarrolladores del navegador e inspecciona la ventana de la consola:

```text
Application error RuntimeError: NG0100: ExpressionChangedAfterItHasBeenCheckedError: Expression has changed after it was checked. Previous value: ''. Current value: 'My e-shop'. Expression location: _AppComponent component. Find more at https://angular.dev/errors/NG0100
```

El mensaje anterior indica que cambiar el valor de la propiedad `title` causó el error.

Hacer clic en el enlace [https://angular.dev/errors/NG0100](https://angular.dev/errors/NG0100) nos redirigirá a la guía de errores adecuada en la documentación de Angular para obtener más información. La guía de errores explica el error específico y describe cómo solucionar el problema en el código de nuestra aplicación.

Cuando entendemos los mensajes de error que se originan en el framework Angular, podemos solucionarlos fácilmente.

---

### Sección 11.4: Resumen

El manejo de errores durante el tiempo de ejecución o el desarrollo es crucial para toda aplicación Angular. En este capítulo, aprendimos cómo manejar los errores que ocurren durante el tiempo de ejecución de una aplicación Angular, como los errores HTTP o del lado del cliente. También aprendimos cómo entender y solucionar los errores de aplicación lanzados por el framework Angular.

En el próximo capítulo, aprenderemos cómo vestir nuestra aplicación para que se vea más atractiva con la ayuda de Angular Material. Angular Material tiene muchos componentes y estilos listos para que los uses en tus proyectos. Así que démosle a tu proyecto de Angular el amor que se merece.
