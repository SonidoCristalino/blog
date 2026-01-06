+++ 
date = '2026-01-04T22:34:47-03:00'
draft = true
title = '04 - Configuraciones - Parte nº 9'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
series = ["Curso Terraform"]
+++

# Conditional Expressions

En Terraform, las expresiones condicionales se utilizan para evaluar una condición y tomar decisiones basadas en el resultado de esa evaluación. Esto
puede ser útil para controlar el comportamiento de recursos y la configuración en función de ciertas condiciones.

Las expresiones condicionales en Terraform se pueden utilizar en varios contextos, como en el valor de un atributo de un recurso, en un bloque de
recurso condicional, en la creación de variables condicionales, entre otros.

Aquí tenemos un ejemplo de cómo usar una expresión condicional en Terraform: 

```terraform
variable "enable_s3_bucket" {
    default = true
}

resource "aws_s3_bucket" "example" {
    bucket = var.enable_s3_bucket ? "example-bucket" : null
}
```

En este ejemplo, se define una variable `enable_s3_bucket` que indica si se debe crear un bucket de Amazon S3 o no. Luego, en el recurso
`aws_s3_bucket`, se utiliza una expresión condicional para determinar el valor del atributo `bucket`. Si `enable_s3_bucket` es `true`, se asigna el
valor `"example-bucket"` al atributo `bucket`, de lo contrario se asigna `null`.

Las expresiones condicionales en Terraform siguen la sintaxis `condición ? valor_si_verdadero : valor_si_falso`. En este caso, `condición` es una
expresión booleana que evalúa si `enable_s3_bucket` es `true`. Si la condición es verdadera, se utiliza el valor después del `?`, de lo contrario se
utiliza el valor después del `:`.

Las expresiones condicionales pueden volverse más complejas al combinarlas con otras funciones y operadores lógicos, lo que te permite crear lógica
condicional más avanzada en tu código de Terraform.

Hay que recordar que la expresión condicional sigue la siguiente sintaxis:  `condición ? valor_si_verdadero : valor_si_falso`.

Aquí proponemos tener una variable denominada `is_test` que permitirá crear o bien un recurso para el ambiente de `dev` en caso de que se encuentre en
`true`, o de lo contrario, crear un recurso para el ambiente de `prod`. 

Por ejemplo, si nosotros tenemos el siguiente código: 

```terraform

resource "aws_instance" "prod" {
  ami           = "ami-0d7a109bf30624c99"
  instance_type = "t2.xlarge"

  tags   = {
    Name = "Prod instance"
  }
}

resource "aws_instance" "dev" {
  ami           = "ami-0d7a109bf30624c99"
  instance_type = "t2.micro"

  tags   = {
    Name = "Dev Instance"
  }
}
```

Si nosotros generamos un archivo de configuración con estos dos recursos y corremos `terraform plan`, esto lo que hará es generar los dos recursos. Lo
que nosotros buscamos es que o bien se cree uno o bien se cree el otro, dependiendo del valor de una variable `is_test`. 

Para esto, en el mismo archivo de configuración creamos una variable denominada `is_test`. Y luego a cada bloque de instancias le agregamos `count =
var.is_test? 1 : 0`. Hay que recordar que el atributo `count` permite generar la cantidad de copias del mismo recurso. 

## Práctica

### Ejemplo nº 1

Archivos utilizados: 

1. `51_main.tf`
2. `52_variables.tf`
3. `terraform.ftvars` 

Implementación:

1. `51_main.tf`

```terraform
variable "is_test" {
  type    = bool
  default = true
}

resource "aws_instance" "prod" {
  ami           = "ami-0d7a109bf30624c99"
  instance_type = "t2.xlarge"
  count         = var.is_test ? 0 : 1

  tags          = {
    Name        = "Prod Instance"
  }
}

resource "aws_instance" "dev" {
  ami           = "ami-0d7a109bf30624c99"
  instance_type = "t2.micro"
  count         = var.is_test ? 1 : 0

  tags          = {
    Name        = "Dev Instance"
  }
}
```
Por lo tanto, la salida del comando `terraform plan` cuando el valor de la variable `is_test` es `true`, será: 

