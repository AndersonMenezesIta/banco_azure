# banco_azure

# 🔷 Guia Completo - Banco de Dados no Microsoft Azure

## 📋 Índice
1. [Visão Geral dos Serviços de Banco de Dados](#visão-geral)
2. [Azure SQL Database - Configuração Passo a Passo](#azure-sql-database)
3. [Azure Database for MySQL](#azure-mysql)
4. [Azure Database for PostgreSQL](#azure-postgresql)
5. [Azure Cosmos DB](#azure-cosmos-db)
6. [Configurações de Segurança](#segurança)
7. [Monitoramento e Performance](#monitoramento)
8. [Backup e Recuperação](#backup)
9. [Otimização de Custos](#custos)
10. [Melhores Práticas](#melhores-práticas)
11. [Troubleshooting Comum](#troubleshooting)

---

## 🌟 Visão Geral dos Serviços de Banco de Dados {#visão-geral}

### Principais Serviços Azure para Banco de Dados

| Serviço | Tipo | Melhor Para | Características |
|---------|------|-------------|-----------------|
| **Azure SQL Database** | SQL Relacional | Aplicações empresariais | Totalmente gerenciado, alta disponibilidade |
| **Azure SQL Managed Instance** | SQL Relacional | Migração de SQL Server | Compatibilidade com SQL Server |
| **Azure Database for MySQL** | SQL Relacional | Aplicações web/mobile | Open source, flexível |
| **Azure Database for PostgreSQL** | SQL Relacional | Aplicações complexas | Recursos avançados, extensões |
| **Azure Cosmos DB** | NoSQL | Aplicações globais | Multi-modelo, distribuição global |

### 💡 **Dica Importante**
> Sempre considere o padrão de uso, localização geográfica dos usuários e requisitos de compliance antes de escolher o serviço.

---

## 🔵 Azure SQL Database - Configuração Passo a Passo {#azure-sql-database}

### Pré-requisitos
- Conta Azure ativa
- Subscription com créditos ou plano pago
- Resource Group criado

### 📝 Passo 1: Criando o SQL Database

#### Via Portal Azure
1. **Acesse o Portal Azure** → portal.azure.com
2. **Clique em "Create a resource"**
3. **Busque por "SQL Database"**
4. **Configure as informações básicas:**

```
Subscription: [Sua subscription]
Resource Group: [Seu resource group]
Database Name: [nome-do-banco]
Server: [Criar novo servidor]
```

#### Via Azure CLI
```bash
# Criar Resource Group
az group create \
  --name myResourceGroup \
  --location "East US"

# Criar SQL Server
az sql server create \
  --name mysqlserver \
  --resource-group myResourceGroup \
  --location "East US" \
  --admin-user sqladmin \
  --admin-password P@ssw0rd123!

# Criar SQL Database
az sql db create \
  --resource-group myResourceGroup \
  --server mysqlserver \
  --name mySampleDatabase \
  --service-objective Basic
```

### 📝 Passo 2: Configuração do Servidor SQL

#### Configurações Essenciais:
- **Server name**: Nome único globalmente
- **Server admin login**: Nome do administrador
- **Password**: Senha complexa (mín. 8 caracteres)
- **Location**: Região mais próxima dos usuários
- **Version**: Sempre use a versão mais recente

#### ⚠️ **Atenção às Regras de Firewall**
```sql
-- Permitir acesso do Azure
-- No portal: Networking → Allow Azure services: YES

-- Adicionar IP específico
-- No portal: Networking → Add client IP
```

### 📝 Passo 3: Escolha do Tier de Performance

#### Opções Disponíveis:
1. **Basic**: Desenvolvimento e testes
   - 5 DTUs
   - 2GB de armazenamento
   - Backup 7 dias

2. **Standard**: Cargas de trabalho leves a moderadas
   - 10-3000 DTUs
   - Até 1TB
   - Backup 35 dias

3. **Premium**: Aplicações críticas
   - 125-4000 DTUs
   - Até 1TB
   - Backup 35 dias
   - Maior IOPS

#### 💰 **Dica de Custos**
> Use o tier Basic para desenvolvimento e Standard S0 para produção de baixo volume. Monitore sempre o uso para otimizar custos.

---

## 🟡 Azure Database for MySQL {#azure-mysql}

### Características Principais
- **Versões Suportadas**: 5.7, 8.0
- **Alta Disponibilidade**: Built-in
- **Backup Automático**: 7-35 dias
- **SSL**: Habilitado por padrão

### Configuração Rápida

#### Via Portal
1. **Create a resource** → **Azure Database for MySQL**
2. **Escolha o deployment option**:
   - **Single Server**: Para aplicações simples
   - **Flexible Server**: Para maior controle e flexibilidade

#### Configurações Recomendadas:
```
Server Name: mysql-server-[projeto]
Version: 8.0 (mais recente)
Compute + Storage: Burstable B1ms (desenvolvimento)
Admin Username: mysqladmin
```

#### String de Conexão:
```php
<?php
$host = "mysql-server-projeto.mysql.database.azure.com";
$username = "mysqladmin@mysql-server-projeto";
$password = "sua_senha";
$database = "nome_database";

$connection = mysqli_connect($host, $username, $password, $database);
?>
```

### 🔧 **Configurações Importantes**

#### Parâmetros do Servidor:
```sql
-- Verificar configurações atuais
SHOW VARIABLES LIKE 'max_connections';
SHOW VARIABLES LIKE 'innodb_buffer_pool_size';

-- No portal Azure: Settings → Server parameters
-- max_connections: Ajustar conforme necessidade
-- slow_query_log: ON (para monitoramento)
```

---

## 🟣 Azure Database for PostgreSQL {#azure-postgresql}

### Vantagens do PostgreSQL no Azure
- **Extensões suportadas**: PostGIS, pg_stat_statements
- **Replica de leitura**: Para distribuir carga
- **Point-in-time recovery**: Restauração precisa
- **Criptografia**: Em trânsito e em repouso

### Configuração e Otimização

#### Configurações de Performance:
```sql
-- Verificar configurações atuais
SELECT name, setting, unit FROM pg_settings 
WHERE name IN ('shared_buffers', 'work_mem', 'max_connections');

-- Configurar via portal Azure
-- Settings → Server parameters:
shared_buffers = 256MB  -- 25% da RAM disponível
work_mem = 4MB         -- Para operações de ordenação
max_connections = 100   -- Ajustar conforme aplicação
```

#### String de Conexão Python:
```python
import psycopg2

conn = psycopg2.connect(
    host="postgres-server-projeto.postgres.database.azure.com",
    database="nome_database",
    user="postgresadmin@postgres-server-projeto",
    password="sua_senha",
    port=5432,
    sslmode="require"
)
```

---

## 🌐 Azure Cosmos DB {#azure-cosmos-db}

### APIs Disponíveis
- **Core (SQL)**: Documentos JSON
- **MongoDB**: Compatibilidade com MongoDB
- **Cassandra**: Wide-column store
- **Gremlin**: Graph database
- **Table**: Key-value pairs

### Configuração para Alta Performance

#### Modelagem de Dados:
```json
// Documento otimizado para Cosmos DB
{
    "id": "user123",
    "partitionKey": "region-east",
    "userData": {
        "name": "João Silva",
        "email": "joao@email.com",
        "orders": [
            {
                "orderId": "order456",
                "date": "2024-01-15",
                "items": [...],
                "total": 299.99
            }
        ]
    }
}
```

#### ⚡ **Dicas de Performance**:
1. **Escolha da Partition Key**: Use propriedade com alta cardinalidade
2. **RU (Request Units)**: Monitore e ajuste conforme necessidade
3. **Indexing Policy**: Configure apenas índices necessários
4. **TTL**: Use para dados temporários

```javascript
// Exemplo de consulta otimizada
const querySpec = {
    query: "SELECT * FROM c WHERE c.partitionKey = @region AND c.status = @status",
    parameters: [
        { name: "@region", value: "east" },
        { name: "@status", value: "active" }
    ]
};
```

---

## 🔒 Configurações de Segurança {#segurança}

### Autenticação e Autorização

#### Azure Active Directory Integration
```sql
-- Para SQL Database
CREATE USER [usuario@dominio.com] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [usuario@dominio.com];
```

#### Firewall Rules
```bash
# Via Azure CLI
az sql server firewall-rule create \
  --resource-group myResourceGroup \
  --server mysqlserver \
  --name AllowMyIP \
  --start-ip-address 203.0.113.0 \
  --end-ip-address 203.0.113.255
```

### Criptografia

#### Always Encrypted (SQL Database):
```sql
-- Criar chave mestra
CREATE COLUMN MASTER KEY CMK_Auto1
WITH (
    KEY_STORE_PROVIDER_NAME = 'AZURE_KEY_VAULT',
    KEY_PATH = 'https://keyvault.vault.azure.net/keys/key1/...'
);

-- Criar chave de criptografia
CREATE COLUMN ENCRYPTION KEY CEK_Auto1
WITH VALUES (
    COLUMN_MASTER_KEY = CMK_Auto1,
    ALGORITHM = 'RSA_OAEP'
);
```

### 🛡️ **Checklist de Segurança**:
- [ ] Firewall configurado adequadamente
- [ ] SSL/TLS habilitado
- [ ] Usuários com privilégios mínimos
- [ ] Auditoria habilitada
- [ ] Backup criptografado
- [ ] Rede virtual configurada (se necessário)

---

## 📊 Monitoramento e Performance {#monitoramento}

### Azure Monitor Integration

#### Métricas Importantes:
- **DTU/CPU percentage**: Uso de recursos
- **Data IO percentage**: I/O do disco
- **Log IO percentage**: I/O do log
- **Connection failures**: Falhas de conexão
- **Blocked queries**: Consultas bloqueadas

#### Configurar Alertas:
```bash
# Via Azure CLI
az monitor metrics alert create \
  --name "High DTU Alert" \
  --resource-group myResourceGroup \
  --scopes "/subscriptions/.../resourceGroups/.../providers/Microsoft.Sql/servers/mysqlserver/databases/mydb" \
  --condition "avg Percentage CPU > 80" \
  --window-size 5m \
  --evaluation-frequency 1m
```

### Query Performance Insight

#### Identificar Queries Lentas:
```sql
-- Para SQL Database
SELECT 
    qs.sql_handle,
    qs.execution_count,
    qs.total_elapsed_time / qs.execution_count AS avg_elapsed_time,
    SUBSTRING(st.text, (qs.statement_start_offset/2) + 1,
        ((CASE statement_end_offset 
            WHEN -1 THEN DATALENGTH(st.text)
            ELSE qs.statement_end_offset END 
            - qs.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) AS st
ORDER BY avg_elapsed_time DESC;
```

---

## 💾 Backup e Recuperação {#backup}

### Tipos de Backup

#### Automated Backups:
- **SQL Database**: 7-35 dias de retenção
- **MySQL/PostgreSQL**: 7-35 dias
- **Cosmos DB**: Backup contínuo

#### Long-term Retention (SQL Database):
```bash
# Via Azure CLI
az sql db ltr-policy set \
  --resource-group myResourceGroup \
  --server mysqlserver \
  --database mydb \
  --weekly-retention P4W \
  --monthly-retention P12M \
  --yearly-retention P5Y
```

### Point-in-Time Recovery

#### Restaurar Database:
```bash
# SQL Database - Point in time restore
az sql db restore \
  --resource-group myResourceGroup \
  --server mysqlserver \
  --name mydb-restored \
  --source-database mydb \
  --time "2024-01-15T10:30:00"
```

### 📋 **Estratégia de Backup Recomendada**:
1. **Automated backup**: Sempre habilitado
2. **Long-term retention**: Para dados críticos
3. **Geo-redundant backup**: Para disaster recovery
4. **Testes regulares**: Validar processo de restore

---

## 💰 Otimização de Custos {#custos}

### Estratégias de Economia

#### Reserved Capacity:
- **1 ano**: Até 30% de desconto
- **3 anos**: Até 50% de desconto
- **Aplicável**: SQL Database, MySQL, PostgreSQL

#### Auto-scaling:
```bash
# Configurar auto-scaling para SQL Database
az sql elastic-pool create \
  --resource-group myResourceGroup \
  --server mysqlserver \
  --name mypool \
  --edition Standard \
  --dtu 100 \
  --db-dtu-min 10 \
  --db-dtu-max 50
```

#### Tier Rightsizing:
```sql
-- Monitorar uso de DTU
SELECT 
    end_time,
    avg_dtu_percent,
    max_dtu_percent
FROM sys.dm_db_resource_stats
WHERE end_time > GETDATE() - 1  -- Último dia
ORDER BY end_time DESC;
```

### 💡 **Dicas de Economia**:
- Use **Burstable tiers** para cargas variáveis
- Configure **auto-pause** para desenvolvimento
- Implemente **connection pooling**
- Use **read replicas** para distribuir carga
- Monitore e ajuste **storage tier**

---

## ⭐ Melhores Práticas {#melhores-práticas}

### Desenvolvimento

#### Connection String Security:
```csharp
// ❌ Não faça isso
string connString = "Server=server.database.windows.net;Database=db;User=user;Password=pass123;";

// ✅ Use Azure Key Vault
public async Task<string> GetConnectionStringAsync()
{
    var client = new SecretClient(new Uri("https://keyvault.vault.azure.net/"), new DefaultAzureCredential());
    var secret = await client.GetSecretAsync("database-connection-string");
    return secret.Value.Value;
}
```

#### Connection Pooling:
```python
# Para PostgreSQL
import psycopg2.pool

# Criar pool de conexões
connection_pool = psycopg2.pool.SimpleConnectionPool(
    minconn=1,
    maxconn=20,
    host="server.postgres.database.azure.com",
    database="mydb",
    user="user@server",
    password="password"
)
```

### Produção

#### Health Checks:
```csharp
// .NET Health Check
services.AddHealthChecks()
    .AddSqlServer(connectionString, name: "sql-database")
    .AddNpgSql(connectionString, name: "postgresql-database");
```

#### Retry Logic:
```python
import time
import random

def execute_with_retry(query, max_retries=3):
    for attempt in range(max_retries):
        try:
            return execute_query(query)
        except Exception as e:
            if attempt == max_retries - 1:
                raise e
            
            wait_time = (2 ** attempt) + random.uniform(0, 1)
            time.sleep(wait_time)
```

### 📋 **Checklist de Produção**:
- [ ] Connection pooling implementado
- [ ] Retry logic configurado
- [ ] Health checks funcionando
- [ ] Monitoramento ativo
- [ ] Alertas configurados
- [ ] Backup testado
- [ ] Disaster recovery planejado
- [ ] Security review completo

---

## 🔧 Troubleshooting Comum {#troubleshooting}

### Problemas de Conectividade

#### Erro: "Cannot connect to server"
```bash
# 1. Verificar firewall
az sql server firewall-rule list \
  --resource-group myResourceGroup \
  --server mysqlserver

# 2. Testar conectividade
telnet server.database.windows.net 1433

# 3. Verificar DNS
nslookup server.database.windows.net
```

#### Timeout de Conexão:
```sql
-- Verificar conexões ativas
SELECT 
    DB_NAME(dbid) as DatabaseName,
    COUNT(dbid) as NumberOfConnections,
    loginame as LoginName
FROM sys.sysprocesses 
WHERE dbid > 0
GROUP BY dbid, loginame;
```

### Problemas de Performance

#### DTU/CPU Alto:
```sql
-- Identificar queries consumindo recursos
SELECT TOP 10
    qs.execution_count,
    qs.total_logical_reads,
    qs.total_logical_writes,
    qs.total_worker_time,
    t.text as query_text
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) t
ORDER BY qs.total_worker_time DESC;
```

#### Lock e Deadlocks:
```sql
-- Verificar locks ativos
SELECT 
    r.session_id,
    r.command,
    r.status,
    r.wait_type,
    r.wait_time,
    r.blocking_session_id,
    s.text as sql_text
FROM sys.dm_exec_requests r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) s
WHERE r.blocking_session_id <> 0;
```

### Troubleshooting por Serviço

#### Azure SQL Database:
- **Query Store**: Sempre habilitado
- **Automatic tuning**: Considere habilitar
- **Intelligent Insights**: Para análise de performance

#### MySQL:
- **slow_query_log**: Habilitar para análise
- **PERFORMANCE_SCHEMA**: Usar para diagnóstico
- **Binary logging**: Para point-in-time recovery

#### PostgreSQL:
- **pg_stat_statements**: Extensão para análise de queries
- **log_statement**: Configurar para auditoria
- **auto_vacuum**: Verificar configuração

---

## 📚 Recursos Adicionais

### Documentação Oficial:
- [Azure SQL Database Documentation](https://docs.microsoft.com/azure/azure-sql/)
- [Azure Database for MySQL Documentation](https://docs.microsoft.com/azure/mysql/)
- [Azure Database for PostgreSQL Documentation](https://docs.microsoft.com/azure/postgresql/)
- [Azure Cosmos DB Documentation](https://docs.microsoft.com/azure/cosmos-db/)

### Ferramentas Úteis:
- **Azure Data Studio**: IDE gratuita para bancos de dados
- **SQL Server Management Studio (SSMS)**: Para SQL Database
- **Azure CLI**: Automação e scripting
- **PowerShell**: Gerenciamento via scripts

### Cursos e Certificações:
- **AZ-204**: Developing Solutions for Microsoft Azure
- **DP-900**: Azure Data Fundamentals
- **AZ-104**: Azure Administrator Associate

---

## 🎯 Conclusão

Este guia fornece uma base sólida para trabalhar com banco de dados no Microsoft Azure. Lembre-se de sempre:

1. **Planejar adequadamente** a arquitetura antes da implementação
2. **Monitorar constantemente** performance e custos
3. **Aplicar security best practices** desde o início
4. **Testar regularmente** backups e disaster recovery
5. **Manter-se atualizado** com novas funcionalidades Azure

### 📞 Próximos Passos:
1. Praticar com uma conta gratuita Azure
2. Implementar um projeto piloto
3. Configurar monitoramento e alertas
4. Documentar procedimentos específicos da sua organização

---

> **💡 Dica Final**: Mantenha este guia atualizado conforme suas experiências práticas e novas funcionalidades do Azure. A documentação viva é sempre mais valiosa!

**Última atualização**: Maio 2025  
**Versão**: 1.0
