-- DESCOMENTAR AS LINHAS SE FOR RODAR A QUERY EM UM REGISTERED SERVERS

--declare @versao varchar(20)
--select @versao = LEFT( convert(varchar, SERVERPROPERTY('ProductVersion')), 4) where LEFT( convert(varchar, SERVERPROPERTY('ProductVersion')), 4) >= '13.0'

--IF @versao IS NOT NULL
--	BEGIN
		WITH CTE AS
		(
			SELECT
			b.name, SUM(size) * 8 /1024  AS SIZE_MB 
			FROM sys.master_files AS A
			INNER JOIN sys.databases AS B ON A.database_id = B.database_id
			WHERE 1=1
				AND A.database_id > 4
				AND type_desc = 'ROWS'
				AND B.state = 0
				AND B.is_query_store_on = 0
				AND B.is_in_standby = 0
			GROUP BY b.name
		)
		SELECT name, [SIZE_MB],
		CASE
			WHEN SIZE_MB >= 10240 THEN 'ALTER DATABASE ' + QUOTENAME(name) + ' SET QUERY_STORE (OPERATION_MODE = READ_WRITE, MAX_STORAGE_SIZE_MB = 5120);'
			WHEN SIZE_MB >= 5120 THEN 'ALTER DATABASE ' + QUOTENAME(name) + ' SET QUERY_STORE (OPERATION_MODE = READ_WRITE, MAX_STORAGE_SIZE_MB = 2048);'
			ELSE 'ALTER DATABASE' + QUOTENAME(name) + ' SET QUERY_STORE (OPERATION_MODE = READ_WRITE, MAX_STORAGE_SIZE_MB = 1024);'
		END AS [COMADO PARA HABILITAR] FROM CTE
--	END	