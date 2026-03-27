# 🚗 Sistema de Locadora de Veículos — Fase 1

> Banco de dados relacional para gerenciamento completo de locação de veículos, motoristas, clientes, manutenções e multas.

---

## 📋 Visão Geral

Este projeto implementa a estrutura de banco de dados (PostgreSQL) de um sistema de locadora de veículos. A **Fase 1** cobre toda a modelagem relacional, criação de tabelas, inserção de dados iniciais e de exemplo, constraints, índices, views e procedures.

---

## 🗂️ Estrutura dos Arquivos

| Arquivo | Descrição |
|---|---|
| `01_CREATE_TABLES` | Diagrama de entidades e relacionamentos (modelo conceitual) |
| `02_SCRIPTS_ORGANIZADOS` | Criação das tabelas base: `categoria_veiculo`, `status_veiculo`, `atendentes` |
| `03_Criação_das_tabelas_dependentes` | Criação das tabelas dependentes: `motorista`, `clientes`, `veiculo`, `aluguel`, `manutencao`, `multa` |
| `04_add_constraints_indexes_sql` | Adição de constraints (CHECK, FK) e índices de performance |
| `05_Inserção_de_dados_iniciais` | Dados iniciais: atendentes, motoristas e clientes |
| `06_Inserção_dados_de_exemplo` | Dados de exemplo: veículos, aluguéis, manutenções e multas |
| `07_Exemplos_de_operações_UPDATE` | Exemplos de operações de atualização (quilometragem, status, multas) |
| `08_Exemplos_de_operações_DELETE` | Exemplos de operações de exclusão com proteção por FK |
| `09_Criação_de_views` | Views para consultas: veículos disponíveis, aluguéis ativos, faturamento mensal |
| `10_Criação_de_procedures_e_funções` | Funções e procedures: cálculo de valor final, finalização de aluguel |
| `11_LISTA_COMPLETA_DE_TABELAS_E_ABREVIAÇÕES` | Dicionário de dados completo com abreviações para consultas |

---

## 🗃️ Modelo de Dados

### Tabelas Principais

| Tabela | Abrev. | Descrição |
|---|---|---|
| `atendente` | AT | Usuários do sistema (admin, gerente, atendente) |
| `motorista` | M | Motoristas habilitados para locação |
| `clientes` | C | Clientes (pessoa física ou jurídica) |
| `veiculo` | V | Frota de veículos disponíveis |
| `aluguel` | A | Registro de cada locação realizada |
| `manutencao` | MAN | Histórico de manutenções dos veículos |
| `multa` | MUL | Multas vinculadas aos aluguéis |
| `categoria_veiculo` | CV | Classificação dos veículos (Econômico, SUV, Luxo…) |
| `status_veiculo` | SV | Status operacional dos veículos |

### Relacionamentos

```
MOTORISTA      ──< ALUGUEL >── VEICULO
ATENDENTE      ──< ALUGUEL
CLIENTES       ──< ALUGUEL
ALUGUEL        ──< MULTA
VEICULO        ──< MANUTENCAO
CATEGORIA_VEICULO ──< VEICULO
STATUS_VEICULO    ──< VEICULO
```

---

## ⚙️ Como Executar

Execute os scripts **na ordem numérica** em um banco PostgreSQL:

```bash
# 1. Tabelas base
psql -d sua_database -f 02_SCRIPTS_ORGANIZADOS.sql

# 2. Tabelas dependentes
psql -d sua_database -f 03_Criação_das_tabelas_dependentes.sql

# 3. Constraints e índices
psql -d sua_database -f 04_add_constraints_indexes_sql.sql

# 4. Dados iniciais
psql -d sua_database -f 05_Inserção_de_dados_iniciais.sql

# 5. Dados de exemplo
psql -d sua_database -f 06_Inserção_dados_de_exemplo.sql

# 6. Views e procedures (opcional)
psql -d sua_database -f 09_Criação_de_views.sql
psql -d sua_database -f 10_Criação_de_procedures_e_funções.sql
```

> ⚠️ Todos os scripts usam `IF NOT EXISTS` e `ON CONFLICT DO NOTHING`, sendo seguros para reexecução.

---

## 🔒 Constraints e Validações

- **`motorista.status`** — aceita apenas: `ativo`, `inativo`, `bloqueado`
- **`aluguel.status`** — aceita apenas: `ativo`, `concluido`, `cancelado`, `pendente`
- **`manutencao.tipo`** — aceita apenas: `preventiva`, `corretiva`, `revisao`, `pneus`, `funilaria`
- **`multa.status`** — aceita apenas: `pendente`, `pago`, `cancelado`
- **`veiculo.valor_diaria`** — deve ser maior que zero
- **`veiculo.ano`** — entre 1900 e ano atual + 1
- **Datas de aluguel** — `data_inicio <= data_fim` e dentro do período de contrato

