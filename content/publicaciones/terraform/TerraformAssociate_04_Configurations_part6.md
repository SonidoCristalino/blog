+++ 
date = '2025-12-11T22:34:47-03:00'
draft = false
title = '04 - Configuraciones - Parte nº 6'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
+++


# Precedencia en la definición de variables

Según lo que venimos viendo en secciones anteriores, la pregunta que surje con respecto a las asignaciones de variables es *¿Cuál es el orden de precedencia que
tiene la asignación de variables en Terraform?*

## Orden de precedencia

El orden de precendencia es la siguiente: 

1. Variables de Entorno
2. Si está presente, el archivo `terraform.tfvars` . 
3. Si está presente, el archivo `terraform.tfvars.json`. 
4. Cualquier archivo `.auto.tfvars` o `.auto.tfvars.json`, los cuales son procesados en forma lexicográfica según su nombre. 
5. Cualquier opción en la línea de comando de tipo `-var` y `-var-file` en el orden que sean provistas. 
6. En caso de que ninguna de las anteriores exista, entonces evalúa el valor `by default`. Esto se ve en la siguiente sección denominada como `Valores por defecto`. 

Esto significa que en caso de estar configurado los archivos tales como se muestran en los puntos 2 y en 5, quien tiene mayor precedencia es el 5, por lo tanto será
con el valor del caso 5 con el que quedará asignada la variable. 

## Ejemplo nº 1
Tenemos definido: 

1. `TF_VAR_instance_type = "t2.micro"`
2. Y en un archivo, lo siguiente 

```terraform
terraform.tfvars: instance_type = "t2.large"
```

* Resultado final será: `t2.large`

## Ejemplo nº 2
Tenemos definido: 

1. `TF_VAR_instance_type = "t2.micro"`
2. En un archivo lo siguiente: 
```terraform
terraform.tfvars: instance_type = "t2.large"
```
3. Se ingresa el comando `terraform plan -var="instance_type=m5.large"`

* Resultado final: `m5.large`

## Default values
Si se asignan valores por defecto en las definiciones de variables, este valor será el que tendrá **menor** precedencia por sobre todos los demás. Estamos hablando
de una definición de estas características: 

```terraform
variable "instance_type" { 
    default = "t2.micro" 
}
```

Por lo tanto, este valor será asignado a la variable `instance_type` en caso de que **NO ESTÉ CONFIGURADO NINGÚN VALOR** en ninguna otra parte de la siguiente
lista: 

1. Variables de Entorno
2. Si está presente, el archivo `terraform.tfvars` . 
3. Si está presente, el archivo `terraform.tfvars.json`. 
4. Cualquier archivo `.auto.tfvars` o `.auto.tfvars.json`, los cuales son procesados en forma lexicográfica según su nombre. 
5. Cualquier opción en la línea de comando de tipo `-var` y `-var-file` en el orden que sean provistas. 
6. En caso de que ninguna de las anteriores exista, entonces evalúa el valor `by default`. Esto se ve en la siguiente sección denominada como `Valores por defecto`. 

# Tipos de variables para las variables

En Terraform, los tipos de variables que podemos utilizar en nuestros archivos de configuración son:

1. **String**: Este tipo representa una cadena de texto.
2. **Number**: Representa un número entero o decimal.
3. **Bool**: Un booleano, es decir, un valor verdadero o falso.
4. **List**: Una lista ordenada de valores del mismo tipo.
5. **Map**: Un conjunto de pares clave-valor, donde todas las claves son del mismo tipo y todos los valores son del mismo tipo.

Estos tipos de variables nos permiten definir parámetros flexibles en nuestras configuraciones de Terraform, lo que hace que nuestras infraestructuras sean más
dinámicas y adaptables.

## Tipos de restricciones
Lo que permite establecer esta configuración es el valor que se aceptará cuando se les asigne un valor a estas variables. Por ejemplo, si nosotros definimos una
variable como `string` y luego queremos ingresarle un número, al validar nuestro código mediante `terraform validate` nos generará un error. 

Para aquellos fanáticos de `vim`, es posible instalar el siguiente plugin (no oficial): `hashivim/vim-terraform`, mediante el cual es posible tener tanto un
coloreado de código como también una validación en tiempo real. 

```terraform
variable "instance_name" { 
    type = number 
}
```

Examinemos un ejemplo: Imaginemos que a partir de un requerimiento se nos pide que la variable `instance_name` debe contener el número de identificación de cada
empleado. Para que ningún empleado pueda ingresar un valor como "john-123" lo que se hace es configurar esa variable con el tipo `number` a `instance_name` para que
de esta forma si el empleado quiere ingresar una cadena de caracteres, terraform lo impida. 

## Práctica
Archivos a utilizar: 

