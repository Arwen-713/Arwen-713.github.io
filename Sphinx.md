## 基本思路
1. 创建工程文件夹
1. 安装sphinx
1. 配置文档工程，创建文档，make html；
1. 登陆readthedocs.org，导入github的repo（自己先提前建好空的repo，用于这个项目）；
1. 注意python的配置文件路径要正确；
1. 在github中，将repo配置service hooks到readthedocs，激活这个服务；
1. 使用git将本地工程文档推送到github：make clean工程文件夹，git add，commit，push完成；
1. 然后到readthedocs查看更新的结果，应该几分钟内就完成，如果创建失败，查看一些配置文件是否有问题。

### 步骤
1. 打开cmd命令提示符,cd到目标路径,在已安装python的前提下安装sphinx:`pip install -U sphinx`  
   **建议创建Python虚拟环境后安装:`python -m venv .venv` `source .venv/bin/activate` `python -m pip install sphinx`**
1. 安装完成后，输入`sphinx-build --version`查看已安装版本(确认安装成功)  
1. 通过`sphinx-quickstart docs`工具提示,在docs文件夹中为项目创建基本目录和配置布局:
   > Separate source and build directories (y/n) [n]: Write “y” (without quotes) and press Enter.  
   > Project name: Write “Lumache” (without quotes) and press Enter.  
   > Author name(s): Write “Graziella” (without quotes) and press Enter.  
   > Project release []: Write “0.1” (without quotes) and press Enter.  
   > Project language [en]: Leave it empty (the default, English) and press Enter.  
   现在文件夹内容如下:  
   > docs  
   > ├── build &emsp;&emsp;:一个空目录(目前)，用于保存呈现的文档  
   > ├── make.bat &thinsp;:方便的脚本可以简化一些常见的Sphinx操作  
   > ├── Makefile &ensp;:方便的脚本可以简化一些常见的Sphinx操作  
   > └── source   
   > &emsp;├── conf.py &emsp;:保存Sphinx项目配置的Python脚本.包含指定给sphinx-quickstart的项目名称和版本，以及一些额外的配置键  
   > &ensp;&ensp;├── index.rst &ensp;:项目的根文档，作为欢迎页面，包含“目录树”(或toctree)的根  
   > &emsp;├── _static  
   > &nbsp;&nbsp;&nbsp;&thinsp;└── _templates  
1. 第一次将文档呈现为HTML:`sphinx-build -b html docs/source/ docs/build/html`  
1. 在浏览器中打开 docs/build/html/index.html 即可看到
2. 或安装`pip install sphinx-autobuild`,借助sphinx-autobuild工具启动HTTP服务: `sphinx-autobuild source build/html`,通过 http://127.0.0.1:8000 查看

## 修改模板
- 主题
1. 安装新主题:`pip install sphinx-rtd-theme` 
2. 修改主题:打开 docs/source/conf.py 修改 html_theme = 'sphinx_rtd_theme'

- markdown插件
1. 安装依赖`pip install --upgrade recommonmark`
2. 设置:打开 docs/source/conf.py 添加以下内容:
```
from recommonmark.transform import AutoStructify
from recommonmark.parser import CommonMarkParser
# 使用插件支持markdowm   # 使用插件支持markdowm   
extensions.append('recommonmark')
# 针对`.md`为后缀的文件做markdown渲染   
source_suffix[".md"] = 'markdown'
# 设置markdown渲染器的自定义项 
def setup(app):  
    github_doc_root = 'https://localhost:5000'  
    app.add_config_value('recommonmark_config', {  
    #'url_resolver': lambda url: github_doc_root + url, # 如果不是本地调试,可以将自己项目的url根目录写上  
    "enable_auto_toc_tree": True,  
    "auto_toc_tree_section": "目录",  
    'auto_toc_maxdepth': 2, # 设置toc_tree深度           
    "enable_math": True, # 支持```math,注意目前inline_math会有问题因此不建议使用.           
    'enable_eval_rst': True, # 支持嵌入rst       
    }, True)
```
3. 安装markdown表格`pip install sphinx_markdown_tables`  

- 新增markdown页面
1. 在source/下创建hpc.md文件
2. 新页面加入source/index.rst的toctree的`:caption: Contents:`下隔一行后空三个空格(必须):`hpc #不含扩展名`

- 多语言支持 https://blog.hszofficial.site/recommend/2020/11/27/%E4%BD%BF%E7%94%A8Sphinx%E5%86%99%E9%A1%B9%E7%9B%AE%E6%96%87%E6%A1%A3/#%E5%A4%9A%E8%AF%AD%E8%A8%80%E6%94%AF%E6%8C%81
1. 安装插件`pip install sphinx-intl`
2. 设置:打开 docs/source/conf.py 添加以下内容:`locale_dirs = ['locale/']  # gettext_compact = False # optional.`
3. 将文档内容转化为pot文件,放入docs/source/gettext文件夹下(与conf.py同级):`sphinx-build -b gettext source source/gettext`  
4. 使用sphinx-intl工具将pot文件都转化成po文件用于翻译:`sphinx-intl update -p docs/source/gettext -d docs/locale -l en -l ja`,这一步的-d docs/locale和上面配置的locale_dirs对应,一种语言会对应一个文件夹,其中的文件和源文件是一一对应的关系.[.po并没有翻译,待解决]
5. 编译还是使用sphinx-build,只是需要加上-D language='<语言>'并且注意目标文件夹,建议将目标文件夹放在主文件夹下的子文件夹下`sphinx-build -D language='en' -b html source transtext/en`
