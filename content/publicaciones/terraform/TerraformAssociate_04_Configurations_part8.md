+++ 
date = '2025-12-26T22:34:47-03:00'
draft = false
title = '04 - Configuraciones - Parte nº 8'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
series = ["Curso Terraform"]
+++

## Obteniendo datos de Maps y List en variables

Lo que se abordará en esta lección es cómo relacionar las variables con los valores de un tipo `list` o de un tipo `map`. Por ejemplo, un tipo `list` es la siguiente variable: 

```terraform
elb_az = ["us-west-2a", "us-west-2b", "us-west-2c"]
```

## Ejemplos

### Ejemplo: Map type

Por ejemplo, si nosotros tenemos la siguiente definición de variables: 

```terraform
variable "list" {
    type    = list
    default = ["m5.large", "m5.xlarge", "t2.medium"]
}

variable "types" {
    type           = map
    default        = {
        us-east-1  = "t2.micro"
        us-west-2  = "t2.nano"
        us-south-3 = "t2.small"
    }
}
```
Y luego la siguiente configuración: 

```terraform
resource "aws_instance" "myEC2" {
    ami           = "ami-120384135345sf"
    instance_type = var.types["us-west-2"]
}
```

Esto dará como resultado `t2.nano`, pero porque es un tipo `map`. Invocamos `terraform plan`: 

```bash
+ resource "aws_instance" "myEC2" {
      + ami                                  = "ami-120384135345sf"
      + arn                                  = (known after apply)
      ...
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      ...
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.nano"
      + ipv6_address_count                   = (known after apply)

```

### Ejemplo: List type

Ahora, por ejemplo, si nosotros tenemos lo mismo: 

```terraform
variable "list" {
    type    = list
    default = ["m5.large", "m5.xlarge", "t2.medium"]
}

variable "types" {
    type           = map
    default        = {
        us-east-1  = "t2.micro"
        us-west-2  = "t2.nano"
        us-south-3 = "t2.small"
    }
}
```

Pero ingresamos lo siguiente:

```terraform
resource "aws_instance" "myEC2" {
    ami           = "ami-120384135345sf"
    instance_type = var.list[2]
}
```
Esto dará como resultado `t2.medium`, pero porque es un tipo `list`. Invocamos `terraform plan`: 

```bash
+ resource "aws_instance" "myEC2" {
      + ami                                  = "ami-120384135345sf"
      + arn                                  = (known after apply)
      ...
      + instance_lifecycle                   = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.medium"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after ap
```

### Ejemplo: List type

Ahora, por ejemplo, si nosotros tenemos lo mismo: 

```terraform
variable "list" {
    type    = list
    default = ["m5.large", "m5.xlarge", "t2.medium"]
}

variable "types" {
    type           = map
    default        = {
        us-east-1  = "t2.micro"
        us-west-2  = "t2.nano"
        us-south-3 = "t2.small"
    }
}
```

Pero ingresamos lo siguiente:

```terraform
resource "aws_instance" "myEC2" {
    ami           = "ami-120384135345sf"
    instance_type = var.list[1]
}
```
Esto dará como resultado `m5.xlarge`, pero porque es un tipo `list`. Invocamos `terraform plan`: 

```bash
  + resource "aws_instance" "myEC2" {
      + ami                                  = "ami-120384135345sf"
      + arn                                  = (known after apply)
      ...
      + instance_state                       = (known after apply)
      + instance_type                        = "m5.xlarge"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
```

## Count and Count Index

En Terraform, un **count parameter** es un atributo que se utiliza para crear múltiples instancias de un recurso de manera dinámica basándose en un valor numérico especificado. Este parámetro se utiliza
principalmente en recursos que pueden repetirse varias veces con configuraciones similares, como instancias de máquinas virtuales, subredes, grupos de seguridad, etc.

Cuando se utiliza el parámetro **count** se especifica un número entero que indica cuántas veces se debe crear el recurso. Terraform generará automáticamente tantas instancias del recurso como se
especifique en el parámetro "count", y cada instancia se numerará de manera incremental. Esto permite definir múltiples instancias del mismo recurso con una sola declaración en el código.

Por ejemplo, supongamos que deseas crear tres instancias de máquinas virtuales en un proveedor de nube. Puedes usar el parámetro "count" para lograr esto de la siguiente manera: 

