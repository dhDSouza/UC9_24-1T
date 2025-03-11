## **Aula: Java Swing com Gradle e Conexão com Banco de Dados em MVC**

### **Objetivo da Aula**
- Entender como estruturar um projeto Java Swing utilizando Gradle.
- Implementar a conexão com banco de dados seguindo o padrão **MVC**.
- Aprender boas práticas para organização do código.

---

## **1. Criando um Projeto Java Swing com Gradle**

### **Passo 1: Criando um novo projeto Gradle**
Se estiver usando o IntelliJ IDEA:
1. Vá em **File** → **New** → **Project**.
2. Escolha **Gradle** e marque **Java**.
3. Configure o nome do projeto e finalize.

Caso prefira criar manualmente pelo terminal:
```sh
gradle init --type java-application
```

### **Passo 2: Adicionando Dependências**
No arquivo **`build.gradle`**, adicione:

```gradle
plugins {
    id 'java'
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'mysql:mysql-connector-java:8.0.33' // Conector MySQL
    implementation 'org.xerial:sqlite-jdbc:3.42.0.0'  // Conector SQLite
}
```

Depois, execute:
```sh
gradle build
```

---

## **2. Estruturando o Projeto em MVC**

### **Analogia: Restaurante**
Um restaurante bem organizado tem:
- **View (Garçom):** Interface com o usuário (telas do Swing).
- **Controller (Cozinheiro):** Processa os pedidos e gerencia as regras de negócio.
- **Model (Estoque/Cadastro de pratos):** Interage com o banco de dados.

A estrutura de diretórios será:

```
src/main/java/com/exemplo
│── model
│   ├── Conexao.java
│   ├── Cliente.java
│   ├── ClienteDAO.java
│── view
│   ├── TelaPrincipal.java
│── controller
│   ├── ClienteController.java
│── Main.java
```

---

## **3. Implementação Prática**

### **Model: Conectando ao Banco de Dados**

A classe `Conexao.java` gerencia a conexão com o banco e cria a tabela automaticamente:

```java
package model;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class Conexao {
    private static final String URL = "jdbc:mysql://localhost:3306/seu_banco";
    private static final String USUARIO = "root";
    private static final String SENHA = "";
    private static Connection connection;

    public static Connection conectar() {
        try {
            if (connection == null || connection.isClosed()) {
                connection = DriverManager.getConnection(URL, USUARIO, SENHA);
                criarTabela();
            }
            return connection;
        } catch (SQLException e) {
            throw new RuntimeException("Erro na conexão com o banco de dados", e);
        }
    }

    public static void fechar() {
        try {
            if (connection != null && !connection.isClosed()) {
                connection.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private static void criarTabela() {
        String sql = "CREATE TABLE IF NOT EXISTS clientes (id INT AUTO_INCREMENT PRIMARY KEY, nome VARCHAR(255) NOT NULL)";
        
        try (Statement stmt = connection.createStatement()) {
            stmt.execute(sql);
            System.out.println("Tabela 'clientes' verificada/criada com sucesso.");
        } catch (SQLException e) {
            throw new RuntimeException("Erro ao criar a tabela no banco de dados", e);
        }
    }
}
```

A classe `Cliente.java` representa um cliente:

```java
package model;

public class Cliente {
    private int id;
    private String nome;

    public Cliente(int id, String nome) {
        this.id = id;
        this.nome = nome;
    }

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }

    public String getNome() { return nome; }
    public void setNome(String nome) { this.nome = nome; }
}
```

A classe `ClienteDAO.java` gerencia operações no banco:

```java
package model;

import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class ClienteDAO {
    public void adicionarCliente(Cliente cliente) {
        String sql = "INSERT INTO clientes (nome) VALUES (?)";

        try (Connection conn = Conexao.conectar();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, cliente.getNome());
            stmt.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public List<Cliente> listarClientes() {
        List<Cliente> clientes = new ArrayList<>();
        String sql = "SELECT * FROM clientes";

        try (Connection conn = Conexao.conectar();
             Statement stmt = conn.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {

            while (rs.next()) {
                clientes.add(new Cliente(rs.getInt("id"), rs.getString("nome")));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return clientes;
    }
}
```

---

### **Controller: Manipulando os Dados**

```java
package controller;

import model.Cliente;
import model.ClienteDAO;

import java.util.List;

public class ClienteController {
    private ClienteDAO clienteDAO = new ClienteDAO();

    public void adicionarCliente(String nome) {
        Cliente cliente = new Cliente(0, nome);
        clienteDAO.adicionarCliente(cliente);
    }

    public List<Cliente> listarClientes() {
        return clienteDAO.listarClientes();
    }
}
```

---

### **View: Criando a Interface Gráfica**

```java
package view;

import controller.ClienteController;
import model.Cliente;

import javax.swing.*;

public class TelaPrincipal extends javax.swing.JFrame {
    private ClienteController controller = new ClienteController();

    public TelaPrincipal() {
        initComponents();
    }

    private void btnRegistrarActionPerformed(java.awt.event.ActionEvent evt) {
        controller.adicionarCliente(txtNome.getText());
        atualizarLista();
    }

    private void atualizarLista() {
        txtAreaClientes.setText("");
        for (Cliente c : controller.listarClientes()) {
            txtAreaClientes.append(c.getId() + " - " + c.getNome() + "\n");
        }
    }
}
```

---

## **4. Exercícios**
1. Adicionar um campo **email** no cadastro de clientes.
2. Criar um botão para excluir clientes.
3. Permitir a edição do nome de um cliente.
4. Modificar `Conexao` para suportar SQLite.