1. `48_main.tf`
2. `48_variables.tf`
3. `terraform.tfvars`

### Ejemplo nº 1
1. Archivo `48_main.tf`: 

```terraform
resource "aws_instance" "AWSInstance" {
    ami           = "ami-0c101f26f147fa7fd"
    instance_type = "t2.micro"
    tags          = {
        Name = var.instance_name
    }
}
```

2. Archivo `48_variables.tf`: 

```python
variable "instance_name" {
    type = number
}
```

3. Archivo `terraform.tfvars`: 

```python
variable "instance_name" {
    type = number
}
```

En este caso, terraform nos dará un **ERROR** cuando ingresemos `terraform plan` porque el tipo de variable está definido como `number` y nosotros le estamos ingresando
un string. 

### Ejemplo nº 2

1. Archivo `48_main.tf`: 

```terraform
resource "aws_instance" "AWSInstance" {
    ami           = "ami-0c101f26f147fa7fd"
    instance_type = "t2.micro"
    tags          = {
        Name = var.instance_name
    }
}
```

2. Archivo `48_variables.tf`: 

```terraform
variable "instance_name" {
    type = string
}
```

3. Archivo `terraform.tfvars`: 

```terraform
instance_name = 30397781
```
El problema acá es que si bien en la definición nosotros le indicamos que debería de ser un tipo `string`, cuando ingresamos el número en el archivo
`terraform.tfvars` lo que sucede es que convierte estos números en cadena de caracteres y nos arroja lo siguiente luego de haber invocado al comando `terraform
plan`: 

```bash
 + resource "aws_instance" "AWSInstance" {
      + ami                                  = "ami-0c101f26f147fa7fd"
      + arn                                  = (known after apply)
      ...
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      ...
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      ...
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "30397781"
        }
      + tags_all                             = {
          + "Name" = "30397781"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)
    }
```

Como podemos ver, el lenguaje utilizado por Terraform de manera interna, **no es fuertemente tipado** por lo que estos errores pueden llegar a ocurrir de manera muy
común. 

### Ejemplo nº 3
Lo que utilizaremos ahora en vez de una instancia de AWS, será un ELB, que es un balanceador de carga. Esto es así ya que las zonas de disponibilidad se encuentra
definido como un tipo `list` dentro de la [documentación](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/elb)

1. `48_main.tf`

```terraform
resource "aws_elb" "bar" {
    name               = var.elb_name
    availability_zones = var.elb_az

listener {
    instance_port     = 8000
    instance_protocol = "http"
    lb_port           = 80
    lb_protocol       = "http"
  }
  
health_check {
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 3
    target              = "HTTP:8000/"
    interval            = 30
  }

    cross_zone_load_balancing   = true
    idle_timeout                = var.elb_timeout
    connection_draining         = true
    connection_draining_timeout = var.elb_timeout

    tags = {
        Name = "foobar-terraform-elb"
    }
}

```

2. `48_variables.tf`

```terraform
variable "elb_name" {
  type = string
}

variable "elb_az" {
  type = list
}

variable "elb_timeout" {
  type = number
}
```
3. `terraform.tfvars`

```terraform
elb_name    = "aws-elb-name"
elb_az      = ["us-west-2a", "us-west-2b", "us-west-2c"]
elb_timeout = 400
```

En este caso podemos ver que si en el archivo `48_variables.tf` nosotros no declaramos que el tipo `elb_az` sea un tipo `list`, terraform por más que nosotros le
ingresemos una cadena de caracteres, no nos permitirá ingresarla porque no puede hacer la correspondencia de valores entre un string y un list. Por lo tanto,
deberemos de ingresar el tipo `list` para poder solventar éste problema. 

## Nota sobre módulo en GitHub
Algo importante a tener en cuenta es que debemos de mirar cómo se encuentran definidas las variables según el módulo o servicio que estemos utilizando, en los
repositorios de GitHub. Por ejemplo, en este caso estuvimos utilizando el módulo `elb`, por lo tanto si buscamos en Google, el repositorio será algo como "github
terraform aws elb": 

1. **Módulo**:  [link](https://github.com/terraform-aws-modules/terraform-aws-elb)
2. **Variables**: [link](https://github.com/terraform-aws-modules/terraform-aws-elb/blob/master/variables.tf)

Veremos que en la definición de variables (`variables.tf`) para cada una de las **Input Variables** que se manejan dentro del módulo de ELB. La cual sería una gran
ayuda para saber qué tipo deberíamos de gestionar a nuestras variables para poder ingresarlas en los scripts. 

## Continuará...

En las próximas secciones veremos los tipos de datos `List` y `Maps` acompañado de algunos ejemplos prácticos. 

¡Hasta luego!