```bash
  + resource "aws_instance" "dev" {
      + ami                                  = "ami-0d7a109bf30624c99"
      + arn                                  = (known after apply)
      ...
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      ...
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      ...
      ...
      ...
        }
      + tags_all                             = {
          + "Name" = "Dev Instance"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

En cambio, la salida del comando `terraform plan` cuando el valor de la variable `is_test` es `false`, será: 

```bash
  + resource "aws_instance" "prod" {
      + ami                                  = "ami-0d7a109bf30624c99"
      + arn                                  = (known after apply)
      ...
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      ...
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.xlarge"
      + ipv6_address_count                   = (known after apply)
      ...
      ...
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "Prod Instance"
        }
      + tags_all                             = {
          + "Name" = "Prod Instance"
        }
      + tenancy                              = (known after apply)
      ...
      
Plan: 1 to add, 0 to change, 0 to destroy.
```

### Ejercicio nº 2

Otra de las cosas que se pueden hacer es ingresar la cantidad de la configuración en 3 para que sea más evidente el cambio: 

```terraform
resource "aws_instance" "dev" {
  ami           = "ami-0d7a109bf30624c99"
  instance_type = "t2.micro"
  count         = var.is_test ? 3 : 0

  tags          = {
    Name        = "Dev Instance"
  }
}
```

Y por otro lado, distribuimos la definición y la asignación de la variable `is_true` a través de las otros archivos. Por lo tanto la salida del comando `terraform plan` es la siguiente:

```bash
  + resource "aws_instance" "dev" {
      + ami                                  = "ami-0d7a109bf30624c99"
      ..
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      ..
      + instance_type                        = "t2.micro"
      ..
      ..
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "Dev Instance"
        }
      + tags_all                             = {
          + "Name" = "Dev Instance"
        }
      ..

Plan: 3 to add, 0 to change, 0 to destroy.
```

## Local Values

En Terraform, un "local value" es una forma de definir una variable local dentro de un archivo de configuración de Terraform. Estas variables locales
se utilizan para calcular valores que pueden ser reutilizados dentro del mismo archivo de configuración, lo que permite simplificar la configuración y
mejorar la legibilidad del código.

Los valores locales se definen utilizando el bloque `locals` en un archivo de configuración de Terraform. Aquí tienes un ejemplo básico de cómo se
define un valor local: 

```terraform
locals {
    instance_count = 3
}
```

En este ejemplo, se define un valor local llamado `instance_count` con un valor de `3`. Una vez definido, este valor local puede ser referenciado en
otras partes del archivo de configuración de Terraform utilizando la sintaxis `local.nombre_del_valor`, donde `nombre_del_valor` es el nombre del
valor local definido.

Por ejemplo, podrías usar el valor local `instance_count` en la configuración de un recurso de esta manera: 

```hcl
resource "aws_instance" "example" {
    count = local.instance_count
    # Otras configuraciones...
}
```

En este caso, el valor de `count` para el recurso `aws_instance` se establecerá en el valor de `instance_count` definido como variable local.

Los valores locales pueden contener expresiones más complejas, incluidas referencias a otras variables y funciones de Terraform. Esto permite calcular
valores basados en lógica más avanzada dentro de tu configuración de Terraform.

Los valores locales en Terraform son una forma conveniente de definir y reutilizar valores calculados dentro de un archivo de configuración, lo que
puede ayudar a hacer que tu código sea más modular y fácil de mantener.

En este caso por ejemplo, lo que utilizamos es el atributo `tags` que siempre es común a muchos recursos de AWS. Por lo tanto, para no tener que
repetir código, se puede definir una variable local y poder de esta manera hacer más legible el código. 

## Práctica

### Ejemplo nº 1

Archivos: 
1. `52_main.tf`
2. `52_variables.tf`
3. `terraform.ftvars` 

Implementación: 

1. `52_main.tf`

```terraform
resource "aws_instance" "dev" {
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.micro"
    tags          = local.common_tags
    count         = var.is_test ? 3 : 0
}

