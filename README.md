# CertScriptTool
Creates PowerShell scripts that can trivially install/remove/bind SSL (self-signed or otherwise) certificates


# Description

This script makes generating and sharing certificates much simpler. 
   
   It can either take an existing certificate or generate a self-signed 
   certificate.
   
   When generating a certificate, all that is required is:
    - one or more domain names 
    - zero or more additional IpAddresses (it will attempt to resolve the DNS 
      names and add IPs for them, and localhost) 
    - password (can accept plaintext or SecureString [WHY? See Below!])  
    
   When using an existing certificate, the cert can come from a file, a path to 
   a cert, or a X509Certificate2 object.
    
   ===================
   NOTE ABOUT PASSWORD
   ===================
   
   I purposefully allowed plaintext passwords for two reasons.
      1. I hate having to play with SecureStrings when doing development. 
         It just makes things a pain. I'll mind my P's and Q's thank you very much.
         
      2. The current build of PowerShell on Nano doesn't have full support of 
         SecureString. This made it kinda difficult to actually use.
   

## EXAMPLE
``` powershell
    # create a self-signed certificate 
    PS C:\ > New-CertificateScript.ps1 -SelfSigned -DnsNames contoso.com, www.contoso.com -Password "MYPASSWORD" -OutputScript .\mycert.ps1 
    
    Generated certificate: AC23D228856B5CDE28618467C652F216EABB6D1D
    Created Certificate Script : C:\mycert.ps1
    
    # check to see what's in the generated cert:
    PS C:\ >.\mycert.ps1 -showinfo
    This script has a certificate with thumbprint AC23D228856B5CDE28618467C652F216EABB6D1D
       - The private key for this certificate is available (password protected)
       - Supported Hosts (IPs/Hostnames)
          - DNS Name=contoso.com
          - DNS Name=www.contoso.com
          - IP Address=0000:0000:0000:0000:0000:0000:0000:0001
          - IP Address=127.0.0.1
          - IP Address=64.4.6.100
          - IP Address=65.55.39.10
       - Supported Common Names
          - contoso.com www.contoso.com - This is a Self-Signed certificate    
    
    # install it on a server:
    PS C:\ >.\mycert.ps1 -install -password "MYPASSWORD"
    
    # install it on a client:
    PS C:\ >.\mycert.ps1 -install 
    
    # remove the certificate from a client or server:
    PS C:\ >.\mycert.ps1 -remove 

## EXAMPLE
``` powershell
    # create certificate script from a .pfx file
    PS C:\ > New-CertificateScript.ps1 -Cert .\mycert.pfx -PfxPassword "pwd4pfx" -Password "MYPASSWORD" -OutputScript .\mycert.ps1 

    Created Certificate Script : C:\myscript.ps1
    
    # check to see what's in the cert:
    PS C:\ > .\myscript.ps1 -showinfo
    This script has a certificate with thumbprint 0DE35C6536B30630F6B5CC6419B7EA0F3FCD50C2
   - The private key for this certificate is available (password protected)
   - Supported Hosts (IPs/Hostnames)
      - DNS Name=contoso
      - DNS Name=127.0.0.1
   - Supported Common Names
      - contoso

    # install it on a server:
    PS C:\ >.\mycert.ps1 -install -password "MYPASSWORD"
    
    # install it on a client:
    PS C:\ >.\mycert.ps1 -install 
    
    # remove the certificate from a client or server:
    PS C:\ >.\mycert.ps1 -remove 
```   
