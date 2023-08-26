-- historico contadores, rodar na dts_tools ou traces, por causa da tabela!
DECLARE  @DT_INICIO DATETIME = '20221219 13:00'
		,@DT_FIM DATETIME = '20221219 14:20'


;with cte1
as
(
SELECT DT = CONVERT(DATE,DT_LOG),  datepart(HOUR,Dt_Log) HORA,   datepart(minute,Dt_Log) as minuto, BatchRequests = VALOR
 FROM Registro_Contador B 
WHERE
	B.Id_Contador = 1
AND 	Dt_Log BETWEEN (@DT_INICIO) AND (@DT_FIM)
),
cte2
as
(
SELECT  DT = CONVERT(DATE,DT_LOG),  datepart(HOUR,Dt_Log) HORA,   datepart(minute,Dt_Log) as minuto,User_Connection = Valor FROM Registro_Contador B 
WHERE
	B.Id_Contador = 2
AND 	Dt_Log BETWEEN (@DT_INICIO) AND (@DT_FIM)
),
cte3
as
(
SELECT  DT = CONVERT(DATE,DT_LOG),  datepart(HOUR,Dt_Log) HORA,   datepart(minute,Dt_Log) as minuto,CPU = Valor FROM Registro_Contador B 
WHERE
	B.Id_Contador = 3
AND 	Dt_Log BETWEEN (@DT_INICIO) AND (@DT_FIM)
)
,cte4
as
(

SELECT  DT = CONVERT(DATE,DT_LOG),  datepart(HOUR,Dt_Log) HORA,   datepart(minute,Dt_Log) as minuto,[Page Life Expectancy] = VALOR FROM Registro_Contador B 
WHERE
	B.Id_Contador = 4
AND 	Dt_Log BETWEEN (@DT_INICIO) AND (@DT_FIM)
)
select
	data = convert(varchar(10),cte1.dt,103) ,
	cte1.HORA,
	cte1.MINUTO,
	BatchRequests,
	User_Connection,
	CPU,
	[Page Life Expectancy]
from
	cte1
inner join cte2 on cte1.minuto = cte2.minuto AND CTE1.DT = cte2.DT and cte1.hora = cte2.hora
inner join cte3 on cte1.minuto = cte3.minuto AND CTE1.DT = cte3.DT and cte1.hora = cte3.hora
inner join cte4 on cte1.minuto = cte4.minuto AND CTE1.DT = cte4.DT and cte1.hora = cte4.hora
order by 
	cte1.dt,
	cte1.hora,
	cte1.minuto