resource "aws_instance" "prod" {
    ami           = "ami-0d7a109bf30624c99"
    instance_type = "t2.xlarge"
    tags          = local.common_tags
    count         = var.is_test ? 0 : 1
}
```

2. `52_variables.tf`

```terraform
variable "is_test" {
    type    = bool
    default = true
}

locals {
    common_tags = {
        Owner   = "Devops Teams"
        service = "Backend"
    }
}
```


3. `terraform.ftvars`   

```terraform
    is_test = true
```

Por lo tanto, la salida del comando `terraform plan` será: 

```bash
  + resource "aws_instance" "dev" {
      + ami                                  = "ami-0d7a109bf30624c99"
      ...
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      ...
      + instance_type                        = "t2.micro"
      ...
      + placement_partition_number           = (known after apply)
      ...
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Owner"   = "Devops Teams"
          + "service" = "Backend"
        }
      + tags_all                             = {
          + "Owner"   = "Devops Teams"
          + "service" = "Backend"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)
    }

Plan: 3 to add, 0 to change, 0 to destroy.
```

Por lo tanto, vemos que genera 3 instancias porque la variable `is_test` se encuentra en `true` y además podemos observar que los tags se generan de
forma correcta con los dos valores ingresados de forma local. 

### Algunos puntos importantes

También esta nomenclatura permite un abanico de oportunidades como se puede ver en la filmina, donde se permite establecer un nombre según si viene
definido o no en la variable `var.name` y esto se puede agregar en cada una de las instancias y por lo tanto se tiene repetido el mismo comportamiento
en todas los recursos que iremos a construir. 

```terraform
locals {
    environment = "production"
    instance_type = local.environment == "production" ? "t2.large" : "t2.micro"
}
```

En este ejemplo, se define un valor local llamado `instance_type` que se utiliza para especificar el tipo de instancia de AWS EC2 que se utilizará. La
expresión condicional `local.environment == "production" ? "t2.large" : "t2.micro"` evalúa si la variable local `environment` es igual a
`"production"`. Si es verdadero, asignará `"t2.large"` a `instance_type`; de lo contrario, asignará `"t2.micro"`. 

Este ejemplo ilustra cómo podemos usar una expresión condicional dentro de un valor local para asignar diferentes valores basados en una condición
dada. Esto puede ser útil cuando necesitas configuraciones diferentes según el entorno, como producción o desarrollo.

Hay varias situaciones comunes en las que se pueden utilizar los valores locales en Terraform:

1. **Reutilización de valores calculados**: Cuando necesitas calcular un valor basado en una expresión compleja o una combinación de otras variables y
   quieres reutilizar este valor en múltiples lugares dentro de tu configuración.

2. **Parametrización de configuraciones repetitivas**: Cuando tienes configuraciones repetitivas que podrían necesitar cambios en el futuro y
   prefieres definir estos valores una sola vez como valores locales para facilitar su mantenimiento y actualización.

3. **Definición de constantes**: Cuando tienes valores que son constantes dentro de tu configuración y prefieres definirlos una vez como valores
   locales en lugar de repetirlos en varios lugares.

4. **Aumentar la legibilidad del código**: Cuando quieres hacer que tu código sea más legible y comprensible al asignar nombres significativos a
   valores calculados o constantes.

5. **Simplificación de lógica condicional**: Cuando necesitas simplificar la lógica condicional dentro de tu configuración al calcular valores
   intermedios que se utilizan en múltiples lugares.

6. **Facilitar la modularidad**: Cuando estás construyendo módulos reutilizables y quieres permitir que los usuarios de esos módulos personalicen
   ciertos valores sin tener que modificar directamente la lógica del módulo.

7. **Cálculos de valores predeterminados**: Cuando deseas establecer valores predeterminados para configuraciones que pueden ser sobrescritos por el
   usuario, pero también necesitas calcular valores predeterminados basados en otras variables.

Los valores locales son una herramienta versátil en Terraform que pueden ser utilizados en una variedad de situaciones para simplificar y
mejorar la configuración de la infraestructura. Su uso puede ayudar a hacer que el código sea más modular, fácil de mantener y comprensible.

### Otro ejemplo (de ChatGPT)

En Terraform, puedes usar valores locales para definir expresiones y lógica que se reutilizan en tu configuración. Un valor local puede incluir
expresiones condicionales (`condition ? true_value : false_value`) para decidir qué valor asignar en función de una condición.

Supongamos que quieres definir el tipo de instancia de AWS en función del entorno (producción o desarrollo). 

```terraform
# Definición de la variable environment
variable "environment" {
    type    = string
    default = "development"
}

