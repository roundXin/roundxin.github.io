### pip升级ValueError: Unable to find resource t64.exe in package pip._vendor.distlib报错解决办法
>- python.exe -m pip install --upgrade pip 报错：ValueError: Unable to find resource t64.exe in package pip._vendor.distlib或
  - `ERROR: To modify pip, please run the following command:
    D:\lvsiqin\common\APPlist\miniconda3\envs\rasa2\python.exe -m pip install --upgrade pip
    WARNING: There was an error checking the latest version of pip.`
  - 解决方案：卸载 setuptools
    - python -m pip uninstall pip setuptools
    - 升级pip：pip install --upgrade pip
    - 重新安装setuptools: pip install --upgrade setuptools 
### Spacy安装：
>- 安装命令：`pip install https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-3.0.0/en_core_web_sm-3.0.0.tar.gz`
>- 安装命令： pip install /Users/you/en_core_web_sm-3.0.0.tar.gz
