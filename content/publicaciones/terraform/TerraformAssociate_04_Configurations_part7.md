+++ 
date = '2025-12-19T22:34:47-03:00'
draft = false
title = '04 - Configuraciones - Parte nº 7'
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "Configurations"] 
series = ["Curso Terraform"]
+++

# Data Type - List

## ¿Qué es el tipo de dato `List`?

En Terraform, el tipo de dato `list` es una colección ordenada de valores. Los elementos en una lista pueden ser de cualquier tipo de dato, como números, cadenas,
mapas u otros. Las listas se definen utilizando corchetes (`[]`), y los elementos se separan con comas. 

```terraform
variable "example_list" {
        type    = list(string)
        default = ["value1", "value2", "value3"]
    }

    resource "aws_instance" "example" {
        count         = length(var.example_list)
        ami           = "ami-12345678"
        instance_type = "t2.micro"
        tags          = {
            Name = var.example_list[count.index]
    }
}
```

En este ejemplo, se define una variable `example_list` que es una lista de cadenas. Luego, se utiliza esta lista para crear múltiples instancias de AWS EC2. La
propiedad `count` en el recurso `aws_instance` se establece en el número de elementos de la lista, y cada instancia se etiqueta con uno de los valores de la lista.

## Ejemplo nº 1

**NOTA**: Los archivos son meramente ejemplos, es simplemente para ejemplificar lo que se viene hablando de tipos de datos. 

1. `48_main.tf`

```terraform
resource "aws_instance" "AWSInstance" {
    ami                    = "ami-0c101f26f147fa7fd"
    instance_type          = "t2.micro"
    vpc_security_group_ids = var.instance.vpc
    tags                   = {
        Name = var.instance_name
    }
}
```

3. `48_variables.tf`

```terraform
variable "instance_name" {
    type = number
}
    
variable "instance_vpc" {
    type = list
}
```

4. `terraform.tfvars`

```terraform
    instance_name = "AWSDevInstance"
    instance.vpc = "value1"
```

Esto causará un error, porque el tipo que pide la variable `vpc_security_group_ids` es del tipo list, y como se puede observar, el valor que le damos en el archivo
`terraform.tfvars` es de tipo string. Por lo tanto, para convertirla en lista, deberíamos de utilizar la siguiente nomenclatura: 

     instance.vpc = ["value1"]

## Ejemplo nº 2

**NOTA**: Los archivos son meramente ejemplos, es simplemente para ejemplificar lo que se viene hablando de tipos de datos. 

1. `48_main.tf`

```terraform
resource "aws_instance" "AWSInstance" {
    ami                    = "ami-0c101f26f147fa7fd"
    instance_type          = "t2.micro"
    vpc_security_group_ids = var.instance.vpc

    tags = {
        Name = var.instance_name
    }
}
```

3. `48_variables.tf`

```terraform
variable "instance_name" {
    type = number
}

variable "instance_vpc" {
    type = list
}
```

4. `terraform.tfvars`

```terraform
instance_name = ["emiliano"]
```

En este caso, si nosotros ingresamos `terraform plan` podremos ver que efectivamente terraform nos permite actuar o realizar este plan porque la nomenclatura
utilizada en el archivo `terraform.tfvars`es la de una lista con un sólo elemento. 

## Ejemplo nº 3

1. `48_main.tf`

```terraform
resource "aws_instance" "AWSInstance" {
    ami                    = "ami-0c101f26f147fa7fd"
    instance_type          = "t2.micro"
    vpc_security_group_ids = var.instance.vpc

    tags = {
        Name = var.instance_name
    }
}
```

3. `48_variables.tf`

```terraform
variable "instance_name" {
  type = number
}

variable "instance_vpc" {
  type = list
}
```

4. `terraform.tfvars`

```terraform
instance_name = ["emiliano", "ejemplo 1", "ejemplo 2"]
```

En este caso, si utilizamos el comando `terraform.tfvars` también vamos a poder ver que el comando `terraform plan` es aceptado ya que se le ingresa una lista. 

## Ejemplo nº 4

1. `48_main.tf`

```terraform
resource "aws_instance" "AWSInstance" {
    ami                    = "ami-0c101f26f147fa7fd"
    instance_type          = "t2.micro"
    vpc_security_group_ids = var.instance.vpc

    tags = {
    Name = var.instance_name
    }
}
```

3. `48_variables.tf`

```terraform
variable "instance_name" {
  type = number
}

variable "instance_vpc" {
  type = list(number)
}
```

4. `terraform.tfvars`

```terraform
instance_name = [5,8]
```

Podemos ver que también se puede configurar que una lista sólo acepte ciertos tipos de valores, como por ejemplo una lista de números; para esto, debemos de
configurar esto en la definición del tipo de la variable, de la siguiente manera: `type=list(number)`. 

Si ingresamos `terraform plan` podemos ver que efectivamente toma de forma correcta nuestra configuración: 

```bash
  + user_data                       = (known after apply)
      + user_data_base64            = (known after apply)
      + user_data_replace_on_change = false
      + vpc_security_group_ids      = [
          + "5",
          + "8",
        ]
    }
```

Pero si en cambio lo que hacemos es modificar la lista y le pasamos una cadena de caracteres (en vez de números como se encuentra definido en `variables.tf`) de la
siguiente forma en el archivo `terraform.tfvars`: 

