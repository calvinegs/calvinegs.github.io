---
title: "使用 Python 上的虛擬環境(一) venv"
date: 2023-09-22
description: "Python 的虛擬環境是用來管理使用套件版本問題的解決方案，實務上有許多不同的方案，本編筆記先來介紹 Python 內建的 venv"
tags: ["python", "virtual Environment", "venv"]
categories: ["python"]
---

Python 安裝程式除了會在本機安裝進執行檔外，也會連帶安裝一些標準函式庫的套件，但Python 應用程式通常還會用到不在標準函式庫的套件和模組。由於某些特殊的臭蟲修正應用程式有時候會需要某個特定版本的函式庫，或是不同應用程式是根據某函式庫特定版本的介面所撰寫。

這意味著不太可能安裝一套 Python 就可以滿足所有應用程式的要求。如果應用程式 A 需要一個特定的模組的 1.0 版，但另外一個應用程式 B 需要 2.0 版，那麼這整個需求不管安裝 1.0 或是 2.0 都會衝突，以致於應用程式無法使用。

Python 的虛擬環境是用來管理使用套件版本問題的解決方案，實務上有許多不同的方案，包含有：venv、virtualenv、pipenv、conda...等。

本編筆記先來介紹 Python 內建的 venv 這個虛擬環境管理工具。"venv" 是 Python 的預設內建的模組，自 Python 3.3 版本以來就存在，因此不需要額外安裝任何其他套件即可使用，方便就是它的最大好處，但相對於其他解決方案，它也就陽春了許多。

在介紹 venv 前讓我們先從安裝 Python 說起。

# 在 windows 系統中安裝 Python 

在 windows 作業系統上安裝 Python 有許多不同的方式，這裡只展示最典型、最簡易的方式 - 使用安裝執行檔。

## 先確認你的作業系統上是否已經安裝了 Python 及其版本
- 在 cmd 程式中輸入 python --version，如果執行本指令後顯示空白則表示尚未安裝任何版本的 python

> 開啟 cmd.exe，在 windows 桌面上按下快速鍵 Window + R (組合鍵)叫出 "執行視窗"，輸入 "cmd" 後按下“確定”按鈕來啟動 cmd 程式 (註：若你要以administrator身份開啟 cmd 程式的話，請使用 "ctrl+shift+enter"組合鍵來取代按下“確定”按鈕的動作)

