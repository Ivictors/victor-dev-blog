---
title: "Especificação antes do código gerado por IA"
description: "Como requisitos explícitos, invariantes e testes mantêm o desenvolvimento de backend com IA revisável."
date: 2026-11-17
lang: pt-BR
locale: pt
layout: post-localized
translation-id: specification-before-ai-code
link: /pt/posts/especificacao-antes-codigo-ia/
alternate: /en/posts/specification-before-ai-code/
tags: ia, engenharia-de-software, arquitetura
author: victor
---

Pedir que um assistente de IA implemente uma funcionalidade é fácil. Revisar se o resultado está correto é mais difícil quando o comportamento esperado nunca foi escrito.

O limite útil é uma especificação pequena: problema, atores, invariantes, falhas e evidências que provarão que a mudança funciona. Ela não precisa ser um documento grande. Precisa tornar a decisão visível.

## Comece pelo comportamento

Antes de gerar código, descreva o que deve acontecer do ponto de vista do usuário ou do sistema. Para um comando de pagamento, isso pode incluir:

- um cliente autenticado pode enviar um pedido;
- a mesma chave de idempotência não gera duas cobranças;
- um estado inválido retorna um erro definido;
- uma cobrança bem-sucedida produz um único evento de domínio.

Essas afirmações dão um alvo à implementação e permitem detectar uma solução aparentemente plausível, mas incorreta.

## Registre invariantes e não objetivos

Uma invariante é uma regra que deve continuar verdadeira. Exemplos: “um pedido não pode ser pago duas vezes” e “um usuário não pode ler a fatura de outro tenant”. Um não objetivo também é útil: ele impede que a implementação se transforme silenciosamente em uma reestruturação maior.

O modelo pode sugerir uma abordagem, mas não deve inventar uma regra de negócio porque o prompt estava incompleto. Decisões pendentes precisam continuar visíveis para aprovação do desenvolvedor.

## Use testes como evidência

Os testes devem representar o comportamento descrito na especificação. Um teste falhando pode revelar um requisito ausente; um teste passando fornece evidência para um cenário. Nenhum dos dois substitui a revisão de autorização, persistência, retries ou impacto operacional.

Um ciclo prático é:

1. escrever o cenário de aceitação;
2. confirmar que ele falha pelo motivo esperado;
3. pedir a menor implementação possível;
4. executar as verificações relevantes;
5. revisar o diff contra as invariantes originais.

Assim, a assistência da IA permanece dentro de um processo de engenharia controlado.

## Peça um plano antes do código

Para uma mudança complexa, peça ao modelo que identifique arquivos, dependências, riscos e etapas de validação. Revise o plano antes da implementação. Para uma correção pequena, uma solicitação curta e um teste focado podem ser suficientes.

O objetivo não é produzir um documento para cada edição. É manter o raciocínio proporcional ao risco e tornar explícitas as decisões importantes.

O desenvolvimento com IA funciona melhor quando contexto, requisitos, autonomia controlada e validação objetiva são combinados. O modelo acelera a implementação; a especificação mantém o resultado responsável.
