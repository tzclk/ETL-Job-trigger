Trigger an ETL job inside from another job
This sheet make it possible to run a SAPDS etl job inside an another etl job.
First of all you need to export 2nd job. 'Export Execution Command' inside of the Sap Data Services Management Console.
It will generate a .bat file that you can run to start the job.
Execute it with the exec built-in function in a script at the end of the 1st job.
It is meaningful when you use it inside the job.
There is a sample below. ALVW_HISTORY is one of the default audit tables of SAP.
 
#wait for 2 seconds
 
#sleep(2000);
 
 
Print('JOB_X running');
 
 
Print(exec('C:\ProgramData\SAP BusinessObjects\Data Services\log\JOB_X.bat','',8));
 
 
 
#Print('Trigger JOB_X');
 
#Print(exec('C:\ProgramData\SAP BusinessObjects\Data Services\log\JOB_X.bat','',8));
 
#Remain idle for 2 secs so that Job Status is Stable (Status moves from S to D for a Successful Job and E for Error)
 
#Sleep(2000);
 
#Pick up the latest job Start time
 
#$G_MaxTimestamp =  sql('DS_SAP_DS_REP', 'SELECT  MAX(START_TIME) FROM ALVW_HISTORY WHERE SERVICE=\'JOB_X\';');
 
#PRINT($G_MaxTimestamp);
 
#Check the latest status of the preceding job
 
#$G_JobStatus = sql('DS_SAP_DS_REP', 'SELECT STATUS FROM ALVW_HISTORY WHERE SERVICE=\'JOB_X\' AND START_TIME=\'[$G_MaxTimestamp]\';');
 
#PRINT($G_JobStatus);
 
#if ($G_JobStatus='E')
 
#begin
 
#PRINT('First Job Failed');
 
#raise_exception('First Job Failed');
 
#end
 
#else
 
#begin
 
#print('First Job Success, Second Job will be Triggered');
 
#end
 
#Print('Trigger job_2');
 
#Print(exec('C:\Program Files (x86)\SAP BusinessObjects\Data Services\log\job_2.bat','',8));