![image](https://user-images.githubusercontent.com/21993717/269820085-db610614-634c-4855-91b4-e9da4ddf20dc.png)


```dos
c:\>python --version
Python 3.8.10   # 表示已安裝有 python 3.8.10 版本
```

- 可在 cmd 程式中使用 where 指令來查看已安裝的 python 是存放在那個目錄下
```dos
c:\>where python
```

## 下載 windows 版的 Python 安裝程式

下載的 URL: https://www.python.org/downloads/windows/

在這個網頁中可以看到：
- 最新的版本是 3.11.5
- 3.11.5 版本是屬“穩定版”
- 3.11.5 版的發行日期是 9/24/2023
- 在 windows 10 中建議下載 "Windows installer (64-bit)" 這個 65 位元版安裝程式


![image](https://user-images.githubusercontent.com/21993717/269828095-6ab7402e-db12-4abd-9b27-19c43da521b0.png)

下載成功後，在下載目錄中會看到 python-3.11.5-amd64.exe 

![image](https://user-images.githubusercontent.com/21993717/269827495-11dc0f72-ce6e-49ab-b2a4-58db16fad758.png)



## 先檢視安裝前時的 windows 環境變數

- 開啟 "系統" 中的 "進階系統設定"

![image](https://user-images.githubusercontent.com/21993717/269828186-b3282260-84f6-4ab0-8498-34491e121db0.png)

![image](https://user-images.githubusercontent.com/21993717/269828271-bdc55d5a-2fca-421f-a8a8-909636fcd72e.png)

> 在 "系統內容" - "進階" - "環境變數"　中可以看到目前在作業系統中所有的"環境變數"：

![image](https://user-images.githubusercontent.com/21993717/269828361-214d5737-9be4-41eb-923a-f59368548ba6.png)

> 編輯 PATH 這個環境變數：

![image](https://user-images.githubusercontent.com/21993717/269828472-766eb195-d18f-4f9e-a774-70c46959a7dd.png)

> 顯示目前 PATH環境變數 的設定值：
![image](https://user-images.githubusercontent.com/21993717/269828558-a50a0936-0f18-4e1f-b9a7-74a68a644dbf.png)

## 進行 Python 的安裝

在 cmd 中直接執行已下載成功的安裝執行檔 python-3.11.5-amd64.exe，在安裝的畫面中，首先先勾選畫面下方的“Add python.exe to PATH" 的選項，在按下"Install Now" 

註：在此處可以看到python 預設安裝的路徑，同時也說明會一併安裝 IDLE(簡易編輯器)及pip(套件安裝管理工具)和相關說明文件

![image](https://user-images.githubusercontent.com/21993717/269827981-991954da-c405-4f34-9c3d-a28c631809ae.png)

進行安裝中
![image](https://user-images.githubusercontent.com/21993717/269829890-be7d47e8-5d60-48cb-83fc-c28732087e18.png)

安裝成功：見下圖表示安裝成功，為使相關設定值生效，請登出後再登入（或重新開機）

![image](https://user-images.githubusercontent.com/21993717/269830453-9f8d961f-45c0-4569-a8b4-593570f0a36c.png)


## 檢視安裝好的相關設定

使用前面介紹過的幾種方式來查看安裝完成後的結果和安裝前有何不同。

- python3 --version
- 使用 where 指令
- 查看系統環境變數 - PATH

![image](https://user-images.githubusercontent.com/21993717/269832098-a2015d86-cb80-4d94-a5cd-b197088271e9.png)


![image](https://user-images.githubusercontent.com/21993717/269832340-61108375-50a8-4cae-8435-be8a5f91fcb5.png)

還可以在 Windows 的 “應用程式與功能” 中，看到新安裝好的 python 3.11.5 應用程式:

![image](https://user-images.githubusercontent.com/21993717/269832824-6dec0e36-c3c4-484e-ba86-908a8588174f.png)

註：要移除 Python 可以使用“解除安裝”按鈕來進行。


# 使用 venv 模組來管理虛擬環境

## 了解 Python 執行環境

在安裝 Python 的說明中知道我們安裝的 pytho 3.11.5 版的安裝目錄是在 "C:\Users\calvi\AppData\Local\Programs\Python\Python311" (其中 calvi 是登入 windows 時的 User Name)。

python 哪裡來？主要是歸功於 windows 中配置的系統環境變數: PATH，Python 安裝程式執行完安裝動作後，會在 PATH 中加入了一條安裝路徑的設定值。當你在 cmd 程式中輸入了 python 指令時，windows 系統會依此一設定值去找尋 python.exe 的位置，找到後再執行它。

來看看這個Python安裝目錄下的檔案結構：
```
.\Python311 (主目錄)
├── DLLs 
├── Doc 
├── include 
├── Lib   ＝》(Python標準套件的存放目錄)
│   └── site-packages ＝》(第三方套件存放的子目錄)(即 system site-packages)
├── libs 
├── Scripts 
│   ├── pip.exe
│   ├── pip3.11.exe
│   └── pip3.exe
├── tcl 
├── Tools 
├── ...
├── python.exe　＝》直譯器（主要執行檔）
└── ...
```

Python 執行環境主要由執行 Python 直譯器(即 python.exe)的位置所決定。

## venv 虛擬環境管理工具

在 Python 3.3 版中新加入一個用來建立與管理虛擬環境的模組叫做 venv。

"venv" 基於你當前的 Python 安裝創建虛擬環境，它會複製你正在使用的 Python 版本到"虛擬環境"中。

"venv" 提供了基本的虛擬環境功能，包括建立、啟用和停用虛擬環境。

### 建立虛擬環境 

```dos
C:\projects>python -m venv venv-demo

C:\projects>
C:\projects>dir
 磁碟區 C 中的磁碟沒有標籤。
 磁碟區序號:  960E-5612

 C:\projects 的目錄

2023/09/23  上午 11:41    <DIR>          .
2023/09/23  上午 11:41    <DIR>          ..
2023/09/23  上午 11:41    <DIR>          venv-demo
               0 個檔案               0 位元組
               3 個目錄  17,456,541,696 位元組可用

C:\projects>
```

執行了上述建立虛擬環境的指令後，系統會建立一個名為 venv-demo 的子目錄，來看看這個子目錄的結構
```
.\venv-demo (虛擬環境的目錄)

├── include 
├── Lib   
│   └── site-packages ＝》(虛擬環境中存放第三方套件的子目錄)
├── pyven.cfg
└── Scripts 
    ├── activate
    ├── activate.bat (在 cmd 中要啟動虛擬環境的指令)
    ├── Activate.ps1 (在 power shell 中要啟動虛擬環境的指令)
    ├── deactivate.bat (停用虛擬環境的指令)
    ├── pip.exe
    ├── pip3.11.exe
    ├── pip3.exe
    ├── python.exe
    └── pythonw.exe
```

來查看一下 pyvenv.cfg 這個組態檔的內容

```dos
C:\projects\venv-demo>type pyvenv.cfg
home = C:\Users\calvi\AppData\Local\Programs\Python\Python311
include-system-site-packages = false
version = 3.11.5
executable = C:\Users\calvi\AppData\Local\Programs\Python\Python311\python.exe
command = C:\Users\calvi\AppData\Local\Programs\Python\Python311\python.exe -m venv C:\projects\venv-demo

C:\projects\venv-demo>
```

可以發現系統是使用：<b>當前版本</b>的 Python 來創建虛擬環境，它會複製正在使用的 Python 版本到"虛擬環境"中（複製至 Scripts 子目錄中），以本例而言當前的Python版本是3.11.5，因此當你啟用這個虛擬環境時所使用的Python就是這個版本。


### 啟用虛擬環境

啟用虛擬環境是直接呼叫 Script\activate.bat 即可。
在啟用之前先來看看目前的環境變數 PATH 的狀態。在 cmd 中直接輸入 path 再按下 enter 鍵就會顯示目前環境變數 PATH 現有的設定值。

```dos
C:\projects\venv-demo>path
PATH=C:\WINDOWS\system32;C:\WINDOWS;C:\WINDOWS\System32\Wbem;C:\WINDOWS\System32\WindowsPowerShell\v1.0\;C:\WINDOWS\System32\OpenSSH\;C:\Program Files\Microsoft SQL Server\130\Tools\Binn\;C:\ProgramData\chocolatey\bin;C:\Program Files\dotnet\;C:\Program Files\Git\cmd;C:\Program Files (x86)\Pulse Secure\VC142.CRT\X64\;C:\Program Files (x86)\Pulse Secure\VC142.CRT\X86\;C:\Program Files (x86)\Common Files\Pulse Secure\TNC Client Plugin\;C:\Users\calvi\AppData\Local\Programs\Python\Python311\Scripts\;C:\Users\calvi\AppData\Local\Programs\Python\Python311\;C:\Users\calvi\AppData\Local\Microsoft\WindowsApps;C:\Users\calvi\AppData\Local\Programs\Microsoft VS Code\bin;C:\Users\calvi\AppData\Local\nvs\default;C:\Users\calvi\AppData\Local\nvs\;C:\Users\calvi\.dotnet\tools;
```

接著在 cmd 中輸入　C:\projects\venv-demo>c:\projects\venv-demo\Scripts\activate.bat，用來啟用先前我們建立虛擬環境 venv-demo。

```dos
C:\projects\venv-demo>c:\projects\venv-demo\Scripts\activate.bat
```

啟動成功後，cmd 中的提示字元會成 (venv-demo) C:\projects\venv-demo>，其中括弧內的就是虛擬環境的名稱

![image](https://user-images.githubusercontent.com/21993717/270086876-095fe30e-b1d1-475c-8628-4b8bf493d64a.png)


這時我們再來觀查一下環境變數 PATH 目前的狀態

![image](https://user-images.githubusercontent.com/21993717/270086958-e0b4f881-b8a7-4fd4-a60b-38803931314f.png)


由於 “C:\projects\venv-demo\Scripts”　這個設定值位於 PATH 環境變數的最前面，在 cmd 中執行指令時，這個目錄被搜尋的優先權最高。以 pip3 指令為例，當此刻在 cmd 中輸入 pip3 並執行時，優先權的關係被執行的 pip3 程式是位在虛擬環境中的 pip3，而非系統安裝 python 時預設目錄("C:\Users\calvi\AppData\Local\Programs\Python\Python311\Scripts")中的 pip3，也正因這個緣故，此時用 pip3 安裝的套件是會存放在“虛擬環境”所對應的　site-packages 目錄(即C:\projects\venv-demo\Lib\site-packages)中而非 C:\Users\calvi\AppData\Local\Programs\Python\Python311\Lib\site-packages 目錄(即 system site-packages)裡。

![image](https://user-images.githubusercontent.com/21993717/270087170-a1f721c8-00db-49c2-be20-9cbd906916fd.png)


在 cmd 中使用 where 指令來查看一下 python & pip，會發現執行的優先順序都指到了虛擬環境的 Scripts 目錄中了。

![image](https://user-images.githubusercontent.com/21993717/270097115-a8b33723-796f-43dd-8499-e2a052885ddc.png)

### 在虛擬環境中安裝第三方套件

在啟用虛擬環境的狀態下，使用 pip3 install rich 來安裝 rich 這個第三方套件。

```dos
(venv-demo) C:\projects\venv-demo>pip install rich
Collecting rich
  Obtaining dependency information for rich from https://files.pythonhosted.org/packages/c1/d1/23ba6235ed82883bb416f57179d1db2c05f3fb8e5d83c18660f9ab6f09c9/rich-13.5.3-py3-none-any.whl.metadata
  Downloading rich-13.5.3-py3-none-any.whl.metadata (18 kB)
Collecting markdown-it-py>=2.2.0 (from rich)
  Obtaining dependency information for markdown-it-py>=2.2.0 from https://files.pythonhosted.org/packages/42/d7/1ec15b46af6af88f19b8e5ffea08fa375d433c998b8a7639e76935c14f1f/markdown_it_py-3.0.0-py3-none-any.whl.metadata
  Downloading markdown_it_py-3.0.0-py3-none-any.whl.metadata (6.9 kB)
Collecting pygments<3.0.0,>=2.13.0 (from rich)
  Obtaining dependency information for pygments<3.0.0,>=2.13.0 from https://files.pythonhosted.org/packages/43/88/29adf0b44ba6ac85045e63734ae0997d3c58d8b1a91c914d240828d0d73d/Pygments-2.16.1-py3-none-any.whl.metadata
  Downloading Pygments-2.16.1-py3-none-any.whl.metadata (2.5 kB)
Collecting mdurl~=0.1 (from markdown-it-py>=2.2.0->rich)
  Using cached mdurl-0.1.2-py3-none-any.whl (10.0 kB)
Downloading rich-13.5.3-py3-none-any.whl (239 kB)
   ---------------------------------------- 239.8/239.8 kB 1.8 MB/s eta 0:00:00
Downloading markdown_it_py-3.0.0-py3-none-any.whl (87 kB)
   ---------------------------------------- 87.5/87.5 kB 4.8 MB/s eta 0:00:00
Downloading Pygments-2.16.1-py3-none-any.whl (1.2 MB)
   ---------------------------------------- 1.2/1.2 MB 2.3 MB/s eta 0:00:00
Installing collected packages: pygments, mdurl, markdown-it-py, rich
Successfully installed markdown-it-py-3.0.0 mdurl-0.1.2 pygments-2.16.1 rich-13.5.3
```

安裝完成後再使用 pip list 來查看目前環境下所有的套件清單，會發現與安裝套件前的清單已有不同。

```dos
(venv-demo) C:\projects\venv-demo>pip list
Package        Version
-------------- -------
markdown-it-py 3.0.0
mdurl          0.1.2
pip            23.2.1
Pygments       2.16.1
rich           13.5.3
setuptools     65.5.0
```

使用 rich 測試指令　python -m rich　來查看 rich 套件是否已安裝程式

![image](https://user-images.githubusercontent.com/21993717/270088198-1f26f13a-44bd-45b3-9da8-091360f6ef69.png)


### 停用虛擬環境

要停用虛擬環境時，直接在 cmd 中輸入 deactivate 再按 enter 鍵即可。會發現提示字元已恢復成原來的樣子。

```dos
(venv-demo) C:\projects\venv-demo>deactivate
C:\projects\venv-demo>
```

註：不必指定目錄名稱的原因也是因為 PATH 環境變數已有“C:\projects\venv-demo\Scripts”　這個設定值的闗係。

停用虛擬環境後我們可以再使用 pip list & python -m rich 等指令來測試，結果發現 rich 套件並不存在 Global Python 執行環境中。

![image](https://user-images.githubusercontent.com/21993717/270088327-01728335-a0bf-456e-a847-5da3156bf3bc.png)