# 1) Sobre o Projeto-Logico-Ecommerce-DIO
Projeto Logico de um Ecommerce ficticio, com Script de criação, Inserts e Querys, todos testados no PostgreSQL versão 14 

---
## 2) Como Utilizar
1. Tenha instalado algum banco de dados relacional
2. Crie um Banco de Dados 
3. Acesse a Query Tool e coloque o Script para a criação das tabelas **(Terceiro Tópico)**
4. Depois Popule as tabelas com as instâncias do **Quarto tópico**
5. Depois Rode as Queries do **Quinto Tópico** uma por vez, além de outras mais que queira para explorar a schema.

## 3) Script SQL 
~~~SQL
-- Tabela Cliente
CREATE TABLE Cliente (
    idCliente INT PRIMARY KEY,
    Nome VARCHAR(20) NOT NULL,
    Nome_do_meio VARCHAR(20),
    Sobrenome VARCHAR(20) NOT NULL,
    CPF CHAR(11) NOT NULL UNIQUE,
    endereco VARCHAR(100) NOT NULL,
    Data_de_Nascimento DATE NOT NULL
);

-- Tabela Produto
CREATE TABLE Produto (
    idProduto INT PRIMARY KEY,
    Categoria VARCHAR(45) NOT NULL
);

-- Tabela Pedido
CREATE TABLE Pedido (
    idPedido INT PRIMARY KEY,
    Status_do_pedido ENUM('Pendente', 'Em Processamento', 'Concluído', 'Cancelado') NOT NULL,
    descricao VARCHAR(45),
    idCliente INT NOT NULL,
    Frete FLOAT NOT NULL,
    FOREIGN KEY (idCliente) REFERENCES Cliente(idCliente) ON DELETE CASCADE
);

-- Tabela Relacionamento de Produto/Pedido
CREATE TABLE Relacionamento_Produto_Pedido (
    Produto_idProduto INT NOT NULL,
    Pedido_idPedido INT NOT NULL,
    Quantidade INT NOT NULL CHECK (Quantidade > 0),  -- Garantindo que a quantidade seja positiva
    PRIMARY KEY (Produto_idProduto, Pedido_idPedido),
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto) ON DELETE CASCADE,
    FOREIGN KEY (Pedido_idPedido) REFERENCES Pedido(idPedido) ON DELETE CASCADE
);

-- Tabela Estoque
CREATE TABLE Estoque (
    idEstoque INT PRIMARY KEY,
    Local VARCHAR(45) NOT NULL
);

-- Tabela Produto_has_Estoque
CREATE TABLE Produto_has_Estoque (
    Produto_idProduto INT NOT NULL,
    Estoque_idEstoque INT NOT NULL,
    Quantidade INT NOT NULL CHECK (Quantidade >= 0),  -- Garantindo que a quantidade no estoque não seja negativa
    PRIMARY KEY (Produto_idProduto, Estoque_idEstoque),
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto) ON DELETE CASCADE,
    FOREIGN KEY (Estoque_idEstoque) REFERENCES Estoque(idEstoque) ON DELETE CASCADE
);

-- Tabela Fornecedor
CREATE TABLE Fornecedor (
    idFornecedor INT PRIMARY KEY,
    Razao_Social VARCHAR(45) NOT NULL,
    CNPJ VARCHAR(45) NOT NULL UNIQUE
);

-- Tabela Produtos_por_Vendedor
CREATE TABLE Produtos_por_Vendedor (
    Terceiro_Vendedor_idTerceiro INT NOT NULL,
    Produto_idProduto INT NOT NULL,
    Quantidade INT NOT NULL CHECK (Quantidade > 0),  -- Garantindo que a quantidade fornecida seja positiva
    PRIMARY KEY (Terceiro_Vendedor_idTerceiro, Produto_idProduto),
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto) ON DELETE CASCADE
);

-- Tabela Disponibilizando_um_produto
CREATE TABLE Disponibilizando_um_produto (
    Fornecedor_idFornecedor INT NOT NULL,
    Produto_idProduto INT NOT NULL,
    PRIMARY KEY (Fornecedor_idFornecedor, Produto_idProduto),
    FOREIGN KEY (Fornecedor_idFornecedor) REFERENCES Fornecedor(idFornecedor) ON DELETE CASCADE,
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto) ON DELETE CASCADE
);

-- Tabela Terceiro_Vendedor
CREATE TABLE Terceiro_Vendedor (
    idTerceiro_Vendedor INT PRIMARY KEY,
    Razao_Social VARCHAR(45) NOT NULL,
    Local VARCHAR(45) NOT NULL,
    Nome_Fantasia VARCHAR(45),
    Terceiro_Vendedorcor VARCHAR(45)
);