---

## 📊 Views Disponíveis

| View | Descrição |
|---|---|
| `vw_veiculos_disponiveis` | Lista veículos com status "Disponível", ordenados por diária |
| `vw_alugueis_ativos` | Aluguéis em andamento com dados de motorista, veículo e atendente |
| `vw_faturamento_mensal` | Faturamento agrupado por mês/ano dos aluguéis concluídos |

---

## 🔧 Procedures e Funções

### `calcular_valor_final(p_aluguel_id INTEGER)`
Retorna o valor total de um aluguel somando o valor do contrato com as multas pendentes.

```sql
SELECT calcular_valor_final(1);
```

### `finalizar_aluguel(p_aluguel_id INTEGER, p_data_pagamento DATE)`
Marca o aluguel como `concluido`, registra a data de pagamento e libera o veículo.

```sql
CALL finalizar_aluguel(1, '2024-03-15');
```

---

## 🧪 Dados de Exemplo Incluídos

| Entidade | Registros |
|---|---|
| Atendentes | 3 (Admin, Atendente, Gerente) |
| Motoristas | 3 |
| Clientes | 3 (1 PJ + 2 PF) |
| Veículos | 8 (Econômico, Intermediário, SUV, Luxo, Pickup) |
| Aluguéis | 3 (ativo, concluído, concluído com multa) |
| Manutenções | 3 |
| Multas | 2 |

---

## 🚀 Fase 2 — Inovação (EcoDrive + IA Preditiva)

A Fase 2 estende o sistema com duas funcionalidades inovadoras integradas ao **Dashboard do Cliente LocaMais**, acessível em:

