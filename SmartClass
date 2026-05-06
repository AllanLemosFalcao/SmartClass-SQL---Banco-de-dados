create database smartclass;
use smartclass;

-- 1. Tabela de Usuários

CREATE TABLE usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    email_institucional VARCHAR(255) UNIQUE NOT NULL,
    cpf VARCHAR(14) UNIQUE NOT NULL,
    categoria VARCHAR(50) NOT NULL 
    );

-- 2. Tabela de Credenciais
CREATE TABLE credenciais (
    usuario_id INT PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    senha VARCHAR(255) NOT NULL, -- Deve armazenar o hash da senha
    CONSTRAINT fk_credencial_usuario FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE CASCADE
);

-- 3. Tabela de Salas
CREATE TABLE salas (
    id INT AUTO_INCREMENT PRIMARY KEY,
    identificacao VARCHAR(100) NOT NULL UNIQUE
);

-- 4. Tabela de Chaves
CREATE TABLE chaves (
    rfid_tag_uid VARCHAR(100) PRIMARY KEY, -- O UID da tag atua como identificador único
    numero_slot INT UNIQUE NOT NULL,
    status VARCHAR(50) NOT NULL DEFAULT 'disponível', -- Ex: 'disponível', 'em uso'
    sala_id INT UNIQUE NOT NULL,
    CONSTRAINT fk_chave_sala FOREIGN KEY (sala_id) REFERENCES salas(id) ON DELETE CASCADE
);

-- 5. Tabela de Regras de Uso
CREATE TABLE regras_de_uso (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    sala_id INT NOT NULL,
    periodo_permitido_inicio TIME NOT NULL, 
    periodo_permitido_fim TIME NOT NULL,
    CONSTRAINT fk_regra_usuario FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE CASCADE,
    CONSTRAINT fk_regra_sala FOREIGN KEY (sala_id) REFERENCES salas(id) ON DELETE CASCADE
);

-- 6. Tabela de Movimentações (Logs chaves)
CREATE TABLE movimentacoes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    chave_rfid_tag_uid VARCHAR(100) NOT NULL,
    data_hora TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    tipo_acao VARCHAR(50) NOT NULL, -- 'Retirada' ou 'Devolução'
    CONSTRAINT fk_movimentacao_usuario FOREIGN KEY (usuario_id) REFERENCES usuarios(id),
    CONSTRAINT fk_movimentacao_chave FOREIGN KEY (chave_rfid_tag_uid) REFERENCES chaves(rfid_tag_uid)
);

-- 7. Tabela de Logs do Sistema (Auditoria Geral)
CREATE TABLE logs_sistema (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT, 
    acao VARCHAR(100) NOT NULL, 
    descricao TEXT, 
    ip_origem VARCHAR(45), 
    data_hora TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_log_usuario FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE SET NULL
);

-- 8. Tabela de Agendamentos (Reservas pelo Calendário)
CREATE TABLE agendamentos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    sala_id INT NOT NULL,
    data_agendamento DATE NOT NULL, -- NOVO: Define o dia exato no calendário (Ex: '2026-05-12')
    horario_inicio TIME NOT NULL,
    horario_fim TIME NOT NULL,
    status VARCHAR(50) DEFAULT 'Agendado', -- Pode ser: 'Agendado', 'Concluído', 'Cancelado'
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_agendamento_usuario FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE CASCADE,
    CONSTRAINT fk_agendamento_sala FOREIGN KEY (sala_id) REFERENCES salas(id) ON DELETE CASCADE
);


-- 1. Insert tabela 'usuarios'
INSERT INTO usuarios (nome, email_institucional, cpf, categoria) VALUES
('Carlos Eduardo Silva', 'carlos.silva@edu.pe.senac.br', '123.456.789-01', 'Professor'),
('Marina Costa Souza', 'marina.souza@edu.pe.senac.br', '234.567.890-12', 'Gestor'),
('Roberto Alves Gomes', 'roberto.gomes@edu.pe.senac.br', '345.678.901-23', 'Limpeza'),
('Fernanda Lima Santos', 'fernanda.santos@edu.pe.senac.br', '456.789.012-34', 'TI');