-- Tabela Produtos_por_Vendedor_Terceiro
CREATE TABLE Produtos_por_Vendedor_Terceiro (
    Terceiro_Vendedor_idTerceiro INT NOT NULL,
    Produto_idProduto INT NOT NULL,
    Quantidade INT NOT NULL CHECK (Quantidade > 0),  -- Garantindo que a quantidade fornecida seja positiva
    PRIMARY KEY (Terceiro_Vendedor_idTerceiro, Produto_idProduto),
    FOREIGN KEY (Produto_idProduto) REFERENCES Produto(idProduto) ON DELETE CASCADE,
    FOREIGN KEY (Terceiro_Vendedor_idTerceiro) REFERENCES Terceiro_Vendedor(idTerceiro_Vendedor) ON DELETE CASCADE
);



~~~


---

## 4) Inserts

~~~SQL
-- Inserção de Clientes
INSERT INTO Cliente (idCliente, Nome, Nome_do_meio, Sobrenome, CPF, endereco, Data_de_Nascimento)
VALUES 
(1, 'João', 'Carlos', 'Silva', '12345678901', 'Rua A, 123', '1990-05-15'),
(2, 'Maria', 'Fernanda', 'Oliveira', '98765432100', 'Rua B, 456', '1985-10-22'),
(3, 'Carlos', 'Eduardo', 'Costa', '11122334455', 'Rua C, 789', '1992-08-30'),
(4, 'Ana', 'Beatriz', 'Lima', '44455566677', 'Rua D, 101', '1988-12-11'),
(5, 'Lucas', 'Henrique', 'Santos', '22233344455', 'Rua E, 202', '2000-03-25');

-- Inserção de Produtos
INSERT INTO Produto (idProduto, Categoria)
VALUES
(1, 'Eletrônicos'),
(2, 'Roupas'),
(3, 'Alimentos'),
(4, 'Livros'),
(5, 'Beleza'),
(6, 'Brinquedos');

-- Inserção de Pedidos
INSERT INTO Pedido (idPedido, Status_do_pedido, descricao, idCliente, Frete)
VALUES 
(1, 'Pendente', 'Pedido de smartphone', 1, 15.50),
(2, 'Concluído', 'Pedido de camisa', 2, 5.75),
(3, 'Em Processamento', 'Pedido de arroz', 3, 2.25),
(4, 'Cancelado', 'Pedido de livro de PHP', 4, 0),
(5, 'Concluído', 'Pedido de maquiagem', 5, 10.30);

-- Inserção de Relacionamento Produto/Pedido
INSERT INTO Relacionamento_Produto_Pedido (Produto_idProduto, Pedido_idPedido, Quantidade)
VALUES
(1, 1, 1),  -- Smartphone
(2, 2, 2),  -- Camisa
(3, 3, 5),  -- Arroz
(4, 4, 1),  -- Livro de PHP
(5, 5, 3),  -- Maquiagem
(6, 2, 1);  -- Brinquedo

-- Inserção de Estoque
INSERT INTO Estoque (idEstoque, Local)
VALUES
(1, 'Armazém 1'),
(2, 'Armazém 2'),
(3, 'Armazém 3'),
(4, 'Loja Física 1'),
(5, 'Loja Física 2');

-- Inserção de Produto_has_Estoque
INSERT INTO Produto_has_Estoque (Produto_idProduto, Estoque_idEstoque, Quantidade)
VALUES
(1, 1, 100),
(2, 2, 200),
(3, 3, 300),
(4, 4, 50),
(5, 5, 150),
(6, 2, 120);

-- Inserção de Fornecedores
INSERT INTO Fornecedor (idFornecedor, Razao_Social, CNPJ)
VALUES
(1, 'Fornecedor Eletrônicos LTDA', '12345678000199'),
(2, 'Roupas Fashion', '98765432000123'),
(3, 'Alimentos Super', '11223344000100'),
(4, 'Livros Online', '55667788000145'),
(5, 'Beleza Pro', '99887766000132');

-- Inserção de Produtos_por_Vendedor
INSERT INTO Produtos_por_Vendedor (Terceiro_Vendedor_idTerceiro, Produto_idProduto, Quantidade)
VALUES
(1, 1, 50),  -- Vendedor 1 vende 50 smartphones
(2, 2, 100), -- Vendedor 2 vende 100 camisas
(3, 3, 150), -- Vendedor 3 vende 150 pacotes de arroz
(4, 4, 20),  -- Vendedor 4 vende 20 livros de PHP
(5, 5, 60);  -- Vendedor 5 vende 60 unidades de maquiagem

-- Inserção de Disponibilizando_um_produto
INSERT INTO Disponibilizando_um_produto (Fornecedor_idFornecedor, Produto_idProduto)
VALUES
(1, 1),  -- Fornecedor Eletrônicos fornece smartphones
(2, 2),  -- Roupas Fashion fornece camisas
(3, 3),  -- Alimentos Super fornece arroz
(4, 4),  -- Livros Online fornece livros de PHP
(5, 5);  -- Beleza Pro fornece maquiagem

