
+++ 
date = '2025-12-08T22:34:47-03:00'
draft = false
title = '04 - Configuraciones - Parte nº 5'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
+++

# Archivo para definición de variables (TFVARS)
## Esquema

Tenemos dos tipos de archivos que Hashicorp recomienda tener en proyectos grandes: 
1. `variables.tf`
2. `terraform.tfvars`

## Definición

1. **variables.tf**: Este archivo es utilizado para definir las variables que serán utilizadas en nuestra configuración de Terraform. Las variables en Terraform son
útiles para parametrizar nuestra infraestructura y permitir una configuración más dinámica y reutilizable. En el archivo `variables.tf`, defines las variables junto
con su tipo (como string, número, lista, etc.) y puedes asignarles un valor por defecto si lo deseas.

Ejemplo de un archivo `variables.tf`:  

```terraform
variable "region" {
    type    = string
    default = "us-east-1"
}

variable "instance_type" {
    type    = string
    default = "t2.micro"
}
```

2. **terraform.tfvars**: Este archivo es utilizado para asignar valores concretos a las variables definidas en `variables.tf`. Es un archivo de configuración que
   contiene los valores específicos que serán utilizados cuando ejecutemos nuestra configuración de Terraform. Estos valores pueden ser diferentes según el entorno
(desarrollo, pruebas, producción, etc.) o según los requisitos de la infraestructura.

Ejemplo de un archivo `terraform.tfvars`: 

```terraform
region        = "us-west-2"
instance_type = "t3.micro"
```

La relación entre estos dos archivos es que las variables definidas en `variables.tf` son referenciados en nuestra configuración de Terraform, mientras que los valores
concretos para esas variables se proporcionan en el archivo `terraform.tfvars`.

Cuando ejecutamos estos scripts, Terraform buscará automáticamente el archivo `terraform.tfvars` en el directorio de trabajo y utilizará los valores allí definidos para
reemplazar las variables correspondientes en nuestra configuración. Esto nos permite tener una separación clara entre la lógica de nuestra infraestructura y los valores
específicos que pueden variar entre entornos o dependiendo de los requisitos de nuestra aplicación.

Como se dijo anteriormente, se recomienda separar en 3 tipos de archivos la utilización de nuestras variables en Terraform: 

1. Por un lado tenemos el propio script de Terraform, donde se codifican cada uno de los recursos a construir. 
2. En el archivo `variables.tf` ingresamos la definición de las variables a utilizar como puede ser el valor por defecto, la descripción, el tipo de dato que aceptará, etc; y luego,
3. En el archivo `terraform.tfvars` asignamos el valor a esas variables. Hashicorp recomienda que el nombre sea `terraform`, ya que si se le asigna otro nombre, se deberá de indicar en el comando `apply` el tipo de variable que deberá tomar para las variables definidas de la siguiente manera: `terraform apply -var-file=prod.tfvars`

## Separación entre ambientes

Lo interesante es que el archivo `.tfvars` puede estar dividido por ejemplo entre la cantidad de ambientes que tengamos, por ejemplo entre los ambientes `dev` y
`prod`. Estos dos ambientes compartirán la definición de las variables pero no así su valor.

Esto lo que permite es invocar el comando `terraform apply -var-file=prod.tfvars` lo que terminará construyendo un ambiente con los valores productivos. 

En cambio, si utilizamos el comando `terraform apply -var-file=dev.tfvars` utilizaremos los valores para el ambiente de desarrollo. 

## Comando: `terraform apply -var-file=<FILE.TFVARS>`
El comando `terraform apply -var-file=<file.tfvars>` se utiliza en Terraform para aplicar los cambios en la infraestructura definida en nuestra configuración, tomando
los valores de variables especificados en un archivo de variables externo (usualmente con extensión `.tfvars`).

Aquí una explicación detallada de cada parte del comando:

- **`terraform apply`**: Este es el comando principal de Terraform que se utiliza para aplicar los cambios definidos en nuestra configuración de Terraform. Cuando
  ejecutas `terraform apply`, Terraform analiza los archivos de configuración en nuestro directorio de trabajo y determina qué acciones deben tomarse para alcanzar
el estado deseado de la infraestructura.

