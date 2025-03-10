## **Aula: Java Swing com Gradle e Conexão com Banco de Dados em MVC**  

### **Objetivo da Aula**  
- Entender como estruturar um projeto Java Swing utilizando Gradle.  
- Implementar a conexão com banco de dados seguindo o padrão **MVC**.  
- Aprender boas práticas para organização do código.  

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

## **3. Estruturando em MVC**  

### **Analogia: Restaurante**  
Um restaurante bem organizado tem:  
- **View (Garçom):** Lida com os clientes e exibe o menu.  
- **Controller (Cozinheiro):** Processa os pedidos e os encaminha.  
- **Model (Estoque/Cadastro de pratos):** Guarda as informações dos ingredientes e receitas.  

No nosso caso, o **Model** representa o banco de dados, o **View** são as telas Swing, e o **Controller** gerencia a lógica.  

A estrutura de diretórios será:  

```
src/main/java/com/exemplo
│── model
│   ├── Conexao.java
│   ├── Cliente.java
│   ├── ClienteDAO.java
│── view
│   ├── ClienteView.java
│── controller
│   ├── ClienteController.java
│── Main.java
```

---

## **4. Implementação Prática**  

### **Model: Conectando ao Banco de Dados**  
O **Model** é responsável por lidar com o banco de dados.  

Criamos a classe **Conexao.java** para gerenciar a conexão:  

```java
package com.exemplo.model;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class Conexao {
    private static final String URL = "jdbc:mysql://localhost:3306/seu_banco";
    private static final String USUARIO = "root";
    private static final String SENHA = "";

    public static Connection conectar() {
        try {
            return DriverManager.getConnection(URL, USUARIO, SENHA);
        } catch (SQLException e) {
            throw new RuntimeException("Erro na conexão com o banco de dados", e);
        }
    }
}
```

Agora, criamos a classe **Cliente.java**, que representa a tabela do banco:  

```java
package com.exemplo.model;

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

A classe **ClienteDAO.java** será responsável por buscar e salvar clientes no banco:  

```java
package com.exemplo.model;

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

### **Controller: Manipulando os dados**  

```java
package com.exemplo.controller;

import com.exemplo.model.Cliente;
import com.exemplo.model.ClienteDAO;

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
package com.exemplo.view;

import com.exemplo.controller.ClienteController;
import com.exemplo.model.Cliente;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class ClienteView extends JFrame {
    private ClienteController controller = new ClienteController();
    private JTextField campoNome = new JTextField(20);
    private JTextArea areaClientes = new JTextArea(10, 30);

    public ClienteView() {
        setTitle("Cadastro de Clientes");
        setSize(400, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new FlowLayout());

        JButton botaoAdicionar = new JButton("Adicionar Cliente");
        botaoAdicionar.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                controller.adicionarCliente(campoNome.getText());
                atualizarLista();
            }
        });

        add(new JLabel("Nome:"));
        add(campoNome);
        add(botaoAdicionar);
        add(new JScrollPane(areaClientes));

        atualizarLista();
        setVisible(true);
    }

    private void atualizarLista() {
        areaClientes.setText("");
        for (Cliente c : controller.listarClientes()) {
            areaClientes.append(c.getId() + " - " + c.getNome() + "\n");
        }
    }

    public static void main(String[] args) {
        new ClienteView();
    }
}
```

---

## **5. Exercícios**  

1. **Adicionar campo "email"**: Modifique o código para incluir um campo **email** na classe `Cliente` e altere o banco de dados, o DAO e a interface gráfica para suportar esse novo atributo.  

2. **Excluir Cliente**: Adicione um botão para remover um cliente da lista.  

3. **Editar Cliente**: Permita que o usuário edite o nome de um cliente já cadastrado.  

4. **Conectar com SQLite**: Modifique a classe `Conexao` para usar SQLite em vez de MySQL.  
