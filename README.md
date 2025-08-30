# trabalho_final-_banco_de_dados
### Trabalho Grupo 02 - Serratec: Hyago Guimarães, Isabella Machado, Lorrane Aló Cruz, Natalia Siqueira, João Menezes, Rafael Amaral


Este projeto apresenta a modelagem e implementação de um sistema de banco de dados para uma clínica odontológica. O objetivo é informatizar o atendimento e a gestão de pacientes, consultas e dentistas, permitindo que a clínica mantenha um controle eficiente sobre agendamentos, procedimentos realizados e histórico dos atendimentos. O sistema foi projetado para ser utilizado por atendentes e profissionais da clínica, facilitando o acesso e atualização dos dados de forma segura e organizada.

### MODELO CONCEITUAL:
<img width="758" height="468" alt="image" src="https://github.com/user-attachments/assets/96f15bdb-f9d2-43f6-98a2-5f3b2d6e4ee9" />

### MODELO LÓGICO: 
<img width="1105" height="729" alt="image" src="https://github.com/user-attachments/assets/08e76159-d10b-401d-9f7f-68837dde0a8a" />



```sql

SQL


create DATABASE clinica_odontologica
----------------------------------------------------------------------------
-- Criar Schema

CREATE SCHEMA gestao
----------------------------------------------------------------------------
-- Tabela de Pacientes
CREATE TABLE gestao.paciente (
    id_paciente SERIAL PRIMARY KEY,
    nome_completo VARCHAR(200) NOT NULL,
    cpf VARCHAR(11) unique NOT NULL,
    data_nascimento DATE NOT NULL,
    telefone VARCHAR(20),
    email VARCHAR(200),
    endereco VARCHAR(255)
);

-- Tabela de Dentistas
CREATE TABLE gestao.dentista (
    id_dentista SERIAL PRIMARY KEY,
    nome_completo VARCHAR(255) NOT NULL,
    cpf VARCHAR(11) UNIQUE NOT NULL,
    cro VARCHAR(20) NOT NULL,
    especialidade VARCHAR(100) NOT NULL
);

-- Tabela de Procedimentos
CREATE TABLE gestao.procedimento (
    id_procedimento SERIAL PRIMARY KEY,
    nome_procedimento VARCHAR(100) NOT NULL,
    descricao_procedimento TEXT,
    duracao_media_minutos INT NOT NULL
);

-- Tabela de Consultas
CREATE TABLE gestao.consulta (
    id_consulta SERIAL PRIMARY KEY,
    id_paciente INT NOT NULL,
    id_dentista INT NOT NULL,
    data_consulta DATE NOT NULL,
    horario_consulta TIME NOT NULL,
    descricao_atendimento TEXT,
    prescricao TEXT,
    FOREIGN KEY (id_paciente) REFERENCES gestao.paciente(id_paciente),
    FOREIGN KEY (id_dentista) REFERENCES gestao.dentista(id_dentista),
    UNIQUE (data_consulta, horario_consulta, id_dentista)
);

-- Tabela associativa entre Consulta e Procedimento
CREATE TABLE gestao.consulta_procedimento (
    id_consulta INT NOT NULL,
    id_procedimento INT NOT NULL,
    PRIMARY KEY (id_consulta, id_procedimento),
    FOREIGN KEY (id_consulta) REFERENCES gestao.consulta(id_consulta),
    FOREIGN KEY (id_procedimento) REFERENCES gestao.procedimento(id_procedimento)
);
-- Tabela de Horarios de Atendimento dos Dentistas
CREATE TABLE gestao.horario_atendimento (
    id_horario_atendimento SERIAL PRIMARY KEY,
    id_dentista INT NOT NULL,
    dia_semana VARCHAR(15) NOT NULL,
    horario_inicio TIME NOT NULL,
    horario_fim TIME NOT NULL,
    FOREIGN KEY (id_dentista) REFERENCES gestao.dentista(id_dentista)
);
--2. Permitir que atendentes atualizem os dados

CREATE TABLE gestao.atendente (
    id_atendente SERIAL PRIMARY KEY,
    nome_completo VARCHAR(255) not null,
    cpf VARCHAR(14) not null,
    telefone varchar(20),
    email varchar(210)
);

--2. Inserir atendentes (mínimo de 10 registros)

INSERT INTO gestao.atendente (nome_completo, cpf, telefone, email) VALUES
	('Cleber Souza', '201.201.201-01', '11999990001', 'cleber.souza@gmail.com'),
	('Yago Gomes', '202.202.202-02', '11999990002', 'yago.gomes@gmail.com'),
	('João Silva', '203.203.203-03', '11999990003', 'joao.silva@gmail.com'),
	('Natália Siqueira', '204.204.204-04', '11999990004', 'natalia.siqueira@gmail.com'),
	('Rafaela Vieira', '205.205.205-05', '11999990005', 'rafaela.vieira@gmail.com'),
	('Ricardo Lima', '206.206.206-06', '11999990006', 'ricardo.lima@gmail.com'),
	('Denise Rocha', '207.207.207-07', '11999990007', 'denise.rocha@gmail.com'),
	('Isabella Falcão', '208.208.208-08', '11999990008', 'isabella.falcao@gmail.com'),
	('Mariana Rosa', '209.209.209-09', '11999990009', 'mariana.rosa@gmail.com'),
	('Fernando Nunes', '210.210.210-10', '11999990010', 'fernando.nunes@gmail.com');

--Permitir que os atendentes da clínica acessem e atualizem os dados dos
--pacientes, consultas e dentistas.
CREATE ROLE atendente;

GRANT SELECT, UPDATE ON gestao.paciente TO atendente;
GRANT SELECT, UPDATE ON gestao.consulta TO atendente;
GRANT SELECT, UPDATE ON gestao.dentista TO atendente;

--Exemplo com 3 atendentes cadastrados:
-- Cleber Souza
CREATE ROLE cleber LOGIN PASSWORD 'cleber2023';
GRANT SELECT, INSERT, UPDATE ON gestao.paciente TO cleber;
GRANT SELECT, INSERT, UPDATE ON gestao.consulta TO cleber;
GRANT SELECT, INSERT, UPDATE ON gestao.consulta_procedimento TO cleber;
GRANT SELECT ON gestao.dentista, gestao.procedimento TO cleber;

-- Yago Gomes
CREATE ROLE yago LOGIN PASSWORD 'yago2024';
GRANT SELECT, INSERT, UPDATE ON gestao.paciente TO yago;
GRANT SELECT, INSERT, UPDATE ON gestao.consulta TO yago;
GRANT SELECT, INSERT, UPDATE ON gestao.consulta_procedimento TO yago;
GRANT SELECT ON gestao.dentista, gestao.procedimento TO yago;

-- João Silva
CREATE ROLE joao LOGIN PASSWORD 'joao2025';
GRANT SELECT, INSERT, UPDATE ON gestao.paciente TO joao;
GRANT SELECT, INSERT, UPDATE ON gestao.consulta TO joao;
GRANT SELECT, INSERT, UPDATE ON gestao.consulta_procedimento TO joao;
GRANT SELECT ON gestao.dentista, gestao.procedimento TO joao;

-- Inserindo 10 pacientes

ALTER TABLE gestao.paciente
ALTER COLUMN cpf TYPE VARCHAR(14);

INSERT INTO gestao.paciente (nome_completo, cpf, data_nascimento, telefone, email, endereco) VALUES
	('Lorrane Aló Cruz', '111.111.111-11', '1990-05-15', '11987654321', 'lorrane.alocruz@gmail.com', 'Rua A, 123'),
	('Denise de Oliveira', '222.222.222-22', '1985-11-20', '11998765432', 'denise.oliveira@gmail.com', 'Av. B, 456'),
	('Isabella Machado', '333.333.333-33', '1978-02-10', '11976543210', 'isabella.machado@gmail.com', 'Rua C, 789'),
	('Hyago Rosa', '444.444.444-44', '1995-08-25', '11965432109', 'hyago.rosa@gmail.com', 'Av. D, 1011'),
	('Lorrane Aló Cruz', '555.555.555-55', '2001-03-30', '11954321098', 'lorrane.alocruz2@gmail.com', 'Rua E, 1213'),
	('Natália Siqueira', '666.666.666-66', '1982-06-05', '11943210987', 'natalia.siqueira@gmail.com', 'Rua F, 1415'),
	('Rafael Amaral', '777.777.777-77', '1999-09-12', '11932109876', 'rafael.amaral@gmail.com', 'Av. G, 1617'),
	('Lucia Mendes', '888.888.888-88', '1970-04-18', '11921098765', 'lucia.mendes@gmail.com', 'Rua H, 1819'),
	('Rafael Gomes', '999.999.999-99', '2000-03-04', '11910987654', 'rafael.gomes@gmail.com', 'Av. I, 2021'),
	('Fernanda Barros', '101.101.101-01', '2000-03-04', '11909876543', 'fernanda.barros@gmail.com', 'Rua J, 2223');

INSERT INTO gestao.paciente (nome_completo, cpf, data_nascimento, telefone, email, endereco) VALUES
('Gabriel Herrera', '121.121.121-21', '2005-09-04', '11909876549', 'fernanda.b@gmail.com', 'Rua J, 6523');

--SQL de 3 exclusão de registros com condições em alguma tabela.
DELETE FROM gestao.consulta_procedimento WHERE id_consulta IN(
SELECT id_consulta FROM gestao.consulta WHERE id_paciente = 10);

DELETE FROM gestao.consulta WHERE id_paciente = 10

DELETE FROM gestao.paciente WHERE id_paciente = 10

select * from gestao.paciente

--SQL de atualização de registros com condições em alguma tabela.
UPDATE gestao.paciente
SET nome_completo = 'João Gabriel Menezes',
data_nascimento = '2006-03-30',
telefone = '11954321098',
email = 'joao.m@gmail.com',
endereco = 'Rua E, 1215' 
WHERE cpf = '555.555.555-55';

-- Inserindo 10 dentistas
ALTER TABLE gestao.dentista
ALTER COLUMN cpf TYPE VARCHAR(14);

INSERT INTO gestao.dentista (nome_completo, cpf, cro, especialidade) VALUES
('Dr. Roberto Silva', '112.112.112-12', 'CRO-SP 12345', 'Ortodontia'),
('Dra. Amanda Silva', '223.223.223-23', 'CRO-SP 23456', 'Odontopediatria'),
('Dr. Felipe Silva', '334.334.334-34', 'CRO-SP 34567', 'Endodontia'),
('Dra. Patricia Silva', '445.445.445-45', 'CRO-SP 45678', 'Periodontia'),
('Dr. Gustavo Silva', '556.556.556-56', 'CRO-SP 56789', 'Clinico Geral'),
('Dra. Carolina Silva', '667.667.667-67', 'CRO-SP 67890', 'Ortodontia'),
('Dr. Marcelo Silva', '778.778.778-78', 'CRO-SP 78901', 'Implantodontia'),
('Dra. Renata Silva', '889.889.889-89', 'CRO-SP 89012', 'Protese Dentaria'),
('Dr. Daniel Silva', '990.990.990-90', 'CRO-SP 90123', 'Clinico Geral'),
('Dra. Bianca Silva', '100.100.100-00', 'CRO-SP 01234', 'Cirurgia');

--SQL de atualização de registros com condições em alguma tabela.
UPDATE gestao.dentista
SET nome_completo = 'Dr. Jorge Henrique',
cro = 'CRO-SP 80125',
especialidade = 'Clinico Geral'
WHERE cpf = '990.990.990-90';
select * from gestao.dentista

-- Inserindo 10 procedimentos
INSERT INTO gestao.procedimento (nome_procedimento, descricao_procedimento, duracao_media_minutos) VALUES
('Limpeza', 'Remocao de tartaro e polimento', 50),
('Clareamento', 'Branqueamento dos dentes com laser', 90),
('Canal', 'Tratamento de endodontia', 120),
('Extracao', 'Remocao de dente', 120),
('Aplicacao de Fluor', 'Protecao contra caries', 30),
('Restauracao de Resina', 'Preenchimento de cavidade', 50),
('Protese Fixa', 'Instalacao de coroa', 180),
('Facetas de Porcelana', 'Laminas esteticas nos dentes', 150),
('Aparelho Ortodontico', 'Instalacao de aparelho fixo', 120),
('Consulta de Rotina', 'Avaliacao geral da saude bucal', 30);

--SQL de atualização de registros com condições em alguma tabela.
UPDATE gestao.procedimento
SET nome_procedimento = 'Profilaxia Dental',
duracao_media_minutos = '50'
WHERE descricao_procedimento = 'Remocao de tartaro e polimento';
select * from gestao.procedimento

-- Inserindo 15 consultas (para garantir mais de 10 e ter dados para as consultas)
INSERT INTO gestao.consulta (id_paciente, id_dentista, data_consulta, horario_consulta, descricao_atendimento, prescricao) VALUES
(1, 5, '2025-08-20', '10:00:00', 'Avaliacao de rotina e limpeza', 'Nenhum medicamento prescrito.'),
(2, 2, '2025-08-21', '14:30:00', 'Revisao de tratamento infantil', 'Nenhum medicamento.'),
(3, 3, '2025-08-22', '09:00:00', 'Tratamento de canal do dente 36', 'Amoxicilina 500mg, 1 comprimido a cada 8h por 7 dias.'),
(4, 1, '2025-08-23', '11:00:00', 'Manutencao de aparelho ortodontico', 'Nenhum medicamento.'),
(5, 5, '2025-08-24', '15:00:00', 'Extracao de dente siso', 'Ibuprofeno 600mg, 1 comprimido a cada 6h por 5 dias.'),
(6, 4, '2025-08-25', '10:00:00', 'Consulta para periodontia', 'Nenhum medicamento.'),
(7, 6, '2025-08-26', '13:00:00', 'Instalacao de aparelho', 'Nenhum medicamento.'),
(8, 7, '2025-08-27', '16:00:00', 'Consulta para implante', 'Nenhum medicamento.'),
(9, 8, '2025-08-28', '08:30:00', 'Consulta de protese', 'Nenhum medicamento.'),
(10, 9, '2025-08-29', '10:30:00', 'Revisao geral', 'Nenhum medicamento.'),
(1, 5, '2025-09-01', '10:00:00', 'Revisao pos-extracao', 'Nenhum medicamento.'),
(2, 2, '2025-09-02', '14:30:00', 'Nova consulta com crianca', 'Nenhum medicamento.'),
(1, 1, '2025-09-03', '11:00:00', 'Ajuste de aparelho', 'Nenhum medicamento.'),
(2, 2, '2025-09-04', '14:00:00', 'Aplicacao de fluor', 'Nenhum medicamento.'),
(5, 5, '2025-09-05', '15:30:00', 'Consulta de emergencia', 'Prescricao de antibiotico.');

-- Inserindo 10 horarios de atendimento
INSERT INTO gestao.horario_atendimento (id_dentista, dia_semana, horario_inicio, horario_fim) VALUES
(1, 'Segunda-feira', '09:00:00', '18:00:00'),
(2, 'Terca-feira', '09:00:00', '17:00:00'),
(3, 'Quarta-feira', '10:00:00', '19:00:00'),
(4, 'Quinta-feira', '08:00:00', '12:00:00'),
(5, 'Sexta-feira', '14:00:00', '20:00:00'),
(6, 'Segunda-feira', '09:00:00', '18:00:00'),
(7, 'Terca-feira', '10:00:00', '19:00:00'),
(8, 'Quarta-feira', '08:00:00', '17:00:00'),
(9, 'Quinta-feira', '14:00:00', '17:00:00'),
(10, 'Sexta-feira', '10:00:00', '16:00:00');

-- Inserindo dados na tabela associativa (consulta_procedimento)
INSERT INTO gestao.consulta_procedimento (id_consulta, id_procedimento) VALUES
(1, 1), -- Consulta de Joao (Limpeza)
(1, 10), -- Consulta de Joao (Rotina)
(2, 5), -- Consulta de Maria (Aplicacao de Fluor)
(3, 3), -- Consulta de Carlos (Canal)
(4, 9), -- Consulta de Ana (Aparelho)
(5, 4), -- Consulta de Bruno (Extracao)
(6, 10), -- Consulta de Juliana (Rotina)
(7, 9), -- Consulta de Pedro (Aparelho)
(8, 7), -- Consulta de Lucia (Protese)
(9, 8), -- Consulta de Rafael (Facetas)
(10, 10), -- Consulta de Fernanda (Rotina)
(11, 4), -- Consulta de Joao (Revisao pos-extracao)
(12, 5), -- Consulta de Maria (Aplicacao de Fluor)
(13, 9), -- Consulta de Joao (Ajuste de aparelho)
(14, 5), -- Consulta de Maria (Aplicacao de Fluor)
(15, 10), -- Consulta de Bruno (Rotina)
(15, 3); -- Consulta de Bruno (Canal)

select * from gestao.consulta_procedimento

--índice que busca por CPF de paciente
CREATE INDEX idx_paciente_cpf ON gestao.paciente(cpf)

--índice para buscar data das consultas
CREATE INDEX idx_consulta_data_consulta ON gestao.consulta(data_consulta)

SELECT * FROM gestao.paciente WHERE cpf = '121.121.121-21'

-- Quantidade de consultas realizadas por especialidade
SELECT d.especialidade, COUNT(c.id_consulta) AS total_consultas
FROM gestao.dentista AS d
LEFT JOIN gestao.consulta AS c ON d.id_dentista = c.id_dentista
GROUP BY d.especialidade
ORDER BY total_consultas DESC;

-- Quantidade de consultas realizadas por cada dentista
SELECT d.nome_completo AS dentista,COUNT(c.id_consulta) AS total_consultas
FROM gestao.dentista AS d
LEFT JOIN gestao.consulta AS c ON d.id_dentista = c.id_dentista
GROUP BY  d.nome_completo
ORDER BY  total_consultas DESC;

-- Quantidade de consultas realizadas por cada paciente
SELECT p.nome_completo AS paciente, COUNT(c.id_consulta) AS total_consultas
FROM gestao.paciente AS p
LEFT JOIN gestao.consulta AS c ON p.id_paciente = c.id_paciente
GROUP BY  p.nome_completo
ORDER BY total_consultas DESC;

--View com lista de consultas ordenadas por data
CREATE OR REPLACE VIEW gestao.vw_lista_consultas AS
SELECT c.id_consulta,
       p.nome_completo AS nome_paciente,
       d.nome_completo AS nome_dentista,
       c.data_consulta,
       pr.nome_procedimento AS procedimento_realizado
FROM gestao.consulta AS c
JOIN gestao.paciente AS p ON c.id_paciente = p.id_paciente
JOIN gestao.dentista AS d ON c.id_dentista = d.id_dentista
LEFT JOIN gestao.consulta_procedimento cp ON c.id_consulta = cp.id_consulta
LEFT JOIN gestao.procedimento pr ON cp.id_procedimento = pr.id_procedimento
ORDER BY c.data_consulta DESC;

-- Média de consultas por dentista
SELECT ROUND(AVG(quantidade_consultas),2) AS media_consultas_por_dentista
FROM (SELECT d.id_dentista, COUNT(c.id_consulta) AS quantidade_consultas
FROM gestao.dentista AS d
LEFT JOIN gestao.consulta AS c ON d.id_dentista = c.id_dentista
GROUP BY d.id_dentista) AS subconsulta;

```sql
