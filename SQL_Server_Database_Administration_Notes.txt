# SQL Server Database Administration: Principles and Notes
## Based on Chapters 1 and 2 by Prof. C. El Amrani

### 1. Introduction to SQL Server Databases
- **Definition**:
  - **Administrator**: A database is a set of organized files (`.MDF` for data, `.LDF` for logs) that require backup, cleanup, reorganization, and security.
  - **User**: A space to store and retrieve information.
  - **Developer**: A collection of tables, indexes, and procedures for efficient and secure data operations.
- **Core Files**:
  - `.MDF`: Primary data file.
  - `.LDF`: Transaction log file.
- **Database Objects**:
  - **Tables**: Store data (up to 1024 columns, ~2 billion tables per database).
  - **Indexes**: Speed up data retrieval (clustered or non-clustered).
  - **Triggers**: Execute actions on `INSERT`, `UPDATE`, or `DELETE`.
  - **UDDTs**: User-defined data types for consistency across tables.
  - **Defaults**: Auto-fill column values if not specified.
  - **Views**: Named `SELECT` queries acting as virtual tables.
  - **UDFs**: Scalar (single value) or table-valued (table output) functions.
  - **Stored Procedures**: Predefined logic for reusable tasks.
  - **Schemas**: Logical containers for database objects (since SQL Server 2005).
  - **Diagrams**: Visualize table relationships.

### 2. Database Creation and Management
- **Commands**:
  - `CREATE DATABASE`: Define database with data and log files (e.g., `CREATE DATABASE db1 ON (NAME='db1data', FILENAME='C:\data\db1data.mdf', SIZE=1MB, MAXSIZE=5MB, FILEGROWTH=1MB)`).
  - `ALTER DATABASE`: Modify file size or properties (e.g., `ALTER DATABASE db1 MODIFY FILE (NAME='db1data', SIZE=2MB)`).
  - `DBCC SHRINKDATABASE`: Reduce database size (e.g., `DBCC SHRINKDATABASE (db1, 10)` for 10% free space).
  - `DROP DATABASE`: Delete database and files.
  - `sp_helpdb`: Display database details.
  - `ALTER DATABASE ... SET READ_ONLY`: Restrict modifications.
- **Best Practice**: Limit databases to 100–200 tables for maintenance.

### 3. Data Types
- **Categories**:
  - Binary: `binary`, `varbinary`.
  - Character: `char`, `varchar`, `text`.
  - Unicode: `nchar`, `nvarchar`, `ntext`.
  - Date/Time: `datetime`, `smalldatetime`.
  - Numeric: `decimal`, `float`, `real`, `int`, `bigint`, `smallint`, `tinyint`.
  - Monetary: `money`, `smallmoney`.
  - Special: `bit`, `timestamp`, `uniqueidentifier`, `cursor`, `sql_variant`, `table`, `image`.

### 4. Tables and Constraints
- **Table Creation**:
  - Syntax: `CREATE TABLE Clients (nom char(30) NOT NULL, prenom char(30) NULL, ville char(25) NOT NULL)`.
  - Maximum 1024 columns; row count limited by storage.
- **Constraints**:
  - **Primary Key**: Ensures unique, non-null values (e.g., `CONSTRAINT Pkey PRIMARY KEY (nom, prenom)`).
  - **Foreign Key**: Enforces referential integrity (e.g., `CONSTRAINT fk1 FOREIGN KEY (NumCli) REFERENCES test1(NumCli1)`).
  - **Unique**: Ensures unique values, allows NULL (e.g., `CONSTRAINT UniMat UNIQUE (matricule)`).
  - **Default**: Sets default values (e.g., `CREATE DEFAULT DefCodePostal AS '90000'`; bind with `sp_bindefault`).
  - **Check**: Validates data (e.g., `CHECK (age > 18)` or `CHECK (ville IN ('tanger', 'rabat', 'fes'))`).
- **Rules**: Independent validation rules (e.g., `CREATE RULE AgeRule AS @age>=18 AND @age<=65`; bind with `sp_bindrule`).

### 5. User-Defined Data Types (UDDTs)
- **Purpose**: Standardize data types across tables.
- **Commands**:
  - Create: `sp_addtype CodePostal, 'char(5)', 'NOT NULL'`.
  - Rename: `sp_rename ancien_nom, nouveau_nom, 'USERDATATYPE'`.
  - Drop: `sp_droptype CodePostal`.
- **Usage**: Apply to columns for consistency.

### 6. Indexes
- **Purpose**: Speed up data retrieval.
- **Types**:
  - **Clustered**: Physically sorts table data (1 per table).
  - **Non-clustered**: Separate structure (default, multiple allowed).
- **Guidelines**:
  - Avoid for small tables (<200–300 rows) or columns with few unique values.
  - Index columns in `WHERE`, `ORDER BY`, or joins.
- **Syntax**: `CREATE CLUSTERED INDEX NomPrenomIdx ON Clients (nom, prenom)`.

### 7. Views
- **Purpose**: Simplify queries, act as virtual tables.
- **Commands**:
  - Create: `CREATE VIEW vue1 AS SELECT nom, produit, ville FROM clients JOIN commandes ON ...`.
  - Modify: `ALTER VIEW vue1 AS ...`.
  - Drop: `DROP VIEW vue1 {RESTRICT | CASCADE}`.
- **Options**: `RESTRICT` prevents dropping if used elsewhere; `CASCADE` drops dependent objects.

### 8. Transact-SQL (T-SQL)
- **Features**: Extends SQL92 with variables, control structures, cursors, etc.
- **Variables**:
  - Declare: `DECLARE @PrixMoy money`.
  - Set: `SET @PrixMoy = (SELECT AVG(UnitPrice) FROM products)`.
- **Control Structures**:
  - **IF/ELSE**: Conditional logic (e.g., `IF @NbProd > 100 BEGIN ... END`).
  - **WHILE**: Loops (e.g., `WHILE @nombre <= 6 BEGIN ... END`).
  - **CASE**: Conditional expressions (e.g., `CASE WHEN UnitPrice < 30 THEN 'pas cher' ... END`).
  - **GOTO**: Jumps to labels (e.g., `GOTO debut`).
- **Stored Procedures**:
  - Create: `CREATE PROCEDURE GetProductID AS SELECT ProductID FROM products`.
  - Execute: `EXEC GetProductID` or `EXEC LeProduit 10`.
- **User-Defined Functions (UDFs)**:
  - **Scalar**: Returns single value (e.g., `CREATE FUNCTION PTTC (@PHT float, @TVA float) RETURNS float ...`).
  - **Table-Valued**: Returns table (e.g., `CREATE FUNCTION PU (@Prix money) RETURNS table ...`).
- **Triggers**:
  - Types: `AFTER` (post-event) or `INSTEAD OF` (pre-event).
  - Syntax: `CREATE TRIGGER triggertest1 ON Territories FOR INSERT AS ...`.

### 9. Practical Tips
- **Practice**: Use Northwind or AdventureWorks databases to test commands.
- **Optimization**: Index strategically, avoid over-indexing, and use constraints for data integrity.
- **Maintenance**: Regularly back up databases, monitor file growth, and use `DBCC` commands for optimization.
- **Learning Resources**: Microsoft SQL Server documentation, online forums, and sample databases.

### 10. Key Notes
- **Data Integrity**: Use constraints and rules to ensure valid data.
- **Performance**: Indexes and stored procedures improve efficiency.
- **T-SQL Power**: Leverage variables, control structures, and UDFs for complex logic.
- **Scalability**: Design databases with maintenance in mind (e.g., limit table count, optimize file sizes).
