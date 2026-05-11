# Garatujas
## Oque precisa para fazer um Servidor funcionar

### 1. Instalar o Bun

Linux/macOS:

```bash id="lyp6rx"
curl -fsSL https://bun.sh/install | bash
```

Windows:

[Bun Oficial](https://bun.sh?utm_source=chatgpt.com)

Verifique:

```bash id="3mkp2k"
bun --version
```

---

### 2. Criar projeto

```bash id="gtibut"
mkdir servidor-bun
cd servidor-bun
```

---

### 3. Inicializar

```bash id="n1mk50"
bun init
```

Ele cria:

* `package.json`
* `tsconfig.json`
* estrutura básica

---

### 4. Criar servidor

Crie:

```txt id="w4mx9i"
src/index.ts
```

E coloque:

```ts id="djjx3w"
const server = Bun.serve({
    port: 3000,

    fetch(req) {
        return new Response("Servidor Bun + TypeScript funcionando!");
    },
});

console.log(`Servidor rodando em http://localhost:${server.port}`);
```

---

### 5. Rodar

```bash id="8o4c7r"
bun run src/index.ts
```

ou:

```bash id="7j58q9"
bun src/index.ts
```

---

### Como esse servidor funciona

Agora a parte importante.

---

### 1. `Bun.serve()`

```ts id="4h4e9r"
Bun.serve({...})
```

Isso cria um servidor HTTP.

É parecido com:

* Node HTTP
* Express
* Fastify

Mas muito mais leve e rápido.

---

### 2. Porta

```ts id="f4q2r4"
port: 3000
```

Define onde o servidor vai escutar.

A URL fica:

```txt id="mk6g0z"
http://localhost:3000
```

---

### 3. Método `fetch`

```ts id="z8n9m0"
fetch(req) {
```

Toda requisição passa aqui.

O Bun usa o padrão Web API moderno, igual navegador.

---

### 4. `req`

```ts id="2cl7e0"
req
```

É a requisição HTTP.

Você consegue acessar:

* URL
* headers
* método
* body

Exemplo:

```ts id="d8k72d"
console.log(req.method);
console.log(req.url);
```

---

### 5. `Response`

```ts id="1n6e8u"
return new Response("Olá");
```

Retorna a resposta HTTP.

---

### Fluxo do servidor

```txt id="0k2g8k"
Navegador
   ↓
Request HTTP
   ↓
Bun recebe
   ↓
fetch()
   ↓
Response enviada
   ↓
Navegador recebe
```

---

### Criando rotas

Você pode verificar a URL manualmente.

Exemplo:

```ts id="wcfm2k"
const server = Bun.serve({
    port: 3000,

    fetch(req) {
        const url = new URL(req.url);

        if (url.pathname === "/") {
            return new Response("Home");
        }

        if (url.pathname === "/api") {
            return Response.json({
                nome: "Mateus",
                runtime: "Bun",
            });
        }

        return new Response("404", {
            status: 404,
        });
    },
});

console.log(`Servidor rodando em ${server.url}`);
```

---

### Explicação das rotas

#### Pegando URL

```ts id="7v9v7i"
const url = new URL(req.url);
```

Transforma a URL em objeto.

---

#### Verificando rota

```ts id="4rmw0t"
url.pathname === "/api"
```

Confere qual endpoint foi acessado.

---

#### JSON

```ts id="3s6rt0"
Response.json({...})
```

Retorna JSON automaticamente.

---

### Testando

#### Home

```txt id="j1c9nf"
http://localhost:3000/
```

Resposta:

```txt id="ylhy8q"
Home
```

---

#### API

```txt id="13s41r"
http://localhost:3000/api
```

Resposta:

```json id="8icjvq"
{
  "nome": "Mateus",
  "runtime": "Bun"
}
```

---
