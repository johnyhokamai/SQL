Exec sp_WhoIsActive @get_outer_command=1,@get_plans = 1,
@find_block_leaders =1, @sort_order = '[blocked_session_count] DESC',
@output_column_list = '[dd hh:mm:ss.mss],[blocked_session_count],[open_tran_count],[session_id],[blocking_session_id],[wait_info],[status],[login_name],[host_name],[database_name],[program_name],[sql_text], [sql_command],[reads],[writes],[query_plan]'
--,@filter = '10', @filter_type = 'session'
--sp_who2
-- select * from msdb.dbo.sysjobs where job_id =