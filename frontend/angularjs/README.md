# Buenas prácticas para AngularJS 

> Esta guia ha sido desarrollada para versiones de AngularJS >1.3.0 <1.6.0, y teniendo como referencia la versión 1.4.3

## Tabla de contenidos

* [watever](#watever)

# Estructura de la aplicación

Una aplicación AngularJS está estructurada en módulos, (Module a partir de ahora). Un Module encapsula la lógica y representación visual de una funcionalidad concreta de la aplicación. Esta estructura modular se implementa en el diseño de la estructura de archivos, ayundando a interpretar la aplicación de manera visual con sólo ver el arbol de carpetas.

Pongamos por ejemplo como sería la estructura de la página de Twitter con este diseño (simplificada).
![](frontend/angularjs/assets/twitter-timeline.png)
![](frontend/angularjs/assets/twitter-profile.png)
![](frontend/angularjs/assets/twitter-moments.png)
![](frontend/angularjs/assets/twitter-notifications.png)

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

## Componentes / Component

Los Components principalmente son vistas + lógica, (.html + .controller.js + .service.js). Si los Modules sirven para conectar las diferentes partes que tienen en común pertenecer a un aspecto funcional de la aplicación, cada Component de un Module sirve para controlar una funcionalidad de la vista.

> Si usas una versión de AngularJS comprendida entre la 1.3 y la 1.5 puedes utilizar Components con el Polyfill 'Angular-Component' de Todd Motto

## Elemento / Element

Un Element es una parte de la vista de un Module o Component separada de su .html principal por motivos de limpieza u orden. Un Element carece de lógica, por lo que sólo puede conformarlo archivos .html o .scss.

> Los Element no forman parte de los elementos definidos por AngularJS. Nosotros hemos definido este término para normalizar su uso, pero no forma parte del 'standar' de AngularJS.

## Controladores / Controller

En Angular el flujo de datos se maneja por medio de los Controllers. Por lo general un Controller está asociado a un html y a un Component, manejando lo que la vista va a mostrar y ocupando de recuperar y tratar la información por medio de services.

# Reglas generales de estilo

Mantén las funciones pequeñas, no más de 75 líneas de código (- = mejor).

`Mas facil mantener, entender, reutilizar y debuggear el código`

## Responsabilidad única

Define cada elemento de los Modules (Controllers/Components/Providers) en archivos separados.

`Facilita la lectura, el mantenimiento y la búsqueda de incidencias`

```javascript
/* Evitar */
// ...profile.module.js
angular.
	.module('twitter.profile', ['ngRoute'])
	.controller('ProfileController', ProfileController)
	.service('profileService', profileService);
function ProfileController() { }
function profileService() { }


/* OK */
// ...profile.module.js
angular.module('twitter.profile', ['ngRoute']);

// ...profile.controller.js
angular
	.module('twitter.profile')
	.controller('ProfileController', ProfileController);
function ProfileController() { }

// ...profile.service.js
angular
	.module('twitter.profile')
	.factory('profileService', profileService);
function profileService() { }
```

## IIFE *Inmmediately Invoked Function Expression*

Pon el código en una envoltura de función que la aisle del Scope global. 
<sub>*Por brevedad, el resto de ejemplos mostrados no muestran este tipo de declaración*</sub>

`Evita colisiones en la declaración de variables y que estas se mantengan en el ciclo global`

```javascript
// ...profile.controller.js
/* Evitar */
angular
	.module('twitter.profile')
	.controller('ProfileController', ProfileController);
// Esta función se crea como variable global.
function ProfileController() { }


/* OK */
(function() {
	'use strict';

	angular
		.module('twitter.profile')
		.controller('ProfileController', ProfileController);

		function ProfileController() { }
})();
```

## Declaración de variables/funciones públicas

Declara todas las variables y funciones públicas al inicio de la función, y separa el resto de la implementación tras el código de estos elementos.

```javascript
/* Evitar */
function ProfileController(profileService) {
	var vm = this;
	vm.twitts = [];
	vm.user = undefined;

	function getUserData() {
		var userData = profileService.getUserData();
		vm.twitts = profileService.getTwitts(vm.user.id);
		return userData;
	}
	var deleteTwitt = function(id) {
		profileService.deleteTwitt(id);
		vm.twitts = profuleService.getTwitts(vm.user.id);
	}
	vm.deleteTwitt = deleteTwitt;

	getUserData();
}

/* OK */
function ProfileController(profileService) {
	var vm = this;
	vm.twitts = [];
	vm.user = undefined;
	vm.deleteTwitt = deleteTwitt;

	getUserData();

	var deleteTwitt = function(id) {
		profileService.deleteTwitt(id);
		vm.twitts = profuleService.getTwitts(vm.user.id);
	}

	/////////////////
	
	function getUserData() {
		var userData = profileService.getUserData();
		vm.twitts = profileService.getTwitts(vm.user.id);
		return userData;
	}
}
```

# Reglas específicas de estilo

## Modules

### Nombres únicos

Pon nombres únicos a los Modules e incluye el nombre del padre en sub-módulos. Ie: 'twitter' como Module principal, 'twitter.profile' como Module de la aplicación.

> Evita colisión de nombres, facilita comprender la jerarquía y el ubicar los archivos en la estructura.

### Declaración

No asignes a una variable la declaración de un Module, y separa la declaración de dependencias en un array.

```javascript
// ...app.module.js
/* Evitar */
var app = angular.module('twitter', [
	'ngAnimate',
	'twitter.core'
	'twitter.shared',
	'twitter.modules'
]);
app.config(config);

function config() {
	...
}

/* OK */
var dependencies = [
	'ngAnimate',
	'twitter.core'
	'twitter.shared',
	'twitter.modules'
];
angular.module('twitter', dependencies);
angular.module('twitter').config(config);

function config() {
	
}
```

## Components

### controllerAs



## Controllers

### vm = this

Declara en el Controller una variable `var vm = this;` a la que asignar las diferentes funciones y variables públicas. Mientras se mantenga la regla de Controllers livianos será facil visualizar el Controller al debuggear sin necesidad de manejar un objeto con su mismo nombre.

```javascript
/* Evitar */
function ProfileController() {
	this.name = {};
	this.editAvatar = function() { };
}

/* OK */
function ProfileController() {
	var vm = this;
	vm.name = {};
	vm.editAvatar = function() { };
}
```

### Lógica del constructor/inicio en $onInit

Introducir la lógica a realizar en el inicio del Controller dentro de la función $onInit.

`Asegura que todos los bindings declarados en el Component están disponibles.`

```javascript
/* Evitar */
function TimelineController(timelineService) {
	var vm = this;
	// from binding: vm.profile
	vm.twitts = timelineService.getTwitts(vm.profile.id);
	...
}

/* OK */
function TimelineController(timelineService) {
	var vm = this;
	vm.twitts = [];
	// from binding: vm.profile
	
	vm.$onInit = function() {
		vm.twitts = timelineService.getTwitts(vm.profile.id);
	};
}
```

### Delega lógica a los Services

Los Controller deberían servir como punto de unión entre la vista y los Service, incluyendo sólo lógica que sea muy específica, no reutilizable.

`Poniendo la lógica en los Services, puede ser reutilizada por varios Controllers`
`Facilita la interpretación del Controller en su conjunto`
`Atomiza las operativas de manera que es mas facil realizar test unitarios y encontrar bugs`

```javascript
/* Evitar */
function ProfileController($http, $q) {
	var vm = this;
	vm.profile = undefined;
	...
	function getProfile() {
		var settings = { ... };
		return $http.get(settings)
			.then( ... )
			. catch ( ... );
	}
}

/* OK */
function ProfileController(profileService) {
	var vm = this;
	vm.profile = undefined;

	vm.$onInit = function() {
		vm.profile = profileService.getProfile();
	};
}
```

## Services

### Llamadas REST

Procura utilizar `$resource` antes que `$http`, que simplifica y hace más claro de interpretar el código de llamadas REST.