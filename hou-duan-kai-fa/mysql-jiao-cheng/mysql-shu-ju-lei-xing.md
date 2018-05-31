# MySQL 数据类型

MySQL支持多种类型，大致可以分为三类：数值、日期/时间和字符串\(字符\)类型。

### 数值类型

MySQL支持所有标准SQL数值数据类型。

这些类型包括严格数值数据类型\(INTEGER、SMALLINT、DECIMAL和NUMERIC\)，以及近似数值数据类型\(FLOAT、REAL和DOUBLE PRECISION\)。

关键字INT是INTEGER的同义词，关键字DEC是DECIMAL的同义词。

BIT数据类型保存位字段值，并且支持MyISAM、MEMORY、InnoDB和BDB表。

作为SQL标准的扩展，MySQL也支持整数类型TINYINT、MEDIUMINT和BIGINT。下面的表显示了需要的每个整数类型的存储和范围。

* **TINYINT**：1 字节，小整数值

* **SMALLINT**：2 字节，大整数值

* **MEDIUMINT**：3 字节，大整数值

* INT或INTEGER：4 字节，大整数值

* BIGINT：8 字节，极大整数值

* FLOAT：4 字节，单精度浮点数值

* DOUBLE：8字节，双精度浮点数值

* DECIMAL：对DECIMAL\(M,D\) ，如果M&gt;D，为M+2否则为D+2，小数值

### 日期和时间类型

表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。

* DATE：3字节，格式：YYYY-MM-DD，日期值
* TIME：3字节，HH:MM:SS，时间值或持续时间

* YEAR：1字节，年份值

* DATETIME：8字节，YYYY-MM-DD HH:MM:SS，混合日期和时间值

* TIMESTAMP：4字节，YYYYMMDD HHMMSS，混合日期和时间值，时间戳

### 字符串类型

字符串类型指CHAR、VARCHAR、BINARY、VARBINARY、BLOB、TEXT、ENUM和SET。该节描述了这些类型如何工作以及如何在查询中使用这些类型。

* CHAR：0-255字节，定长字符串
* VARCHAR：0-65535 字节，变长字符串
* TINYBLOB：0-255字节，不超过 255 个字符的二进制字符串
* TINYTEXT：0-255字节，短文本字符串
* BLOB：0-65 535字节，二进制形式的长文本数据
* TEXT：0-65 535字节，长文本数据
* MEDIUMBLOB：0-16 777 215字节，二进制形式的中等长度文本数据
* MEDIUMTEXT：0-16 777 215字节，中等长度文本数据
* LONGBLOB：0-4 294 967 295字节，二进制形式的极大文本数据
* LONGTEXT：0-4 294 967 295字节，极大文本数据 



