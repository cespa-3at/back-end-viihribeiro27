[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/EKrYHeU8)
# back-end-3at

front back e banco

Atividade: Conectando o Formulário ao Banco de Dados (Node.js + Express)

Nesta tarefa, você vai transformar seu banco de dados estático em uma aplicação real. Você criará um servidor que recebe informações de um formulário HTML e as grava automaticamente no seu arquivo `.db`.

Objetivo

Integrar o **Frontend** (HTML) com o **Backend** (Node.js) e a **Persistência** (SQLite3), permitindo que qualquer pessoa cadastre contatos no seu banco através do navegador.

---

Passo 1: Criar o Banco e a Estrutura (Terminal)

No terminal do seu Codespace, crie o arquivo e a tabela que receberá os dados. Substitua `seu_nome` pelo seu nome real:

1.  **Criar o arquivo:**

    terminal

    ```
    sqlite3 seu_nome.db

    ```

2.  **Criar a Tabela (Dentro do prompt `sqlite>`):**  
    _Copie e cole o comando abaixo e dê Enter:_

    sql - terminal

    ```
    CREATE TABLE contatos (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        nome TEXT NOT NULL,
        telefone TEXT,
        email TEXT UNIQUE
    );

    ```

3.  **Validar e Sair:**  
    Digite `.tables` para ver se a tabela `contatos` aparece. Depois, saia com `.exit`.

---

Passo 2: Criar o Servidor

Agora vamos criar o código que recebe os dados do site e entrega para o banco que você acabou de criar.

1.  **Instalar as ferramentas:** No terminal, digite:

    Terminal

    ```
    npm install express sqlite3

    ```

2.  **Criar o arquivo `server.js`:** Crie um novo arquivo e cole o código que faz a ponte:

    javascript

   ```
const express = require('express');
const sqlite3 = require('sqlite3').verbose();
const path = require('path');
const app = express();

// Configura o servidor para entender os dados vindos do formulário (POST)
app.use(express.urlencoded({ extended: true }));

// Conecta ao banco de dados (certifique-se de que o nome do arquivo .db está correto)
const db = new sqlite3.Database('./seu_nome.db');

// ROTA PRINCIPAL: Abre o formulário (index.html) quando você acessa o site
app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'index.html'));
});

// ROTA DE SALVAMENTO: Recebe os dados e grava no SQLite
app.post('/salvar', (req, res) => {
    const { nome, telefone, email } = req.body;
    const sql = `INSERT INTO contatos (nome, telefone, email) VALUES (?, ?, ?)`;

    db.run(sql, [nome, telefone, email], function(err) {
        if (err) {
            return res.send("<h1>Erro ao salvar!</h1><p>" + err.message + "</p><a href='/'>Voltar</a>");
        }
        res.send("<h1>Sucesso!</h1><p>Contato salvo no banco.</p><a href='/'>Voltar e cadastrar outro</a>");
    });
});

// Inicia o servidor na porta 3000
app.listen(3000, () => {
    console.log("------------------------------------------");
    console.log("Servidor rodando em http://localhost:3000");
    console.log("Pressione Ctrl + C para parar o servidor");
    console.log("------------------------------------------");
});

 ```
---

Passo 3: Criar o Formulário (A Interface)

Crie o arquivo `index.html` para o usuário digitar os dados:
Lembrando que o objetivo não é focar no html ...

html

```
<form action="/salvar" method="POST">
    <input type="text" name="nome" placeholder="Nome" required>
    <input type="text" name="telefone" placeholder="Telefone">
    <input type="email" name="email" placeholder="E-mail">
    <button type="submit">Salvar no Banco</button>
</form>

```

---

Passo 4: Testar e Validar

1.  No terminal, inicie o servidor: `node server.js`.
2.  Clique em **"Open in Browser"** e preencha o formulário.
3.  Após ver a mensagem de sucesso, volte ao terminal e verifique se o dado "entrou na gaveta":

terminal

    ```
    sqlite3 seu_nome.db "SELECT * FROM contatos;"

    ```

---

Entrega (Git)

Envie tudo para o seu repositório para correção:

terminal

```
git add index.html server.js seu_nome.db
git commit -m "Tarefa: Integração completa HTML + Node + SQLite"
git push origin main

```

Dicas:

- **O Servidor precisa estar rodando:** Se você fechar o terminal ou parar o processo (`Ctrl+C`), o formulário vai dar erro de "Site fora do ar".
- **Caminho do Banco:** Verifique se o nome no código (`new sqlite3.Database('./seu_nome.db')`) é exatamente igual ao arquivo que você criou na atividade anterior.
- **Erro 404:** Se aparecer "Cannot POST /salvar", verifique se o `action` no HTML é igual ao nome da rota no `app.post`.
