---
title: Comenzar con la API de la Base de Datos de Git
intro: 'La API de la Base de datos de Git te da acceso de escritura y lectora para los objetos sin procesar de Git que se encuentran en tu base de datos de Git en {% data variables.product.product_name %} y para listar y actualizar tus referencias (cabezas de rama y etiquetas).'
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
topics:
  - API
---

### Resumen

Básicamente, esto te permite volver a implementar muchas de las funcionalidades de Git sobre nuestra API mediante la creación de objetos sin procesar (raw) directamente en la base de datos y actualizando las referencias de rama que técnicamente podrían hacer todo lo que pueda hacer Git sin que se éste se instale.

La API de la Base de Datos de Git devolverá un `409 Conflict` si el repositorio de Git está vacío o no disponible.  Que un repositorio se muestre como no disponible habitualmente significa que {% data variables.product.product_name %} está en el proceso de crearlo. Para los casos de repositorios vacíos, puedes utilizar la terminal "[Crear o actualizar el contenido de un archivo](/v3/repos/contents/#create-or-update-file-contents)" para crear contenido e inicializar el repositorio para que puedas utilizar la API de la Base de Datos de Git. Contacta a {% data variables.contact.contact_support %} si este estado de respuesta persiste.

![resumen de la base de datos de git](/assets/images/git-database-overview.png)

Para obtener más información sobre la base de datos de objetos de Git, por favor lee el capítulo [Internos de Git](http://git-scm.com/book/en/v1/Git-Internals) en el libro Pro de Git.

Como ejemplo, si quieres confirmar un cambio en un archivo de tu repositorio, lo que harías es:

* Obtener el objeto de la confirmación actual
* Recuperar el árbol al cual apunta
* Recuperar el contenido del objeto del blob que tiene el árbol para esa ruta de archivo en particular
* Cambiar el contenido de alguna manera y publicar un objeto de blob nuevo con este contenido nuevo, obteniendo el SHA del blob a cambio
* Publicar un nuevo objeto de árbol con ese indicador de la ruta del archivo reemplazándolo con el SHA de tu blob nuevo y obteniendo a cambio el SHA del árbol
* Crear un objeo de confirmación nuevo con el SHA de la confirmación actual como el padre y el SHA del árbol nuevo, obteniendo a cambio el SHA de la confirmación
* Actualizar la referencia de tu rama para apuntar al nuevo SHA de la confirmación

Puede que parezca complejo, pero en realidad es bastante simple cuando entiendes el modelo y te proporciona la oportunidad de hacer un sin fin de cosas cuando lo haces potencialmente con la API.

### Verificar la capacidad de fusión de las solicitudes de extracción

{% warning %}

**¡Advertencia!** Por favor no dependas de utilizar Git directamente de {% if currentVersion != "free-pro-team@latest" and currentVersion ver_lt "enterprise-server@2.19" %}[`GET /repos/{owner}/{repo}/git/refs/{ref}`](/v3/git/refs/#get-a-reference){% endif %}{% if currentVersion == "free-pro-team@latest" or currentVersion ver_gt "enterprise-server@2.18" %}[`GET /repos/{owner}/{repo}/git/refs/{ref}`](/v3/git/refs/#get-a-reference){% endif %} para las actualizaciones a las referencias de `merge` de Git, ya que este contenido se vuelve obsoleto sin aviso previo.

{% endwarning %}

Una API consumible debe solicitar explícitamente que una solicitud de extracción cree una confirmación de fusión de _prueba_. Una confirmación de fusión de _prueba_ se crea cuando ves la solicitud de extracción en la IU y cuando se muestra el botón de "Fusionar", o cuando [obtienes](/v3/pulls/#get-a-pull-request), [creas](/v3/pulls/#create-a-pull-request), o [editas](/v3/pulls/#update-a-pull-request) una solicitud de extracción utilizando la API de REST. Sin esta solicitud, las Referencias de Git para `merge` se harán obsoletas hasta la próxima vez que alguien vea la solicitud de extracción.

Si actualmente usas métodos de sondeo que produzcan referencias de Git de `merge`, entoces GitHub recomienda que utilices los siguientes pasos para obtener los últimos cambios desde la rama base (habitualmente `master`):

1. Recibir el webhook de la solicitud de extracción.
2. Llamar a [`GET /repos/{owner}/{repo}/pulls/{pull_number}`](/v3/pulls/#get-a-pull-request) para iniciar un job en segundo plano para crear el candidato de la confirmación de fusión.
3. Sondear tu repositorio utilizando [`GET /repos/{owner}/{repo}/pulls/{pull_number}`](/v3/pulls/#get-a-pull-request) para ver si el atributo `mergeable` está como `true` o como `false`. Puedes utilizar Git directamente o {% if currentVersion != "free-pro-team@latest" and currentVersion ver_lt "enterprise-server@2.19" %}[`GET /repos/{owner}/{repo}/git/refs/{ref}`](/v3/git/refs/#get-a-reference){% endif %}{% if currentVersion == "free-pro-team@latest" or currentVersion ver_gt "enterprise-server@2.18" %}[`GET /repos/{owner}/{repo}/git/refs/{ref}`](/v3/git/refs/#get-a-reference){% endif %} para las actualizaciones a las referencias de `merge` de Git únicamente después de llevar a cabo los pasos anteriores.
