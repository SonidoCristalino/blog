+++ 
date = '2025-11-12T22:10:20-03:00'
draft = false
title = '03 - Desplegando la infraestructura'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Despliegue"] 
series = ["Curso Terraform"]
+++

# Sección nº 3

## Autenticación (Authentication) y Autorización (Authorization)

Para comenzar con Terraform, lo que primero debemos de generar son los accesos correctos para que Terraform pueda generar la infraestructura que nosotros le pedimos. Si nosotros no generamos estos accesos desde el lado de AWS, Terraform cada vez que quiera impactar los cambios solicitados, obtendrá un error como respuesta. AWS por defecto no permite que ningún servicio se conecte a su nube excepto que configuremos los accesos de forma correcta. 

Pero primero hagamos la distinción entre Autenticación (Authentication y Authorization)

1. **Authentication (Autenticación)**: La autenticación se refiere al proceso de verificar la identidad de un usuario o un sistema para asegurarse de que sean quienes dicen ser. En el contexto de AWS, la autenticación implica la verificación de las credenciales de acceso proporcionadas por un usuario o una aplicación para confirmar su identidad antes de permitirles acceder a los recursos y servicios de AWS. Las credenciales de acceso pueden incluir nombres de usuario y contraseñas, así como también tokens de acceso o certificados digitales.

2. **Authorization (Autorización)**: La autorización se refiere al proceso de determinar qué acciones y recursos están permitidos para un usuario o una aplicación después de que su identidad haya sido autenticada con éxito. En el contexto de AWS, la autorización implica definir y aplicar políticas de acceso que determinen qué usuarios o roles tienen permiso para realizar acciones específicas en los recursos de AWS. Estas políticas de acceso se definen mediante el uso de AWS Identity and Access Management (IAM), y pueden basarse en una variedad de criterios, como identidades de usuario, grupos, roles, direcciones IP y más.

La autenticación en AWS verifica la identidad de un usuario o una aplicación, mientras que la autorización determina qué acciones y recursos están permitidos para ese usuario o aplicación después de la autenticación. Juntas, la autenticación y la autorización garantizan que solo los usuarios autorizados tengan acceso a los recursos y servicios de AWS según sus roles y permisos asignados.

Por lo tanto, Terraform necesita el acceso a credenciales con determinados permisos dentro de la nube del proveedor para poder generar los deploys solicitados en el código. Por lo tanto, deberemos de tener un usuario y password que Terraform utilizará para poder ingresar a la nube de AWS y luego con los accesos gestionados para ese usuario, podrá desplegar la infraestructura requerida. 

### Documentación del Proveedor AWS: Autenticación

Algo importante a tener en cuenta es que para distintos proveedores existen distintas credenciales de acceso solicitados por Terraform. Por ejemplo, para AWS Terraform necesitará `access_key` y `secret_key`, por lo tanto, cada proveedor que utilicemos en Terraform, requerirá distintos medios de autenticación. 

