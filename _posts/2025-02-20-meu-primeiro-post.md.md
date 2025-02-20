---
title: "*Teste" 
author: Gabriel Fernandes
date: 2025-02-20
categories: [Malware Breakdown]
tags: [FIN7, Spear Phishing, e-mail, attachment, macros, Flare VM]
---


# **🔐 O que é Broken Access Control?**

**Broken Access Control** refere-se a falhas em mecanismos que deveriam restringir o acesso a recursos ou funcionalidades de uma aplicação. Isso significa que os usuários podem acessar, modificar ou excluir dados ou funcionalidades que não estão autorizados a ver ou manipular. É uma das vulnerabilidades mais comuns e críticas em aplicações web, frequentemente listada entre as principais ameaças no **OWASP Top 10**.

## **🔑 Principais Conceitos Relacionados ao Controle de Acesso**

### **1️⃣ Autenticação (Authentication)**

Autenticação é o processo de verificar a identidade de um usuário. Ou seja, garantir que a pessoa que está tentando acessar o sistema é realmente quem diz ser.

**Métodos comuns de autenticação:**

- **Senha tradicional**: O usuário insere um nome de usuário e uma senha.
- **Autenticação Multifator (MFA)**: Combina algo que o usuário sabe (senha) com algo que possui (token) ou algo que é (biometria).
- **OAuth/OpenID Connect**: Protocolos para login unificado e seguro em aplicações.

🔍 **Erro comum relacionado a Broken Access Control:**

- Aplicações que falham em verificar a identidade do usuário adequadamente, permitindo acesso não autorizado.

---

### **2️⃣ Gerenciamento de Sessão (Session Management)**

O gerenciamento de sessão mantém um estado persistente para um usuário autenticado, permitindo que ele navegue sem precisar se autenticar a cada requisição.

**Componentes de um gerenciamento de sessão seguro:**

- **Cookies seguros:** Devem ser marcados como `HttpOnly`, `Secure`, e com `SameSite` ativado.
- **Tokens JWT:** Devem ser assinados corretamente para evitar manipulação.
- **Expiração de sessão:** Sessões devem expirar após um tempo de inatividade para evitar sequestro de sessão.
- **Proteção contra Fixação de Sessão:** Garante que um atacante não consiga reutilizar um ID de sessão válido.

🔍 **Erro comum relacionado a Broken Access Control:**

- Sessões que não expiram corretamente ou que podem ser reutilizadas por atacantes para sequestrar a conta de um usuário legítimo.

---

### **3️⃣ Controle de Acesso (Access Control)**

Controle de acesso refere-se ao mecanismo que define quais recursos ou funcionalidades um usuário pode acessar.

**Tipos de controle de acesso:**

1. **Baseado em Função (RBAC - Role-Based Access Control)**: Acesso concedido com base na função do usuário (exemplo: `admin`, `user`).
2. **Baseado em Atributos (ABAC - Attribute-Based Access Control)**: Usa atributos (exemplo: localidade, horário, dispositivo) para determinar permissões.
3. **Baseado em Listas de Controle de Acesso (ACL)**: Especifica quem pode acessar quais recursos diretamente.

🔍 **Erro comum relacionado a Broken Access Control:**

- APIs que não validam corretamente se o usuário tem permissão para executar determinada ação.

---

### **4️⃣ Controle de Acesso Vertical (Vertical Access Control / Privilege Escalation)**

Ocorre quando um usuário consegue elevar seus privilégios indevidamente, acessando funções administrativas ou mais avançadas sem permissão.

**Exemplo:**

- Um usuário comum acessa a URL de um painel administrativo:
    
    plaintext
    
    CopyEdit
    
    `http://exemplo.com/admin`
    
    Se a aplicação não validar a função do usuário corretamente, ele poderá acessar essa página indevidamente.

🔍 **Erro comum:**

- Aplicações que não verificam adequadamente os privilégios de um usuário, permitindo que ele atue como administrador.

🔒 **Mitigação:**

- Implementar checagens rigorosas de privilégios no backend.

---

### **5️⃣ Controle de Acesso Horizontal (Horizontal Access Control / IDOR - Insecure Direct Object References)**

Ocorre quando um usuário consegue acessar dados ou recursos de outros usuários do mesmo nível de privilégio.

**Exemplo:**

- Um usuário acessa a URL do perfil de outro usuário apenas alterando o ID na URL:
    
    plaintext
    
    CopyEdit
    
    `http://exemplo.com/perfil?id=1001  (perfil do usuário A)   http://exemplo.com/perfil?id=1002  (perfil do usuário B)`  
    
    Se a aplicação não verificar a identidade do usuário, ele poderá visualizar ou editar o perfil de outra pessoa.

🔍 **Erro comum:**

- APIs que aceitam qualquer ID sem verificar se pertence ao usuário autenticado.

🔒 **Mitigação:**

- Sempre verificar se o usuário autenticado tem permissão para acessar um determinado recurso antes de retornar a resposta.

---

### **6️⃣ Controle de Acesso Context-Dependent (Baseado em Contexto)**

O controle de acesso baseado em contexto leva em consideração fatores adicionais além da autenticação, como localização, horário ou dispositivo utilizado.

**Exemplo:**

- Um funcionário só pode acessar certos dados quando estiver dentro da rede corporativa.
- Bloqueio de login quando um usuário tenta acessar de um país diferente do habitual.

🔍 **Erro comum:**

- Aplicações que não utilizam contexto para restringir acessos, permitindo que um atacante de outro país acesse uma conta sem nenhuma verificação adicional.

🔒 **Mitigação:**

- Implementar monitoramento de contexto, bloqueando acessos suspeitos.
    

---

## **🛠 Tipos Comuns de Broken Access Control**

### **1. Escalada de Privilégios**

Um usuário comum consegue obter permissões de administrador ou outras funções privilegiadas. Isso pode ocorrer devido a uma validação inadequada das permissões.

**Exemplo:** Um usuário que modifica a URL ou manipula parâmetros para acessar funções administrativas.

### **2. Insecure Direct Object References (IDOR)**

Refere-se a vulnerabilidades onde um usuário pode acessar objetos (dados) diretamente alterando identificadores (IDs) em URLs ou requisições. Se a aplicação não verifica se o usuário tem permissão para acessar esse objeto, o acesso indevido pode ocorrer.

**Exemplo:** Acessar o perfil de outro usuário através de uma URL como `http://example.com/profile?id=2`.

### **3. Bypass de Verificações de Acesso**

Um usuário pode contornar verificações de acesso, seja manipulando solicitações HTTP ou explorando falhas no código.

**Exemplo:** Uma API que não implementa autenticação adequada, permitindo que qualquer pessoa execute operações sensíveis.

### **4. Falta de Controles em APIs**

APIs que não implementam controles de acesso adequados podem permitir que usuários não autenticados realizem operações críticas, como deletar dados ou criar novos registros.

**Exemplo:** Uma API que permite operações `POST` ou `DELETE` sem verificar se o usuário está autenticado e autorizado.

---

## **⚠️ Causas Comuns de Broken Access Control**

4. **Verificações de Acesso Ausentes ou Inadequadas:** Falta de validação para verificar se um usuário tem permissão para realizar uma ação.
5. **Referências Diretas a Objetos:** Uso de IDs visíveis que podem ser facilmente manipulados pelos usuários.
6. **Configuração Inadequada de APIs:** APIs que não implementam autenticação e autorização corretamente.
7. **Falta de Testes de Segurança:** Ausência de revisões regulares ou testes de penetração que identificariam falhas de controle de acesso.

---
