## **Aula: Implementando um Sistema de Login com Hash de Senha em Java Swing (MVC e Gradle)**

### **Objetivo da Aula**
- Criar um sistema de login/logout usando **Java Swing**.
- Implementar **hash de senha** ao salvar no banco de dados.
- Seguir o padrão **MVC** para organização do código.
- Utilizar **Gradle** para gerenciar dependências.

---

## **1. Criando o Projeto Java Swing com Gradle**

### **Passo 1: Criando um novo projeto Gradle**
Se estiver usando o IntelliJ IDEA:
1. Vá em **File** → **New** → **Project**.
2. Escolha **Gradle** e marque **Java**.
3. Configure o nome do projeto e finalize.

Caso prefira criar pelo terminal:
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
    implementation 'org.mindrot:jbcrypt:0.4' // Biblioteca para hash de senha
}
```

Depois, execute:
```sh
gradle build
```

---

## **2. Estruturando o Projeto em MVC**
A estrutura de diretórios será:

```
src/main/java/com/exemplo
│── model
│   ├── Conexao.java
│   ├── Usuario.java
│   ├── UsuarioDAO.java
│── view
│   ├── TelaLogin.java
│   ├── TelaPrincipal.java
│── controller
│   ├── UsuarioController.java
│── Main.java
```

---

## **3. Implementação do Sistema de Login**

### **Model: Conexão com o Banco de Dados**

A classe `Conexao.java` gerencia a conexão com o banco:

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

    private static void criarTabela() {
        String sql = "CREATE TABLE IF NOT EXISTS usuarios (" +
                     "id INT AUTO_INCREMENT PRIMARY KEY, " +
                     "usuario VARCHAR(255) NOT NULL, " +
                     "senha VARCHAR(255) NOT NULL)";
        try (Statement stmt = connection.createStatement()) {
            stmt.execute(sql);
        } catch (SQLException e) {
            throw new RuntimeException("Erro ao criar a tabela", e);
        }
    }
}
```

---

### **Model: Usuário e DAO**

A classe `Usuario.java` representa um usuário:

```java
package model;

public class Usuario {
    private int id;
    private String usuario;
    private String senha;

    public Usuario(String usuario, String senha) {
        this.usuario = usuario;
        this.senha = senha;
    }

    public int getId() { return id; }
    public String getUsuario() { return usuario; }
    public String getSenha() { return senha; }
}
```

A classe `UsuarioDAO.java` gerencia operações no banco:

```java
package model;

import org.mindrot.jbcrypt.BCrypt;
import java.sql.*;

public class UsuarioDAO {
    public boolean registrarUsuario(String usuario, String senha) {
        String sql = "INSERT INTO usuarios (usuario, senha) VALUES (?, ?)";
        String senhaHash = BCrypt.hashpw(senha, BCrypt.gensalt());

        try (Connection conn = Conexao.conectar();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setString(1, usuario);
            stmt.setString(2, senhaHash);
            stmt.executeUpdate();
            return true;
        } catch (SQLException e) {
            e.printStackTrace();
            return false;
        }
    }

    public boolean validarLogin(String usuario, String senha) {
        String sql = "SELECT senha FROM usuarios WHERE usuario = ?";

        try (Connection conn = Conexao.conectar();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setString(1, usuario);
            ResultSet rs = stmt.executeQuery();
            
            if (rs.next()) {
                return BCrypt.checkpw(senha, rs.getString("senha"));
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }
}
```

---

### **Controller: Manipulação dos Usuários**

```java
package controller;

import model.UsuarioDAO;

public class UsuarioController {
    private UsuarioDAO usuarioDAO = new UsuarioDAO();

    public boolean registrarUsuario(String usuario, String senha) {
        return usuarioDAO.registrarUsuario(usuario, senha);
    }

    public boolean validarLogin(String usuario, String senha) {
        return usuarioDAO.validarLogin(usuario, senha);
    }
}
```

---

### **View: Tela de Login**

```java
package view;

import controller.UsuarioController;
import javax.swing.*;

public class TelaLogin extends JFrame {
    private UsuarioController controller = new UsuarioController();
    private JTextField txtUsuario = new JTextField();
    private JPasswordField txtSenha = new JPasswordField();
    private JButton btnLogin = new JButton("Login");

    public TelaLogin() {
        setTitle("Login");
        setSize(300, 200);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);

        txtUsuario.setBounds(50, 30, 200, 25);
        txtSenha.setBounds(50, 60, 200, 25);
        btnLogin.setBounds(100, 100, 100, 30);

        btnLogin.addActionListener(e -> {
            String usuario = txtUsuario.getText();
            String senha = new String(txtSenha.getPassword());

            if (controller.validarLogin(usuario, senha)) {
                JOptionPane.showMessageDialog(this, "Login bem-sucedido!");
            } else {
                JOptionPane.showMessageDialog(this, "Usuário ou senha incorretos");
            }
        });

        add(txtUsuario);
        add(txtSenha);
        add(btnLogin);
    }
}
```

---

## **4. Exercícios**
1. Criar um botão de **registro de usuário**.
2. Criar uma tela principal para a aplicação, que exiba a mensagem Bem vindo <nome do usuário> ao entrar.
3. Implementar um botão **logout**.
4. Melhorar a interface gráfica com **JPanels e Layouts**.