# Definición de variables de AMI para cada entorno
variable "ami_production" {
    type    = string
    default = "ami-prod-12345678"
}

variable "ami_development" {
    type    = string
    default = "ami-dev-87654321"
}

# Definición de valores locales
locals {
    instance_type = var.environment == "production" ? "t2.large" : "t2.micro"
    ami_id        = var.environment == "production" ? var.ami_production : var.ami_development
}

# Recurso de instancia AWS
resource "aws_instance" "example" {
    ami           = local.ami_id
    instance_type = local.instance_type
    tags          = {
        Name        = "example-instance"
        Environment = var.environment
    }
}
```

Explicación:

1. **Definición de la variable `environment`**:
- Define la variable `environment` de tipo `string`, con el valor por defecto `development`.

2. **Definición de las variables `ami_production` y `ami_development`**:
- Estas variables contienen las IDs de las AMIs para producción y desarrollo, respectivamente.

3. **Definición de valores locales**:
- **`instance_type`**: Utiliza una expresión condicional para establecer el tipo de instancia en función del entorno. Si el entorno es `production`,
  el tipo de instancia será `t2.large`, de lo contrario será `t2.micro`.
- **`ami_id`**: Similarmente, selecciona la AMI adecuada en función del entorno.

4. **Uso de valores locales en el recurso `aws_instance`**:
- El recurso `aws_instance` utiliza los valores locales `local.ami_id` y `local.instance_type` para configurar la instancia de AWS.

Este ejemplo muestra cómo usar valores locales y expresiones condicionales para ajustar configuraciones dinámicamente en Terraform según el entorno u otras condiciones.

# Terraform Functions

## Lista de funciones

Para ver todo el listado de funciones built-in que contiene Terraform, podemos remontarnos a la [siguiente página](https://developer.hashicorp.com/terraform/language/functions)

Terraform categoriza cada una de las funciones según su finalidad. Eso lo podemos observar en el menú izquierdo de la pantalla. Debemos recordar que
Terraform **NO SOPORTA** funciones generadas por el usuario. 

## Definición de Funciones en Terraform

En Terraform, las funciones son herramientas que permiten realizar manipulaciones y operaciones en los datos dentro de tus archivos de configuración.
Estas funciones proporcionan una forma poderosa de realizar transformaciones, cálculos y manipulaciones en los valores de tus recursos, variables y
otros elementos dentro de tu configuración.

Las funciones en Terraform se utilizan principalmente para:

1. **Manipulación de cadenas**: Terraform proporciona varias funciones para trabajar con cadenas, como concatenación, conversión de mayúsculas/minúsculas, división y reemplazo de cadenas.
2. **Operaciones numéricas**: Puedes realizar operaciones matemáticas simples como suma, resta, multiplicación y división utilizando funciones.
3. **Trabajo con listas y mapas**: Terraform ofrece funciones para trabajar con listas y mapas, como agregar elementos, eliminar elementos, filtrar elementos, etc.
4. **Generación de valores dinámicos**: Puedes utilizar funciones para generar valores dinámicos basados en condiciones o en otros datos dentro de tu configuración.
5. **Validación y manipulación de datos**: Las funciones pueden ayudarte a validar y limpiar datos antes de utilizarlos en tus recursos.
6. **Manipulación de fechas y horas**: Terraform proporciona funciones para trabajar con fechas y horas, como obtener la fecha actual, convertir formatos de fecha, agregar o restar tiempo, etc.

Por ejemplo, aquí hay un uso básico de una función en Terraform que concatena dos cadenas: 

```hcl
resource "aws_s3_bucket" "example" {
    bucket = "example-${lower(var.environment)}"
}
```

En este caso, la función `lower()` se utiliza para convertir el valor de la variable `environment` a minúsculas antes de concatenarlo con la cadena
`"example-"`.

Las funciones en Terraform te permiten realizar una amplia variedad de manipulaciones y cálculos en tus datos, lo que te ayuda a crear configuraciones
más dinámicas y flexibles. Puedes encontrar una lista completa de funciones disponibles en la documentación oficial de Terraform.

Es importante notar que Terraform no soporta funciones generadas por el usuario. Sin embargo, existen funciones generadas dentro de Terraform para dar
soporte a múltiples escenarios y están divididos (como se puede ver en la filmina) en varios tipos. 

En la [documentación de Terraform](https://developer.hashicorp.com/terraform/language/functions) podremos ver cada una de las funciones (en el menú de
la izquierda) dependiendo de su utilidad.


### Comando: `terraform console`

Para poder consultar el funcionamiento de las funciones built-in de Terraform, tenemos la posibilidad de invocar al comando `terraform console`. 

El comando `terraform console` es una herramienta interactiva que te permite evaluar expresiones y funciones de Terraform en tiempo real dentro de una
consola interactiva. Esto te permite probar y experimentar con código Terraform sin necesidad de aplicar los cambios a tu infraestructura.

Cuando ejecutas el comando `terraform console`, Terraform inicia una sesión interactiva donde puedes ingresar y evaluar expresiones de Terraform. Por
ejemplo, puedes realizar cálculos simples, manipulaciones de cadenas, operaciones matemáticas, y mucho más.

Por ejemplo, podrías ejecutar el siguiente comando: 

```bash
terraform console
```

Una vez que ingreses a la consola interactiva, podrías ingresar expresiones como estas: 

```hcl
> "Hello, " + "World!"
```

El cual te daría como resultado: 

```
"Hello, World!"
```

Otras operaciones más complejas también son posibles, por ejemplo: 

```hcl
> upper("hello")
```

Dará como resultado: 

```
"HELLO"
```

Esta herramienta es útil para probar rápidamente cómo se comportan las expresiones y las funciones de Terraform antes de integrarlas en tus archivos
de configuración. Además, puede ser útil para depurar y comprender mejor cómo funcionan ciertas funciones o expresiones en Terraform. Sin embargo, hay
que tener en cuenta que `terraform console` no tiene acceso a ningún estado de Terraform o recursos reales, por lo que algunas operaciones que dependen del
estado de la infraestructura real no serán posibles de evaluar.

### Práctica nº 1

La idea es ver cómo funcionan cada una de las funciones. En el código escrito, se podrán ver algunas de las funciones utilizadas, tales como: `lookup()`. 

Archivos: 
1. `53_main.tf`
2. `53_variables.tf`
3. `terraform.ftvars` 

1. `53_main.tf`

```terraform
# A simple instance in EC2
resource "aws_instance" "terraformEC2" {
    ami           = lookup(var.zones, var.region, "us-east-2b")
    instance_type = var.instance_type
    count         = 1
    tags = local.common_tags
}
```

2. `53_variables.tf`

```terraform
variable "region" {
    type    = string
    default = "us-east-3c"
}