🔗 **[https://v0-dashboard-loca-mais.vercel.app](https://v0-dashboard-loca-mais.vercel.app)**

---

### 🎮 Programa EcoDrive (Gamificação)

Sistema de fidelidade e recompensas para motoristas que dirigem de forma sustentável e responsável.

**Tabelas envolvidas:**

| Tabela | Descrição |
|---|---|
| `nivel_fidelidade` | Define os níveis Bronze, Prata e Ouro com pontos mínimos e percentual de desconto |
| `pontuacao_motorista` | Armazena o saldo de pontos atual, nível e data da última atualização de cada motorista |

**Níveis e benefícios:**

| Nível | Pontos Mínimos | Desconto |
|---|---|---|
| 🥉 Bronze | 0 | 0% |
| 🥈 Prata | 1.000 | 5% |
| 🥇 Ouro | 3.000 | 10% |

**Como os pontos são calculados:**
- Pontos acumulados com base no valor total dos aluguéis concluídos
- Conquistas especiais são concedidas por comportamento responsável (ex: 500 km sem multas = +50 pontos)
- O nível é atualizado automaticamente conforme o saldo de pontos cresce

**O que o motorista vê no dashboard:**
- Nível atual com ícone (Bronze / Prata / Ouro)
- Saldo de pontos e progresso até o próximo desconto
- Conquistas recentes com pontuação obtida

---

### 🤖 IA Preditiva de Manutenção

Motor de alertas inteligentes que analisa a frota e gera recomendações automáticas de manutenção preventiva antes que falhas ocorram.

**Tabela envolvida:**

| Tabela | Descrição |
|---|---|
| `insight_ia_manutencao` | Armazena alertas gerados para cada veículo com mensagem, probabilidade de falha e data prevista |

**Regras de geração de alertas:**

| Condição | Alerta Gerado | Probabilidade |
|---|---|---|
| Quilometragem > 50.000 km | Risco de falhas mecânicas aumentado | 70% |
| Sem manutenção há mais de 1 ano | Revisão obrigatória recomendada | 50% |
| Veículo com mais de 10 anos | Atenção a sistemas críticos | 60% |
| Nenhuma condição crítica | Manutenção preventiva de rotina | 30% |

**O que o motorista vê no dashboard:**
- Sugestão de manutenção preventiva com prazo estimado (ex: "verificação dos freios em aproximadamente 15 dias")
- Análise de condução com média de velocidade e impacto na pontuação EcoDrive
- Recomendações personalizadas baseadas no padrão de uso do veículo alugado

---

### 📁 Scripts da Fase 2

| Arquivo | Descrição |
|---|---|
| `Tabelas_de_inovacao__Fase_2_` | Criação das tabelas `nivel_fidelidade`, `pontuacao_motorista` e `insight_ia_manutencao` |
| `Inserção_de_dados_de_inovação` | Inserção dos níveis de fidelidade, cálculo de pontuação dos motoristas e geração dos insights de IA |

**Ordem de execução (após a Fase 1):**

```bash
psql -d sua_database -f Tabelas_de_inovacao__Fase_2_.sql
psql -d sua_database -f Inserção_de_dados_de_inovação.sql
```

---

## 🛠️ Tecnologias

- **Banco de Dados:** PostgreSQL
- **Linguagem:** SQL / PL/pgSQL
- **Compatibilidade:** PostgreSQL 12+

---

## 📌 Observações

- CPFs e CNPJs são armazenados **sem pontuação**
- Todos os scripts são idempotentes (podem ser executados mais de uma vez com segurança)
- A tabela `aluguel` centraliza as informações de contrato (período, forma de pagamento, comprovante), eliminando tabelas auxiliares desnecessárias

  ## 🚀 Benefícios do Modelo

* ✔️ **Integridade garantida:** Constraints `CHECK` e `FOREIGN KEY` impedem dados inválidos (ex.: CNH vencida, valor de aluguel negativo, duplicidade de CPF/placa).
* ✔️ **Proteção contra exclusões indevidas:** `ON DELETE RESTRICT` bloqueia remoção de registros com dependências ativas.
* ✔️ **Consistência transacional:** Uso de chaves estrangeiras mantém a coerência entre aluguéis, veículos, motoristas e multas.
* ✔️ **Automação de regras de negócio:** Pontuação automática no programa EcoDrive e geração de insights de manutenção preditiva.
* ✔️ **Pronto para produção:** Estrutura normalizada, scripts versionados e protótipo de interface funcional.

---

## 🏆 Diferencial

Este projeto se destaca por:

* **Constraints avançadas** – Validações de domínio (níveis de acesso, datas futuras, valores positivos) diretamente no banco.
* **Gamificação (EcoDrive)** – Sistema de pontuação e níveis de fidelidade (Bronze, Prata, Ouro) que incentiva bons motoristas com descontos progressivos.
* **Inteligência de dados** – Alertas preditivos de manutenção baseados em quilometragem, tempo sem revisão e idade do veículo.
* **Integridade referencial completa** – Relacionamentos bem definidos que evitam inconsistências operacionais.
* **Interface profissional** – Protótipos HTML que demonstram a experiência do usuário com dashboards personalizados e telas de gestão.\

## 📊 Modelo de Dados Relacional

```mermaid
erDiagram
    MOTORISTA ||--o{ ALUGUEL : realiza
    CLIENTES ||--o{ ALUGUEL : solicita
    ATENDENTE ||--o{ ALUGUEL : registra
    VEICULO ||--o{ ALUGUEL : utilizado_em
    VEICULO ||--o{ MANUTENCAO : possui
    ALUGUEL ||--o{ MULTA : gera
    CATEGORIA_VEICULO ||--o{ VEICULO : classifica
    STATUS_VEICULO ||--o{ VEICULO : define
    MOTORISTA ||--|| PONTUACAO_MOTORISTA : possui
    NIVEL_FIDELIDADE ||--o{ PONTUACAO_MOTORISTA : define_regras
    VEICULO ||--o{ INSIGHT_IA_MANUTENCAO : monitorado_por

    MOTORISTA {
        int id
        string nome
        string cpf
        date data_nascimento
        string cnh
        date validade_cnh
        string telefone
        string email
        string endereco
        string status
        timestamp criado_em
    }

    CLIENTES {
        int id
        string nome
        string cpf
        string email
        string telefone
    }

    ATENDENTE {
        int id
        string nome
        string cpf
        string email
        string senha
        string nivel_acesso
    }

    CATEGORIA_VEICULO {
        int id
        string nome
        string descricao
    }

    STATUS_VEICULO {
        int id
        string nome
    }

    VEICULO {
        int id
        string modelo
        string placa
        int ano
        string chassi
        string cor
        int categoria_id
        int status_id
        decimal valor_diaria
        int quilometragem
        timestamp criado_em
    }

    ALUGUEL {
        int id
        int motorista_id
        int veiculo_id
        int atendente_id
        int cliente_id
        date data_inicio
        date data_fim
        date data_pagamento
        string forma_pagamento
        decimal valor_total
        string status
        string comprovante_url
        timestamp criado_em
    }

    MANUTENCAO {
        int id
        int veiculo_id
        string descricao
        date data_inicio
        date data_fim
        decimal custo
        string tipo
    }

    MULTA {
        int id
        int aluguel_id
        string descricao
        decimal valor
        date data_multa
        string status
    }

    PONTUACAO_MOTORISTA {
        int id
        int motorista_id
        int saldo_pontos
        int nivel_id
        timestamp ultima_atualizacao
    }

    NIVEL_FIDELIDADE {
        int id
        string nome
        int pontos_minimos
        decimal desconto_percentual
    }

    INSIGHT_IA_MANUTENCAO {
        int id
        int veiculo_id
        string mensagem_alerta
        decimal probabilidade_falha
        date data_previsao
    }
