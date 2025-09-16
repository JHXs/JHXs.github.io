---
title: 【AUR】打包小插件 krunner-pinyin-search
date: 2025-09-16
tags:
  - Linux
  - 搞基记录
  - Arch
  - AUR
lastmod:
  - 2025-09-16T20:29:51+08:00
slug: 20:04
author: hansel
share: true
dir: posts
---
# make源代码类型—krunner-pinyin-search

> [https://github.com/AOSC-Dev/krunner-pinyin-search](https://github.com/AOSC-Dev/krunner-pinyin-search) 为例

这是我第一次打包 AUR 软件包，一路都是问AI实现的。哈哈哈哈!
## 编辑 PKGBUILD 文件

```Shell
# Maintainer: Your Name <your.email@example.com>
pkgname=krunner-pinyin-search
pkgver=r4.47f1440
pkgrel=1
pkgdesc="一个krunner插件，为KDE提供了用拼音搜索应用程序的功能。支持全拼/首字母/汉字混合输入。"
arch=(x86_64)
url="https://github.com/AOSC-Dev/krunner-pinyin-search"
license=(GPL3)
depends=(qt6-base kde-cli-tools)
makedepends=(cmake extra-cmake-modules qt6-tools)
source=("git+https://github.com/AOSC-Dev/krunner-pinyin-search.git")
sha256sums=('SKIP')

pkgver() {
  cd "$pkgname"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
  cd "$pkgname"
  cmake -B build -S . \
    -DCMAKE_BUILD_TYPE=Release \
    -DKDE_INSTALL_USE_QT_SYS_PATHS=ON \
    -DCMAKE_INSTALL_PREFIX=/usr
  cmake --build build
}

package() {
  cd "$pkgname"
  cmake --install build --prefix="$pkgdir/usr"
}

post_install() {
  echo ">>> Please restart KRunner for the plugin to take effect."
  echo "    You can do this with: kquitapp6 krunner"
}

post_upgrade() {
  post_install
}

pre_remove() {
  echo ">>> Tip: After removing this plugin, you may need to restart KRunner."
  echo "    You can do this with: kquitapp6 krunner"
}
```

本地构建测试

- 构建安装包

```shell
makepkg -s
```

- 生成如下目录结构

    ```Shell
    ~/apps/aur/krunner-pinyin-search
    ❯ tree -L 2
    .
    ├── krunner-pinyin-search
    │   ├── config
    │   ├── description
    │   ├── FETCH_HEAD
    │   ├── HEAD
    │   ├── hooks
    │   ├── info
    │   ├── objects
    │   ├── packed-refs
    │   └── refs
    ├── krunner-pinyin-search-1.0-1-x86_64.pkg.tar.zst
    ├── pkg
    │   └── krunner-pinyin-search
    ├── PKGBUILD
    └── src
        └── krunner-pinyin-search
    ```
    
    - `krunner-pinyin-search` 是一个 **git 裸仓库（bare repository）,**它包含了所有 git 的元数据和历史记录（例如 `.git/objects`、`.git/refs` 等），但没有实际可用的源代码文件，因此被称为“裸仓库”。
    - `$pkgname` 对应的是 **源代码目录**：`~/apps/aur/krunner-pinyin-search/src/krunner-pinyin-search`
    - `$pkgdir` 对应的是 **临时打包目录**：`~/apps/aur/krunner-pinyin-search/pkg/krunner-pinyin-search` 。
    - 在 `makepkg` 运行期间，系统上的 `/usr` 等关键目录**不会受到任何影响**，所有安装操作都是在 `$pkgdir` 这个沙盒环境中进行的。最终，`makepkg` 会将 `$pkgdir` 目录下的所有内容打包成 `krunner-pinyin-search-1.0-1-x86_64.pkg.tar.zst` 文件。
- 使用 `namcap` 检查
    
    ```Shell
    sudo pacman -S namcap
    ```
    
    ```Shell
    namcap PKGBUILD
    namcap <pkgname>-<pkgver>-<pkgrel>-x86_64.pkg.tar.zst
    ```

- 安装软件包

```shell
sudo pacman -U krunner-pinyin-search-*.pkg.tar.zst
```

## 上传 AUR

### 配置密钥连接

- **生成 AUR 专用 ED25519 密钥**

```Shell
ssh-keygen -t ed25519 -f ~/.ssh/aur -C "aur-$(whoami)"
# 一路回车，不要设密码
```

- **把新生成的公钥贴到 AUR**

```Shell
cat ~/.ssh/aur.pub
```

复制输出 → 登录 [https://aur.archlinux.org](https://aur.archlinux.org/) → **My Account** → **Edit Profile** → **SSH Public Key** → 粘贴 → **Save**。

- **告诉 SSH 连 AUR 时专用这把钥匙**

在 `~/.ssh/config` 里加一段（没有就新建）：

```Shell
Host aur.archlinux.org
    User aur
    IdentityFile ~/.ssh/aur
    IdentitiesOnly yes
```

保存后给权限：

```Shell
chmod 600 ~/.ssh/config
```

### 上传AUR软件包

- **克隆空仓库（即“创建”包）**

```Shell
# 先退到你想放仓库的目录，例如
cd ~/aur          # 随意位置
git clone ssh://aur@aur.archlinux.org/krunner-pinyin-search.git
cd krunner-pinyin-search
```

你会看到提示 _empty repository_，这表示 AUR 已在后台为你建好空仓库。

然后把现成的 `PKGBUILD` 和 `.SRCINFO` 复制进来：

```Shell
cp ~/apps/aur/krunner-pinyin-search/{PKGBUILD,.SRCINFO} .
git add PKGBUILD .SRCINFO
git commit -m "Initial commit"
git push origin master
```

推送成功后，页面

[https://aur.archlinux.org/packages/krunner-pinyin-search](https://aur.archlinux.org/packages/krunner-pinyin-search)

就会立即出现，包正式上架 AUR！

### 日常更新流程模板

- 🔄 何时必须更新
    
|场景|操作|
|---|---|
|上游打了新 tag / 新 commit|升 `pkgver` 并重新生成 `pkgrel=1`|
|构建失败、缺依赖|改 `depends` / `makedepends` 并 `pkgrel++`|
|仅调整 PKGBUILD 注释、安装提示|直接 `pkgrel++` 即可|

> [!tip] 
> - `pkgrel = 1` 是 **“package release”** 的缩写，直译叫“包发行号”。  
>  - 它的作用：**在同一次上游版本（pkgver）内，标记 PKGBUILD 自身被修订的次数**。
>  - **“源码动 → pkgver 升，pkgrel 归 1；脚本动 → pkgrel 加 1，pkgver 不动。”**
       
- **🛠️ 最小更新流程（以 git 版本为例）**
    
    - 进本地仓库
    
    ```Shell
    cd ~/apps/aur/krunner-pinyin-search   # 你放 PKGBUILD 的目录
    ```
    
    - 拉取最新源码并生成新版本号
    
    ```Shell
    rm -rf src pkg                     # 可选，清旧构建缓存
    makepkg -o                         # 只下载，不构建
    pkgver=$(git -C src/<dirname> rev-list --count HEAD).$(git -C src/<dirname> rev-parse --short HEAD)
    sed -i "s/^pkgver=.*/pkgver=$pkgver/" PKGBUILD
    pkgrel=1                           # 如果是新 commit，重置为 1
    ```
    
    - 验证 & 打包
    
    ```Shell
    makepkg -f                         # 本地完整构建
    namcap PKGBUILD *.pkg.tar.zst      # 无 Error 再往下
    ```
    
    - 重新生成 `.SRCINFO`
    
    ```Shell
    makepkg --printsrcinfo > .SRCINFO
    ```
    
    - 提交 & 推送
    
    ```Shell
    git add PKGBUILD .SRCINFO
    git commit -m "update to $pkgver"
    git push
    ```