Podemos revisar la [documentación oficial](https://registry.terraform.io/providers/hashicorp/aws/latest/docs) de Terraform para el proveedor de AWS. Aquí podremos observar lo que necesitaremos gestionar desde el lado de AWS. 

En esta documentación, se puede observar el proceso que genera Terraform para poder gestionar las solicitudes a la API de AWS. Se puede buscar en la sección 'Provider Configuration' de la documentación, que explícitamente es necesario lo siguiente: 

```terraform
provider "aws" {
  region     = "us-west-2"
  access_key = "my-access-key"
  secret_key = "my-secret-key"
}
```

Lo mismo sucede con otros providers como *GitHub*, *Azure*, etc. Por ejemplo, en Github, cuando un usuario externo le damos permisos al repositorio, podemos atomizar estos permisos y permitirle determiados accesos para determinadas acciones (lo que anteriormente denominamos como Autorización). 

## Creación de usuario (AWS IAM)
Para esto, necesitaremos utilizar la cuenta Root de AWS e iremos al servicio de IAM para poder crear un nuevo usuario. Debemos tener especial cuidado cuando manipulamos la consola de AWS logueados con la cuenta de ROOT ya que tenemos el control total de la cuenta. 

1. Nos logueamos a nuestra cuenta de AWS con el usuario Root
2. Vamos al servicio de IAM > Users > Create User
3. User Name: Terraform
4. Permission > Attach policies directly
5. Y seleccionamos el siguiente permiso: Administrator Access
6. Create User

Una vez que nuestro nuevo usuario se encuentra creado, lo que deberemos de hacer es:  
1. Lo seleccionamos de la lista de IAM > Users
2. En la pestaña Security Credentials > Create Access Key > CLI
3. Agregamos alguna descripción que querramos 
4. Create Access Key
5. ¡Cuidado! Hay que tener presente que estas credenciales se **podrán ver por única vez en este paso**, es por ello que por temas de seguridad, se recomienda bajar el csv donde se tienen las llaves en texto plano.

Luego de esto, copiamos tanto el Access Key como el valor del Secret Access Key. 

## Lanzamiento de nuestra primera máquina virtual (EC2)

Al utilizar Terraform para la gestión de infraestructura, es fundamental comprender que los nombres de los servicios y recursos varían según el proveedor de nube. En el contexto de Amazon Web Services (AWS), su servicio de computación (servidores virtuales) se denomina **EC2**, acrónimo de *Elastic Compute Cloud*.

### Consideraciones Esenciales para el Despliegue

1. **Regiones Geográficas:** Cada proveedor de nube ofrece la posibilidad de desplegar recursos en diferentes **regiones**. La selección de la región debe basarse estrictamente en los requisitos de la organización, considerando factores como la latencia, la soberanía de los datos y la cercanía a los usuarios finales.

2. **Especificaciones de Instancia (EC2):** Al crear una instancia EC2 en AWS, es importante definir sus características técnicas. Estas especificaciones determinarán el rendimiento y la capacidad del servidor. Las propiedades clave a configurar incluyen:
* **Tipo de Instancia:** Define la capacidad de procesamiento (CPU).
* **Memoria (RAM):** Establece la cantidad de memoria disponible.
* **Almacenamiento:** Determina el tamaño y el tipo de los discos asociados.
* **Imagen de Máquina de Amazon (AMI):** Especifica el sistema operativo base de la instancia.

Estas características deben ser definidas de manera explícita en el código de configuración de Terraform. Al ejecutar el código, Terraform interpretará estas definiciones y emitirá la orden precisa al proveedor (AWS) para que las instancias se creen con las especificaciones requeridas.

### Creación de la instancia EC2 de manera manual
Antes de pasar a crear un servicio de forma automática en Terraform, recomiendo ver algún video en Youtube sobre la creación de una instancia de manera manual
mediante la consola de AWS, para ver el tiempo que toma configurar y desplegar un servicio de estas características. 

[Video de ejemplo](https://www.youtube.com/watch?v=n7J5fTT54nk)

### Despliegue de la Primera Instancia con Terraform

Para iniciar el despliegue de nuestra primera instancia en AWS utilizando Terraform, es imprescindible consultar la **documentación oficial** del proveedor. El punto de referencia es el registro de [Terraform para el proveedor AWS](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

#### Uso de la Documentación del Recurso `aws_instance`

La documentación del proveedor AWS especifica cómo estructurar los *scripts* de Terraform para cada servicio. Dado que el objetivo es lanzar una instancia EC2, nos enfocaremos en la sección correspondiente al recurso [aws_instance](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance)

Esta documentación proporciona ejemplos prácticos que sirven como punto de partida para nuestra configuración. Tomaremos como base el siguiente bloque de código:

```terraform
resource "aws_instance" "web" {
    ami           = data.aws_ami.ubuntu.id
    instance_type = "t3.micro"
    tags          = { Name = "HelloWorld" }
}
```

#### Análisis de la Configuración del Recurso

En el bloque de código, es fundamental comprender los siguientes elementos:

  * **`resource`**: Es la palabra clave que indica que estamos declarando un recurso de infraestructura.
  * **`"aws_instance"`**: Este es el **tipo de recurso**. Hace referencia explícita al servicio de instancia EC2 en AWS. Este parámetro debe mantenerse.
  * **`"web"`**: Es el **nombre local** o la etiqueta de referencia única que le daremos al recurso dentro de nuestro código de Terraform. Este nombre se cambiará a `"terraformec2"` para identificarlo en este proyecto.
  * **`ami`**: Especifica el **Amazon Machine Image (AMI)**, que es la imagen base del sistema operativo.
  * **`instance_type`**: Define el **tipo de instancia** (por ejemplo, `t2.micro` o `t3.micro`), lo que determina la capacidad de cómputo (CPU y memoria).

#### Obtención del AMI

El valor para el parámetro `ami` se obtiene directamente desde la consola de AWS. Para ello, se puede navegar a la sección **EC2 \> Launch Instances \> Application and OS Images**, y seleccionar la imagen deseada (por ejemplo, las que tienen la indicación *uefi-preferred*).

### Script Final de Configuración

Una vez definidos todos los parámetros, la configuración final de nuestro *script* (`.tf`) debe incluir la definición del **proveedor** y el **recurso**.

**Nota sobre credenciales:** Aunque en este ejemplo se incluyen las claves de acceso, **no se recomienda** gestionar las credenciales de esta forma en entornos de producción. Se sugiere utilizar métodos más seguros como Roles de IAM o variables de entorno. Las claves en toda este tutorial son **meramente ilustrativas** y deben ser reemplazadas por los valores que correspondan a cada *key* obtenida por el usuario:

```terraform
provider "aws" {
    region      = "us-east-1"
    access_key  = "my-access-key"
    secret_key  = "my-secret-key"
}

resource "aws_instance" "terraformec2" {
    ami             = "ami-0d7a109bf30624c99" # Sustituir por un AMI válido y reciente
    instance_type   = "t2.micro"
}
```

### Secuencia de Comandos para el Despliegue Inicial

Una vez completada la escritura del *script* de configuración en Terraform, el despliegue de la infraestructura requiere la ejecución de una serie de comandos en secuencia.

1.  **`terraform init`**: Este es el primer comando a ejecutar. Su función es inicializar el directorio de trabajo, descargar los *plugins* del proveedor necesarios (en este caso, AWS), y preparar el *backend* para la gestión de estados. Es una etapa fundamental para que Terraform pueda gestionar la infraestructura solicitada.

2.  **`terraform validate`**: Tras la inicialización, se invoca este comando para inspeccionar la sintaxis de los archivos de configuración (`.tf`). **`terraform validate`** verifica que el código esté escrito correctamente y que respete las reglas de HCL (HashiCorp Configuration Language).

3.  **`terraform plan`**: Una vez validada la sintaxis, este comando genera un plan de ejecución. El plan describe detalladamente las acciones que Terraform llevará a cabo para alcanzar el estado deseado definido en el código (por ejemplo, qué recursos se crearán, modificarán o eliminarán). Es crucial notar que **`terraform plan` no aplica ninguna modificación** a la infraestructura real, sino que únicamente traza y muestra el impacto de la configuración.

4.  **`terraform apply`**: Si el plan generado es satisfactorio y se aprueba la ejecución, se invoca **`terraform apply`**. Este comando es el responsable de ejecutar el plan propuesto, interactuando efectivamente con el proveedor (AWS) para crear o modificar los recursos de infraestructura.

5.  **Verificación Post-Despliegue**: Es importante acceder a la consola de AWS (sección **EC2 \> Instances**) para confirmar visualmente que la infraestructura ha sido impactada y gestionada correctamente de acuerdo con la configuración codificada.

### Actualización de la Configuración: Asignación de Etiquetas (*Tags*)

Una vez que la instancia ha sido desplegada con éxito, el siguiente paso práctico es actualizarla, por ejemplo, asignándole una etiqueta (o *tag*). Las etiquetas son pares clave-valor que resultan esenciales para la organización, la facturación y la diferenciación de recursos dentro del entorno de AWS.

El proveedor AWS sugiere la siguiente sintaxis para añadir una etiqueta de nombre al recurso `aws_instance`: [Referencia](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance)

```terraform
tags = { Name = "EC2-Example" }
```

En este caso, la **clave** es `Name` y el **valor** es `EC2-Example`.

#### Modificación del Código

Al integrar esta etiqueta, el bloque del recurso en nuestro código de Terraform debe ser modificado para incluir la nueva propiedad:

```terraform
resource "aws_instance" "terraformec2" {
    ami             = "ami-0d7a109bf30624c99"
    instance_type   = "t2.micro"
    tags            = { Name = "EC2-Example" }
}
```

#### Secuencia de Aplicación de Cambios

Para aplicar esta modificación, se sigue un proceso simplificado de comandos:

1.  **`terraform plan`**: Se ejecuta nuevamente para que Terraform compare el estado actual de la infraestructura (ahora existente en AWS) con el nuevo estado deseado definido en el código. El resultado mostrará una diferencia (*diff*) indicando que se realizará una modificación (*1 to add, 0 to destroy, 1 to change*).

2.  **`terraform apply`**: Si el plan de modificación es aceptado, este comando se invoca para ejecutar la actualización. Terraform aplicará el cambio en caliente, asignando la etiqueta a la instancia EC2 existente.

3.  **Verificación de la Actualización** Al regresar a la consola de AWS y actualizar la lista de instancias EC2, se observará que el nombre (basado en la etiqueta `Name`) ha sido correctamente impactado en la primera instancia desplegada.

### Provider section
La sección que no cambiará a lo largo del tiempo en nuestro código, será la perteneciente a la sección `provider`. Y es así porque aquí se tiene la configuración necesaria para que Terraform pueda conectarse a la nube de AWS y crear nuestras instancias. Por lo tanto, la siguiente sección, SIEMPRE SERÁ LA MISMA para todos nuestros scripts: 
```terraform
provider "aws" {
    region      = "us-east-1"
    access_key  = "my-access-key"
    secret_key  = "my-secret-key"
}
```

### Punto de Seguridad Crítico

Las claves de acceso (`Access Key`) y claves secretas (`Secret Key`) de AWS cumplen la función de credenciales (equivalentes a nombre de usuario y contraseña) para gestionar su cuenta de AWS.

**Advertencia de Seguridad:**

Cuando comparta su código de Terraform en foros públicos, plataformas de aprendizaje (como la sección de Preguntas y Respuestas de Udemy) o cualquier otro medio, es fundamental asegurarse de **NO** incluir las claves de acceso y secretas.

Antes de publicar cualquier fragmento de código, debe eliminar o sustituir estas claves por marcadores de posición. Nunca exponga las credenciales de su cuenta.

## Recursos y proveedores (proveedores)
 
Lo interesante de utilizar Terraform es que tiene soporte para mas de 3000 proveedores (entre ellos los más conocidos como AWS, GCP, Azure, Alibaba, etc). Esto se puede ver si nos dirigimos al [siguiente link](https://registry.terraform.io/)

En esta página lo que podemos ver es que si vamos a la sección **Browser providers** podremos listar todos los proveedores de servicios que da soporte Terraform; por lo tanto nos puede dar un indicio de lo grande que ha sido la adopción de Terraform en estos últimos años. 

### Proveedor

En Terraform, un proveedor (*provider* en inglés) es un componente fundamental que permite a Terraform interactuar con servicios en la nube, proveedores de infraestructura local u otros sistemas externos para gestionar recursos. Cada proveedor es responsable de traducir las configuraciones de Terraform en acciones específicas para el servicio que representa.

Aquí hay algunos puntos clave sobre los proveedores en Terraform:

1. **Interfaz de API**: Los proveedores proporcionan una interfaz de programación de aplicaciones (API) que Terraform utiliza para comunicarse con los servicios o plataformas que desees gestionar. Esta API permite a Terraform realizar operaciones como crear, modificar y eliminar recursos.

2. **Recursos**: Cada proveedor define un conjunto de recursos que pueden ser gestionados a través de Terraform. Estos recursos pueden ser máquinas virtuales, bases de datos, redes, balanceadores de carga, entre otros, dependiendo del servicio que el proveedor represente. Terraform proporciona un conjunto extenso de recursos integrados para varios proveedores, y también permite a los usuarios crear sus propios recursos personalizados si es necesario.

3. **Configuración del proveedor**: Para utilizar un proveedor en Terraform, necesitas configurarlo en tu archivo de configuración. Esto generalmente incluye detalles como el nombre del proveedor, la región, las credenciales de autenticación y cualquier otra configuración específica del proveedor.

4. **Versiones y actualizaciones**: Los proveedores de Terraform son software independiente y se actualizan regularmente para agregar nuevas funcionalidades, corregir errores y mantenerse al día con los cambios en los servicios que representan. Es importante especificar la versión del proveedor en tu archivo de configuración de Terraform para asegurarte de que tus configuraciones funcionen de manera consistente, incluso si hay actualizaciones disponibles.

En resumen, los proveedores en Terraform son extensiones que permiten a Terraform interactuar con servicios en la nube y otros sistemas externos para gestionar la infraestructura como código. Proporcionan una abstracción uniforme sobre una amplia variedad de servicios y permiten a los usuarios gestionar sus recursos de manera consistente y automatizada.  

### `terraform init`
Como se dijo anteriormente, un proveedor para Terraform es una extensión que permite adoptar un método a la hora de interactuar con las APIs de los distintos proveedores de la nube. 

Cuando invocamos el comando `terraform init` lo que sucede es que Terraform descargará la estensión (plugin) necesaria que se especifica en nuestro código `tf`. Como en nuestro ejemplo anterior, el proveedor del que nos íbamos a servir sería AWS, terraform adopta esta configuración y descarga automáticamente la extensión para saber cómo interactua con la API de los servicios de AWS. 

Cuando esta extensión se descarga, se hace dentro de una nueva carpeta que se crea denominada `.terraform`, la cual se encuentra oculta en el directorio raíz en el que iniciamos nuestro proyecto de Terraform (algo similar sucede cuando en nuestro repositorio queremos iniciar un versionado con git haciendo `git init`, el cual inicializa todos sus archivos de configuración en la carpeta oculta `.git`). 

Si listamos todos los directorios que se encuentran en `.terraform` y buscamos la extensión descargada para nuestro proveedor, veremos que habrá descargado un archivo de casi 430 Mb: 

```
.terraform/providers/registry.terraform.io/hashicorp/aws/5.41.0/linux_amd64: total 429M 
```

### Trabajar con distintos proveedores
Si nosotros queremos trabajar por ejemplo con otro proveedor, como Azure, simplemente nos será suficiente ingresar la siguiente línea a nuestro código de ejemplo `first_ec2.tf`: 

```
provider "azurerm" { 
}
```

Y luego invocar el comando `terraform init`. Esto será suficiente como para que Terraform entienda que debe descargar la extensión para poder manejar la API de todos los servicios de Azure. 

El inconveniente que tiene esto es que por cada repositorio, tendremos unos *500Mb* apropiados sólo para el ejecutable que provee la lógica para que nuestro código se pueda comunicar con las APIs. 

### Recursos

En Terraform, un "recurso" (*resource* en inglés) es una entidad representada dentro de la configuración que describe un objeto específico que existe dentro de tu infraestructura. Estos recursos pueden ser máquinas virtuales, bases de datos, redes, grupos de seguridad, balanceadores de carga, entre otros.

Cada recurso en Terraform está asociado a un proveedor particular. Por ejemplo, si estás trabajando con Amazon Web Services (AWS) y quieres crear una instancia EC2, utilizarías el recurso `aws_instance` y especificarías los detalles necesarios, como el tipo de instancia, la imagen de la máquina virtual, el tipo de almacenamiento, etc.

Aquí hay un ejemplo básico de cómo se vería un recurso en un archivo de configuración de Terraform: 

```
resource "aws_instance" "example" { 
    ami           = "ami-0c55b159cbfafe1f0" 
    instance_type = "t2.micro"
} 
```

En este ejemplo:

- `aws_instance` es el tipo de recurso que estamos creando, que representa una instancia EC2 en AWS.
- `"example"` es el nombre dado a este recurso en el contexto de este archivo de configuración. Este nombre se utiliza para hacer referencia a este recurso en otros lugares de tu configuración.
- Dentro de las llaves `{}`, se especifican los atributos del recurso, como la AMI (Amazon Machine Image) a utilizar y el tipo de instancia.

Los recursos son la base de la configuración de Terraform y representan las entidades que deseas gestionar en tu infraestructura. Terraform utiliza esta información para generar un plan de ejecución que describe cómo se modificará tu infraestructura para alcanzar el estado deseado.

### Resource block

Debemos comprender que cada bloque tiene un tipo de recurso asociado (en el caso anterior es `aws_instance`) junto con un nombre local para denominar a ese recurso (que en nuestro ejemplo lo denominamos `terraformec2`) 

Algo importante a tener en cuenta es que el nombre del recurso junto con el nombre local asignado a ese recurso, hacen un identificador único en todo el código: 

Por lo tanto, si tenemos este ejemplo: 


```
resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99" 
    instance_type = "t2.micro" 
}

resource "aws_instance" "terraformec2" { 
    ami           = "ami-33333222255555151" 
    instance_type = "t3.micro" 
}
```

Veremos que ambas instancias son distintas, pero que para Terraform no lo es, ya que junto con el nombre del recurso `aws_isntance` junto con el nombre local para ese recurso asignado `terraformec2`, Terraform **considera que es el mismo recurso**, ya que el recurso y su nombre NO pueden ser el mismo. 

### Cada proveedor con su recurso

Otra de las condiciones que establece Terraform es que para cada tipo de recurso, debe ir acompañado con su tipo de proveedor declarado. Es decir, NO SE PUEDE hacer lo siguiente: 

```
provider "azurerm" {
    source  = "hashicorp/azurerm"
    version = ">= 2.0.0, < 3.0.0"
}

resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99" 
    instance_type = "t2.micro" 
}
```

Vemos que el recurso `aws_instance` es un recurso que NO pertenece a **Azure**, sino al proveedor de **AWS**. 

Aún así, es posible en el mismo archivo, tener dos declaraciones de proveedores y por lo tanto esto permite manejar tanto los recursos de uno como del otro en un mismo código tf. 

### ¿Necesito aprenderme las particularidades de cada proveedor? 
La respuesta rotunda es no. Si uno comprende el core de Terraform, no será difícil el trabajar con distintos proveedores. Ya que la sintáxis del lenguaje es igual a lo largo de todos los proveedores. 

### Problemas con los proveedores

Puede ser que a lo largo de nuestro uso de Terraform, nos encontremos con errores o problemas relacionados específicamente a cada proveedor que soporta de manera oficial la herramienta. Cuando nos suceda esto, deberemos de encontrar la respuesta en cada una de las distintas páginas que mantiene abiertas Hashicorp para cada uno de los proveedores. Si no encontramos la respuesta a nuestro problema, podremos subir un ticket reportando el inconveniente con las pruebas para que sea analizada desde la parte técnica. 

Si bien, es muy difícil encontrar un bug, ya que millones de personas utilizan Terraform a lo largo del mundo, más que seguro que ya exista una solución a nuestro problema. En caso de que no sea así, podremos como se mencionó anteriormente levantar un ticket en la página correspondiente al proveedor que estemos utilizando. 

## Provider Tiers (niveles de proovedores)
Para entender lo que son los Provider Tiers (niveles de provedores), antes hay que entender primero: 
1. Lo que es un Provider Registry. 
2. Lo que es un Provider Mainteiner. 
3. Y por último lo que significa Provider Tiers. 

### Provider Registry (Proveedor de registro)

El proveedor de registros (*provider registry* en inglés) en Terraform es un servicio proporcionado por HashiCorp que actúa como un repositorio centralizado de proveedores de Terraform y módulos. Este registro permite a los usuarios buscar, descubrir y acceder fácilmente a los proveedores de Terraform y módulos creados y mantenidos por la comunidad y por HashiCorp.

Aquí hay algunas características clave del proveedor de registros en Terraform:

1. **Exploración y búsqueda**: El proveedor de registros proporciona una interfaz de búsqueda que permite a los usuarios encontrar proveedores de Terraform y módulos según sus necesidades. Los usuarios pueden buscar por nombre, categoría, etiquetas y otros criterios para encontrar los recursos que mejor se adapten a sus requisitos.

2. **Publicación y distribución**: Los maintainers de proveedores y módulos pueden publicar sus creaciones en el proveedor de registros para que otros usuarios de Terraform puedan utilizarlos. Esto facilita la distribución de código y fomenta la colaboración en la comunidad de Terraform.

3. **Versionado y control de calidad**: El proveedor de registros incluye características de versionamiento que permiten a los maintainers de proveedores y módulos publicar diferentes versiones de sus creaciones. Los usuarios pueden especificar qué versión de un proveedor o módulo desean utilizar en sus configuraciones de Terraform, lo que ayuda a garantizar la estabilidad y compatibilidad de las configuraciones.

4. **Integración con Terraform CLI**: Los usuarios pueden acceder al proveedor de registros directamente desde la línea de comandos de Terraform utilizando el comando `terraform init` con la opción `-upgrade`. Esto permite a Terraform descargar automáticamente los proveedores y módulos necesarios desde el registro durante la inicialización del directorio de trabajo.

En resumen, el proveedor de registros en Terraform es una herramienta poderosa que facilita la gestión y distribución de proveedores de Terraform y módulos, simplificando el proceso de descubrimiento y utilización de recursos de infraestructura como código en la comunidad de Terraform.

### Providers Maintainers
Los "provider maintainers" en Terraform son individuos o equipos responsables del desarrollo y mantenimiento de los proveedores de Terraform. Un proveedor en Terraform es un complemento que permite a Terraform interactuar con servicios en la nube, proveedores de infraestructura local u otros sistemas externos para gestionar recursos.

Los proveedores mantienen la integración de Terraform con servicios específicos y se encargan de asegurar que los proveedores sean compatibles, estables y estén actualizados con las últimas características y cambios de los servicios que representan. Esto implica tareas como:

1. **Desarrollo de nuevas características**: Los maintainers de los proveedores trabajan en la implementación de nuevas características y funcionalidades que sean compatibles con los servicios que el proveedor representa. Esto puede incluir la adición de nuevos recursos, la mejora de la compatibilidad con versiones específicas de API de servicio, entre otros.

2. **Corrección de errores**: Los maintainers son responsables de solucionar problemas y errores que surjan en el proveedor. Esto puede incluir errores de comportamiento, problemas de rendimiento o cualquier otro problema que afecte el funcionamiento del proveedor.

3. **Actualización de versiones**: Los maintainers aseguran que los proveedores se mantengan actualizados con las últimas versiones de los servicios que representan. Esto implica seguir de cerca los cambios y actualizaciones de los servicios en la nube y adaptar el proveedor en consecuencia.

4. **Soporte y comunidad**: Los maintainers pueden proporcionar soporte a los usuarios que utilizan el proveedor, responder preguntas en foros, resolver problemas reportados por la comunidad y colaborar con otros usuarios para mejorar la documentación y la experiencia general del proveedor.

En resumen, los maintainers de los proveedores juegan un papel importante en el ecosistema de Terraform al garantizar que los proveedores sean confiables, estables y estén al día con los servicios en la nube y otras plataformas que representan. Su trabajo ayuda a los usuarios de Terraform a aprovechar al máximo la herramienta al proporcionar una integración sólida con una amplia gama de servicios y plataformas.

### Provider Tiers

Existen 3 niveles de soporte para los proveedores y módulos de Terraform: 
- **Official**: son aquellos proveedores que pertenecen y son mantenidos por la compañía Hashicorp
- **Partner**: son aquellos proveedores que pertenecen y son mantenidos por compañías de tecnología que mantienen una *asociación* con Hashicorp. En este caso puede ser que Google mantenga algunos módulos para Terraform.
- **Community**: En este caso los módulos o proveedores pertenecen y son mantenidos por contribuyentes individuales, externos a Hashicorp y que no mantienen ninguna alianza estratégica. 

### Ejemplo práctico
Para ver esto último, podemos ir al [siguiente link](https://registry.terraform.io/browse/providers)

En la esquina superior izquierda, podremos ver los tres diferentes niveles de los que hablamos. Esta sección permite filtrar los proveedores por sus distintos niveles. Por ejemplo, podemos tildar sólo el filtro de 'Official' y podemos ver que proveedores como AWS, Azure o GCP son mantenidos exclusivamente por la compañía Hashicorp.  Ahora si filtramos por el `partner tier` podremos ver que algunos de estos proveedores son Alibaba u Oracle, lo que significa que estos proveedores son matenidos por empresas externas pero relacionadas a Hashicorp. 

Lo que hay que comprender es que para el nivel de `community tiers` los bugs que puedan resultar de estos módulos o proveedores, pueden tardar mucho tiempo en ser resueltos, por lo tanto no son recomendados para producción. 

### Provider registry: namespaces
En el contexto del registro de proveedores de Terraform, el término `namespaces` se refiere a una forma de organizar y categorizar los proveedores de Terraform y los módulos asociados dentro del registro. Los "namespaces" actúan como contenedores lógicos que agrupan los recursos relacionados según el autor o el mantenimiento del proveedor.

Cada `namespace` en el registro de proveedores de Terraform está asociado con un nombre único, que generalmente corresponde al nombre del autor o del mantenedor del proveedor o módulo. Estos nombres pueden ser nombres de organizaciones, nombres de usuarios o cualquier otro identificador único.

Los `namespaces` permiten a los usuarios del registro de proveedores de Terraform identificar fácilmente los proveedores y módulos mantenidos por una determinada entidad. Esto facilita la búsqueda y el descubrimiento de recursos relevantes para sus necesidades específicas.

Por ejemplo, si estás buscando un proveedor de Terraform para interactuar con un servicio en la nube específico, puedes buscar proveedores dentro del "namespace" de esa empresa o servicio en el registro de proveedores. Esto te ayudará a encontrar rápidamente los recursos relevantes y confiables asociados con ese proveedor en particular.

Retomando, los `namespaces` en el registro de proveedores de Terraform son una forma de organizar y categorizar los proveedores y módulos, lo que facilita a los usuarios encontrar y utilizar recursos específicos mantenidos por una entidad o autor particular.

Tenemos nuevamente 3 niveles con los que se categorizan estos `namespaces`: 
1. **Official**: Este tendrá el nombre de la compañía Hashicorp que es la encargada del desarrollo de Terraform. 
2. **Partner**: Tendrán el nombre de una organización de terceros, como por ejemplo mongodb/mongodbatlas. 
3. **Community**: tendrán nombres especiales de individuos o cuentas de organizaciones. 

### Ejemplo de `namespaces` 
#### Official namespaces
Por ejemplo, si navegamos en https://registry.terraform.io/browse/providers y abrimos por ejemplo, el provider de AWS y el de Azure, veremos las siguientes URL: 

* https://registry.terraform.io/providers/hashicorp/azurerm/latest
* https://registry.terraform.io/providers/hashicorp/aws/latest

Podemos ver que ambas páginas comparten el mismo namespace `providers/hashicorp/` y esto es porque todos los módulos que se albergan detrás de estos proveedores, han sido desarrollados por Hashicorp. 

#### Partner namespaces
En cambio, si filtramos por el nivel 'Partner', y abrimos el enlace del proveedor de Alibaba Cloud, veremos el siguiente URL: 

* https://registry.terraform.io/providers/aliyun/alicloud/latest
* https://registry.terraform.io/providers/oracle/oci/latest

Podremos ver el siguiente namespaces: `providers/aliyun/alicloud` lo que nos da una idea acerca de los módulos que se encuentran dentro de este espacio son desarrollados por empresas de terceros asociadas a Hashicorp. Lo mismo sucede con la nube de Oracle. 

#### Community namespaces
Ahora filtremos por los proveedores 'community' y veremos por ejemplo lo siguiente: 

* https://registry.terraform.io/providers/milosbackonja/1password/latest

Podremos ver que el namespace ya pertenece a un individuo y luego a una aplicación: "providers/milosbackonja/1password"

### Punto importante sobre los namespace

Algo importante a destacar sobre la sección de bloque `providers`, es que Hashicorp solicita explícitamente a aquellos proveedores que no están soportados por la propia Hashicorp, utilicen el bloque denominado como `required_providers` en vez del `provider "aws"` por ejemplo. Este último se encuentra soportado por Hashicorp, en cambio el primero, se encuentra por fuera del mantenimiento de Hashicorp, y es por eso que tiene que utilizar otro bloque distinto. 

### Práctica
Por ejemplo, si en nuestro código del archivo `first_ec2.tf` ingresamos el siguiente bloque de código solicitando el proveedor `digitalocean` de la siguiente manera: 

```terraform
provider "digitalocean" { }
```

Y luego sobre el directorio donde se encuentra nuestro archivo ejecutamos `terraform init`, producirá un error, ya que el proveedor `digitalocean` al no ser sorportado oficialmente por Hashicorp, no puede descargar los paquetes del proveedor, buscarlos dentro del namespace `hashicorp/digitalocean`, el cual efectivamente no existe por no ser soportado oficialmente. 

¿Cómo sabremos qué namespace le corresponde? Bueno, podemos hacer lo siguiente: 
1. Nos dirigimos a https://registry.terraform.io/browse/providers
2. Luego realizamos una búsqueda con la palabra clave `digitalocean`. Esto dará lugar a una lista de proveedores que estárán ordenados según determinada valoración
   de los usuarios. 
3. En este primer ordenamiento, veremos que el namespaces es `digitalocean/digitalocean`, pero si ingresamos a ese proveedor
4. Obtenemos la URL siguiente: https://registry.terraform.io/providers/digitalocean/digitalocean/latest que efectivamente tiene el namespaces: `providers/digitalocean/digitalocean`
5. ¡PERO! También hay que utilizar el bloque `required_providers`, ya que `digitalocean` NO está oficialmente soportado. 
6. En el ejemplo de uso dentro de la documentación del proveedor, debemos observar el siguiente código de ejemplo: 

```terraform
terraform { 
    required_providers { 
        digitalocean = { 
            source  = "digitalocean/digitalocean" 
            version = "~> 2.0" 
        } 
    } 
}
```

Como vemos, al no ser un proveedor soportado oficialmente por Hashicorp, debemos de utilizar tanto el bloque `required_providers` como también el correcto namespaces `digitalocean/digitalocean` . Una vez modificado esto en el código, podremos inicializar el proyecto mediante `terraform init` de forma correcta. 

### Official required providers
También hay que tener encuenta que la cláusula `required_providers` puede ser utilizada por proveedores que son oficiales, pero NO al revés. Por ejemplo, si nos dirigimos al proveedor oficial de AWS, podremos ver en su documentación el siguiente ejemplo: 

```terraform
terraform { 
    required_providers { 
        aws = { 
            source  = "hashicorp/aws" 
            version = "~> 5.0" 
        } 
    } 
}
```

Esta cláusula la desarrollaremos más en detalle en secciones siguientes. Pero hay que recordar que la cláusula `required_providers` puede utilizarse o no con proveedores oficiales, pero debe usarse **obligadamente con aquellos que no son soportados por Hashicorp**.

Con los proveedores oficiales podemos utilizar para que sea más sencillo de leer, la cláusula `provider "aws"` por ejemplo para utilizar las extensiones de AWS. 

## Crear un Repositorio de GitHub mediante Terraform
Como se dijo en una lección pasada, una de las potencialidades que tiene utilizar IaC y más específicamente Terraform, es que podemos darle seguimiento a todas las modificaciones de nuestra infraestructura en el tiempo mediante un versionado de código como puede ser `git`. 

En este caso lo que se hará es crear un repositorio de GitHub pero mediante Terraform, demostrando que la potencia de Terraform es hablar con las APIs de los servicios de la nube y por eso es que es una herramienta tan abarcativa. 

### Práctica: Github
Lo que primero debemos de buscar es el proveedor `Github` en la página de Terraform, obteniendo el [siguiente enlace](https://registry.terraform.io/providers/integrations/github/latest)

Como veremos, el namespace es `providers/integrations/github`. Deberemos de tener en cuenta 3 cosas: 
1. Deberemos de crear un nuevo archivo con el nombre `github.tf` y se encontrará en el mismo directorio que el anterior archivo `first_ec2.tf`. 
2. La sección que corresponde a la selección del proveedor. 
3. La parte que corresponde a la authentication. 

Por lo tanto, luego de generar un archivo con nombre `github.tf` copiamos la primera parte que será la siguiente: 

```terraform
terraform { 
    required_providers { 
        github = { 
            source  = "integrations/github" 
            version = "~> 6.0" 
        } 
    } 
}
```

Y luego para la otra sección, iremos a la parte de la documentación que se encuentra señalizado como `authentication`, veremos la siguiente sección: 

```terraform
provider "github" { 
    token = var.token # or `GITHUB_TOKEN` 
}
```

Por lo tanto, el código nos quedará de la siguiente manera: 

```terraform
terraform { 
    required_providers { 
        github = { 
            source  = "integrations/github" 
            version = "~> 6.0" 
        } 
    } 
}
```

Para poder generar un **token de Github**, deberemos de realizar los siguiente pasos: 
1. Ingresamos a nuestra cuenta de Github
2. Settings 
3. Developer settings
4. Personal Access Token
5. Fine-grained token -> Generate New Token
6. Lo generamos y el nombre de nuestro token será `Terraform`
7. En la sección de Repository Acces marcamos "All repositories"
8. En Permissions, nos posicionamos en la sección "Administration" y le otorgamos el acceso de Read/Write. 
9. Generate Token
10. Copiamos el token y lo pegamos en la sección `provider "github"` indicada anteriormente, quedando de la siguiente manera:

```terraform
# Configure the GitHub Provider 
provider "github" { 
    token = "github_pat_aBcD123456EeFfGgHhIiJ1111111111111111111111199999999999999"
}
```

Hasta ahora no hemos generado ningún servicio en el código, por lo tanto iremos nuevamente a la página del proveedor Github de Terraform y filtraremos por el servicio `github_repository`:  https://registry.terraform.io/providers/integrations/github/latest/docs/resources/repository

```terraform
resource "github_repository" "example" { 
    name        = "example"
    description = "My awesome codebase"
    visibility  = "public"
}
```

#### Código completo
Por lo tanto, el archivo `github.tf` quedará de la siguiente manera: 

```terraform
terraform { 
    required_providers { 
        github = { 
        source  = "integrations/github" 
        version = "~> 6.0" 
        } 
    } 
}

# Configure the GitHub Provider 
provider "github" { 
    token = "github_pat_aBcD123456EeFf11111111111111111111111999999999999999999999" 
}

resource "github_repository" "example" { 
    name        = "example"
    description = "My awesome codebase"
    visibility  = "public"
}
```

Por lo tanto, luego de crear el archivo, lo que debemos de hacer es:  
1. Aplicar el comando `terraform init` y una vez que haya descargado las extensiones para el proveedor de `github` pasaremos al segundo punto. 
2. El  comando `terraform plan`. Deberemos de ver que el comando `plan` nos indicará que sólo impactará una sola modificación (que será la creación de nuestro repositorio "example") 
3. Y si estamos de acuerdo con ello invocaremos el comando `terraform apply`

## Terraform Destroy

Así como existe el comando `terraform apply` para llevar a cabo el plan conseguido por `terraform plan`, también existe un comando que nos permite eliminar toda la infraestructura creada. Este comando es `terraform destroy`. 

### Preliminar: terraform plan -destroy
Para saber qué plan de destrucción aplicará `terraform destroy`, debemos invocar al comando `terraform plan -destroy`

### Comando: terraform destroy
El comando para destruir o eliminar toda la infraestructura que se ha creado con Terraform es `terraform destroy`. Este comando desmantela y elimina todos los recursos que fueron creados por Terraform según la configuración definida en tus archivos.

Es importante tener en cuenta que `terraform destroy` eliminará todos los recursos definidos en tu configuración, por lo que debes tener cuidado al usarlo en entornos de producción para evitar la pérdida de datos o recursos importantes.

Antes de ejecutar `terraform destroy`, es una buena práctica revisar el plan de destrucción generado por Terraform para asegurarte de que comprendes qué recursos se eliminarán. Puedes obtener este plan ejecutando `terraform plan -destroy` para ver una lista detallada de los recursos que serán eliminados.

Una vez estés seguro de que deseas continuar con la eliminación de los recursos, simplemente ejecuta `terraform destroy` y confirma la acción cuando se te solicite. Terraform comenzará a eliminar los recursos de manera segura y controlada de acuerdo con la configuración definida en tus archivos.

El comando `terraform destroy` se utiliza para eliminar de forma segura y controlada todos los recursos que Terraform ha creado y administra según la configuración definida en tus archivos de Terraform. Aquí hay más información sobre este comando:

1. **Eliminación de recursos**: Cuando ejecutas `terraform destroy`, Terraform desmantela y elimina todos los recursos que ha creado según la configuración en tus archivos. Esto incluye la eliminación de instancias de máquinas virtuales, grupos de seguridad, redes, bases de datos, y cualquier otro recurso gestionado por Terraform.

2. **Confirmación de eliminación**: Antes de llevar a cabo la eliminación de los recursos, Terraform solicitará confirmación para asegurarse de que estás seguro de que deseas proceder con la acción. Debes confirmar explícitamente la eliminación escribiendo "yes" en la línea de comandos para evitar la eliminación accidental de recursos importantes.

3. **Actualización del estado**: Después de eliminar los recursos, Terraform actualizará el archivo de estado para reflejar los cambios realizados. Esto asegura que Terraform tenga un registro actualizado del estado de tu infraestructura y evita conflictos futuros al aplicar cambios.

4. **Control de recursos**: Es importante tener en cuenta que `terraform destroy` eliminará todos los recursos definidos en tu configuración, por lo que debes tener cuidado al usarlo en entornos de producción para evitar la pérdida de datos o recursos importantes. Se recomienda realizar una copia de seguridad o una revisión cuidadosa de los recursos antes de ejecutar este comando en un entorno de producción.

En resumen, `terraform destroy` es el comando que utilizas cuando deseas eliminar de forma segura y controlada todos los recursos gestionados por Terraform. Asegúrate de entender completamente el impacto de esta acción antes de ejecutarla, especialmente en entornos de producción.

### `terraform destroy` consideraciones
Como se dijo anteriormente, para saber el plan qué se aplicará a la hora de eliminar los recursos, se debe invocar a `terraform plan -destroy`. Debemos de recordar que este comando tomará en cuenta para eliminar a aquellos recursos que se encuentren dentro de la carpeta de proyecto. Es decir, tomará en cuenta los recursos creados por los siguientes archivos: 
* `first_ec2.tf`
* `github.tf`

Por lo tanto, Terraform sólo eliminará los recursos que se hayan creado dentro de estos dos archivos y no más. Por eso siempre es recomendable gestionar previamente un plan de destrucción, para poder evidenciar aquellos recursos que serán eliminados. 

### Comando: `terraform destroy -target`
Algunas veces, no vamos a necesitar eliminar todos los recursos que se encuentran dentro de nuestro repositorio. En el caso actual, si nosotros aplicaríamos `terraform destroy`, se eliminarían los recursos de: 
* `first_ec2.tf`
* `github.tf`

Pero supongamos que nosotros queremos mantener las instancias generadas en AWS pero queremos eliminar los recursos gestionados en GitHub ¿Cómo podemos hacer esto?. 

Esto se puede hacer mediante el comando `terraform destroy -target <TARGET>`. 

El comando `terraform destroy -target <TARGET>` es una variación del comando `terraform destroy` que te permite especificar un recurso específico que deseas destruir en lugar de eliminar todos los recursos gestionados por Terraform. Esto puede ser útil cuando solo quieres eliminar un recurso particular en lugar de toda la infraestructura, lo que te permite controlar de manera más precisa qué recursos se eliminarán.

Aquí hay más detalles sobre cómo funciona este comando:

1. **Especificación del objetivo**: Con la opción `-target`, puedes especificar el recurso específico que deseas destruir. Esto se hace proporcionando la dirección del recurso como argumento. La dirección del recurso es la ruta completa al recurso en el árbol de recursos de Terraform, como en nuestro caso `aws_instances.terraformec2` o `github_respository.example`. 

2. **Destrucción del recurso objetivo**: Cuando ejecutas `terraform destroy -target <TARGET>`, Terraform desmantela y elimina únicamente el recurso especificado y cualquier recurso que dependa directa o indirectamente de él. Terraform analiza la dependencia del recurso objetivo y elimina los recursos en el orden adecuado para evitar conflictos o errores.

3. **Precaución**: Es importante tener en cuenta que `terraform destroy -target <TARGET>` elimina solo el recurso especificado y sus dependencias, pero no elimina otros recursos no relacionados que puedan estar presentes en tu configuración de Terraform. Por lo tanto, debes tener cuidado al usar este comando para evitar la eliminación accidental de recursos importantes o infraestructura dependiente.

Por lo tanto, `terraform destroy -target <TARGET>` te permite eliminar de manera selectiva un recurso específico y sus dependencias utilizando Terraform. Esto te brinda un mayor control sobre el proceso de destrucción de la infraestructura y te permite evitar la eliminación accidental de recursos no deseados. Sin embargo, debes utilizar este comando con precaución y asegurarte de comprender completamente su impacto en tu infraestructura.

Para eliminar por ejemplo, nuestro recurso de AWS, sin eliminar el de GitHub, lo que debemos hacer es lo siguiente:  

```bash
$ terraform destroy -target aws_instances.terraformec2
```

De lo contrario (en el caso de que querramos eliminar el repositorio de Github) podremos hacer:  

```bash
$ terraform destroy -target github_repository.example
```

### Importante
Algo importante de tener en cuenta, es que la próxima vez que se corra el comando `terraform plan`, este leerá nuevamente los comandos desplegados dentro de nuestro repositorio, encontrará nuevamente el recurso `aws_instance.terraformec2` y planificará crearlo. 

Por lo que es es recomendable que cada vez que destruimos un recurso, es importante quitarlo de nuestro código (en caso que ya no lo necesitemos). De lo contrario, cada vez que corramos el comando `terraform apply`, se volverá a generar una nueva instancia de AWS. Por lo tanto podremos *comentar* el bloque de código para que no vuelva a crearse el servicio de nuevo. 

### Importante 2
Otra forma en la que podemos eliminar recursos es simplemente comentando en el código el recurso que querramos eliminar, luego aplicamos el comando `terraform plan` para ver que efectivamente haya tomando los cambios de los comentarios y en caso de que sea así, eliminará los recursos que han sido comentados ya que entenderá que esos ya no son necesarios de tenerlos activos. 

## Entendiendo el archivo `state`

En las siguientes lecciones se abordará la temática sobre el estado de los archivos. 

Como vimos anteriormente, si nosotros comentamos los recursos dentro de los scripts de `tf` y ejecutamos el comando `terraform plan`, podemos ver que Terraform sabe qué recurso deberá ser destruido como así también qué recurso fue eliminado. La pregunta que surje es "¿cómo hace Terraform para poder saber el estado de cada uno de los recursos?"

Terraform internamente mantiene el estado de nuestro código para saber el estado por el cual pasó cada uno de los recursos detallados dentro. Esto le permite a Terraform poder mapear los recursos del mundo real a nuestra configuración. 

### Terraform state files
En Terraform, los **state files** (archivos de estado) son archivos que contienen información sobre la infraestructura gestionada por Terraform. Estos archivos almacenan el estado actual de los recursos que Terraform administra, incluyendo los recursos creados, sus atributos y cualquier otra información relevante.

Aquí hay algunos puntos clave sobre los archivos de estado en Terraform:

1. **Registro del estado de la infraestructura**: Los archivos de estado registran el estado actual de la infraestructura gestionada por Terraform. Esto incluye información sobre los recursos que Terraform ha creado, modificado o eliminado, así como sus atributos y configuraciones actuales.

2. **Sincronización con la infraestructura real**: Los archivos de estado se utilizan para mantener un registro sincronizado de la infraestructura real y la definición de infraestructura en tus archivos de configuración de Terraform. Esto permite a Terraform determinar qué recursos están gestionados por Terraform y qué cambios han sido aplicados a esos recursos.

3. **Prevención de conflictos**: Los archivos de estado evitan conflictos al permitir que Terraform realice cambios de manera controlada y predecible. Terraform utiliza el archivo de estado como fuente de verdad para determinar qué cambios son necesarios para alcanzar el estado deseado de la infraestructura.

4. **Almacenamiento local o remoto**: Los archivos de estado pueden almacenarse localmente en tu máquina de desarrollo o en un sistema de control de versiones como Git, pero también es común almacenarlos de forma remota en un servicio de almacenamiento específico, como Terraform Cloud, Amazon S3, Azure Blob Storage, o Google Cloud Storage. Almacenar los archivos de estado de forma remota proporciona una mayor seguridad y colaboración entre equipos.

En resumen, los archivos de estado en Terraform son fundamentales para el funcionamiento de la herramienta, ya que registran el estado actual de la infraestructura gestionada y permiten a Terraform realizar cambios de manera segura y predecible. Es importante manejar y proteger los archivos de estado adecuadamente para garantizar la integridad y la seguridad de tu infraestructura.

### Ejemplo
Volvamos al ejemplo de creación de: 
1. Una instancia de AWS
2. Un repositorio de Github

#### Create
Cuando creamos una instancia de AWS y un repositorio de Github mediante Terraform, este además de generar la infraestructura solicitada, mantiene un registro de estos recursos y toda la información que crea necesaria para poder mantener un seguimiento efectivo. A través de estos archivos de estado, es que Terraform puede seguir o trackear el estado de nuestra nuestra infraestructura. 

#### Destroy
Cuando se destruye un recurso mediante el comando `terraform destroy` (por ejemplo la instancia de AWS), el recurso eliminado también genera la eliminación del archivo de estado (state file) asociado. Pero deja sin efecto al recurso de GitHub. 

Es por esto que cuando volvemos a invocar el comando `terraform plan` y luego `terraform apply` Terraform puede leer los archivos de estado y darse cuenta que la instancia de AWS no contiene ningún archivo y el de GitHub si, y por lo tanto deberá de aplicar la creación del recurso. 

### State file name
Podemos observar que al inicializar un directorio de Terraform, se genera un directorio oculto denominado `.terraform`  y dentro de él podemos ver que existe un archivo denominado `terraform.fstate`. Si nosotros abrimos este documento, podremos ver que toda la información se encuentra almacenada con una jerarquía JSON. Aquí es donde Terraform ordena toda la información relevante sobre los recursos que crea. 

Si dentro del archivo no se encuentra el recurso de AWS, para Terraform esto significa que el recurso no existe, ya que no le está haciendo seguimiento, y por lo tanto, cuando apliquemos `terraform plan` y luego `terraform apply`, terraform revisará este archivo y al no encontrar la entrada para el recurso de AWS, lo intentará crear. 

#### Buena práctica para el archivo `.tfstate`
Al trabajar con equipos grandes, una buena práctica es mantener el archivo `terraform.tfstate` en un almacenamiento remoto, como S3 con bloqueo de DynamoDB o Google Cloud Storage. Esto permite que los integrantes del equipo accedan al estado compartido antes de realizar cambios en la infraestructura, garantizando que todos mantengan una visión sincronizada del proyecto y evitando conflictos al aplicar modificaciones concurrentes.

### Backup `.tfstate` file
Terraform también generará un archivo de extensión `.backup` para este archivo de estado cuando se elimina el recurso asociado. Este será el estado del directorio una vez que se haya generado un recurso y eliminado: 

```bash
-rw-rw-r-- 1 user user   308 mar 23 20:44 first_ec2.tf 
-rw-rw-r-- 1 user user   395 mar 23 14:05 github.tf 
drwxr-xr-x 3 user user  4096 mar 20 23:59 .terraform/ 
-rw-r--r-- 1 user user  1377 mar 20 23:59 .terraform.lock.hcl 
-rw-rw-r-- 1 user user   180 mar 23 20:50 terraform.tfstate 
-rw-rw-r-- 1 user user  4858 mar 23 20:50 terraform.tfstate.backup
```

Podemos ver que luego de haber invocado al comando `terraform destroy`, se genera un archivo de nombre `terraform.tfstate.backup`, que le permite a Terraform hacerle un seguimiento a los servicios generados. 

#### Importante
Terraform no sólo guardará los recursos creados a partir de nuestro script, sino también toda la información asociada a los recursos creados, como por ejemplo en caso de ser una instancia de AWS, guardará información sobre el tipo de instancia, los volúmenes asociados, los Security Groups, las interfaces de Red, las direcciones IP, los balanceadores de carga asociados, etc. 

Por lo tanto **no hay que modificar este archivo bajo ninguna circunstancia** ya que el estado en el que quedará la instancia será **no-deseado** y los resultados obtenidos podrían llegar a ser un caos
del cual sería muy difícil volver a un estado anterior. 

## Entendiendo el Desired & Current States 
### Desired State (Estado deseado)
En Terraform, el término **desired state** (estado deseado) se refiere al estado en el que deseas que se encuentre tu infraestructura según lo definido en tus archivos de configuración de Terraform. Este estado deseado se describe mediante la especificación de recursos y sus atributos en los archivos de configuración.

Aquí hay algunos puntos clave sobre el "desired state" en Terraform:

1. **Definición de recursos**: En tus archivos de configuración de Terraform, defines los recursos que deseas que Terraform administre. Estos recursos pueden ser instancias de máquinas virtuales, bases de datos, redes, balanceadores de carga, entre otros, dependiendo del proveedor y los servicios que estés utilizando.

2. **Atributos y configuraciones**: Junto con la definición de recursos, también especificas los atributos y configuraciones de esos recursos que deseas que Terraform aplique. Esto incluye detalles como el tipo de instancia, la región, el tamaño del disco, las reglas de firewall, etc.

3. **Gestión de cambios**: Terraform se encarga de llevar la infraestructura al estado deseado, comparando el estado actual de los recursos con el estado deseado definido en los archivos de configuración. Si hay diferencias entre el estado actual y el estado deseado, Terraform realiza los cambios necesarios para converger hacia el estado deseado.

4. **Planificación y ejecución**: Antes de aplicar los cambios, Terraform genera un plan detallado que describe los pasos necesarios para alcanzar el estado deseado. Este plan muestra qué recursos serán creados, modificados o eliminados para alcanzar el estado deseado. Luego, Terraform ejecuta estos cambios de manera segura y controlada.

En resumen, el "desired state" en Terraform representa el estado en el que deseas que se encuentre tu infraestructura según lo definido en tus archivos de configuración. Terraform utiliza esta especificación para gestionar los recursos y aplicar los cambios necesarios para alcanzar ese estado deseado.

La función principal de Terraform es la de crear, modificar y destruir recursos de infraestructura que se encuentren presentes en el código valiéndose de los archivos de estados que se encuentran en su configuración. 

Aquí podemos ver que estas 3 acciones (crear, modificar y destruir) se harán en pos de llegar a un estado deseado (desired state). Por ejemplo, cuando tenemos el siguiente código: 

```terraform
resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.micro"
    tags          = { 
        Name = "First TerraformEC2" 
    } 
}
```

Lo que tratará de realizar Terraform será de generar una instancia de AWS en nuestra cuenta con las particularidades que se le asignan en el bloque de código interno, como por ejemplo que la AMI sea `ami-0d7a109bf30624c99`, o que el nombre de la instancia sea `First TerraformEC2` 

En caso de que el bloque de código anterior, se encuentre comentado, el comando `terraform apply` le indicará a Terraform que el estado deseado será el de eliminar la instancia. 

### Current state

Puede suceder que una vez que implementamos nuestra infraestructura mediante `terraform apply`, alguien desde el lado del proveedor AWS modifique la instancia y por ejemplo, modifique el tipo de la instancia (de `t2.micro` a `t2 micro`). Esto significa que el 'desired state' y el 'current state' (estado actual) no se corresponderán. 

### Importante

Terraform siempre tratará de que la estructura que despliegue se base en el estado deseado. Por lo tanto, si sucede que hay una inconsistencia entre el estado deseado (que se encuentra configurado en terraform) con el estado actual (que se encuentra desplegado en AWS) como en el ejemplo anterior, entonces lo que tratará de hacer Terraform la próxima vez que se ejecute, es tratar de volver al estado deseado y cuando lancemos `terraform plan` nos mostrará un plan para para volver al estado deseable que se encuentra configurado en el código. Por lo tanto nos mostrará los pasos que aplicará para actualizar el estado actual y llevarlo al estado deseado. 

Por lo tanto, cuando Terraform encuentra una inconsistencia entre el **desired state** y el **current state** tratará de volver todo atrás y aplicar cambios para volver al **desired state** nuevamente. 

## Desafíos con el current state en los valores calculados
### Comando: terraform refresh
El comando `terraform refresh` es utilizado en Terraform para actualizar el estado conocido por Terraform con el estado actual de la infraestructura gestionada por los proveedores. Cuando ejecutas este comando, Terraform realiza una consulta a la infraestructura real para obtener información actualizada sobre los recursos existentes y sus atributos.

Aquí hay más detalles sobre cómo funciona `terraform refresh`:

1. **Actualización del estado**: Terraform compara el estado conocido (almacenado en los archivos de estado) con el estado actual de los recursos en la nube o en el proveedor de infraestructura local. Si hay diferencias entre el estado conocido y el estado actual, Terraform actualiza su estado interno para reflejar la información más reciente.

2. **No aplica cambios**: A diferencia de otros comandos de Terraform como `terraform apply`, `terraform refresh` no aplica cambios a la infraestructura. En su lugar, solo actualiza el estado interno de Terraform con la información actualizada de la infraestructura existente.

3. **Útil para sincronización**: `terraform refresh` es útil cuando necesitas sincronizar el estado de Terraform con cambios realizados externamente a través de otros medios que no sean Terraform, como cambios manuales realizados directamente en la consola del proveedor de nube o mediante otras herramientas de administración de infraestructura.

4. **Precaución**: Aunque `terraform refresh` no realiza cambios en la infraestructura, puede tener implicaciones si hay diferencias significativas entre el estado conocido y el estado real. Siempre es importante revisar cuidadosamente el resultado de `terraform refresh` para comprender cualquier diferencia y asegurarse de que el estado de Terraform refleje con precisión el estado real de la infraestructura.

Por lo tanto, `terraform refresh` se utiliza para actualizar el estado interno de Terraform con la información más reciente sobre los recursos gestionados por los proveedores, sin realizar cambios en la infraestructura. Es útil para mantener sincronizado el estado de Terraform con cambios externos en la infraestructura.

### Current state non matching
Hay que entender que Terraform sólo sigue los recursos que se encuentran especificados dentro del código que nosotros le suministramos. Tengamos por ejemplo que nosotros simplemente tenemos este código: 

```terraform
provider "aws" { 
    region     = "us-east-1" 
    access_key = "my-access-key"
    secret_key = "my-secret-key"
}

resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.micro"
    tags          = {
        Name = "First TerraformEC2" 
    } 
}
```

Implementamos este tipo de instancias mediante `terraform apply`, obteniendo como ya sabemos el archivo de seguimiento `terraform.tfstate` y entre todas las configuraciones que obtiene, vemos lo referido al Security Group

```bash
... "secondary_private_ips": [], "security_groups": [ "default" ], ...
```

Luego de esto, supongamos que un usuario desde el lado de AWS modifica el Security Group de la instancia, pasándola desde `default` a una generada por nosotros con nombre `custom`. Actualizamos el archivo de estado mediante el comando `terraform refresh` y el archivo `terraform.tfstate` se actualiza al siguiente estado: 

```bash
... "secondary_private_ips": [], "security_groups": [ "custom" ], ...
```

Si lanzamos el comando `terraform plan` vamos a ver que terraform nos indicará que no hay ninguna modificación que realizar y que nuestra infraestructura se encuentra actualizada. ¿Y esto por qué? Ya que el Security Group cambió de `default` a `custom`. 

Esto es porque Terraform sólo se ocupa de llevar al estado deseado (desired state) la infraestructura que le da seguimiento y se encuentra configurada en nuestro código, por lo tanto, como el Security Group NO es parte de la infraestructura codificada por nosotros, no actualizará nada. 

## Terraform Provider Versioning
La **versión de proveedor** (provider versioning en inglés) en Terraform se refiere al proceso de gestionar y controlar las versiones de los proveedores de Terraform utilizados en tu configuración. Cada proveedor de Terraform es mantenido y actualizado por un equipo específico, y regularmente se lanzan nuevas versiones para incluir nuevas funcionalidades, correcciones de errores y mejoras de rendimiento.

Aquí hay algunas cosas importantes que debes saber sobre la versión de proveedor en Terraform:

1. **Compatibilidad de versión**: Cada versión de Terraform es compatible con una serie de versiones de proveedor. Esto significa que si estás utilizando una versión específica de Terraform, debes asegurarte de utilizar una versión compatible del proveedor. Puedes consultar la documentación oficial de Terraform o del proveedor para encontrar información sobre la compatibilidad de versiones.

2. **Especificación de versiones**: En tus archivos de configuración de Terraform, puedes especificar la versión del proveedor que deseas utilizar. Esto te permite controlar qué versión del proveedor se utiliza en tu infraestructura y evitar actualizaciones no deseadas que podrían introducir cambios inesperados.

3. **Actualización de versiones**: Es importante mantener tus proveedores de Terraform actualizados para aprovechar las últimas funcionalidades y correcciones de errores. Puedes utilizar herramientas como `terraform init -upgrade` para actualizar automáticamente los proveedores a las últimas versiones compatibles.

4. **Administración de versiones**: Si necesitas utilizar una versión específica del proveedor por razones de compatibilidad o estabilidad, puedes administrar las versiones de los proveedores utilizando herramientas de administración de dependencias, como `terraform init -backend-config`.

La versión de proveedor en Terraform es un aspecto importante a tener en cuenta al trabajar con la herramienta. Te permite controlar qué versiones de proveedores se utilizan en tu infraestructura y garantizar la compatibilidad y estabilidad de tu entorno. Es recomendable mantener tus proveedores actualizados para aprovechar las últimas funcionalidades y correcciones de errores.

### Buenas prácticas en ambientes productivos
Cuando se inicializa un proyecto de Terraform mediante `terraform init`, si no se especifica la versión, entonces por defecto Terraform utilizará la versión más actual. 

En las implementaciones para ambientes productivos, siempre se deben tener en la configuración de Terraform señalada la versión del proveedor con la que nosotros querramos contar, de lo contrario, podríamos estar trabajando con una versión 8 y luego de un tiempo al aplicar nuevos cambios a la misma infraestructura la versión del proveedor pasa a la 9 y esto puede traer problemas graves. 

Es por eso que siempre es buena práctica establecer la versión del proveedor con la que querremos implementar nuestra infraestructura: 

```terraform
terraform { 
    required_providers { 
        github = { 
            source  = "integrations/github" 
            version = "~> 3.0" 
        } 
    } 
}
```

De esta manera aseguramos que siempre estemos trabajando con la misma extensión **3.0** de Terraform que maneja la API de Github. 

### Argumentos para el provider
Como podemos ver, cuando especificamos en un bloque de código `required_providers` la versión, los símbolos utilizados pueden ser varios. A continuación damos una explicación sobre estos. 

En Terraform, el bloque `required_providers` se utiliza para especificar qué proveedores y qué versiones de esos proveedores son requeridos por nuestra configuración. Cuando defines un proveedor en este bloque, puedes especificar la versión del proveedor utilizando símbolos semánticos para indicar rangos de versiones.

Aquí hay una explicación de los símbolos comunes utilizados para configurar la versión del proveedor en el bloque `required_providers`:

1. **Versión exacta**: Puedes especificar una versión exacta del proveedor utilizando el formato `= X.Y.Z`, donde `X.Y.Z` es la versión específica del proveedor que deseas utilizar. Por ejemplo, `= 2.0.0` especifica que se requiere exactamente la versión 2.0.0 del proveedor.

2. **Rango de versiones**: Puedes especificar un rango de versiones utilizando los operadores de comparación `>`, `<`, `>=`, `<=` o `~>`. Por ejemplo:
    - `>= 2.0.0` significa que se requiere una versión igual o superior a 2.0.0.
    - `<= 3.0.0` significa que se requiere una versión igual o inferior a 3.0.0.
    - `> 1.2.0, < 2.0.0` significa que se requiere una versión mayor que 1.2.0 pero menor que 2.0.0.
    - `~> 1.2.0` significa que se requiere una versión que sea compatible con 1.2.0, lo que incluye cualquier versión 1.x.y donde `x` es igual a 2 e `y` es mayor o igual a 0.

3. **Última versión**: Si no especificas ninguna versión, Terraform utilizará la **última versión disponible del proveedor**. Sin embargo, esto puede no ser siempre recomendable en entornos de producción, ya que puede introducir cambios no previstos.

Aquí hay un ejemplo de cómo se vería el bloque `required_providers` especificando diferentes versiones del proveedor: 

```hcl
terraform {
    required_providers {
        aws = {
            source  = "hashicorp/aws"
            version = ">= 3.0.0"
        }
        azurerm = {
            source  = "hashicorp/azurerm"
            version = ">= 2.0.0, < 3.0.0"
        }
    }
}
```

En este ejemplo, se requiere cualquier versión igual o superior a `3.0.0` del proveedor de AWS, y cualquier versión compatible con la gama `2.x.y` pero inferior a `3.0.0` del proveedor de Azure. Estas versiones se utilizarán cuando inicialices tu configuración de Terraform utilizando `terraform init`.

### Importante
**NO se recomienda** utilizar el argumento `~>` para ambientes productivos (ver apartado anterior para entender el significado), ya que puede suceder que nuestra infraestructura funcione adecuadamente con la versión `3.0`, pero no lo haga de forma correcta con la versión `3.1`, por lo tanto este argumento se desaconseja totalmente. En ambientes productivos, se aconseja el uso de `=`. 

### EL archivo `terraform.lock.hcl`
El archivo `terraform.lock.hcl` es un archivo generado por Terraform que se utiliza para bloquear las versiones específicas de los módulos de Terraform y los proveedores de infraestructura utilizados en tu configuración. Este archivo ayuda a garantizar que las mismas versiones de los módulos y proveedores se utilicen de manera consistente en diferentes entornos y durante diferentes ejecuciones de Terraform.

Aquí hay algunas cosas importantes que debes saber sobre el archivo `terraform.lock.hcl`:

1. **Versiones específicas**: El archivo `terraform.lock.hcl` contiene una lista de las versiones específicas de los módulos de Terraform y los proveedores de infraestructura que se han utilizado en la configuración. Estas versiones son determinadas por Terraform durante la ejecución de `terraform init` y se basan en las restricciones de versión especificadas en los archivos de configuración, como `required_providers` y `required_version`.

2. **Reproducibilidad**: Al incluir versiones específicas en el archivo `terraform.lock.hcl`, se garantiza que las mismas versiones de los módulos y proveedores se utilizarán cada vez que se ejecute Terraform en el mismo directorio de trabajo. Esto proporciona una mayor reproducibilidad y evita que las actualizaciones automáticas de los módulos y proveedores afecten la consistencia de la infraestructura.

3. **Control de versiones**: El archivo `terraform.lock.hcl` puede y debe ser incluido en tu sistema de control de versiones (por ejemplo, Git) junto con tus otros archivos de configuración de Terraform. Esto asegura que todos los miembros del equipo utilicen las mismas versiones de los módulos y proveedores y facilita la colaboración en el desarrollo de infraestructura.

4. **Actualización manual**: Aunque Terraform gestiona automáticamente la generación y actualización del archivo `terraform.lock.hcl`, no se recomienda modificar manualmente este archivo. Terraform lo gestionará por ti durante `terraform init` y garantizará que refleje de manera precisa las versiones utilizadas en tu configuración.

El archivo `terraform.lock.hcl` es un componente importante de tus proyectos de Terraform que ayuda a garantizar la consistencia y la reproducibilidad de tu infraestructura al bloquear las versiones específicas de los módulos y proveedores utilizados. Debes incluir este archivo en tu control de versiones y dejar que Terraform lo gestione automáticamente durante las operaciones de inicialización.

Este archivo es creado cuando utilizamos el comando `terraform init`. Este archivo lee la condición que nosotros le indicamos en la sección de versión y descarga el proveedor según esa condición. Luego lo que hace es bloquear la versión que nosotros configuramos para que no se produzcan errores al utilizar distintas versiones de proveedores. 

Lo que sucede cuando se especifica una versión para el proveedor es que automáticamente se genera el archivo `terraform.lock.hcl` que impide que cualquier modificación descuidada genere problemas con los módulos desarrollados en los scripts de terraform. 

Para actualizar la versión, se puede realizar lo que se especifica en el siguiente apartado. 

### Comando `terraform init -upgrade`
El comando `terraform init -upgrade` se utiliza en Terraform para actualizar automáticamente los módulos de Terraform y los proveedores de infraestructura a las últimas versiones compatibles. Este comando es útil cuando queremos asegurarnos de que estamos utilizando las últimas funcionalidades y correcciones de errores proporcionadas por los proveedores de Terraform.

Cuando ejecutamos `terraform init -upgrade`, Terraform realiza las siguientes acciones:

1. **Actualización de módulos y proveedores**: Terraform verifica si hay nuevas versiones de los módulos de Terraform y los proveedores de infraestructura especificados en tus archivos de configuración. Si se encuentran nuevas versiones, Terraform las descarga automáticamente y las instala en tu entorno de trabajo.

2. **Actualización del archivo `terraform.lock.hcl`**: Después de actualizar los módulos y los proveedores, Terraform actualiza el archivo `terraform.lock.hcl` para reflejar las versiones específicas que se utilizarán en tu configuración. Esto garantiza que las mismas versiones sean utilizadas de manera consistente cada vez que se ejecute Terraform en el mismo directorio de trabajo.

3. **Advertencias y confirmaciones**: Durante el proceso de actualización, Terraform puede mostrar advertencias o solicitar confirmaciones si se detectan cambios significativos en las versiones de los módulos o proveedores. Esto te permite revisar y confirmar los cambios antes de que se apliquen.

En cuanto a la relación con las versiones de los proveedores, `terraform init -upgrade` asegura que estés utilizando las últimas versiones de los proveedores de infraestructura compatibles con tu configuración. Esto es importante porque las nuevas versiones pueden incluir nuevas funcionalidades, mejoras de rendimiento, correcciones de errores y parches de seguridad que pueden ser beneficiosos para tu infraestructura.

Es importante tener en cuenta que aunque `terraform init -upgrade` actualiza los proveedores de infraestructura a las últimas versiones compatibles, no actualiza automáticamente la versión de Terraform en sí misma. Para actualizar la versión de Terraform, debes hacerlo manualmente instalando la nueva versión y ejecutando `terraform init` sin la opción `-upgrade`.

Por ejemplo, en una primera instancia, teníamos:  

```hcl
terraform {
    required_providers {
        aws = {
            source  = "hashicorp/aws"
            version = "= 2.50"
        }
    }
}
```

Y luego actualizamos el código a:  

```hcl
terraform {
    required_providers {
        aws = {
            source  = "hashicorp/aws"
            version = "<= 3.0"
        }
    }
}
```

Ingresamos `terraform init -upgrade` y lo que hará Terraform es buscar la nueva versión compatible con lo que especificamos en nuestro código, por ejemplo, podrá descargar la versión `3.0`. De lo contrario, de no ingresar `-upgrade`, el comando lanzará un error. 

### Importante
En el examen nos pueden preguntar sobre el cambio de versión de la siguiente manera: 

"En caso de que hayamos desplegado nuestra infraestructura con una versión 1 del proveedor, y actualmente existe la versión 2 ¿es necesario bajar la nueva versión?" 

Y la respuesta es **depende**. No es conveniente actualizarla por un acto deliverado. Esto algunas veces es necesario cuando por ejemplo nuestro proveedor saca un nuevo servicio que necesitamos tener y sólo la versión 2 la tiene implementada. En este caso deberemos de generar varias pruebas o testing antes de actualizar la infraestructura productiva, y una vez que esté todo testeado, proceder a la actualización. 

## El comando `terraform refresh`

Terraform cuando despliega la infraestructura deseada, no tiene la posibilidad de evitar que esta sea modificada del lado del proveedor de AWS. Es por esto que Terraform requiere tener alguna forma para validar si es que se han ocasionado modificacione de manera manual por fuera de su alcance y con ello saber si la infraestructura gestionada se encuentra de acuerdo con el estado deseado (*desired state*). 

Aquí es donde es importante el comando `terraform refresh`, el cual escanea la infraestructura desplegada para saber el estado actual (current state) y compararlo con el estado desado (desired state) y revisar si no hubo alguna modificación. 

Nosotros no nos veremos en la obligación de tener que invocar continuamente el comando `terraform refresh` ya que este comando se ejecuta por detrás, tanto cuando se invoca el comando `terraform plan` como el comando `terraform apply`. 

### Comando `terraform apply -auto-approve`
El comando `terraform apply -auto-approve` se utiliza en Terraform para aplicar los cambios en nuestra infraestructura sin necesidad de confirmación manual. Cuando ejecutamos este comando, Terraform realiza los siguientes pasos:

1. **Generación del plan**: Terraform analiza tus archivos de configuración y determina qué cambios deben aplicarse a tu infraestructura. Luego, genera un plan detallado que describe los recursos que serán creados, modificados o eliminados para alcanzar el estado deseado.

2. **Visualización del plan**: Antes de aplicar los cambios, Terraform muestra el plan generado en la salida estándar para que puedas revisarlo y confirmar que los cambios propuestos son los esperados y deseables.

3. **Aplicación automática**: Después de mostrar el plan, si ejecutas `terraform apply -auto-approve`, Terraform aplicará automáticamente los cambios descritos en el plan sin solicitar confirmación adicional.  Esto incluye la creación, modificación o eliminación de recursos según lo especificado en el plan.

4. **Seguimiento de la ejecución**: Mientras Terraform aplica los cambios, muestra mensajes de progreso en la salida estándar para indicar qué recursos están siendo creados, modificados o eliminados, así como cualquier error o advertencia que pueda ocurrir durante el proceso.

Es importante tener en cuenta que el uso de `-auto-approve` en `terraform apply` suprime la solicitud de confirmación, lo que significa que los cambios se aplicarán inmediatamente sin la oportunidad de revisarlos manualmente. Por lo tanto, debes usar esta opción con precaución, especialmente en entornos de producción, para evitar cambios no deseados o accidentales en tu infraestructura.

El comando completo sería: 

```bash
$ terraform apply -auto-approve
```

Esto aplicará automáticamente los cambios en tu infraestructura sin necesidad de confirmación manual.

### Práctica
Propongo la siguiente práctica para revisar lo que ya hemos estado viendo: 
1. Crea una instancia en AWS mediante código como lo venimos haciendo en lecciones anteriores. 
2. Aplicar el comando `terraform apply -auto-approve` para que Terraform no pida la confirmación de la aplicación del plan que se genera. 
3. Luego volver a ejecutar el comando `terraform plan` para notar que uno de los mensajes de notificación es porque se genera una actualización por parte de Terraform sobre el estado actual de la infraestructura que se encuentra desplegada. 

### Importante
Se desaconseja el utilizar el comando `terraform refresh` de manera manual ya que algunas veces puede conducir a situaciones indeseadas. Para retratar esto se pasan a explicar algunos casos. 

#### Caso nº 1
Primero vayamos por el primer caso. La situación es similar a la anterior al comienzo: 
1. Crea una instancia en AWS mediante código como lo venimos haciendo en lecciones anteriores. 
2. Aplica el comando `terraform apply -auto-approve` para que Terraform no pida la confirmación de la aplicación del plan que se genera. 
3. Luego volver a ejecutar el comando `terraform plan` para notar que uno de los mensajes de notificación es porque se genera una actualización por parte de Terraform sobre el estado actual de la infraestructura que se encuentra desplegada. 
4. Luego de esto, modificamos la región de AWS, y ponemos por ejemplo, la región `us-west-2`
5. Invocamos el comando `terraform plan` y lo que nos termina mostrando el plan es que de aplicarse, se generará una segunda instancia. ¿Por qué? Porque cambiamos la región a otra distinta que la del comienzo y esto generará que en vez de ir a buscar el estado actual de la instancia a la región anterior, lo vaya a buscar a `us-west-2` y por lo tanto, al no encontrar nada, lo que hará es crear una nueva instancia. 
6. Volvemos a modificar la región y la dejamos igual que antes y luego de ello volvemos a invocar `terraform plan` y veremos que el estado actual corresponde con el estado deseado, por lo tanto no hay nada que aplicar. 

#### Caso nº 2
Luego vamos por el segundo caso y donde la situación algo similiar a la anterior: 
1. Crea una instancia en AWS mediante código como lo venimos haciendo en lecciones anteriores. 
2. Aplica el comando `terraform apply -auto-approve` para que Terraform no pida la confirmación de la aplicación del plan que se genera. 
3. Luego volver a ejecutar el comando `terraform plan` para notar que uno de los mensajes de notificación es porque se genera una actualización por parte de Terraform sobre el estado actual de la infraestructura que se encuentra desplegada. 
4. Luego de esto, modificamos la región de AWS, y ponemos por ejemplo, la región `us-west-2`
5. Pero esta vez invocamos el comando `terraform refresh` y lo que nos termina mostrando el plan es que de aplicarse el plan, se generará una segunda instancia. ¿Por qué? Por lo que digimos anteriormente, porque cambiamos la región a otra distinta que la del comienzo y esto generará que en vez de ir a buscar el estado actual de la instancia a la región anterior, lo vaya a buscar a `us-west-2` y por lo tanto, al no encontrar nada, lo que hará es crear una nueva instancia. 
6. ¡Pero el problema principal! Es que este comando **elimina** todo el contenido del archivo `terraform.tfstate` y genera uno nuevo por lo que dijimos anteriormente, que del lado de Terraform, piensa que al ser una nueva instancia, deberá de configurar todo de nuevo y lanzar un nuevo plan en otra región. 
7. En este escenario, podemos hacer uso del archivo `terraform.tfstate.backup` y copiar toda la configuración anterior desde este archivo al archivo `terraform.tfstate`. 
8. Volvemos a modificar la región y la dejamos igual que antes y luego de ello volvemos a invocar `terraform plan` y veremos que el estado actual corresponde con el estado deseado, por lo tanto no hay nada que aplicar. 

### Conclusiones
Por lo tanto, es preferible o deseable que el comando `terraform refresh` **no se ejecute de manera manual**, para evitar que el archivo `terraform.tfstate` sea elimando como en el caso anterior. Este comando siempre se ejecutará por detrás del comanado `terraform plan` y este último es más seguro ya que como vimos anteriormente, no eliminaría el contenido del archivo `terraform.tfstate`. 

### Importante
El comando `terraform refresh` ha quedado **en desuso** en versiones actuales, por lo tanto el único modo de poder trabajar con él es mediante los comandos anteriormente mensionados: `terraform plan` y `terraform apply`. 

## AWS Provider - Authentication Configuration
Hasta el momento las llaves de acceso (*access_key*) y las llaves secretas (*secret_key*) las hemos puesto en el bloque de provider. Si bien este abordaje permite tener la posibilidad de generar una conexión correcta, en verdad es que no es una buena práctica de seguridad el tener las llaves publicadas. Esto puede llevar a que las llaves se vean comprometidas en alguna plataforma como Github, y de esta forma, se compromete la seguridad. 

Por lo tanto **se prohibe terminantemente** que estos datos sensibles se encuentren dentro del bloque `provider`. Ahora bien, ¿cómo se puede solucionar este problema?

### AWS CLI
Cuando nosotros trabajamos con Terraform desde la perspectiva de AWS, lo que debemos de tener en cuenta es de instalar la CLI de AWS. Si bien no trabajaremos con ella mediante Terraform, si es útil para poder generar, configurar y almacenar las credenciales y los profiles que nos permitirán trabajar de manera más cómoda con Terraform. 

Sobre esto se puede ver: 
- [Instalación de AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [Configuración de AWS CLI y Terraform](https://dev.to/phenomena_ekuss_c2bd80894/step-by-step-guide-to-setting-up-terraform-aws-cli-and-vs-code-46gg)

### Solución nº 1
Por defecto, no deberíamos tener problemas si no generamos las *access_key* ni las *secret_key* en el bloque `provider`. Esto sería correcto, pero siempre que no se tenga un perfil nuevo gestionado en `~/.aws/credentials`. 

Por lo tanto nuestro código quedaría así de simple: 

```terraform
provider "aws" { 
    region     = "us-east-1" 
}

resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.micro"
    tags          = {
    Name = "First TerraformEC2"
    } 
}
```

Los directorios donde mirará Terraform en caso de no proveerle ninguna de las llaves de acceso para Linux o Max, serán: 
```bash
- `$HOME/.aws/config`
- `$HOME/.aws/credentials`
```

### Solución nº 2
La otra solución sería implementar en el bloque `provider` las opciones `shared_config_files` y `shared_credentials_files` de la siguiente manera: 

```terraform
provider "aws" { 
    shared_config_files      = ["~/.aws/config"] 
    shared_credentials_files = ["~/.aws/credentials"] 
    profile                  = "custom" 
}

resource "aws_instance" "terraformec2" { 
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.micro"
    tags          = {
        Name = "First TerraformEC2" 
    } 
}
```

¿Cuál es el problema con esta configuración? Que todos los desarrolladores del equipo que compartan estos archivos deberan de situar los mismos archivos de configuración en el mismo lugar que especifica la configuración. Esto sabemos que es imposible, por lo tanto debemos de tener otra solución más elegante. 

### Solución nº 3
Como se dijo anteriormente, la otra solución es instalar la CLI de AWS para poder gestionar mejor las credenciales. Esto se puede ver en el siguiente enlace: 

* https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

La diferencia que parece haber entre GNU/Linux y Windows es que en este último no hace falta publicar o exportar la variable `$AWS_PROFILE`. 

En la documentación de Terraform perteneciente al provider AWS, es posible ver estas soluciones que hemos estado revisando pero [con más detenimiento](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication-and-configuration)