```terraform
resource "aws_instance" "example" {
    count         = 3
    ami           = "ami-12345678"
    instance_type = "t2.micro"
}
```

En este caso, Terraform creará tres instancias de máquinas virtuales utilizando la misma AMI y tipo de instancia, pero cada una tendrá un nombre único generado automáticamente (por ejemplo,
`aws_instance.example[0]`, `aws_instance.example[1]`, `aws_instance.example[2]`).

El uso del parámetro "count" puede simplificar la gestión de recursos repetitivos y evitar la necesidad de repetir bloques de código idénticos, lo que hace que la infraestructura sea más fácil de
mantener y escalar.

Este tipo de parámetro se utiliza para no repetir dos veces el mismo código ya que de lo contrario, entorpece la lectura. 

## Ejemplos

Archivos: 
1. `50_main.tf`
2. `50_variables.tf`
3. `terraform.ftvars` -> este archivo estará vacío

1. `50_main.tf`

```terraform
resource "aws_instance" "terraformEC2" {
  ami           = var.instance_ami
  instance_type = var.instance_type
  count         = 3

  tags = {
    Name = "Terraform EC2"
  }
}
```
Con esta configuración, terraform debería de crearnos 3 tipos de instancias. Si invocamos `terraform plan` veremos que nos lista 3 instancias y luego la cantidad de recursos a crear: 

```bash
  # aws_instance.terraformEC2[0] will be created
  # ...
  # aws_instance.terraformEC2[1] will be created
  # ...
  # aws_instance.terraformEC2[2] will be created
  # ...
    Plan: 3 to add, 0 to change, 0 to destroy.

```
### Save name resources

El problema con el que nos enfrentamos es el nombre de los recursos que creamos al emplear el parámetro `count`. Estos nombres, como se puede visualizar en la salida del comando `terraform plan`
anterior, se puede observar que se crean y se guardan en una lista que será el nombre local con el que generamos el recurso en nuestro archivo de configuración. 

En nuestro caso sería algo así como `aws_instance.terraformEC2[i]` siendo `i` un iterador menor al número de instancias creadas (en nuestro caso 3). 

### Ejemplo 2

Lo que haremos ahora es generar dos entradas nuevas a los archivos creados: 

```terraform
resource "aws_iam_user" "iamUser" {
  name  = var.iam_user_name
  path  = var.iam_user_path
  count = 2

  tags = {
    Name = "Terraform IAM User"
  }
}


variable "iam_user_name" {
  type    = string
  default = "Emiliano"
}

variable "iam_user_path" {
  type    = string
  default = "~/.config/system"
}
```

Luego invocamos el comando `terraform plan`: 

```bash
  # aws_iam_user.iamUser[0] will be created
  + resource "aws_iam_user" "iamUser" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "Emiliano"
      + path          = "~/.config/system"
      + tags          = {
          + "Name" = "Terraform IAM User"
        }
      + tags_all      = {
          + "Name" = "Terraform IAM User"
        }
      + unique_id     = (known after apply)
    }

  # aws_iam_user.iamUser[1] will be created
  + resource "aws_iam_user" "iamUser" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "Emiliano"
      + path          = "~/.config/system"
      + tags          = {
          + "Name" = "Terraform IAM User"
        }
      + tags_all      = {
          + "Name" = "Terraform IAM User"
        }
      + unique_id     = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```

### Problema con Ejemplo 2

Como podemos observar, se crean dos recursos y los nombres se encontrarían dentro de la lista `aws_iam_user.iamUser[]`. Pero el problema es que el nombre será el mismo para los 2 recursos creados. Cada
recurso se puede distinguir internamente mediante su ARN, por lo tanto no hace falta que se distinga del nombre proporcionado por `var.iam_user_name`. 

### Ejemplo 3

Pero si nosotros queremos identificarlo de manera que a nuestros ojos podamos distinguirlos, lo que podemos hacer es lo siguiente: 

```terraform
# Diferent names in all the IAM users created
resource "aws_iam_user" "iamUser" {
  name  = "User_n${count.index}"
  path  = var.iam_user_path
  count = 2

  tags = {
    Name = "Terraform IAM User"
  }
}

variable "iam_user_name" {
  type    = string
  default = "Emiliano"
}

variable "iam_user_path" {
  type    = string
  default = "~/.config/system"
}
```
De esta manera mediante `${count.index}` es posible que por cada iteración que se haga en los usuarios generados, se creen nuevos nombres. Por lo tanto, la salida del comando `terraform plan` es la
siguiente: 

