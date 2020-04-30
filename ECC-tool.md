# 1. What is ECC tool?
ECC is a python tool which helps to detect coding style issues.<br>
It reports errors for the codes which don't follow [EDK II C Coding Standards Specification](https://edk2-docs.gitbooks.io/edk-ii-c-coding-standards-specification/content/).<br>

# 2. Where is ECC tool?
ECC tool is located in edk2/BaseTools/Source/Python/Ecc.

# 3. How to run ECC tool?<br>
Steps to run ECC tool:<br>
**1). Enter edk2 directory, run: **edksetup.bat**** (**on Windows**)<br>
      **Enter edk2 directory, run: **source edksetup.sh**** (**on Linux**)<br>

**2). Then in edk2 directory, you can type "Ecc" to run ECC tool directly**.<br>

**3). If you meet following errors:**<br>
**Error 1:**<br>
    **import antlr3**<br>
    **ImportError: No module named antlr3**<br>

This error may be met when you run ECC tool with python 2.x, then ECC depends on antlr V3.0.1, you can download it from http://www.antlr3.org/download/Python/ <br>
After download and extract it, you can enter the antlr tool directory and run: <br>
**C:\Python27\python.exe setup.py install** to install it.(**on Windows**) <br>
**python setup.py install** to install it, root access may be required.(**on Linux**) <br>

**Error 2:**<br>
    **import antlr4 as antlr** <br>
    **ModuleNotFoundError: No module named 'antlr4'** <br>

This error may be met when you run ECC tool with python 3.x, then ECC depends on antlr4, you can install it through following command.<br>
**py -3 -m pip install antlr4-python3-runtime** to install it.(**on Windows**) <br>
**sudo python3 -m pip install antlr4-python3-runtime** to install it. (**on Linux**) <br>

**4). You can type "Ecc -h/Ecc --help" to get the help info of ECC tool**.<br>

**5). Common usage model:**<br>
**Ecc -c config file  -e exception file  -t  the target directory which needs to be scanned by ECC -r the ECC scan result CSV file**<br>
(Notes: Please use the full path when specify the target directory to scan.)

config.ini and exception.xml are in edk2/BaseTools/Source/Python/Ecc dir.<br>
**Config.ini** is the configuration file of ECC tool. If config file is not specified when run ECC, it will use the one in Edk2/BaseTools/Source/Python/Ecc directory by default.<br>
**exception.xml** is used to skip some specific coding style issues.<br>

For example, run ECC to check the coding style in Mdepkg:<br>
**Ecc –c  D:/AWORK/edk2/BaseTools/Source/Python/Ecc/config.ini**
**-e D:/AWORK/edk2/BaseTools/Source/Python/Ecc/exception.xml -t D:/AWORK/edk2/MdePkg -r MdePkgECC.csv** <br>

(Notes: When running ECC for a specific sub-dir, it may report some errors such as some library instances are not used, but when running ECC for the whole project, these errors are gone. We can ignore such kind of error when running ECC for a specific sub-dir.)<br>

**6). You may need to maintain the config.ini and exception.xml files by yourself for your project.**<br>

**a) If you want to skip to check some sub-dir or file, you can add them to the SkipDirList, SkipFileList part in the config.ini.**<br>
A list for skip dirs when scaning source code <br>
SkipDirList = **BUILD, ...,** TEST\\TEST <br>
A list for skip file when scaning source code <br>
SkipFileList = **.gitignore,...** <br>

**b) If you want to skip a specific ECC error, you can add them to the exception.xml file.**<br>
The mapping relationship between exception format and ECC error like below.
![](images/EccException.PNG)
