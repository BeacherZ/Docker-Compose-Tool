# Docker-Compose-Tool

本项目主要用来自定义卷映射的绝对路径，统一管理所有项目的卷映射（不含隐藏卷）到同一个目录下

解决各docker项目只推荐卷映射，导致小白依样画葫芦docker内部卷映射到哪里都不清楚

示例站点：[dc.365888.xyz](https://dc.365888.xyz/)

注意：本项目是在这个[docker2compose](https://github.com/hyang0/docker2compose)项目的基础上AI搓的

虽然更迭了好几版了，但是不排除还有BUG，转换完记得肉眼核实映射卷是否合规再复制应用到compose



Docker Run 转 Compose 工具 - 使用说明
======================================

【文件结构】

├── index.html      ← 主页面，直接用浏览器访问

└── volumes.js      ← 配置文件，设置默认卷映射路径

两个文件必须放在同一目录下。


【部署方式】

方式一：放到网站根目录
  将 index.html 和 volumes.js 上传到站点根目录或子目录
  例如：https://你的域名/ 或 https://你的域名/docker/
  
方式二：本地使用
  双击 index.html 直接用浏览器打开即可


【修改默认卷映射路径】

打开 volumes.js，由于我使用dpanel面板，所以默认的内容格式为：

  VOLUME_PREFIX='/home/dpanel/compose/{container_name}'

请按自己的实际需求修改引号内的路径，保存后刷新网页即可生效。

示例：

  VOLUME_PREFIX='/home/dpanel/compose/{container_name}'
  
  VOLUME_PREFIX='/opt/docker/{container_name}'
  
  VOLUME_PREFIX='/data/containers/{container_name}'

其中 {container_name} 会在转换时自动替换为容器名。
如果留空 VOLUME_PREFIX='' 则不做路径替换，保持原始路径。


【注意事项】

1. volumes.js 里的内容必须保持 VOLUME_PREFIX='路径' 格式
   前面的注释行（以 // 开头）可以保留或删除，不影响功能

2. 以下系统路径会自动保持原样，不会被替换：
   /var/run/docker.sock  （Docker 引擎通信）
   
   /etc/localtime        （时区同步）
   
   /etc/timezone
     
   /etc/hosts

   /etc/resolv.conf
       
   /proc /sys /dev        （系统设备/内核）
   
   等其他系统级挂载路径

4. 网页上的"自定义卷映射绝对路径"输入框可以临时修改路径
   但刷新页面后会恢复为 volumes.js 中设置的值

5. 修改 volumes.js 后如果刷新无效
   请按 Ctrl+Shift+R（Windows）或 Cmd+Shift+R（Mac）强制刷新
