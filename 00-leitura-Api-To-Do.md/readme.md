# 00-leitura-Api-To-Do.md

## api.turma02.ts 

```` typescript
import todo from "./core.ts"; // Importa o arquivo core.ts e seus dados. 

// Usa uma função para criar o servidor usando BUN.
const server = Bun.serve({
  port: 3000, // Utiliza a porta padrão 3000. 

  routes: { // Rotas da API.
    "/api/todo": { // Rota principal da lista de tarefas.

       // Lista todos os itens.
      GET: async () => {
        const items = await todo.getItems()
        return Response.json(items)
      },

       // Adiciona um novo item.
      POST: async (req) => {
        const data = await req.json() as any;
        const item = data.item || null;

        // Validação simples.
        if (!item)
          return Response.json('Por favor, forneça um item para adicionar.', { status: 400 });
        await todo.addItem(item);
        return Response.json(data); // Retorna o item adicionado.
      },
    },

    "/api/todo/:index": { // Rota dinâmica usando índice.

      // Atualiza um item existente.
      PUT: async (req) => {
        const index = parseInt(req.params.index);

         // Verifica se o índice é válido.
        if (isNaN(index))
          return Response.json('Índice inválido. um número inteiro é esperado.', { status: 400 });
        const data = await req.json() as any;
        const newItem = data.newItem || null;

        // Verifica se o novo valor foi enviado.
        if (!newItem)
          return Response.json('Por favor, forneça um novo item para atualizar.', { status: 400 });

         // Atualiza o item da lista.
        try {
          await todo.updateItem(index, newItem);
          return Response.json(`Item no índice ${index} atualizado para "${newItem}".`);
        } catch (error: any) {
          return Response.json(error.message, { status: 400 });// Captura possíveis erros.
        }
      },

       // Remove um item da lista.
      DELETE: async (req) => {
        const index = parseInt(req.params.index);

        // Verifica se o índice é válido.
        if (isNaN(index))
          return Response.json('Índice inválido.', { status: 400 });
        try {

           // Remove o item.
          await todo.removeItem(index);
          return Response.json(`Item no índice ${index} removido com sucesso.`);
        } catch (error: any) {
          return Response.json(error.message, { status: 400 }); // Captura possíveis erros.
        }
      },
    },
  },

  async fetch(req) { // Serve arquivos estáticos da pasta public.
    const url = new URL(req.url);
    const path = url.pathname;

    // Define qual arquivo será carregado.
    const filePath = (path === '/')
      ? './public/index.html'
      : `./public${path}`;
    const file = Bun.file(filePath);

      // Retorna o arquivo caso exista.
    if (await file.exists()) {
      return new Response(file);
    }
    return new Response(`Not Found`, { status: 404 }); // Retorna 404 caso não encontre.
  },
});

console.log(`Server running at http://localhost:${server.port}`); // Mostra no terminal onde o servidor está rodando.
````
---

## core.ts

````typescrip

const jsonFilePath = __dirname + '/data.temp.json'; // Caminho do arquivo JSON onde os dados serão salvos.

const list: string[] = await loadFromFile(); // Carrega os dados do arquivo ao iniciar o sistema.


// Lê os dados do arquivo JSON.
async function loadFromFile() {
  try {

    const file = Bun.file(jsonFilePath);   // Cria referência para o arquivo.

    const content = await file.text();  // Lê o conteúdo como texto.

    return JSON.parse(content) as string[];  // Converte o JSON em array.

  } catch (error: any) {

    // Se o arquivo não existir, retorna lista vazia.
    if (error.code === 'ENOENT')
      return [];

    throw error; // Lança outros erros.
  }
}


// Salva os dados atuais no arquivo JSON.
async function saveToFile() {
  try {

    await Bun.write(jsonFilePath, JSON.stringify(list)); // Converte a lista para JSON e salva no arquivo.

  } catch (error: any) {

    // Retorna erro personalizado.
    throw new Error(
      "Erro ao salvar os dados no arquivo: " + error.message
    );
  }
}


// Adiciona um novo item na lista.
async function addItem(item: string) {

  list.push(item); // Adiciona o item no array.

  await saveToFile(); // Salva as alterações no arquivo.
}


// Retorna todos os itens da lista.
async function getItems() {
  return list;
}


// Atualiza um item existente.
async function updateItem(index: number, newItem: string) {

  // Verifica se o índice existe.
  if (index < 0 || index >= list.length)
    throw new Error("Index fora dos limites");

  list[index] = newItem; // Atualiza o item.

  await saveToFile(); // Salva as alterações.
}


// Remove um item da lista.
async function removeItem(index: number) {

  // Verifica se o índice existe.
  if (index < 0 || index >= list.length)
    throw new Error("Index fora dos limites");

  list.splice(index, 1); // Remove o item do array.

  await saveToFile(); // Salva as alterações.
}


// Exporta as funções para serem usadas em outros arquivos.
export default {
  addItem,
  getItems,
  updateItem,
  removeItem
};

````
