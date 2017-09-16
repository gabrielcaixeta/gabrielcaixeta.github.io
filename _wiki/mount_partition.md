---
title: Mount Partition with fstab
layout: wiki
tags: linux, partition, config, fstab
language: Portuguese
---

> **Observação:** Esta página não tem qualquer ambição de ser um manual completo. Ele serve como meu caderno sobre o tema.

Às vezes é necessário adicionar um novo dispositivo de armazenamento, ou uma partição que antes estava como dual boot, para ser montado automaticamente com a inicialização do sistema.

## Identificação da Partição/Disco ##

Isso pode ser realizado com o combando *blkid*:

```bash
 $ sudo blkid
```

Exemplo de saída:

```bash
 /dev/sda1: UUID="e6e662b0-8463-48c5-86c2-a6dfb4edd78c" TYPE="ext4" PARTUUID="e0f7a401-01"
 /dev/sda5: UUID="2345197d-1bb3-433f-b70c-28199a5e7b6b" TYPE="swap" PARTUUID="e0f7a401-05"
 /dev/sdb1: UUID="54e5123d-5c23-433f-53a2-a9d0d96ae8f7" TYPE="ext4" PARTUUID="e0f7a401-05"
```

Para criar a entrada no fstab para indicar que um disco/partição deve ser montata automaticamente é necessário copirar o **UUID** do dispositivo a ser montado e estar atento ao tipo de sistemas de arquivos que ele possui:

## Editanto o fstab ##

Utilize um editor de texto de sua preferência, no exemplo abaixo será mostrado utilizando o editor nano:

```bash
 $ sudo nano /etc/fstab
```

Será mostrado algo como :

```bash
	# /etc/fstab: static file system information.
	#
	# Use 'blkid' to print the universally unique identifier for a
	# device; this may be used with UUID= as a more robust way to name devices
	# that works even if disks are added and removed. See fstab(5).
	#
	# <file system> <mount point>   <type>  <options>       <dump>  <pass>
	# / was on /dev/sda1 during installation
	UUID=e6e662b0-8463-48c5-86c2-a6dfb4edd78c /               ext4    errors=remount-ro 0       1
	# swap was on /dev/sda5 during installation
	UUID=4effc58c-5c4a-4fcf-9a61-5a6a51da5cf2 none            swap    sw              0       0
```

No fim do arquivo coloque o UUID do disco/partição copiada anteriormente (UUID="54e5123d-5c23-433f-53a2-a9d0d96ae8f7")  seguida das informações de ponto de montagem (/media/myNewStorage), tipo de sistemas de arquivos (ext4), e as configurações padrão como segue abaixo (defaults 0 0):

```
	UUID="54e5123d-5c23-433f-53a2-a9d0d96ae8f7" /media/myNewStorage ext4 defaults 0 0
```
Salve o arquivo e saia do editor de texto.

## Configurando o Ponto de Montagem ##

Para que tudo funcione como o esperado será necessário criar a pasta que servirá como ponto de montagem e dar permissão para o usuário poder ler e gravar conteúdos dentro da mesma com os seguintes comandos:

```bash
	$ sudo mkdir -p /media/myNewStorage
	$ sudo chown -R username /media/myNewStorage
```
Na primeira linha é criado a pasta que servirá como ponto de montagem chamada *myNewStorange*. Na segunda linha o comando *chown* altera o dono da pasta myNewStorage e todo o seu conteúdo (-R) para um usuário que neste caso foi chamado de username, mas poderia ser qualquer outro.

Reinicie o sistema operacional e verás que o disco/partição foi montado automaticamente.
