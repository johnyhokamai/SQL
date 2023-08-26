-- script que pega plano de execução dos trechos

    if object_id('tempdb..#tmp') is not null drop table #tmp  
SELECT db_name(s3.dbid) as dbname,
    object_name(s3.objectid, s2.dbid) as obj_name,
    (
        SELECT TOP 1 SUBSTRING(
                s2.text,
                statement_start_offset / 2 + 1,
                (
                    (
                        CASE
                            WHEN statement_end_offset = -1 THEN (LEN(CONVERT(nvarchar(max), s2.text)) * 2)
                            ELSE statement_end_offset
                        END
                    ) - statement_start_offset
                ) / 2 + 1
            )
    ) AS sql_statement,
    s1.creation_time,
    last_execution_time,
    execution_count,
    convert(
        time(2),
        dateadd(
            ms,
            total_elapsed_time / execution_count / 1000.,
            '19900101'
        )
    ) as avg_duration,
    convert(
        time(2),
        dateadd(
            ms,
            total_worker_time / execution_count / 1000.,
            '19900101'
        )
    ) as avg_work_time,
    convert(
        time(2),
        DATEADD(ms, total_elapsed_time / 1000., '19900101')
    ) as total_elapsed_time,
    convert(
        time(2),
        DATEADD(ms, total_worker_time / 1000., '19900101')
    ) total_worker_time,
    total_logical_reads,
    total_physical_reads,
    total_logical_writes,
    s2.text,
    s3.query_plan,
    s1.query_hash,
    max_elapsed_time,
    plan_handle --cast(s3.query_plan as xml) query_plan  
    into #tmp  
FROM sys.dm_exec_query_stats AS s1
    CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS s2
    outer apply sys.dm_exec_text_query_plan (
        plan_handle,
        statement_start_offset,
        statement_end_offset
    ) s3
where --plan_handle = 0x05000500FB08E323505EAE9ADC00000001000000000000000000000000000000000000000000000000000000  
    s3.objectid = object_id('spr_gerar_despesa_pre_fatura_v1') --s2.text like '%ABACOS_RPL.dbo.PEDIDORISCO WITH (ROWLOCK)%' --altera para um trecho da query  
ORDER BY total_worker_time desc --select object_id('spr_gerar_despesa_pre_fatura')
select CONVERT(xml, query_plan),
    max_elapsed_time,
    *
from #tmp order by last_execution_time desc
