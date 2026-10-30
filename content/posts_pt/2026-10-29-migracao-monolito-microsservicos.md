---
title: "Migrar de monólito para microsserviços: comece pelas fronteiras"
description: "Uma leitura prática das decisões de domínio, dados e operação antes de extrair um serviço."
date: 2026-10-29
lang: pt-BR
locale: pt
layout: post-localized
translation-id: migracao-monolito-microsservicos
link: /pt/posts/migracao-monolito-microsservicos/
alternate: /en/posts/monolith-to-microservices-boundaries/
tags: arquitetura, microsservicos
author: victor
---

## Primeiro, qual problema queremos resolver?

Nas minhas notas de arquitetura, anotei “separação de contexto”, “evite nanosserviços” e “comece pelas beiradas”. Essas frases ajudam a lembrar uma decisão maior: extrair serviços só faz sentido quando a separação atende uma necessidade concreta, como ciclos de entrega independentes, responsabilidades mais claras ou escala diferente para partes do sistema.

Um monólito pode ter módulos bem definidos, testes e implantação confiável. O fato de estar em um único processo não o torna automaticamente ruim. Microsserviços acrescentam chamadas de rede, observabilidade distribuída, automação de deploy e decisões sobre consistência. Se essas necessidades ainda não existem, melhorar os limites dentro do monólito pode ser o passo mais útil.

## Encontre uma fronteira de negócio

Antes de separar código, identifique quais regras e dados mudam juntos. Em DDD, um *bounded context* delimita onde um modelo de domínio tem significado próprio. É uma ferramenta para discutir fronteiras; não é uma regra de “um contexto = um microsserviço”.

Imagine um comércio com catálogo, pedidos e pagamentos. Se o catálogo pode evoluir sem coordenar cada alteração com pagamentos, talvez exista uma fronteira. Mas se o novo serviço ainda precisar consultar tabelas internas do monólito a cada requisição, apenas deslocamos o acoplamento para a rede: criamos um monólito distribuído.

## Extraia gradualmente

O padrão **Strangler Fig** propõe modernizar partes do sistema aos poucos, enquanto o código antigo e o novo convivem. Escolha uma capacidade com limite reconhecível, estabeleça como as chamadas serão encaminhadas e mantenha a possibilidade de voltar atrás. Começar por uma borda com poucas dependências reduz a quantidade de decisões simultâneas.

Uma sequência de perguntas ajuda:

1. Qual resultado esperamos da extração e como vamos observá-lo?
2. Quem passa a ser responsável pelas regras e pelos dados dessa capacidade?
3. Quais chamadas síncronas continuarão necessárias e qual o efeito de uma falha nelas?
4. Como migraremos leituras e escritas sem criar duas fontes de verdade?
5. Como testaremos, implantaremos e monitoraremos os dois lados durante a transição?

## Trate os dados como parte da arquitetura

Separar o banco por serviço pode dar autonomia, mas a migração dos dados costuma ser a parte difícil. Uma solução de transição pode exigir sincronização ou projeções de leitura. Dados duplicados não são necessariamente um erro; o risco é não definir qual sistema é a fonte de verdade e qual atraso de atualização é aceitável. Eventos podem ajudar a propagar mudanças, mas trazem reprocessamento, falhas e consistência eventual para o desenho.

Também não basta substituir uma transação local por várias chamadas remotas e esperar o mesmo comportamento. É preciso decidir o que acontece quando uma etapa falha: repetição segura, compensação, intervenção manual ou rejeição da operação. Os testes devem cobrir esses caminhos, além do caso feliz.

## Minha regra prática

Eu começaria com uma fronteira de domínio clara, um motivo verificável para separá-la e um plano de dados e operação. Se não consigo explicar quem é dono de cada regra, como a mudança será implantada e o que ocorre em uma falha parcial, a extração ainda não está pronta. A arquitetura melhora quando essas decisões ficam explícitas, seja em um monólito modular, seja em serviços separados.

**Leituras:** [Strangler Fig, de Martin Fowler](https://martinfowler.com/bliki/StranglerFigApplication.html). Este texto foi elaborado a partir das minhas notas de Full Cycle sobre fundamentos de arquitetura, DDD e migração de monólito para microsserviços.
