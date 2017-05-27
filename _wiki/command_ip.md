---
title: Command IP
layout: wiki
tags: linux, network, ip
language: Portuguese
---

O '''ip''' usado em Linux para visualizar e gerenciar as tabelas de roteamento, interface de rede, túneis e outros aparelhos associados com a rede. Esta página não tem qualquer ambição de ser um manual completo. Ele serve como meu caderno sobre o tema.

## Ativar/desativar o suporte para IPv6 ##

Desativar completamente o suporte de IPv6 pode ser diferente e mais eficiente. Se uma pessoa precisa desativar o suporte a IPv6 por um tempo no dispositivo, isso pode ser feito da seguinte maneira:

```bash
 ip -6 addr del <Endereço Ipv6> dev <Dispositivo de Rede>
```

Exemplo:

```bash
 ip -6 addr del ::1/128 dev lo
```

Se quisermos ativar novamente o suporte ao IPv6, então basta utilizar o comando anterior subistiuindo a palavra-chave '''del''' por '''add''':

```bash
 ip -6 addr add <Endereço Ipv6> dev <Dispositivo de Rede>
```

Exemplo:

```bash
 ip -6 addr add ::1/128 dev lo
```

## Roteamento ##

A tabela de mento roteamento podem ser listado usando:

```bash
 $ ip route show
```

e para o IPv6:

```bash
 $ ip -6 route show
```

## Configurações MTU ##

Se você precisa definir a MTU para um dispositivo de rede, isso pode ser feito facilmente usando o comando:

```bash
 $ ip link set <Dispositivo de rede> mtu <valor da MTU>
```

Exemplo:

```bash
 $ ip link set lo mtu 1500
```

'''Observação:''' O valor padrão para o MTU loopback é o 16436. Para ver o seu valor, você pode usar o comando ifconfig:

```bash
 $ ifconfig lo
```

ou ainda o comando ip:

```bash
 $ ip link show lo
```
Tudo isso pode ser útil em um teste/depuração de um aplicativo de rede.
