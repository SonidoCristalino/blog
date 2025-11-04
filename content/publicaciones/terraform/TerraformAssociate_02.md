+++ 
date = '2025-11-03T22:29:29-03:00'
draft = false
title = "Configuración del Laboratorio"
authors = ["Universo 25"] 
categories = ["Terraform"]
tags = ["Terraform", "Hashicorp", "Certificación", "SetUpLab"] 
+++

# Sección nº 2

## 8. Proceso de instalación de Terraform

La instalación es bastante sencilla. Hashicorp provee el binario para poder instalarlo en distintas plataformas. Para los sistemas basados en GNU/Linux se puede encontrar la forma de instalación en el [siguiente enlace](https://developer.hashicorp.com/terraform/install?product_intent=terraform#linux)

Hay que recordar que Terraform soporta distintos Sistemas Operativos: 
1. Windows
2. macOS
3. Linux
4. FreeBSD
5. OpenBSD
6. Solaris

En esta lección se verá la forma de instalar el binario en Windows y en la siguiente se verá el proceso para instalar en Linux. 

### Instalación en Windows
Para el SO Windows, habrá que ir al [siguiente enlace](https://developer.hashicorp.com/terraform/install?product_intent=terraform#windows) y seguir los pasos. Recomiendo descargar el binario señalado como `AMD64`. Luego, descomprimimos el archivo zip y lo instalamos en la dirección que nosotros querramos. Una vez finalizado este punto, abrimos una terminal de windows e ingresamos el siguiente comando:  

```
$ terraform
```

Si todo el proceso ha salido exitoso, entonces luego del ingreso de este comando, se mostrarán un montón de opciones sobre cómo utilizar el comando `terraform`. 

### Nota importante sobre Windows
Dependiendo del lugar donde se seleccionó para la instalación del binario, el comando `terraform` sólo podrá ejecutarse dentro del directorio donde se instaló. **Esto es algo particular de la instalación de Windows**. 

Por ejemplo, si se instaló `terraform` en el directorio `C:\User\John\Desktop` entonces `terraform` podrá ser utilizado sólo dentro de la carpeta `Desktop` y para que esto no suceda, y podramos utilizarlo en otras jerarquías de directorios, habrá que configurar las variables globales. Esto se hace de la siguiente manera: 

1. Generamos una nueva carpeta dentro de `C:\` denominada como `Binaries` y arrastramos el binario `Terraform.exe` que descomprimimos hacia ésta carpeta. 
2. Luego vamos a `This PC` > Click izquierdo > `Properties` > `Advance system settings` > `Environment Variables`
3. Luego, seleccionamos la variable `Path` > `Edit` > `Browse` > y seleccionamos la carpeta `Binaries`
4. Deberemos de abrir una nueva terminal y luego de ello podremos ir a cualquier carpeta y ejecutar `terraform` y efectivamente veremos que ahora nos toma el comando correctamente. 

## 9. Documentación - Página oficial de descargas

https://www.terraform.io/downloads

## 10. Instalación en MacOS y Linux 
### MacOS
Para MacOS se puede emplear tanto el packet manager como también el binario; ambos se encuentran en el [siguiente enlace](https://developer.hashicorp.com/terraform/install?product_intent=terraform#darwin)

### GNU/Linux
Según nuestro SO, podemos utilizar el gestor de paquetes o el binario según desde el [siguiente link](https://developer.hashicorp.com/terraform/install?product_intent=terraform#linux)

## 11. Seleccionando el mejor IDE 
La idea es encontrar un IDE para poder codificar para Terraform. En este caso particular nosotros utilizaremos `Vim` y el plugin `hashivim/vim-terraform` (instalado mediante VimPlug), por lo tanto no hace falta conseguir ningún IDE. 

El gestor de plugins para `Vim` se puede encontrar en el [siguiente enlace](https://github.com/junegunn/vim-plug)

Algunos de los ides que más se suelen utilizar (y que considero totalmente útiles) son: 
1. Visual Studio Code
2. Sublime Text
3. Atom

## 12. Instalación y Configuración
Como se dijo anteriormente, como nosotros utilizaremos el editor `Vim` y plugins como `hashivim/vim-terraform` o `NerdTree`, no hará falta utilizar otro IDE, 

## 13. Visual Studio Code Extensions
Los plugins que se sugieren instalar (en caso de elegir el editor Visual Studio Code) son los siguientes: 
1. [HashiCorp Terraform](https://github.com/hashicorp/vscode-terraform)

## 14. Sample Code - Extension Test
    
```
resource "aws_instance" "myec2" { 
    ami           = "ami-00c39f71452c08778"
    instance_type = "t2.micro"
}
```

## 15. Configurando nuestra cuenta de AWS. 

Como se mencionó previamente, se recomienda enfáticamente utilizar una nueva cuenta de AWS que aún se encuentre dentro del periodo de la Capa Gratuita (denominada *Free Tier*). Esto tiene por objetivo minimizar los posibles gastos mensuales derivados de la ejecución de los ejemplos y prácticas a lo largo del curso.

La Capa Gratuita de AWS proporciona recursos limitados que pueden utilizarse sin costo durante los primeros doce (12) meses desde la creación de la cuenta. Tras este periodo inicial, o si se exceden las limitaciones impuestas, los recursos comenzarán a generar cargos.

Es obligatorio que los usuarios lean detenidamente las especificaciones y límites de los servicios incluidos en el *Free Tier*. Esta información detallada está disponible en el [siguiente enlace](https://aws.amazon.com/free/)

### Ejemplo de Limitación

Dentro del *Free Tier*, se ofrece la posibilidad de desplegar instancias basadas en la mayoría de los entornos Linux. Sin embargo, si se elige una Imagen de Máquina de Amazon (AMI) basada en Windows, esta acción sí generará un cobro. Por consiguiente, se deben considerar estos límites estrictos al momento de planificar y realizar el despliegue de recursos en AWS.

Por otro lado, el proceder con la creación de la cuenta de AWS, siga el proceso indicado en el portal oficial:

Enlace Directo para el Registro en AWS: [Link](https://portal.aws.amazon.com/billing/signup?nc2=h_ct&src=header_signup&redirect_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation#/start/email)

### Nota Importante

Para completar el registro y la activación de la cuenta, se requerirá una tarjeta de crédito o débito válida. Este paso es obligatorio para fines de verificación de
identidad y facturación futura, aunque los cargos no se aplicarán mientras los recursos se mantengan dentro de los límites del *Free Tier*.