- **`-var-file=<file.tfvars>`**: Esta parte del comando especifica el archivo de variables externo que contiene los valores para las variables definidas en nuestra
  configuración de Terraform. Con esta opción, puedes proporcionar valores para las variables definidas en tus archivos `variables.tf` de una manera más organizada
y separada de la configuración principal.

- **`<file.tfvars>`**: Esto debe ser reemplazado por el nombre del archivo de variables externo que deseas utilizar. Por lo general, estos archivos tienen una
  extensión `.tfvars` (por ejemplo, `terraform.tfvars`, `dev.tfvars`, `production.tfvars`, etc.).

Al ejecutar este comando, Terraform aplicará los cambios en nuestra infraestructura según la configuración definida en tus archivos principales de Terraform
(`main.tf`, `variables.tf`, etc.), tomando los valores de las variables del archivo especificado con `-var-file`. Esto permite una mayor flexibilidad y
reutilización de la configuración de Terraform al separar la lógica de la infraestructura de los valores específicos de cada entorno o configuración.

## Práctica
### Comportamiento normal
Para esta práctica se crearán 3 archivos: 
1. **`44_main.tf`**: donde se tendrá la definición de los recursos. 
2. **`44_variables.tf`**: donde se tendrá la definición de las variables. 
3. **`terraform.tfvars`**: el cual contendrá los valores para cada una de las variables. Es muy importante que el nombre sea `terraform.tfvars`, ya que es el nombre
   que por default terraform buscará para poder llevar a cabo la interpolación de valores al ejecutar `terraform plan` es en base a este nombre. 

En el archivo `44_main.tf` vamos a poder ver algo como esto: 

```terraform
resource "aws_instance" "terraformEC2" {
  ami           = var.ami
  instance_type = var.instance_type

  tags   = {
    Name = "Terraform EC2"
  }
}
```

Se puede ver que tanto en la variable `ami` como en `instance_type` hacen referencia a valores de entrada (input values) que se encuentran definidos en el archivo
`44_variables.tf`: 

```terraform
variable "ami" {}
variable "instance_type" {}
```

Y luego, estas variables pueden tomar el valor del archivo `terraform.tfvars`: 

```terraform
ami           = "ami-0c101f26f147fa7fd"
instance_type = "t2.micro"
```

