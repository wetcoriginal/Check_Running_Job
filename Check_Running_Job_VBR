#This script is a previous version of the Check_Running_Jobs_VBR.ps1 script you can found here : 
#https://github.com/wetcoriginal/Check_Running_Job/blob/main/Check_Running_Jobs_VBR
#By @wetcoriginal for VeeamBackup&Replicate jobs supervision

Add-PSSnapin -Name VeeamPSSnapIn -ErrorAction SilentlyContinue
Remove-Variable * -ErrorAction SilentlyContinue; 
Remove-Module *; $error.Clear();

$EstRun = get-date
$Jobs = get-vbrjob
$Job = $null

foreach($Job in $Jobs)
{
    $LastRun = Get-VBRSession -Job $Job -Last | Select-Object -ExpandProperty CreationTime
    $DiffTime = new-timespan $LastRun $EstRun
    $LastSession = $Job.findlastsession()
    if(($LastSession.State -eq "Working") -and ($DiffTime.TotalHours -gt 24))
    {
        $global:CriticalCount++
        $global:OutMessageTemp += "CRITICAL - The job '" + $Job.Name + "' has been running for more than 24 hours`r`n"
        $global:ExitCode=2
        if($global:ExitCode -ne 2) {$global:ExitCode = 1}
    }
    elseif (($LastSession.State -eq "Working") -and ($DiffTime.TotalHours -lt 24))
    {
         $global:OutMessageTemp += "OK - The job '" + $Job.Name + "' is in progess since " + $DiffTime.Hours + " hours and " + $DiffTime.Minutes + " minutes `r`n"
         $global:OkCount++
    }
}

######################################################
#           Main loop (well, not exactly a loop)     #
######################################################
 
$nextIsJob=$false
$oneJob=$false
$jobToCheck=""
$WrongParam=$false
$DisabledJobs=$true
$global:OutMessageTemp=""
$global:OutMessage=""
$global:Exitcode=""
$WarningPreference = 'SilentlyContinue'
 
#Ajout de variables pour compter le nombre d'erreurs
$global:WarningDisabledCount=0
$global:WarningCount=0
$global:CriticalCount=0
$global:OkCount=0
$TotalCount=0
$global:Graph=""

if($LastSession.State -eq "Success" -or "Failed")
{
    $global:OutMessageTemp += "OK - No current backup job is running`r`n"
    $global:OkCount++
}
else{
    $global:OutMessageTemp += "OK - All Veeam Backup jobs are running normally`r`n"
    $global:OkCount++
}

$TotalCount=$global:WarningDisabledCount + $global:WarningCount + $global:CriticalCount + $global:OkCount
$global:OutMessage="TOTAL=>" + $TotalCount + " / OK=>" + $global:OkCount + " / CRITICAL=>" + $global:CriticalCount + " / DISABLE=>" + $global:WarningDisabledCount + " / WARNING=>" + $global:WarningCount
#Ajout variable Graph pour visualisation graphique sur centreon
$global:Graph=" |  Ok=" + $global:OkCount + " Warning=" + $global:WarningCount + " Critical=" + $global:CriticalCount

write-host $global:OutMessageTemp
write-host $global:OutMessage
exit $global:Exitcode
