<div style="color: #FF69B4;">

# ✨ Guia Prático de APIs e Requisições HTTP

<div align="center" style="color: #DB7093;">
Um guia completo para entender e trabalhar com APIs
</div>

## 📌 Índice
- [O que é uma API?](#-o-que-é-uma-api)
- [Métodos HTTP](#-métodos-http)
- [Status Codes](#-status-codes)
- [Tipos de Parâmetros](#-tipos-de-parâmetros)
- [Exemplos Práticos](#-exemplos-práticos)
- [Boas Práticas](#-boas-práticas)

## 🌸 O que é uma API?

<div style="color: #FFB6C1;">

API (Application Programming Interface) é como um "cardápio de restaurante":
- O cliente (você) faz um pedido
- O garçom (API) leva o pedido para a cozinha (servidor)
- A cozinha prepara e devolve o pedido
- O garçom entrega o prato pronto ao cliente

Na prática, é um conjunto de regras que permite diferentes softwares se comunicarem.
</div>

## 🚀 Métodos HTTP

<div style="color: #DB7093;">

### Principais Métodos
- **GET**: Buscar dados
  ```http
  GET /api/usuarios
  ```

- **POST**: Criar dados
  ```http
  POST /api/usuarios
  {
    "nome": "Maria",
    "email": "maria@email.com"
  }
  ```

- **PUT**: Atualizar dados (completo)
  ```http
  PUT /api/usuarios/1
  {
    "nome": "Maria Silva",
    "email": "maria@email.com"
  }
  ```

- **PATCH**: Atualizar dados (parcial)
  ```http
  PATCH /api/usuarios/1
  {
    "nome": "Maria Silva"
  }
  ```

- **DELETE**: Remover dados
  ```http
  DELETE /api/usuarios/1
  ```
</div>

## 🎯 Status Codes

<div style="color: #FF69B4;">

### Principais Códigos
- **2xx**: Sucesso
  - 200: OK
  - 201: Criado
  - 204: Sem conteúdo

- **4xx**: Erro do Cliente
  - 400: Requisição inválida
  - 401: Não autorizado
  - 404: Não encontrado
  - 422: Entidade não processável

- **5xx**: Erro do Servidor
  - 500: Erro interno
  - 503: Serviço indisponível
</div>

## 📦 Tipos de Parâmetros

<div style="color: #FFB6C1;">

### Query Params
```javascript
// URL: /api/usuarios?status=ativo&ordem=nome
{
  status: 'ativo',
  ordem: 'nome'
}
```

### Path Params
```javascript
// URL: /api/usuarios/123
{
  id: '123'
}
```

### Request Body
```javascript
// POST /api/usuarios
{
  "nome": "Maria",
  "email": "maria@email.com",
  "senha": "123456"
}
```

### Headers
```javascript
{
  "Authorization": "Bearer token123",
  "Content-Type": "application/json"
}
```
</div>

## 💻 Exemplos Práticos

<div style="color: #DB7093;">

### Fetch API
```javascript
// GET Request
fetch('https://api.exemplo.com/usuarios')
  .then(response => response.json())
  .then(data => console.log(data));

// POST Request
fetch('https://api.exemplo.com/usuarios', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    nome: 'Maria',
    email: 'maria@email.com'
  })
});
```

### Axios
```javascript
// GET Request
axios.get('https://api.exemplo.com/usuarios')
  .then(response => console.log(response.data));

// POST Request
axios.post('https://api.exemplo.com/usuarios', {
  nome: 'Maria',
  email: 'maria@email.com'
});
```
</div>

## 💡 Boas Práticas

<div style="color: #FFB6C1;">

### RESTful
- Use substantivos no plural para recursos
  - ✅ `/usuarios`
  - ❌ `/getUsuario`

- Use HTTP methods corretamente
  - ✅ `GET /usuarios`
  - ❌ `POST /buscarUsuarios`

### Segurança
- Sempre use HTTPS
- Implemente rate limiting
- Valide inputs
- Use autenticação quando necessário

### Versionamento
- Inclua versão na URL
  - `/api/v1/usuarios`
  - `/api/v2/usuarios`

### Documentação
- Documente todos os endpoints
- Inclua exemplos de requisição/resposta
- Descreva todos os parâmetros
- Mantenha atualizada
</div>

## 🔄 Tratamento de Erros

<div style="color: #DB7093;">

### Estrutura de Erro
```javascript
{
  "erro": true,
  "mensagem": "Usuário não encontrado",
  "codigo": "USER_NOT_FOUND",
  "status": 404
}
```

### Try/Catch
```javascript
try {
  const response = await fetch('https://api.exemplo.com/usuarios');
  const data = await response.json();
} catch (erro) {
  console.error('Erro:', erro);
}
```
</div>

---

<div align="center" style="color: #FF69B4;">

**Dúvidas?**
Consulte a documentação das APIs que está utilizando ou abra uma issue!

*Feito com ♥️ para a comunidade dev*

</div>

</div>
