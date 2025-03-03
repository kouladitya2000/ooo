# Input bindings are passed in via param block.
param($Timer)

# Get the current universal time in the default string format
$currentUTCtime = (Get-Date).ToUniversalTime()

# The 'IsPastDue' porperty is 'true' when the current function invocation is later than scheduled.
if ($Timer.IsPastDue) {
    Write-Host "PowerShell timer is running late!"
}

# Write an information log with the current time.
Write-Host "PowerShell timer trigger function ran! TIME: $currentUTCtime"


try
{
    Connect-AzAccount -Identity -Subscription sub-mq01-azcst-01
}catch {
    Write-Host "Error connecting to Azure using fun app identity. $_.Exception"
    throw $_.Exception
}
$pass = Get-AzKeyVaultSecret -VaultName 'azcst-kv-aa-mq-01' -Name 'appid-f' -AsPlainText
$password = ConvertTo-SecureString $pass -AsPlainText -Force
$TenantId = '2fa2ec5a-717a-4157-8'
$ApplicationId = '998b6fc5-1'
$Credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $ApplicationId, $password
try{
Connect-AzAccount -ServicePrincipal -TenantId $TenantId -Subscription sub-mq01-azcst-01 -Credential $Credential
} 
catch{
    Write-Host "Error connecting to Azure. $_.Exception"
    throw $_.Exception
}


SCRIPT
