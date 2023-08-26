--Deverá ser rodado na base que você deseja ver quantas tabelas e quais possuem registro

SELECT SCHEMA_NAME(T.SCHEMA_ID) + '.' + T.NAME AS NOMETABELA,
 P.ROWS AS QTDELINHAS
 FROM SYS.TABLES T
 INNER JOIN SYS.PARTITIONS P ON (P.OBJECT_ID = T.OBJECT_ID AND INDEX_ID < 2)
 ORDER BY QTDELINHAS DESC