En el archivo nº 2, podremos definir de forma más granular el tipo de variable, ya que si vamos a la [documentación](https://developer.hashicorp.com/terraform/language/values/variables#arguments), podemos encontrar distintos argumentos:

Una vez que ejecutamos `terraform plan` podremos ver si la salida es lo que esperábamos: 

```bash
+ resource "aws_instance" "terraformEC2" {
  + ami                                  = "ami-0c101f26f147fa7fd"
  + arn                                  = (known after apply)
  + associate_public_ip_address          = (known after apply)
  ...
  + instance_lifecycle                   = (known after apply)
  + instance_state                       = (known after apply)
  + instance_type                        = "t2.micro"
  + ipv6_address_count                   = (known after apply)
  ...
  + security_groups                      = (known after apply)
  + source_dest_check                    = true
  + spot_instance_request_id             = (known after apply)
  + subnet_id                            = (known after apply)
  + tags                                 = {
      + "Name" = "Terraform EC2"
    }
  + tags_all                             = {
      + "Name" = "Terraform EC2"
    }
  + tenancy                              = (known after apply)
  ...
  + vpc_security_group_ids               = (known after apply)
}

Plan: 1 to add, 0 to change, 0 to destroy.
```

Como podemos ver, los valores para `ami` y para `instance_type` fueron generados con éxito. 

### Valores por defecto nº 1
En este caso si seguimos teniendo la misma disposición de archivos: 
1. `44_main.tf`: donde se tendrá la definición de los recursos. 
2. `44_variables.tf`: donde se tendrá la definición de las variables y sus valores por default
3. `terraform.tfvars`: el cual contendrá los valores para cada una de las variables, pero en este caso no tendrá ningún valor

En este caso el archivo `44_main.tf` quedará igual, pero el segundo archivo `44_variables.tf` tendrá los valores de las variables por defecto y el archivo
`terraform.tfvars` no contendrá ningún valor. 

Por lo tanto, el archivo `44_variables.tf` contiene los siguientes valores: 

```terraform
variable "ami" {
  default     = "ami-11222333444555"
  description = "This is an AMI from us-east-1"
}

variable "instance_type" {
  default     = "t2.super.micro"
  description = "This is for develop use"
}
```

Como podemos observar, los valores por defecto para ambas variables son ficticios. Como el archivo `terraform.tfvars` se encuentra vacío, entonces Terraform al no
encontrar valores para ser asociadas a las variables definidas, les asignará el valor por defecto. 

Por lo tanto, al invocar el comando `terraform plan` obtenemos lo siguiente: 

```bash
+ resource "aws_instance" "terraformEC2" {
      + ami                                  = "ami-11222333444555"
      + arn                                  = (known after apply)
      ...
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      ...
      + instance_type                        = "t2.super.micro"
      + ipv6_address_count                   = (known after apply)
      ...
```

### Valores por defecto nº 2
Volvemos a tener el mismo esquema anterior, pero: 
1. **`44_main.tf`**: donde se tendrá la definición de los recursos. 
2. **`44_variables.tf`**: donde se tendrá la definición de las variables y sus valores por default
3. **`terraform.tfvars`**: el cual contendrá los valores para cada una de las variables, pero en este caso tendrá un valor asociado de forma correcta. 

El archivo `44_variables.tf` seguirá teniendo los valores por defecto, pero el archivo `terraform.tfvars` tendrá también valores los cuales tendrán mayor precedencia que los definidos como `defaults`. Por lo tanto el contenido de estos dos archivos será: 

1. `44_variables.tf` 

```terraform
variable "ami" {
  default     = "ami-11222333444555"
  description = "This is an AMI from us-east-1"
}

variable "instance_type" {
  default     = "t2.super.micro"
  description = "This is for develop use"
}
```

2. `terraform.tfvars`

```terraform
ami           = "ami-0c101f26f147fa7fd"
instance_type = "t2.micro"
```

Por lo tanto, al invocar el comando `terraform plan` obtenemos los valores definidos en `terraform.tfvars` y no en el archivo ` 44_variables.tf`: 

```bash
+ resource "aws_instance" "terraformEC2" {
  + ami                                  = "ami-11222333444555"
  + arn                                  = (known after apply)
  ...
  + instance_state                       = (known after apply)
  + instance_type                        = "t2.super.micro"
  + ipv6_address_count                   = (known after apply)
  ...
  + subnet_id                            = (known after apply)
  + tags                                 = {
      + "Name" = "Terraform EC2"
    }
  + tags_all                             = {
      + "Name" = "Terraform EC2"
    }
  + tenancy                              = (known after apply)
  + user_data                            = (known after apply)
  + user_data_base64                     = (known after apply)
  + user_data_replace_on_change          = false
  + vpc_security_group_ids               = (known after apply)
}
```

##  Nota sobre el nombre del archivo `tfvars`
**Como se dijo anteriormente**: ¡Es muy importante que el nombre donde se encuentran los valores de las variables sea `terraform.tfvars` ya que es el nombre que por
default terraform buscará para poder llevar a cabo la interpolación de valores al ejecutar `terraform plan`. 

Si el nombre **ES DISTINTO**, entonces deberemos de utilizar la siguiente secuencia de comandos 

`terraform plan -var-file=<FILE.tfvars>` 

y luego 

`terraform apply -var-file=<FILE.tfvars>`. 

Esto último es útil cuando se tienen distintos ambientes y por lo tanto se quiere desplegar infraestructura distintas, por lo tanto en este caso por ejemplo tendremos: 

1. Un archivo denominado `dev.tfvars` el cual tiene los valores para las variables del ambiente de desarrollo
2. Una vez que queramos generar el plan invocamos `terraform plan -var-file="dev.tfvars"`
3. Si estamos satisfechos con el plan, ingresamos `terraform apply -var-file="dev.tfvars"`

# Enfoques para la asignación de variables 

Si nosotros definimos una variable, es necesario que se defina la variable en algún archivo, como puede ser `variables.tf`, pero además de ello debe tener un valor
asociado a ella. En caso de que esto último no se defina, es decir que no tenga valor asignado, entonces al ejecutar por ejemplo el comando `terraform plan` lo que
sucederá es que Terraform nos pedirá que ingresemos de manera manual el valor de la variable. 

Pero luego de haber ingresado el valor cuando se realizó el comando `terraform plan` también se deberá de ingresar el valor nuevamente cuando se invoque el comando `terraform apply` ya que el valor no queda cacheado y por lo tanto se pierde. 

### Nota sobre el ingreso manual
Por lo tanto, en caso de que hayamos definido todas las partes de una variable y al ejecutar la planificación, nos solicita el ingreso del valor de la variable,
entonces es un síntoma acerca de que Terraform no encuentra el archivo donde se alojan los valores de las variables. Por lo tanto, habrá que ver si el nombre
corresponde a `terraform.tfvars` o si es distinto a este, entonces utilizar `terraform plan -var-file=<FILE.tfvars>`

### Definición de variables

Los 4 lugares por los que Terraform buscará para cargar los valores de las variables serán: 

1. La definición por default de los valores de las variables
2. Archivo de definición de variables (`tfvars`)
3. Variables de entorno
4. Configuración de variables mediante la línea de comando. 

### Variable Default
El primer lugar donde va a tomar los valores será de la propia definición de la variable, por ejemplo: 

```terraform
variable "app_port" { 
    default = "8080" 
}
```

En este será el primer lugar donde tomará los valores de las variables. En caso de que no esté definido en este lugar, pasa al próximo lugar para evaluar el valor. 

### Tfvars files

El próximo lugar donde mirará Terraform para asignar el valor a la variable será en los archivos definidos con la extensión `.tfvars`, como se comentó en las secciones anteriores. Por ejemplo, siguiendo con la variable definida anteriormente, podríamos tener un archivo denominado `terraform.tfvars` donde se explicite lo siguiente: 

```terraform
app_port = "8080"
```

En caso de que tampoco tengamos definida el valor de las variables dentro de un archivo de tipo `.tfvars` entonces se sigue al próximo escaño. 

### Asignación de valores mediante CLI
También podemos definir un valor de las variables mediante el uso de la línea de comandos utilizando el parámetro `-var="app_port=8080"`, donde el parámetro `-var` permite asignar el valor de una variable determinada mediante la línea de comando. En la filmina podemos ver que se puede ajustar tanto al comando `terraform plan` como al comando `terraform apply` por lo tanto, quedaría de la siguiente manera: 

1. `terraform plan -var="app_port=8080"`
2. `terraform apply -var="app_port=8080"`

Recordar que de esta manera, los valores utilizados en el punto nº 1 **NO** quedarán guardados en el caché, por lo tanto si utilizamos seguidamente el comando `apply`
deberemos de ingresar nuevamente el parámetro `-var` para significar el valor de la variable nuevamente. 

Como se puede observar, el nombre de la variable ingresada mediante la CLI no deberá de contener al comienzo el prefijo `var`. En caso de que la variable en nuestro
código sea `var.app_port`, cuando la ingresamos por la CLI, deberemos de ingresarla de la siguiente manera: 

`terraform plan -var="app_port=8080"`

### Variables de entorno
Por último, tenemos la posibilidad de configurar las variables mediante **variables de entorno**. Para declarar una variable de entorno, debemos de seguir una
convención para generar el nombre, la cual se encuentra documentada [aquí](https://developer.hashicorp.com/terraform/cli/config/environment-variables#tf_var_name)

Podemos ver que debemos de nombrar la variable de entorno anteponiéndole el nombre `TF_VAR_` por lo tanto, en el ejemplo anterior deberíamos de exportar nuestra variable de la siguiente manera:  

```terraform
$ export TF_VAR_app_port="8080"
```

Por lo tanto si en nuestro script de Terraform se encuentra configurado de la siguiente forma: 

```terraform
variable "app_port" {}
```

Y luego invocamos `terraform plan`, automáticamente Terraform buscará como última instancia el nombre `TF_VAR_APP_PORT` y de encontrarlo en las variables de
entorno, entonces lo asigna a la variable correspondiente. 

## Variables de entorno en Linux/Mac
Para configurar las variables de ambiente en Linux, se puede buscar en internet cómo realizarlo. Por ejemplo aquí hay [un link](https://linuxize.com/post/how-to-set-and-list-environment-variables-in-linux/) para poder entender los comandos `printenv` o `export`. 

## Continuará...
En las próximas lecciones veremos la precedencia a la hora de definir los valores para nuestras variables y cómo podemos configurar un tipo de datos a nuestras
variables. 

¡Hasta pronto!
