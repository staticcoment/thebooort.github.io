---
title: "Fish-shell (o cómo tener una terminal bonita)"
layout: post
date: 2018-10-26 22:44
image: /assets/images/fish-shell.png
headerImage: true
tag:
- informática
- shell
- bash
use_math: true
star: true
category: blog
author: bart
description: Probando fish-shell 
---
# Fish-shell 

Hace ya un tiempo que vengo jugando un poco con mi ordenador y aprendiendo a usar herramientas nuevas. 
El grado en matemáticas te deja un background vasto y cultivado en ramas de la matemática de todo tipo, 
pero, sin duda, aporta pocas pistas para aquel que quiere profundizar en informatica al ver la inseparable
relacion que las une (sobre todo si te gusta la matematica aplicada). 

Aprender es descubrir nuevos parajes, visitar sitios desconocidos y asombrarte de hasta donde puedes llegar.
Y esa sensación, si la compartes, debes llevarla contigo siempre. 

Con esa idea en mente antes de acabar la carrera me puse a juguetear con la terminal, luego con bash y bueno,
el resto vendrá en sucesivos posts. 

El post de hoy tienen un comienzo muy simple: **LA TERMINAL ES SOSA Y NO DEMASIADO INFORMATIVA**.
Por supuesto que los colores son bonitos, pero también nos ayudan a identificar más rapidamente ciertos
mensajes (es indudable como asociamos el rojo a peligro/advertencia/__algo va muy mal__). Por tanto desde
que empecé a usar la terminal (y cualquier otra cosa) me gusta editar y configurar a mi gusto sus opciones,
y , evidentemente, entre ellas están los colores. (reescribir esta mierda)


## Algunos tips
### Manipular la variable PATH en fish-shell
Si os habéis despitado y no os acordáis de que hemos cambiado y mejorado nuestra terminal, es posible que al añadir alguna variable al `PATH`[^1] lo hagáis en `.bashrc`. Y si seguís sin acordaros de esto os preguntaréis que diablos pasa. 

Al cambiar a fish, el `PATH` lo debéis editar en `~config/fish/config.fish` (creadlo si no lo tenéis).
Con ello tenemos dos opciones para añadir un path:
- Temporal:

```
$ set -gx PATH $PATH tu/path/aqui
```
`-g` y `-x` significan __global__ y __exportar__.
-Permanente:
```
$ touch ~/.config/fish/config.fish
$ echo "set -gx PATH \$PATH <path>" >> ~/.config/fish/config.fish
```
El primer comando es para asegurarnos de tener el archivo de configuración (puedes no tenerlo!).

[Fuente](https://coderwall.com/p/hicrzq/manipulating-path-variable-in-fish-shell) donde encontré la respuesta.

[^1]:Añadir algo al path se utiliza para usar nombres cortos en la shell en lugar que tener que poner toda la direccion hasta el ejecutable cada vez que queremos ejecutarlo.