```terraform
vpc = ["emiliano", "florencia"]
```

Entonces veremos el siguiente error: 

```bash
    Planning failed. Terraform encountered an error while generating this plan.

    ╷
    │ Error: Invalid value for input variable
    │ 
    │   on terraform.tfvars line 3:
    │    3: vpc = ["emiliano", "florencia"]
    │ 
    │ The given value is not suitable for var.vpc declared at 48_variables_2.tf:15,1-15: a number is required.
```

# Data Type - Map

## ¿Qué es el tipo de dato Map? 

En Terraform, el tipo de dato `map` es una colección no ordenada de pares clave-valor, donde cada clave es única y está asociada con un valor. Las claves son
generalmente cadenas, pero los valores pueden ser de cualquier tipo de dato. Los mapas son útiles para almacenar y acceder a configuraciones y datos estructurados. 

```terraform
variable "example_map" {
  type = map(string)
  default = {
    "us-east-1" = "ami-12345678"
    "us-west-2" = "ami-87654321"
    "eu-west-1" = "ami-11223344"
  }
}

resource "aws_instance" "example" {
  ami           = var.example_map[var.region]
  instance_type = "t2.micro"
  tags = {
    Name = "example-instance"
  }
}

variable "region" {
    type    = string
    default = "us-west-2"
}
```

En este ejemplo:

1. **Definición de la variable `example_map`**:
- Se declara una variable llamada `example_map` de tipo `map(string)`, lo que indica que es un mapa donde las claves son cadenas y los valores también son cadenas.
- Se establece un valor por defecto que es un mapa con pares clave-valor que representan regiones de AWS y sus respectivas AMIs.
2. **Definición de la variable `var.region`**: 
- Como se puede observar, la variable `example_map` relaciona un string con otro string, permite el mapeo entre una clave y un valor. 
- Es por eso que es necesario definirla como un tipo `string` el cual se le asigna un valor por defecto, para que en caso de que se corra el comando `terraform
  plan` el valor de `var.region` se asocie con el valor correspondiente dentro de `example_map`. 
3. **Uso en un recurso**:
- En el recurso `aws_instance`, el valor de `ami` se selecciona del mapa `example_map` utilizando otra variable `var.region` que contiene la clave correspondiente a
  la región de AWS deseada.

## Uso de tags como maps

En terraform, cuando se trata de generar un recurso de tipo `aws_instance` el atributo `tags` espera un tipo `map`. Este tipo de datos representa una colección de
elementos tipo clave-valor. Podemos observar la definición en la página oficial: 

```terraform
tags = { 
    Name = "HelloWorld" 
}
```

Esto se puede ver como que al valor `Name` le hace corresponder el valor `HelloWorld`. Si queremos ingresar múltiples valores, deberíamos de definir una variable de
tipo `map` de la siguiente manera: 

```terraform
variable "mapa" {
    type          = map
    default       = {
        Name        = "app-name"
        Environment = "dev"
        Team        = "payments"
    }
}

```

En esta nueva definición, podemos observar que para cada valor `Name` `Environment` `Team`, le hace coincidir el valor de tipo string como `"app-name"`, `"dev"`,
`"payments"`. . 

## Ejemplo nº 1

Archivos: 
1. `48.main_3.tf`
2. `48.variables_3.tf`
3. `terraform.tfvars`

Si tenemos en cuenta la modificación realizada en estos archivos: 

```terraform
resource "aws_instance" "myEC2" {
    ami                    = "ami-120384135345sf"
    instance_type          = var.lista[1]
    vpc_security_group_ids = var.vpc
    tags                   = var.tags_map
}
```

```terraform
variable "tags_map" {
    type          = map
    default       = {
        Name        = "app-name"
        Environment = "dev"
        Team        = "payments"
    }
}
```
Podemos llamar a `terraform plan` y observar que se toma de forma correcta los tags: 

```bash
+ spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Environment" = "dev"
          + "Name"        = "app-name"
          + "Team"        = "payments"
        }
      + tags_all                             = {
          + "Environment" = "dev"
          + "Name"        = "app-name"
          + "Team"        = "payments"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = [
          + "30",
          + "50",
          + "60",
        ]
    }
```
La forma en que tenemos para identificar el ingreso de este tipo de valores (así como una lista podemos identificarla mediante los corchetes `[]`), a un tipo `map`
es mediante los los caracteres denominados **llaves** `{}`. 

## Continuará ...

En las próximas lecciones veremos cómo utilizar correctamente los tipos de datos `maps` y cómo seleccionar valores a través de ellos para automatizar aún más
nuestros scripts. 

También quería hacer notar que estoy retrasado con las publicaciones de este tipo de contenido ya que mi gata Emma está muriendo de cáncer. Sé que no es relevante
para nadie, pero para mi es alguien que me acompaño por durante casi 12 años y que se esté muriendo tan rápidamente hace que todo lo demás en mi vida se torne
vacío. 

Hasta toda esta información se puede obtener ahora desde cualquier servicio de IA; así como yo la he utilizado para poder obtener una definición clara sobre algunos
conceptos, pero algunas veces noto que todo este trabajo es como echarle agua al océano. 