```terraform
  # aws_iam_user.iamUser[0] will be created
  + resource "aws_iam_user" "iamUser" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "User_n0"
      + path          = "~/.config/system"
      + tags          = {
          + "Name" = "Terraform IAM User"
        }
      + tags_all      = {
          + "Name" = "Terraform IAM User"
        }
      + unique_id     = (known after apply)
    }

  # aws_iam_user.iamUser[1] will be created
  + resource "aws_iam_user" "iamUser" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "User_n1"
      + path          = "~/.config/system"
      + tags          = {
          + "Name" = "Terraform IAM User"
        }
      + tags_all      = {
          + "Name" = "Terraform IAM User"
        }
      + unique_id     = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```

Podemos ver que el nombre de cada uno de los usuarios de IAM serán los siguientes:  

```bash
    + name          = "User_n0"
    + name          = "User_n1"
```

Lo que se tiene que tener en cuenta que `${count.index}` se podrá utilizar en un mismo bloque donde se tiene definido el `count` . 

### Problema con Ejemplo 3

El problema que se tiene utilizando nombres como `User_n0` o `User_n1` es que no hacen referencia a nada. Es muy difícil saber en ambientes u organizaciones grandes, a qué hace referencia cada uno de
estos usuarios. Por lo tanto, hay una manera mucho más aceptable de identificar los nombres de los recursos gestionados y es mediante la utilización del tipo `list`

### Ejemplo 4

Sería mucho mejor utilizar una lista de nombres para que de esta forma podamos identificar mucho mejor los recursos que generaremos, por lo tanto podríamos gestionar una variable con los siguientes
nombres: 

```terraform
resource "aws_iam_user" "iamUser" {
  name  = var.iam_user_name[count.index]
  path  = var.iam_user_path
  count = 4

  tags = {
    Name = "Terraform IAM User"
  }
}

variable "iam_user_name" {
  type    = list
  default = ["dev.iam.user", "qa.iam.user", "stage.iam.user", "prod.iam.user"]
}
```

Luego de utilizar `terraform plan`, obtenemos: 

```bash
  # aws_iam_user.iamUser[0] will be created
  + resource "aws_iam_user" "iamUser" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "dev.iam.user"
      + path          = "~/.config/system"
      + tags          = {
          + "Name" = "Terraform IAM User"
        }
      + tags_all      = {
          + "Name" = "Terraform IAM User"
        }
      + unique_id     = (known after apply)
    }

  # aws_iam_user.iamUser[1] will be created
  + resource "aws_iam_user" "iamUser" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "qa.iam.user"
      + path          = "~/.config/system"
      + tags          = {
          + "Name" = "Terraform IAM User"
        }
      + tags_all      = {
          + "Name" = "Terraform IAM User"
        }
      + unique_id     = (known after apply)
    }

  # aws_iam_user.iamUser[2] will be created
  + resource "aws_iam_user" "iamUser" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "stage.iam.user"
      + path          = "~/.config/system"
      + tags          = {
          + "Name" = "Terraform IAM User"
        }
      + tags_all      = {
          + "Name" = "Terraform IAM User"
        }
      + unique_id     = (known after apply)
    }

  # aws_iam_user.iamUser[3] will be created
  + resource "aws_iam_user" "iamUser" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "prod.iam.user"
      + path          = "~/.config/system"
      + tags          = {
          + "Name" = "Terraform IAM User"
        }
      + tags_all      = {
          + "Name" = "Terraform IAM User"
        }
      + unique_id     = (known after apply)
    }

Plan: 4 to add, 0 to change, 0 to destroy.
```

## Continuará...

En las siguientes secciones aprenderemos a utilizar Expresiones condicionales para poder hacer que nuestro código sea mucho más dinámico según determinadas condiciones. 

Como dije anteriormente, no estoy subiendo muy seguido este tipo de cursos porque mi gata se me está muriendo. Tengo cursos de Docker, Kubernetes, AWS, Python y varias herramientas más pero realmente la
pérdida de mi gata, no me dan ganas de hacer absolutamente nada. 

¡Saludos!

