---
title: "Como usar Org-mode para desahogar tu RAM"
layout: post
date: 2018-10-30 19:57
image: /assets/images/orgmodelogo.svg
headerImage: true
tag:
- informática
- bash
- org-mode
- emacs
use_math: true
star: true
category: blog
author: bart
description: Org-mode para gestionar links  
---
# Cómo usar org-mode para capturar y almacenar bookmarks
## Aventuras en emacsland #1 (es un titulo horrible que posible cambie, tranqui)
![example](/assets/images/exampleorgmodebook.gif)
Dentro del vasto mundo de emacs y org-mode tenemos multitud de herramientas a nuestra disposición. 

Cuando volví a lanzarme a esta piscina, una de las cosas que tenía en mente era replicar de alguna manera las aplicaciones __Read-it-later__ como [pocket](http://getpocket.com). Básicamente estas aplicaciones te permiten almacenar enlaces apra leerlos luego, y, no sé vosotros, pero mi RAM necesita un respiro de tener taantas pestañas abiertas.

Es cierto que por ahora hay algún incovenniente por la sincronización con el smartphone, aunque no es algo que me quite mucho el sueño, pues suelo leer más desde el ordenador. Otras opciones, como implementar tags, o estados que complemente el desarrollo inicial aquí expuesto creo que aportan más interés al proyecto ( al menos para mí).

Quitando eso, usando las numerosas herramientas que una comunidad como la de emacs/org-mode ha desarrollado, replicar la principal finalidad de estas apps es no solo alcanzable, si no alcanzable fácilmente. Vamos al lío.

## Ingredientes
Hacen falta ( y viene bien tener cierta idea, aunque no es imprescindible):
- Emacs.
- Org-mode.
- Org-capture.
- Org-protocol.
- Firefox org-capture-extension.

## Setting up 
Para usar org mode con esta finalidad primer debemos tener instalado org-protocol: [org-protocol webpage](https://orgmode.org/worg/org-contrib/org-protocol.html)

Básicamente, org-protocol intercepta llamadas de emacsclient para desencadenar acciones personalizadas sin dependencias externas. Suponiendo que lo tengamos ya instalado (en la web enlazada vienen toda la información necesaria) comenzamos el set up:

- Primero necesitamos el paquete, para ello añadimos a nuestro fichero de configuración de emacs: `(require 'org-protocol)`

- Despues añadimos el modelo que vamos a utilizar para almacenar la información. En este caso he seguido los modelos estandar que se muestran en la configuración usual, uno para recoger url's (que es el que planeo usar) y otro para recoger citas de texto (no planeo que vaya a usarlo mucho, pero bueno, ya que tenemos esa oportunidad lo dejamos implementado): 

```common_lisp
(defun transform-square-brackets-to-round-ones(string-to-transform)
  "Transforms [ into ( and ] into ), other chars left unchanged."
  (concat 
  (mapcar #'(lambda (c) (if (equal c ?[) ?\( (if (equal c ?]) ?\) c))) string-to-transform))
  )

(setq org-capture-templates `(
	("p" "Protocol" entry (file+headline ,(concat org-directory "notes.org") "Inbox")
        "* %^{Title}\nSource: %u, %c\n #+BEGIN_QUOTE\n%i\n#+END_QUOTE\n\n\n%?")	
	("L" "Protocol Link" entry (file+headline ,(concat org-directory "notes.org") "Inbox")
        "* %? [[%:link][%(transform-square-brackets-to-round-ones \"%:description\")]]\n")
))
```

Como podéis observar, hemos de definir una función, esto se debe a un bug al obtener links que impedía obtener correctamente aquellos con brackets (como los de ArXiv). Al ser una página que visito mucho, para mí era un si o sí. La solución la dió Vincent Picaud en este issue: [https://github.com/sprig/org-capture-extension/issues/37].

Finalmente, para acabar de escribir cosas en la configuración podemos añadir un `(server-start)` en nuestro __init.el__ o, si lo preferimos, con emacs abierto iniciar el server via : `M-x server-start` (esto lo tendríamos que hacer cada vez que iniciamos emacs).

**Si todo ha ido bien, abre una terminal y escribe** 

```bash

emacsclient -n "org-protocol:///capture?url=http%3a%2f%2fduckduckgo%2ecom&title=DuckDuckGo"
```
**esto debería funcionar. En caso contrario, intenta solucionar ese problema antes**.

Después, hacemos el setup que nos permite usar org protocol y comunicarnos con firefox (si usas KDE creo que esto no sirve, pero seguro que hay más información en internet):
  - Installa las librerias de gnome:
  
  ```bash
  sudo apt install gconf2
  
  gconftool-2 -s /desktop/gnome/url-handlers/org-protocol/command '/usr/local/bin/emacsclient %s' --type String

  gconftool-2 -s /desktop/gnome/url-handlers/org-protocol/enabled --type Boolean true
  ```
  
  - Después registra emacsclient como el handler de org-protocol:
  
  ```bash
  cat > "${HOME}/.local/share/applications/org-protocol.desktop" << EOF
  [Desktop Entry]
  Name=org-protocol
  Exec=emacsclient %u
  Type=Application
  Terminal=false
  Categories=System;
  MimeType=x-scheme-handler/org-protocol;
  EOF
  ```
  
#### Cuidao: Si tienes fish instalado no puedes copiar y pegar lo de arriba, mejor échale un vistazo a: [https://github.com/fish-shell/fish-shell/issues/540]() o escríbelo manualmente.

Finalmente, solo queda installar la extensión para Firefox:[Github](https://github.com/sprig/org-capture-extension/blob/master/README.md) o [Link directo a la extensión](https://addons.mozilla.org/es/firefox/addon/org-capture/).

Si todo ha ido bien, todo debería estar funcionando.

### Ojo: Este último paso no es imprescindible, tambien podemos crear un boton en firefox nosotros mismos desde cero ([Tutorial](http://www.mediaonfire.com/blog/2017_07_21_org_protocol_firefox.html))

## Algunos problemas

- Si has modificado los templates de las formas de capturar cosas, sobre todo la letra, recuerda que también debes cambiarlo en la extension de Firefox.

- Otros fallos habituales suelen estar relacionados con la manera en la que emacs usa los servidores (o la extension). Esto puede ser un coñazo, os dejo por aquí links de gente que lo ha sufrido por si os pueden ayudar [1](https://stackoverflow.com/questions/47647948/getting-org-protocol-to-work-on-firefox-quantum-no-server-buffers-remain-to), [2](https://github.com/sprig/org-capture-extension/issues/1). Es posible que os encontréis algunos fallos que no estén corregidos, si es así, echadle un ojo a los issues y animaos a contribuir :).

- Si tienes algun problema con el servidor prueba `M-x server-start` que lo reinicia si está abierto. Eso me solucionó un issue con el modo de capturar trozos de texto.

- He observado que copiar dos veces seguidas y llamar al protocolo esas dos veces, hace que se copien dos veces la informacion en la segunda llamada. No sé si es problema mío o de la extension o de mi config. Si alguno tiene el mismo problema, que me lo comente a ver si debemos abrir issue.

## Más info
Lo de arriba es un compendio de todo lo que me ha hecho falta para instalar esta maravilla. He ido cogiendo información de aquí y allí, y os dejo de nuevo los links de los tutoriales en los que me he basado/ me han ayudado para en esta tarea:
-[http://www.mediaonfire.com/blog/2017_07_21_org_protocol_firefox.html]()
-[https://github.com/sprig/org-capture-extension]()
-[https://orgmode.org/worg/org-contrib/org-protocol.html]()

## What's next?
Como he dicho, seguro que alguien ya ha dado con la idea de sincronizarlo con otros dispositivos (aunque sea usando dropbox). En mi caso llevo un par de días usandolo y no he tenido problemas. Creo que mi siguiente pasó será personalizar todo el procedimiento y añadir tags y estados específicos para bookmarks (quizás algo tipo TOREAD-READ).

¡Hasta nuestra próxima aventura emacquiana amigues!
