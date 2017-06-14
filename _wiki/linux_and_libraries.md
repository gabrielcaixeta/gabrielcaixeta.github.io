---
title: Linux and Libraries
layout: wiki
tags: linux, libraries
language: Portuguese
---

> **Observação:** Esta página não tem qualquer ambição de ser um manual completo. Ele serve como meu caderno sobre o tema.

Biblioteca e Variáveis de sistemas
-----------------------------

Se você precisa testar uma biblioteca compartilhada, não precisa instalá-lo em algum lugar do sistema. Basta criar uma variável de sistema '''LD_LIBRARY_PATH''' e configurá-lo para exportar uma flag.

```bash
export LD_LIBRARY_PATH=/path/to/your/library
```

Se você precisa testar uma Biblioteca com o módulo Python, então você precisa definir outra variável de ambiente'''PYTHONPATH'''

```bash
$ export PYTHONPATH=/path/to/your/python/module
```

ligações externas
--------------

* [TLDP Shared Libraries](http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html)
