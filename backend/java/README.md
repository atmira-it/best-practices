# Buenas prácticas para Java 

> Esta guia ha sido desarrollada para cualquier versión de Java

## Tabla de contenidos

[Conceptos basicos] (# Conceptos basicos "Conceptos basicos")

# Conceptos basicos

Al tratarse de proyectos maven generados a traves de arquetipos tiene una estructura de directorios reconocida, esto significa que debemos situar nuestras clases, tests o recursos en un lugar en concreto, 
para que luego Maven sea capaz de tratarlos correctamente.

Maven tiene cuatro carpetas fuente por defecto:

* src/main/java : donde guardaremos nuestras clases java fuente. Debajo de esta carpeta situaremos nuestras clases en distintos paquetes.
* src/main/resources : aquí almacenaremos los recursos (ficheros xml, ficheros de propiedades, imagenes, …) que pueda necesitar las clases java de nuestro proyecto. Igualmente aquí tienen que ir los ficheros de configuración de Spring o Hibernate por ejemplo.
* src/test/java : en dicha carpeta se guardan las clases de test que se encargarán de probar el correcto funcionamiento de nuestra aplicación. Aquí por ejemplo podemos guardar nuestros test unitarios de JUnit.
* src/test/resources : en esta carpeta guardamos los recursos que usan los test.

Pongamos por ejemplo como sería la estructura de un proyecto maven (simplificada).

```
src/
├── main/
│   ├── java/
│   │   └── com.bankia.ioi.activarContrato.carteraGestionada.v1_0.flow/
│   │   	├── action/
│   │   	│   └── ...
│   │   	├── assamble/
│   │   	│   └── ...
│   │   	├── constantes/
│   │   	│   └── ...
│   │   	├── dto/
│   │   	│   └── ...
│   │   	├── enrichment/
│   │   	│   └── ...
│   │   	└── utilidades/
│   │   	    └── ...
│   ├── resources/
│   │   └── META-INF/
│   │   	├── spring/
│   │   	│   └── ...
│   │   	└── views/
│   │   	    └── ...
│   └─ webapp/
│       ├── WEB-INF/
│       │   └── ...
│       └── WEB-INF-LOCAL/
│           └── ...
│   
└── test/
    ├── java/
    │   └── ...
    └── resources/
        └── ...
 
```

## Lazy Initialitation

Inicializar los objetos solo cuando sea necesario, de esa manera evitaremos sobrecargar la memoria de java.

```java
public class Paises {
 
    private List paises;
 
    public List getPaises() {
        //se inicializa solo cuando es requerido
        if(null == paises) {
            paises = new ArrayList();
        }
        return paises;
    }
}

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
