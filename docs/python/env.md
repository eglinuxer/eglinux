# Python 虚拟环境

!!! tip

    - 使用 `venv`、`pipenv`、`poetry`、`pyenv`、和 `anaconda` 创建虚拟环境。
    - 使用 `pip`、`poetry`、`pipenv`、和 `conda` 安装 Python 包。
    - 使用 `requirements.txt`、`poetry`、和 `pipenv` 管理 Python 包依赖。

- 使用虚拟环境的好处
    * 避免使用 sudo 权限在系统中安装 Python 包，万一安装的包有病毒，那相当于别人直接能拿到你系统的 root 权限。
    * 避免破坏系统的包管理。
    * 避免多个项目之间的不兼容性。

## 1. 创建虚拟环境

- 使用 venv

=== "Linux/macOS"

    ``` bash
    $ python3 -m venv envs/your_env
    $ source envs/your_env/bin/activate
    (your_env) $
    ```

=== "Windows"

    ``` powershell
    PS C:\Users\wolph>python.exe -m venv envs\your_env
    PS C:\Users\wolph> envs\your_env\Scripts\Activate.ps1
    (your_env) PS C:\Users\wolph>
    ```

- 使用 pyenv

``` bash
# 安装 pyenv
curl https://pyenv.run | bash
# 查看 pyenv 管理的 python 版本
pyenv install --list
# 使用 pyenv 安装新的 python 版本
pyenv install 3.10-dev
Cloning https://github.com/python/cpython...
Installing Python-3.10-dev...
Installed Python-3.10-dev to /home/eg/.pyenv/versions/3.10-dev
# 使用 pyenv 创建 python 虚拟环境
pyenv virtualenv 3.10-dev your_pyenv
pyenv activate your_pyenv
```

- 使用 anaconda
    - 我没有使用过，暂时不记录，后续如果使用过了可以记录一下。

## 2. 管理包依赖

- requirements.txt 文件
    - 文件中列出依赖的 python 包，可以指定版本号。
    - pip3 install -r requirements.txt 安装依赖。
        - -r 选项会递归查找依赖，运行包含多个 requirements.txt 文件。
- 从源代码仓库安装
    - pip3 install --editable 'git+https://github.com/wolph/python-progressbar@develop#egg=progressbar2'
        - --editable 选项，简写 -e，作用是安装的时候会将仓库 clone 到虚拟环境中的 src 目录下，当再次运行的时候就会自动 pull 仓库中的更新并进行安装。