# New-CertificateScript.ps1
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
   

### Syntax
``` powershell
    # generate a script for a self-signed certificate
    C:\root\work\CertScriptTool\New-CertificateScript.ps1 
        -SelfSigned 
        -DnsNames <String[]> 
        [-IpAddresses <String[]>] 
        [-Password <Object>] 
        [-OutputScript <Object>] 
        [<CommonParameters>]

    # generate a script for an existing certificate
    C:\root\work\CertScriptTool\New-CertificateScript.ps1 
        -certificate <Object> 
        [-PfxPassword <Object>] 
        [-Password <Object>] 
        [-OutputScript <Object>] 
        [<CommonParameters>]
    
    
    -SelfSigned [<SwitchParameter>] 
        # generates a self-signed certificate for testing

    -DnsNames <String[]>    
        # a list of DNS (machine) names to use for the certificate

    -IpAddresses <String[]> 
        # any additional IP addresses to bind the certificate for 

    -certificate <Object>   
        # use an existing cert instead of generating on. Can be a certificate 
        # file, a certificate path, or an X509Certificate2 object

    -PfxPassword <Object>
        # if passing in a path to a .pfx file, the password for that file

    -Password <Object>
        # the password to protect the private key in the generated script

    -OutputScript <Object>
        # the output script name. Defaults to .\$THUMBPRINT-Certificate.ps1
    
```

### EXAMPLE: create a self-signed certificate 
``` powershell
    
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
    
    ## You can also have it bind the SSL Certificate when installing 
    ## -------------------------------------------------------------
    
    # install it on a server and bind the cert for SSL:
    PS C:\ >.\mycert.ps1 -install -password "MYPASSWORD" -sslport 443
    
    # remove the certificate from a server and unbind the SSL:
    PS C:\ >.\mycert.ps1 -remove -sslport 443
    
```

### EXAMPLE: create a self-signed certificate, install


### EXAMPLE: create certificate script from a .pfx file
``` powershell
    
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
