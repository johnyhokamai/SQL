CREATE TABLE #TEMPDATAFILES( 

[database] varchar (300), 

[filename] varchar(300), 

[path] varchar(300), 

[Currently Allocated Space (MB)] decimal(15,2), 

[Space Used (MB)] decimal(15,2), 

[Available Space (MB)] decimal(15,2), 

[Available Percentage %] decimal(15,2)) 

exec sp_msforeachdb @command1= N' use[?] insert into #TEMPDATAFILES SELECT DB_NAME(),name,filename, 

convert(varchar(300),CONVERT(Decimal(15,2),ROUND(a.Size/128.000,2))) [Currently Allocated Space (MB)], 

convert(varchar(300),CONVERT(Decimal(15,2),ROUND(FILEPROPERTY(a.Name,''SpaceUsed'')/128.000,2))) AS [Space Used (MB)], 

convert(varchar(300),CONVERT(Decimal(15,2),ROUND((a.Size-FILEPROPERTY(a.Name,''SpaceUsed''))/128.000,2))) AS [Available Space (MB)], 

CONVERT(Decimal(15,2),ROUND((a.Size-FILEPROPERTY(a.Name,''SpaceUsed''))/128.000,2)*100/ROUND(a.Size/128.000,2)) 

FROM dbo.sysfiles a (NOLOCK)' 

select * from #TEMPDATAFILES where path like 'C:\%' order by 6 desc -- SELECIONE O DISCO DESEJADO E APÓS O % O TIPO DE ARQUIVO, SE QUISER VER SOMENTE LOG OU MDF

drop table #TEMPDATAFILES 

  