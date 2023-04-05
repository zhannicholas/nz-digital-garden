---
tags:
- powershell
title: PowerShell
categories:
date: 2022-09-30
lastMod: 2022-11-14
---


[PowerShell Documentation](https://learn.microsoft.com/en-us/powershell/).

[PowerShell Gallery](https://www.powershellgallery.com/).

查看 PowerShell 版本：`$PSVersionTable`

# 常用命令


  + ## 系统


    + #### 环境变量

      + 查看环境变量：

        + `Get-ChildItem env:`

        + `dir env:`

      + 设置环境变量：

        + 当环境变量的名字不含特殊字符时：`$env:ENV_NAME = 'ENV_VALUE'`

        + 环境变量的名字含有特殊字符：`${env:ENV_NAME} = 'ENV_VALUE'`

  + ## 文件

    + 计算文件哈希值：`Get-FileHash`

      + 判断从 Windows 传到 Linux 的文件是否完整

        + Powershell: `Get-FileHash <your-file> -Algorithm SHA1`

        + Shell: `shasum <your-file>`

        + 如果两条命令的输出值相同，则可以认为文件是完整的。以上采用的是 `SHA1` 算法，我们也可以使用 `SHA256`，`SHA384`，`SHA512` 等算法

    + 查看文件内容：[`Get-Content`](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-content), `cat`

      + 实时查看文件内容：`Get-Content file -Wait`

      + 读取文件开头的 `n` 行（`head` in Linux）: `Get-Content <file> | Select-Object -First <n> `

      + 读取文件末尾的 `n` 行（`tail` in Linux）： `Get-Content <file> | Select-Object -Last <n>`

    + 解压缩文件：`Expand-Archive C:\a.zip -DestinationPath C:\a`

    + 读取某个目录下的所有文件：`Get-ChildItem -Path <directory>`

      + 只显示文件名：`Get-ChildItem -Path <directory> -Name`

    + 写文件：[`Out-File`](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/out-file?view=powershell-7.3)

      + `Get-Process | Out-File -FilePath .\Process.txt`

  + ## 网络


    + 查看TCP端口占用情况：`Get-NetTCPConnection -LocalPort <your-port-number>`

    + 查看UDP端口占用情况：`Get-NetUDPEndpoint -LocalPort <your-port-number>`

    + 查看占用指定TCP端口占用的进程ID: `(Get-NetTCPConnection -LocalPort <your-port-number>).OwningProcess`

    + 检查网络连通性


      + `Test-NetConnection -ComputerName 192.168.0.6 -InformationLevel "Detailed" -Port 3389`

        + 如果结果中的 `TcpTestSucceeded` 的值为 `True`，则表示端口是通的。

    + 

  + ## 文本


    + Base64

      + 编码 ：`[System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes(<something>))`

      + 解码：`[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("blahblah"))`

    + 字符串

      + 字符串拼接

        + https://ss64.com/ps/syntax-concat.html.

      + 连接字符串

        + [`Join-String`](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/join-string?view=powershell-7.3)

          + `Get-ChildItem -Directory C:\ | Join-String -Property Name -DoubleQuote -Separator ', '`

        + 字符串替换

`
> $appKey = '0xb0 0x02 0xeb 0xab 0xe1 0xfe 0xee 0x25 0x49 0xd9 0xeb 0xe3 0x76 0xe6 0xa3 0x5b 0x9e 0xba 0xdd 0xf0 0x0d 0xc1 0xa3 0xa2 0x3d 0xb8 0x8f 0x91 0x47 0x1a 0x8b 0x67 0xc5 0x0e 0x39 0xfd 0xbc 0x62 0xb8 0x17 0x59 0x5f 0x53 0xd7 0x25 0x49 0xd9 0x90 0xe3 0x64 0x38 0xf8 0xc2 0x61 0x98 0xf6 0xb9 0x63 0xe9 0xf4 0x3a 0x93 0x78 0x53'
 $appKey -replace '0x', ''
b0 02 eb ab e1 fe ee 25 49 d9 eb e3 76 e6 a3 5b 9e ba dd f0 0d c1 a3 a2 3d b8 8f 91 47 1a 8b 67 c5 0e 39 fd bc 62 b8 17 59 5f 53 d7 25 49 d9 90 e3 64 38 f8 c2 61 98 f6 b9 63 e9 f4 3a 93 78 53
$appKey -replace '0x', '' -replace ' ',''
b002ebabe1feee2549d9ebe376e6a35b9ebaddf00dc1a3a23db88f91471a8b67c50e39fdbc62b817595f53d72549d990e36438f8c26198f6b963e9f43a937853
`

        + 

  + ## 计算


    + ### 排序

      + 根据对象属性值排序对象：[`Sort-Object`](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/sort-object)

      + 
