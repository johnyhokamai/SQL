-- Validar a o que está rodando no banco de dados
exec sp_whoisactive @get_outer_command = 1,  @get_task_info =2, @get_plans = 1, @delta_interval = 10, @show_sleeping_spids = 0, @sort_order = '[CPU_delta] DESC'
-- select * from msdb.dbo.sysjobs where job_id =