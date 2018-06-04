# PRIMARY KEY 约束

PRIMARY KEY 约束唯一标识数据库表中的每条记录。

主键包含唯一且不能为 NULL 的值。

每个表都应该有、且只有一个主键。

### CREATE TABLE 时

在 Persons 表创建时在 P\_Id 列上创建 PRIMARY KEY 约束：

MySQL：

```
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
PRIMARY KEY (P_Id)
)
```

定义多个列的 PRIMARY KEY 约束：

MySQL/SQL Server/Oracle/MS Access:

```
CREATE TABLE Persons
(
P_Id int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName)
)
```

> pk_PersonID 的值是由两个列（P\_Id 和 LastName_）组成。

### ALTER TABLE 时

表已创建，如需在 P\_Id 列创建 PRIMARY KEY 约束：

MySQL/SQL Server/Oracle/MS Access:

```
ALTER TABLE Persons
ADD PRIMARY KEY (P_Id)
```

定义多个列的 PRIMARY KEY 约束：

```
ALTER TABLE Persons
ADD CONSTRAINT pk_PersonID PRIMARY KEY (P_Id,LastName)
```

> 使用 ALTER TABLE 语句添加主键，必须把主键列声明为不包含 NULL 值（表首次创建时）。

### 撤销 PRIMARY KEY 约束

MySQL:

```
ALTER TABLE Persons
DROP PRIMARY KEY
```

SQL Server/Oracle/MS Access:

```
ALTER TABLE Persons
DROP CONSTRAINT pk_PersonID
```



