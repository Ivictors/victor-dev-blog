---
title: "Secret Manager e menor privilégio em microsserviços"
description: "Como serviços podem acessar credenciais sem espalhar segredos pelo código-fonte, imagens ou logs."
date: 2026-11-22
lang: pt-BR
locale: pt
layout: post-localized
translation-id: secret-manager-microservices
link: /pt/posts/secret-manager-microservicos/
alternate: /en/posts/secret-manager-microservices/
tags: microsservicos, seguranca, aws
author: victor
---

Senhas de banco, tokens de API e chaves privadas precisam de acesso restrito e rotação. Colocá-los no código ou em imagens de contêiner dificulta controlar quem os utiliza e com que rapidez podem ser substituídos.

## Use um armazenamento especializado

Um Secret Manager armazena valores protegidos, verifica a identidade da carga de trabalho e devolve apenas os segredos que essa identidade pode ler. HashiCorp Vault e AWS Secrets Manager são exemplos. O serviço deve se autenticar por uma identidade confiável da plataforma, e não por uma senha fixa no código.

```text
Serviço de Pagamentos -> identidade da carga -> Secret Manager
Serviço de Pagamentos <- segredo autorizado
Serviço de Pagamentos -> provedor de pagamento
```

Conceda a cada serviço somente os segredos necessários. Evite registrar valores em logs, copiá-los para imagens ou devolvê-los em erros. A rotação também exige que a aplicação consiga recarregar ou renovar credenciais com segurança.

## Planeje indisponibilidade e auditoria

Defina o comportamento quando o Secret Manager estiver indisponível. Um valor em cache de curta duração pode ser aceitável por um período limitado, enquanto uma nova inicialização pode precisar falhar de forma fechada. Registre acessos para investigação e revogue credenciais comprometidas.

Um Secret Manager não é um microserviço próprio para senhas. Criá-lo adiciona responsabilidades difíceis de armazenamento, autenticação, disponibilidade, rotação e auditoria. Integre uma solução especializada e mantenha a proteção dos dados de negócio separada do armazenamento de segredos.