variable "zones" {
    type         = map
    default      = {
        us-east-1a = "ami-051f8a213df8bc090"
        us-east-2b = "ami-052f8a213df8bc091"
        us-east-3c = "ami-051f8a213df8bc092"
        us-east-4d = "ami-051f8a213df8bc093"
    }
}

variable "instance_type" {
    type    = string
    default = "t2.micro"
}

locals {
    common_tags = {
        Name  = "Terraform"
        Owner = "DevOps Team"
    }
}
```

Como podemos ver, la función empleada en este caso es: lookup(var.zones, var.region, "us-east-2b"), la cual permite evaluar en una variable de tipo
denominada `var.zones` el valor de otra variable denominada `var.region`. En caso de no encontrarse este valor, por defecto buscará el valor
`us-east-2b`, por lo tanto tenemos que estar asegurados de que este valor exista en la variable de tipo map. 

### Práctica nº 2

1. `53_main.tf`

```terraform
resource "aws_instance" "terraformEC2" {
    ami           = lookup(var.zones, var.region, "us-east-2b")
    instance_type = var.instance_type
    count         = 2
    tags          = {
        Name        = element(var.tags_list, count.index)
    }
}
```

2. `53_variables.tf`

```python
# ...
    variable "tags_list" {
      type = list
      default = ["Terraform-Dev", "Terraform-Prod"]
    }
