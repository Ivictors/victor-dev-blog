---
title: "Camada anticorrupção: proteja o domínio na fronteira de integração"
description: "Como traduzir um modelo legado sem deixar conceitos externos se espalharem por um microserviço."
date: 2026-11-22
lang: pt-BR
locale: pt
layout: post-localized
translation-id: anti-corruption-layer
link: /pt/posts/camada-anticorrupcao/
alternate: /en/posts/anti-corruption-layer/
tags: microsservicos, arquitetura, ddd
author: victor
---

Uma integração é arriscada quando dois sistemas usam palavras parecidas com significados diferentes. Se um novo serviço de Pedidos começar a usar os códigos do sistema legado de cobrança em todos os lugares, o modelo legado passa a fazer parte do novo domínio.

## Traduza na fronteira

Uma camada anticorrupção (ACL) traduz nomes, estruturas, protocolos e erros entre modelos:

```text
Domínio de Pedidos -> ACL -> Cobrança legada
        PAGO         <-      código B7
```

O serviço recebe seu próprio conceito, como `PAGO`, em vez de espalhar `B7` pelo código de aplicação e domínio. A ACL pode ser um módulo dentro do serviço consumidor ou, quando houver motivo operacional claro, um componente separado.

## Tradução trata significado

Comparar nomes de campos não basta. “Cliente ativo” em vendas pode não significar o mesmo que “cliente ativo” em cobrança. O serviço dono da regra de negócio deve manter essa regra; a ACL trata a diferença na fronteira do sistema.

ACL não é API Gateway. Um gateway roteia ou aplica políticas na borda. A ACL protege o modelo de domínio contra um vocabulário e um contrato externos.

Use o padrão ao integrar um legado, fornecedor ou outro contexto delimitado com regras diferentes. Se os dois modelos já têm o mesmo significado, uma camada de tradução pode acrescentar complexidade sem benefício.
