# Buenas prácticas para AngularJS 

> Esta guia ha sido desarrollada para versiones de AngularJS >1.3.0 <1.6.0, y teniendo como referencia la versión 1.4.3

### Tabla de contenidos

* [watever](#watever)

## Estructura de la aplicación

Una aplicación AngularJS está estructurada en módulos, (Module a partir de ahora). Un Module encapsula la lógica y representación visual de una funcionalidad concreta de la aplicación. Esta estructura modular se implementa en el diseño de la estructura de archivos, ayundando a interpretar la aplicación de manera visual con sólo ver el arbol de carpetas.

Pongamos por ejemplo como sería la estructura de la página de Twitter con este diseño (simplificada).
![](/assets/twitter-timeline.png)
![](/assets/twitter-profile.png)
![](/assets/twitter-moments.png)
![](/assets/twitter-notifications.png)

```
src/
├── app/
│   ├── assets/
│   │   └── ...
│   ├── core/
│   │   ├── app.constants.js
│   │   ├── app.templates.js
│   │   ├── core.module.js
│   │   ├── database.service.js
│   │   ├── logger.service.js
│   │   └── utils.service.js
│   ├── modules/
│   │   ├── profile.module/
│   │   │   ├── profile-stats.element/
│   │   │   │	├── profile-stats.html
│   │   │   │	└── profile-stats.css
│   │   │   ├── profile-info.element/
│   │   │   │   └── profile-info.element/
│   │   │   ├── profile.config.js
│   │   │   ├── profile.controller.js
│   │   │   ├── profile.module.js
│   │   │   ├── profile.routes.js
│   │   │   ├── profile.service.js
│   │   │   ├── profile.html
│   │   │   └── profile.css
│   │   ├── timeline.module/
│   │   │   └── ...
│   │   ├── moments.module/
│   │   │   ├── moments-categories.element/
│   │   │   │   └── ...
│   │   │   ├── moments-moment.element/
│   │   │   │   └── ...
│   │   │   └── ...
│   │   ├── notifications.module/
│   │   │   ├── notifications-categories.element/
│   │   │   │   └── ...
│   │   │   └── ...
│   │   └── modules.module.js
│   ├── shared/
│   │   ├── nav-bar.component/
│   │   │   └── ...
│   │   ├── twitt.component/
│   │   │   └── ...
│   │   ├── trending.component/
│   │   │   └── ...
│   │   ├── send-twitt.component/
│   │   │   └── ...
│   │   ├── follow-panel.component/
│   │   │   └── ...
│   │   ├── follow.directive/
│   │   │   └── ...
│   │   ├── avatar.directive/
│   │   │   └── ...
│   │   └── shared.module.js
│   ├── app.boostrapping.js
│   ├── app.module.js
│   └── app.routes.js
└── index.html
```

## Módulos / Module

Cada Module de la aplicación corresponde a un aspecto funcional de la misma. Por lo general cubren una "sección" grande de la aplicación, (ej. el timeline), aunque no siempre es así y si la complejidad lo requiere un Module puede contener otro Module.

### app.module

Los Modules sirven también para ensamblar la aplicación via dependencias, por lo que se requiere un Module principal que instancie la aplicación y añada como dependencias los Module que conforman la aplicación. Este Module no debe contener lógica funcional.
En caso de utilizar `ui-router`, es conveniente declarar en este punto inicial de la aplicación la ruta por defecto (ej. '/home').

> Para evitar colisión con el nombre de otras aplicaciones AngularJS, se evita usar el nombre 'app' para el Module principal (aunque el archivo siga siendo app.module.js). También se recomienda utilizar una palabra o un acrónimo, ya que afectará al tamaño de la declaración del resto de Modules de la aplicación.

### shared.module

Este Module sirve de punto de conexión entre el app.module y distintos elementos compartidos de la aplicación. Este Module se limita a declarar como dependencias los Module que estén dentro de la carpeta `src/app/shared/`, y así evitar una gran cantida de dependencias en el app.module.js. Más información sobre dependencias en adelante.

> Para una aplicación pequeña puede no hacer falta declarar el shared.module, vinculando los elementos de la carpeta src/app/shared al Module principal de la aplicación.

### modules.module

Este Module realiza la misma función de conexión con app.module pero para cada uno de los Module que cubren funcionalidades principales de la aplicación.