```
En este ejemplo lo que podemos ver es que podemos utilizar la función `element()` para poder recorrer una lista según un índice de `count.index` .
Esto nos permite utilizar varios nombres según lo que se encuentre dentro de la lista `tags_list`. 

Recordar que para probar todas estas funciones tenemos dos recursos disponibles: 
1. La documentación oficial de Terraform. 
2. La consola propia de Terraform que se invoca desde `terraform console`. 

```bash
  # aws_instance.terraformEC2[0] will be created
  + resource "aws_instance" "terraformEC2" {
      + ami                                  = "ami-051f8a213df8bc092"
      ...
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      ...
      + instance_type                        = "t2.micro"
      ...
      + placement_partition_number           = (known after apply)
      ...
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "Terraform-Dev"
        }
      + tags_all                             = {
          + "Name" = "Terraform-Dev"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)
    }

  # aws_instance.terraformEC2[1] will be created
  + resource "aws_instance" "terraformEC2" {
      + ami                                  = "ami-051f8a213df8bc092"
      ...
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      ...
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      ...
      ...
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "Terraform-Prod"
        }
      + tags_all                             = {
          + "Name" = "Terraform-Prod"
        }
      + tenancy                              = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)
    }

```

Podemos ver que ambos tags han sido tomados con éxito. 

### Práctica nº 3

Lo que podemos hacer a continuación es generar una variable para que se imprima en la salida de tipo `output`. El problema aquí es que para que la
variable se imprima deberemos de haber ejecutado el comando `terraform apply`. El problema de esto es que la clave `id_rsa.pub` es un número generado
al azar y por lo tanto se va a terminar de impactar en el proveedor de manera incorrecta. 

1 `53_main.tf`

```terraform
output "time_exec" {
    value = local.timestamp
}
```

2. `53_variables.tf`

```terraform
# ...
locals {
  common_tags = {
    Name  = "Terraform"
    Owner = "DevOps Team"
  }
  timestamp = {
    time = formatdate("DD MM YYY hh:mm", timestamp())
  }
}
```

## Continuará

¡Hola! ¿cómo están? Espero que muy bien. En las próximas clases empezaremos a ver los denominados `Data Sources`, cómo utilizarlos, algunos ejemplos y
sus casos de uso. 

Espero que tengan un feliz comienzo de año 2026.

