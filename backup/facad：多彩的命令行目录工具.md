https://github.com/yellow-footed-honeyguide/facad

**依赖：**
- python3
- meson：Meson 是一个高级构建系统，旨在提供一种易于使用的方式来定义和配置项目的构建过程。它使用 Python 编写，提供了一种简洁的语法来描述项目结构和构建规则。
- ninja：Ninja 是一个低级别的构建工具，专注于速度。

**安装：**
```
wget https://github.com/ninja-build/ninja/releases/download/v1.12.1/ninja-linux.zip
unzip ninja-linux.zip -d /usr/local/bin
sudo pip3 install meson

git clone https://github.com/yellow-footed-honeyguide/facad.git
cd facad
mkdir build && cd build
meson setup .. && ninja
sudo ninja install
```

**使用：**
统计文件夹信息：
```bash
$ facad -a
/home/test/backend
🧮 Total Size    : 522.1M
🗂️ Directories   : 26
🌳 Max Depth     : 7 levels
📁 Deepest Dir   : ./.git/modules/webproto/logs/refs/remotes/origin
🗃️ Files         : 14
🐘 Largest File  : ./build/apps/wms/wms [35.9M]
🏺 Oldest File   : VERSION [2024-07-19 15:07:30]
🆕 Newest File   : go.sum [2024-12-30 10:51:50]
📭 Empty Files   : 0 []
```

列出目录：
```bash
$ facad
/home/test/energy-backend  [main]
📁 .circleci  📁 ewms           📁 scripts         📝 VERSION
📁 .git       📁 kbs            📁 third-party     📑 README.docker.md
📁 .githooks  📁 label-archive  📁 toolsql         📑 README.md
📁 .github    📁 mts            📁 webproto[M]     🐹 go.mod
📁 ars        📁 mts.bridge     📁 wms             💻 build.docker.sh
📁 build      📁 mts2           📁 wms.bridge      💰 build.sh
📁 cap        📁 mwms           🙈 .gitignore      💻 circle-release.sh
📁 docker     📁 oms            🐙 .gitmodules     💻 init.sh
📁 eap        📁 RELEASENOTE    🦊 .gitlab-ci.yml  💰 package.sh
📁 egs        📁 rfcctl         📝 SYSTEM          📝 go.sum

$ facad -l
   13.7M  4m 12d ago  👑: test 📜: 👀✏️🚀  📁 ./cap            (27)
    4.8M  14d ago     👑: test 📜: 👀✏️🚀  📁 ./mts2           (21)
   11.0M  15d ago     👑: test 📜: 👀✏️🚀  📁 ./oms            (18)
    2.7M  15d ago     👑: test 📜: 👀✏️🚀  📁 ./mwms           (17)
   23.3M  14d ago     👑: test 📜: 👀✏️🚀  📁 ./wms            (16)
    2.9M  5m 28d ago  👑: test 📜: 👀✏️🚀  📁 ./mts            (16)
   32.8K  5m 28d ago  👑: test 📜: 👀✏️🚀  📁 ./egs            (14)
  312.0K  15d ago     👑: test 📜: 👀✏️🚀  📁 ./eap            (12)
   51.6K  5m 28d ago  👑: test 📜: 👀✏️🚀  📁 ./kbs            (12)
    5.7M  5m 28d ago  👑: test 📜: 👀✏️🚀  📁 ./webproto       (11)
  527.5K  5m 28d ago  👑: test 📜: 👀✏️🚀  📁 ./ars            (11)
...
```