<div style="color: #FF69B4;">

# 🔐 Guia de Segurança Web Básica

<div align="center" style="color: #DB7093;">
Um guia essencial de segurança para desenvolvedores web
</div>

## 📌 Índice
- [HTTPS e SSL/TLS](#-https-e-ssltls)
- [Autenticação e Autorização](#-autenticação-e-autorização)
- [Tokens e JWT](#-tokens-e-jwt)
- [Vulnerabilidades Comuns](#-vulnerabilidades-comuns)
- [Boas Práticas](#-boas-práticas)
- [Checklist de Segurança](#-checklist-de-segurança)

## 🌐 HTTPS e SSL/TLS

<div style="color: #FFB6C1;">

### O que é HTTPS?
- Versão segura do HTTP
- Criptografa todos os dados transmitidos
- Protege contra interceptação de dados
- Fornece certificado digital para o site

### Por que usar HTTPS?
- Protege dados sensíveis
- Melhora ranking no Google
- Aumenta a confiança dos usuários
- Previne ataques man-in-the-middle

### Como Implementar
```javascript
// Redirecionar HTTP para HTTPS
if (!req.secure) {
    return res.redirect('https://' + req.headers.host + req.url);
}
```
</div>

## 🔑 Autenticação e Autorização

<div style="color: #DB7093;">

### Autenticação
- **O que é**: Verifica quem é o usuário
- **Métodos comuns**:
  - Login com senha
  - OAuth
  - Biometria
  - 2FA (Two-Factor Authentication)

### Autorização
- **O que é**: Verifica o que o usuário pode fazer
- **Métodos comuns**:
  - Roles (papéis)
  - Permissions (permissões)
  - Access Control Lists (ACL)

### Exemplo de Middleware de Autenticação
```javascript
const authMiddleware = (req, res, next) => {
    const token = req.headers.authorization;
    
    if (!token) {
        return res.status(401).json({ error: 'Token não fornecido' });
    }

    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        req.user = decoded;
        next();
    } catch (error) {
        return res.status(401).json({ error: 'Token inválido' });
    }
};
```
</div>

## 🎟️ Tokens e JWT

<div style="color: #FFB6C1;">

### JWT (JSON Web Token)
- **Estrutura**:
  - Header (algoritmo)
  - Payload (dados)
  - Signature (assinatura)

### Exemplo de JWT
```javascript
// Criar token
const token = jwt.sign(
    { userId: user.id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '24h' }
);

// Verificar token
const decoded = jwt.verify(token, process.env.JWT_SECRET);
```

### Armazenamento Seguro
- ✅ HttpOnly Cookies
- ✅ Secure Flag
- ❌ localStorage (evitar para tokens de autenticação)
</div>

## ⚠️ Vulnerabilidades Comuns

<div style="color: #DB7093;">

### 1. XSS (Cross-Site Scripting)
```javascript
// ❌ Vulnerável
element.innerHTML = userInput;

// ✅ Seguro
element.textContent = userInput;
```

### 2. SQL Injection
```javascript
// ❌ Vulnerável
const query = `SELECT * FROM users WHERE id = ${userId}`;

// ✅ Seguro
const query = 'SELECT * FROM users WHERE id = ?';
db.query(query, [userId]);
```

### 3. CSRF (Cross-Site Request Forgery)
```javascript
// Proteção com token CSRF
app.use(csrf());
app.use((req, res, next) => {
    res.locals.csrfToken = req.csrfToken();
    next();
});
```

### 4. Broken Authentication
- Implementar bloqueio após tentativas falhas
- Exigir senhas fortes
- Implementar 2FA quando possível
</div>

## 💡 Boas Práticas

<div style="color: #FF69B4;">

### Senhas
- Nunca armazene senhas em texto puro
- Use bcrypt ou Argon2 para hash
- Implemente política de senhas fortes

```javascript
// Hash de senha com bcrypt
const hashedPassword = await bcrypt.hash(password, 10);

// Verificar senha
const isValid = await bcrypt.compare(password, hashedPassword);
```

### Headers de Segurança
```javascript
// Configurar headers de segurança
app.use(helmet());  // Para Express.js

// Headers importantes
{
    "Content-Security-Policy": "default-src 'self'",
    "X-Frame-Options": "DENY",
    "X-Content-Type-Options": "nosniff",
    "Strict-Transport-Security": "max-age=31536000"
}
```

### Validação de Dados
```javascript
// Exemplo com express-validator
const validateUser = [
    check('email').isEmail(),
    check('password').isLength({ min: 8 }),
    check('name').trim().escape()
];
```
</div>

## ✅ Checklist de Segurança

<div style="color: #FFB6C1;">

### Básico
- [ ] HTTPS em produção
- [ ] Senhas sempre hasheadas
- [ ] Validação de inputs
- [ ] Headers de segurança
- [ ] Rate limiting
- [ ] Logs de segurança

### Autenticação
- [ ] Política de senhas fortes
- [ ] Proteção contra força bruta
- [ ] Tokens seguros
- [ ] Session management
- [ ] 2FA quando possível

### Dados
- [ ] Sanitização de inputs
- [ ] Criptografia de dados sensíveis
- [ ] Backup regular
- [ ] Política de dados pessoais
</div>

## 🚨 Monitoramento

<div style="color: #DB7093;">

### Logs de Segurança
```javascript
// Exemplo de log de tentativa de login
logger.info({
    event: 'login_attempt',
    user: email,
    ip: req.ip,
    userAgent: req.headers['user-agent'],
    timestamp: new Date()
});
```

### Alertas
- Configurar alertas para:
  - Múltiplas tentativas de login falhas
  - Acessos suspeitos
  - Alterações críticas no sistema
  - Erros de servidor frequentes
</div>

---

<div align="center" style="color: #FF69B4;">

**Importante!**
Este é um guia básico. Sempre mantenha-se atualizado sobre novas vulnerabilidades e boas práticas!

*Para a comunidade dev*

</div>

</div>
