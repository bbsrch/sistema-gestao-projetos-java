# 🏦 Sistema de Gestão de Projetos e Equipes

Sistema desktop completo para gestão de projetos, tarefas e equipes, desenvolvido como projeto acadêmico.  
A aplicação permite o cadastro e controle de todas as entidades de um ciclo de vida de projeto, com sistema de autenticação seguro e controle de acesso baseado em perfis de usuário.

---

## 📸 Screenshots

![Tela de login](https://github.com/bbsrch/sistema-gestao-projetos-java/blob/main/prints/login.png)
![Tela de equipes](https://github.com/bbsrch/sistema-gestao-projetos-java/blob/main/prints/equipes.png)
![Tela de usuários](https://github.com/bbsrch/sistema-gestao-projetos-java/blob/main/prints/usuarios.png)
  
---

## ✨ Funcionalidades

O sistema foi desenvolvido seguindo todos os requisitos explícitos e implícitos do projeto, incluindo:

### 🔐 Autenticação e Segurança
- [x] Tela de login para acesso ao sistema.  
- [x] Armazenamento seguro de senhas utilizando hashing com BCrypt.  

### 👥 Controle de Acesso por Perfis
- [x] Três níveis de usuário: administrador, gerente e colaborador.  
- [x] A interface se adapta, desabilitando funcionalidades de acordo com o perfil do usuário logado.  

### 🧑‍💼 Gestão de Usuários (CRUD - Apenas Admin)
- [x] Cadastro, leitura, atualização e exclusão de usuários.  

### 📂 Gestão de Projetos (CRUD)
- [x] Cadastro de projetos com nome, descrição, datas e gerente responsável.  
- [x] Edição e exclusão de projetos.  

### 👨‍👩‍👦 Gestão de Equipes e Membros (Muitos-para-Muitos)
- [x] Cadastro de equipes com nome e descrição.  
- [x] Interface de seleção múltipla para adicionar/remover membros de uma equipe.  

### 🔗 Alocação de Equipes a Projetos (Muitos-para-Muitos)
- [x] Tela dedicada para alocar e desalocar equipes de projetos específicos.  

### 📋 Gestão de Tarefas (CRUD)
- [x] Cadastro de tarefas vinculadas a um projeto e a um responsável.  
- [x] Controle de status, datas previstas e datas reais.  

### 📊 Relatórios Gerenciais
- [x] **Resumo de Andamento:** Tabela com a quantidade de projetos por status (planejado, em andamento, etc.).  
- [x] **Desempenho de Colaboradores:** Tabela com o total de tarefas atribuídas e concluídas por usuário.  
- [x] **Projetos com Risco de Atraso:** Lista de projetos cuja data de término prevista já passou, mas que não foram concluídos.  

---

## 💻 Tecnologias Utilizadas

- **Linguagem:** Java (JDK 21)  
- **Interface Gráfica:** Java Swing  
- **Banco de Dados:** MySQL  

**Bibliotecas Externas:**  
- MySQL Connector/J → Conexão JDBC com o banco de dados.  
- jBCrypt → Hashing seguro de senhas.  
- JCalendar → Componentes de seleção de data.  

---

## 🚀 Como Executar o Projeto

### Pré-requisitos
- Java JDK (versão 17 ou superior)  
- IDE Java (Eclipse, IntelliJ IDEA, etc.)  
- MySQL Workbench (ou outro cliente de BD)  

---

### 1. Configuração do Banco de Dados
Execute o script SQL abaixo no seu MySQL Workbench para criar o banco de dados e todas as tabelas necessárias:

```sql
CREATE DATABASE IF NOT EXISTS gestao_projetos;
USE gestao_projetos;

CREATE TABLE `usuarios` (
  `id_usuario` int NOT NULL AUTO_INCREMENT,
  `nome_completo` varchar(255) NOT NULL,
  `cpf` varchar(14) NOT NULL,
  `email` varchar(255) NOT NULL,
  `cargo` varchar(100) DEFAULT NULL,
  `login` varchar(50) NOT NULL,
  `senha` varchar(255) NOT NULL,
  `perfil` enum('administrador','gerente','colaborador') NOT NULL,
  PRIMARY KEY (`id_usuario`),
  UNIQUE KEY `cpf` (`cpf`),
  UNIQUE KEY `email` (`email`),
  UNIQUE KEY `login` (`login`)
);

CREATE TABLE `projetos` (
  `id_projeto` int NOT NULL AUTO_INCREMENT,
  `nome_projeto` varchar(255) NOT NULL,
  `descricao` text,
  `data_inicio` date NOT NULL,
  `data_termino_prevista` date NOT NULL,
  `status` enum('planejado','em andamento','concluído','cancelado') NOT NULL,
  `id_gerente` int DEFAULT NULL,
  PRIMARY KEY (`id_projeto`),
  KEY `id_gerente` (`id_gerente`),
  CONSTRAINT `projetos_ibfk_1` FOREIGN KEY (`id_gerente`) REFERENCES `usuarios` (`id_usuario`)
);

CREATE TABLE `equipes` (
  `id_equipe` int NOT NULL AUTO_INCREMENT,
  `nome_equipe` varchar(255) NOT NULL,
  `descricao` text,
  PRIMARY KEY (`id_equipe`)
);

CREATE TABLE `tarefas` (
  `id_tarefa` int NOT NULL AUTO_INCREMENT,
  `titulo` varchar(255) NOT NULL,
  `descricao` text,
  `data_inicio_prevista` date DEFAULT NULL,
  `data_fim_prevista` date DEFAULT NULL,
  `data_inicio_real` date DEFAULT NULL,
  `data_fim_real` date DEFAULT NULL,
  `status` enum('pendente','em execução','concluída') NOT NULL,
  `id_projeto` int NOT NULL,
  `id_responsavel` int DEFAULT NULL,
  PRIMARY KEY (`id_tarefa`),
  KEY `id_projeto` (`id_projeto`),
  KEY `id_responsavel` (`id_responsavel`),
  CONSTRAINT `tarefas_ibfk_1` FOREIGN KEY (`id_projeto`) REFERENCES `projetos` (`id_projeto`),
  CONSTRAINT `tarefas_ibfk_2` FOREIGN KEY (`id_responsavel`) REFERENCES `usuarios` (`id_usuario`)
);

CREATE TABLE `usuario_equipe` (
  `id_usuario` int NOT NULL,
  `id_equipe` int NOT NULL,
  PRIMARY KEY (`id_usuario`,`id_equipe`),
  KEY `id_equipe` (`id_equipe`),
  CONSTRAINT `usuario_equipe_ibfk_1` FOREIGN KEY (`id_usuario`) REFERENCES `usuarios` (`id_usuario`),
  CONSTRAINT `usuario_equipe_ibfk_2` FOREIGN KEY (`id_equipe`) REFERENCES `equipes` (`id_equipe`)
);

CREATE TABLE `projeto_equipe` (
  `id_projeto` int NOT NULL,
  `id_equipe` int NOT NULL,
  PRIMARY KEY (`id_projeto`,`id_equipe`),
  KEY `id_equipe` (`id_equipe`),
  CONSTRAINT `projeto_equipe_ibfk_1` FOREIGN KEY (`id_projeto`) REFERENCES `projetos` (`id_projeto`),
  CONSTRAINT `projeto_equipe_ibfk_2` FOREIGN KEY (`id_equipe`) REFERENCES `equipes` (`id_equipe`)
);
```
---

### 2. Configuração da Conexão
No código-fonte, localize o arquivo `src/util/Conexao.java` e altere os dados de usuário e senha para sua configuração local:

```java
// Dentro da classe Conexao.java
Connection conexao = DriverManager.getConnection(
   "jdbc:mysql://localhost:3306/gestao_projetos", // Endereço do banco
   "root", // Usuário do banco
   "0000"  // Senha do banco
);
```
---

### 3. Executando a Aplicação
1. Clone ou faça o download deste repositório.

2. Importe o projeto na sua IDE.

3. Adicione os arquivos `.jar` necessários (MySQL Connector, jBCrypt, JCalendar) ao Build Path do projeto.

4. Execute o método `main` da classe `Principal.java` (pacote `main`).

Dica: Para o **primeiro acesso**, pode ser necessário criar um usuário administrador **diretamente no banco de dados**.

---

## 🏛️ Estrutura do Projeto

O código-fonte está organizado seguindo padrões de mercado para facilitar a manutenção:

- main: Classe principal que inicia a aplicação.

- model: Classes que representam as entidades do sistema (Usuário, Projeto, etc.).

- dao: Data Access Objects – comunicação com o banco de dados.

- view: Telas e componentes da interface gráfica (Swing).

- util: Classes utilitárias (como conexão com o banco).

---

## 👨‍💻 Autores

André Barbosa da Rocha [(@bbsrch)](https://github.com/bbsrch)

Bruno Camilo Gouveia [(@brunodev)](https://github.com/)

Samuel Menchon Bermonte [(@samumecbe)](https://github.com/)

---

Projeto desenvolvido como parte da avaliação A3 para a disciplina de Análise e Desenvolvimento de Sistemas na Universidade Anhembi Morumbi.