-- Inserção de Terceiro_Vendedor
INSERT INTO Terceiro_Vendedor (idTerceiro_Vendedor, Razao_Social, Local, Nome_Fantasia, Terceiro_Vendedorcor)
VALUES
(1, 'Vendedor Eletrônico', 'São Paulo', 'Eletrônicos SP', 'Red'),
(2, 'Fashion Roupas', 'Rio de Janeiro', 'Fashion RJ', 'Blue'),
(3, 'Super Alimentos', 'Belo Horizonte', 'Alimentos BH', 'Green'),
(4, 'Online Livros', 'Brasília', 'Livros Brasília', 'Yellow'),
(5, 'Beleza Cosméticos', 'São Paulo', 'Cosméticos SP', 'Purple');

-- Inserção de Produtos_por_Vendedor_Terceiro
INSERT INTO Produtos_por_Vendedor_Terceiro (Terceiro_Vendedor_idTerceiro, Produto_idProduto, Quantidade)
VALUES
(1, 1, 30),  -- Vendedor Eletrônico vende 30 smartphones
(2, 2, 80),  -- Fashion Roupas vende 80 camisas
(3, 3, 120), -- Super Alimentos vende 120 pacotes de arroz
(4, 4, 15),  -- Online Livros vende 15 livros de PHP
(5, 5, 50);  -- Beleza Cosméticos vende 50 unidades de maquiagem

~~~

---

## 5) QUERIES

~~~SQL
-- Recupera todos os clientes e suas informações
SELECT idCliente, Nome, Sobrenome, CPF, endereco, Data_de_Nascimento
FROM Cliente;

-- Filtra os pedidos realizados por um cliente específico (idCliente = 1)
SELECT idPedido, Status_do_pedido, descricao, Frete
FROM Pedido
WHERE idCliente = 1;

-- Cria um atributo derivado "Frete Com Desconto", aplicando 10% de desconto nos pedidos concluídos
SELECT idPedido, descricao, Frete, 
       CASE 
           WHEN Status_do_pedido = 'Concluído' THEN Frete * 0.9
           ELSE Frete
       END AS Frete_Com_Desconto
FROM Pedido;


-- Agrupa os produtos por pedido e mostra os pedidos com mais de 2 produtos
SELECT Pedido_idPedido, SUM(Quantidade) AS Total_Produtos
FROM Relacionamento_Produto_Pedido
GROUP BY Pedido_idPedido
HAVING SUM(Quantidade) > 2;

-- Realiza uma junção entre as tabelas Cliente e Pedido, recuperando o nome do cliente e a descrição de seus pedidos
SELECT Cliente.Nome, Cliente.Sobrenome, Pedido.idPedido, Pedido.descricao
FROM Cliente
JOIN Pedido ON Cliente.idCliente = Pedido.idCliente;

-- Realiza junção entre as tabelas Produto, Produto_has_Estoque e Fornecedor para mostrar a quantidade em estoque e o fornecedor
SELECT Produto.idProduto, Produto.Categoria, Produto_has_Estoque.Quantidade, Fornecedor.Razao_Social
FROM Produto
JOIN Produto_has_Estoque ON Produto.idProduto = Produto_has_Estoque.Produto_idProduto
JOIN Disponibilizando_um_produto ON Produto.idProduto = Disponibilizando_um_produto.Produto_idProduto
JOIN Fornecedor ON Disponibilizando_um_produto.Fornecedor_idFornecedor = Fornecedor.idFornecedor
WHERE Produto_has_Estoque.Quantidade > 0;

-- Agrupa produtos por categoria e exibe o total de vendas de cada produto, mostrando apenas produtos com vendas superiores a 2 unidades
SELECT Produto.idProduto, Produto.Categoria, SUM(Relacionamento_Produto_Pedido.Quantidade) AS Total_Vendas
FROM Produto
JOIN Relacionamento_Produto_Pedido ON Produto.idProduto = Relacionamento_Produto_Pedido.Produto_idProduto
GROUP BY Produto.idProduto, Produto.Categoria
HAVING SUM(Relacionamento_Produto_Pedido.Quantidade) > 2
ORDER BY Total_Vendas DESC;

-- Exibe os produtos vendidos por vendedores terceirizados, mostrando o nome do vendedor e a quantidade de produtos vendidos
SELECT Terceiro_Vendedor.Nome_Fantasia, Produto.Categoria, Produtos_por_Vendedor_Terceiro.Quantidade
FROM Produtos_por_Vendedor_Terceiro
JOIN Produto ON Produtos_por_Vendedor_Terceiro.Produto_idProduto = Produto.idProduto
JOIN Terceiro_Vendedor ON Produtos_por_Vendedor_Terceiro.Terceiro_Vendedor_idTerceiro = Terceiro_Vendedor.idTerceiro_Vendedor;

-- Realiza junção entre as tabelas Produto, Produto_has_Estoque e Estoque para recuperar produtos com estoque superior a 10 unidades
SELECT Produto.idProduto, Produto.Categoria, Estoque.Local, Produto_has_Estoque.Quantidade
FROM Produto
JOIN Produto_has_Estoque ON Produto.idProduto = Produto_has_Estoque.Produto_idProduto
JOIN Estoque ON Produto_has_Estoque.Estoque_idEstoque = Estoque.idEstoque
WHERE Produto_has_Estoque.Quantidade > 10;

~~~