-- 2. Insert tabela 'credenciais'
INSERT INTO credenciais (usuario_id, email, senha) VALUES
(1, 'carlos.silva@edu.pe.senac.br', '$2b$10$TKh8H1.PbCjV7ui4b8g.Oe/mockHashSenha1'),
(2, 'marina.souza@edu.pe.senac.br', '$2b$10$TKh8H1.PbCjV7ui4b8g.Oe/mockHashSenha2'),
(3, 'roberto.gomes@edu.pe.senac.br', '$2b$10$TKh8H1.PbCjV7ui4b8g.Oe/mockHashSenha3'),
(4, 'fernanda.santos@edu.pe.senac.br', '$2b$10$TKh8H1.PbCjV7ui4b8g.Oe/mockHashSenha4');

-- 3. Insert tabela 'salas'
INSERT INTO salas (identificacao) VALUES
('Sala 101 - Bloco A'),
('Sala 102 - Bloco A'),
('Sala 103 - Bloco A'),
('Sala 104 - Bloco A');

-- 4. Insert tabela 'chaves'
-- padrão de rfid_tag_uid.
INSERT INTO chaves (rfid_tag_uid, numero_slot, status, sala_id) VALUES
('A1:B2:C3:D4', 1, 'disponível', 1), -- Sala 101 - Bloco A (no gancho 1)
('E5:F6:G7:H8', 2, 'em uso',     2), -- Chave da Sala 102 (no gancho 2, atualmente retirada)
('I9:J0:K1:L2', 3, 'disponível', 3), -- Sala 103 (no gancho 3)
('M3:N4:O5:P6', 4, 'disponível', 4); -- Sala 104 (no gancho 4)

-- 5. Insert tabela 'regras_de_uso'
-- horarios fixos para funcionários que Mantêm as chaves(como os da limpeza) durante todo expediente(?)
INSERT INTO regras_de_uso (usuario_id, sala_id, periodo_permitido_inicio, periodo_permitido_fim) VALUES
(1, 1, '08:00:00', '22:00:00'), 
(1, 2, '08:00:00', '22:00:00'), 
(3, 1, '05:00:00', '07:30:00'), 
(3, 2, '05:00:00', '07:30:00'), 
(4, 4, '08:00:00', '18:00:00');

-- 6. Insert tabela 'movimentacoes' (Logs de uso do claviculário)
INSERT INTO movimentacoes (usuario_id, chave_rfid_tag_uid, data_hora, tipo_acao) VALUES
(1, 'A1:B2:C3:D4', '2026-05-05 08:15:00', 'Retirada'),   -- Prof. Carlos pegou a chave da Sala 101
(1, 'A1:B2:C3:D4', '2026-05-05 10:00:00', 'Devolução'),  -- Prof. Carlos devolveu a chave Sala 101
(1, 'E5:F6:G7:H8', '2026-05-06 10:15:00', 'Retirada');   -- Prof. pegou a chave da Sala 102

-- 7. Insert tabela 'logs_sistema'
INSERT INTO logs_sistema (usuario_id, acao, descricao, ip_origem) VALUES
(1, 'LOGIN_SUCESSO', 'Autenticação realizada com sucesso via PWA.', '192.168.1.15'),
(NULL, 'LOGIN_FALHA', 'Tentativa de login com senha incorreta para roberto.gomes@edu.pe.senac.br', '192.168.1.42');

-- Insert agendamentos

INSERT INTO agendamentos (usuario_id, sala_id, data_agendamento, horario_inicio, horario_fim, status) VALUES
(1, 1, '2026-05-10', '14:00:00', '16:00:00', 'Agendado'),  -- Prof. Carlos reservou o Sala 101 à tarde
(2, 2, '2026-05-12', '09:00:00', '12:00:00', 'Agendado'),  -- Gestora Marina reservou a Sala 102 de manhã
(1, 2, '2026-05-01', '19:00:00', '22:00:00', 'Concluído'); -- Uma reserva antiga que já passou

