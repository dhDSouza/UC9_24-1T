# 🖥️ **Atividade Prática: Aplicação Java Swing com MySQL**  

## 📌 **Objetivo**  
Nesta atividade, vocês deverão desenvolver uma aplicação desktop utilizando **Java Swing** para interface gráfica e **MySQL** para armazenamento de dados. O objetivo é aplicar conceitos de **CRUD (Create, Read, Update, Delete)**, conexão com banco de dados e boas práticas de desenvolvimento.  

Vocês trabalharão **em duplas** e terão **3 dias** para concluir e entregar o projeto.  

---

## 📜 **Cenário da Aplicação**  

Vocês foram contratados para desenvolver um sistema de **Cadastro de Produtos** para uma pequena loja. O sistema deve permitir que os usuários realizem operações básicas como cadastrar, listar, atualizar e excluir produtos de um banco de dados.  

O dono da loja quer uma interface simples, mas funcional, onde ele possa gerenciar os produtos com facilidade.  

---

## 🎯 **Requisitos da Atividade**  

### 🔹 **1. Banco de Dados**  
Criem um banco de dados MySQL chamado `loja_db` com uma tabela chamada `produtos`.  

A tabela deve ter os seguintes campos:  
- `id` (INT, PRIMARY KEY, AUTO_INCREMENT)  
- `nome` (VARCHAR(100), NOT NULL)  
- `descricao` (TEXT, NOT NULL)  
- `preco` (DECIMAL(10,2), NOT NULL)  
- `quantidade` (INT, NOT NULL)  

### 🔹 **2. Interface Gráfica (Java Swing)**  
Criem uma aplicação desktop com as seguintes telas:  

#### 🏠 **Tela Principal:**  
- Deve ter um **menu ou botões** para navegar entre as funções do sistema (cadastrar, listar, atualizar e excluir produtos).  

#### ➕ **Tela de Cadastro de Produto:**  
- Campos para inserir **Nome, Descrição, Preço e Quantidade**.  
- Botão "Salvar" para inserir o produto no banco de dados.  

#### 📋 **Tela de Listagem de Produtos:**  
- Uma **tabela (JTable)** para exibir todos os produtos cadastrados.  
- Deve permitir **selecionar um produto** para edição ou exclusão.  

#### ✏️ **Tela de Edição de Produto:**  
- Ao selecionar um produto na listagem, deve abrir uma tela com os dados preenchidos.  
- Deve permitir **alterar** os valores e salvar as mudanças.  

#### ❌ **Excluir Produto:**  
- Deve ser possível excluir um produto da listagem, com um alerta de confirmação.  

### 🔹 **3. Conexão com Banco de Dados**  
- Usem **JDBC** para conectar a aplicação ao banco de dados MySQL.  
- Criem uma classe para gerenciar a conexão com o banco.  

### 🔹 **4. Funcionalidades Obrigatórias**  
✔ Cadastrar produtos no banco de dados.  
✔ Listar os produtos em uma tabela.  
✔ Atualizar os dados de um produto.  
✔ Excluir um produto com confirmação.  

---

## 🔥 **Desafios Extras (Opcional)**  
🚀 **Filtro de Busca:** Adicionem um campo de pesquisa para filtrar produtos pelo nome.  
🚀 **Validações:** Impedir cadastro de produtos com nome vazio, preço negativo ou quantidade menor que zero.  
🚀 **Melhoria no Layout:** Personalizem a interface gráfica para deixar mais agradável e intuitiva.  

---

## 📅 **Entrega e Avaliação**  

### 📂 **O que deve ser entregue?**

No final do terceiro dia, entreguem um **arquivo ZIP** com o código-fonte do projeto.

### 🎯 **Critérios de Avaliação**  

🔹 Funcionalidades implementadas corretamente.  
🔹 Código bem estruturado e organizado.  
🔹 Interface gráfica funcionando.  
🔹 Conexão com banco de dados sem erros.  

---

## 🛠️ **Dicas e Recursos Úteis**  
📌 **Drivers JDBC:** Certifiquem-se de baixar e configurar corretamente o driver do MySQL para Java.  
📌 **Referências:**  
- [Documentação JDBC](https://docs.oracle.com/javase/tutorial/jdbc/basics/)  
- [Java Swing Components](https://docs.oracle.com/javase/tutorial/uiswing/components/)  

🚀 **Mãos à obra, programadores!** Qualquer dúvida, o professor estará tomando um café ☕, não o pertube! Brincadeira 😃
