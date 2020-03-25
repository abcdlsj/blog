+++
title = "ebuild 初探"
date = 2020-01-30T12:00:00+08:00
lastmod = 2020-03-25T20:56:58+08:00
tags = ["linux", "package"]
categories = ["learn"]
draft = false
author = "abcdlsj"
+++

> ebuild是一套软件组建自动化工具程式，用来让软件套件编译与安装流程可以自动化完成，由一组bash脚本程式构成。这套软件的格式由Gentoo Linux专案研发，是软件包管理系统，Portage，的一部分。工具程式BitBake的设计理念，最早来自于ebuild。

<!--more-->


## 基本 {#基本}

[Ebuild Writing](https://devmanual.gentoo.org/ebuild-writing/index.html)

[devmanual index](https://devmanual.gentoo.org/index.html)


## Overlay 建立 {#overlay-建立}

在 `/usr/local/` 目录建立自己的 Overlay

```shell
sudo mkdir -p /usr/local/portage
```

编辑 `/etc/portage/make.conf` 文件添加 `PORTDIR_OVERLAY="/usr/local/portage"`

为 Overlay 添加 `metadata` 子目录

然后放置 `layout.conf`

```nil
mkdir  /usr/local/portage/metadata
echo "masters = gentoo" > /usr/local/portage/metadata/layout.conf
```

还需要在 `/usr/local/portage/` 创建 `profiles` 目录，添加 `repo_name` 文件，写入你的 Overlay 名称

```nil
echo "kfileo" > /usr/local/portage/profiles/repo_name
```


## Overlay 使用 {#overlay-使用}


### ebuild 位置 {#ebuild-位置}

拿 `chez-scheme` 来示例，这是我之前放置的，如果你是网上找的 `ebuild` 位置应该知道，放对就行

```shell
cat /usr/local/portage/dev-scheme/chez-scheme/chez-scheme-9.5.2.ebuild
```

```nil
# Copyright 1999-2016 Gentoo Foundation
# Distributed under the terms of the GNU General Public License v2
# $Id$

EAPI="6"

inherit eutils

DESCRIPTION="Chez Scheme is an implementation of the Revised 6 Report on Scheme (R6RS) with numerous language and programming environment extensions."
HOMEPAGE="https://cisco.github.io/ChezScheme"


NANOPASS_PV="1.9"
NANOPASS_P="nanopass-framework-scheme-${NANOPASS_PV}"
NANOPASS_URI="https://github.com/nanopass/nanopass-framework-scheme/archive/v${NANOPASS_PV}.tar.gz"

ZLIB_PV="1.2.11"
ZLIB_P="zlib-${ZLIB_PV}"
ZLIB_URI="https://github.com/madler/zlib/archive/v${PV}.tar.gz"

ZLIB_PV="1.2.1"
STEX_P="stex-${ZLIB_PV}"
STEX_URI="https://github.com/dybvig/stex/archive/v1.2.1.tar.gz"

SRC_URI="https://github.com/cisco/ChezScheme/archive/v${PV}.tar.gz -> ${PF}.tar.gz
	      ${ZLIB_URI} -> ${ZLIB_P}.tar.gz
	      ${STEX_URI} -> ${STEX_P}.tar.gz
	      ${NANOPASS_URI} -> ${NANOPASS_P}.tar.gz"

LICENSE="Apache-2.0"
SLOT="0"
KEYWORDS="~x86 ~amd64"
IUSE="+threads"

DEPEND="
      sys-libs/ncurses "

RDEPEND="${DEPEND}"

src_configure() {
      local para_thread

      if use threads ; then
	 para_thread="--threads"
      fi

      ./configure \
      ${para_thread} \
      --installprefix=/usr \
      --installbin=/usr/bin \
      --installlib=/usr/lib \
      --installman=/usr/share/man \
      --temproot=${D}
}

src_install() {
      emake install
}

src_unpack() {
      unpack ${A}
      mv ChezScheme-${PV} ${PF}
      mv ${NANOPASS_P}/* ${PF}/nanopass
      mv ${STEX_P}/* ${PF}/stex
      mv ${ZLIB_P}/* ${PF}/zlib

echo 'diff -ur c/Mf-a6le c2/Mf-a6le
--- c/Mf-a6le	2019-03-22 07:05:24.000000000 +0900
+++ c2/Mf-a6le	2019-08-26 07:34:29.914948586 +0900
@@ -16,7 +16,7 @@
 m = a6le
 Cpu = X86_64

-mdclib = -lm -ldl -lncurses -lrt -luuid
+mdclib = -lm -ldl -lncurses -lrt -luuid -ltinfo
 C = ${CC} ${CPPFLAGS} -m64 -msse2 -Wpointer-arith -Wall -Wextra -Werror -Wno-implicit-fallthrough -O2 ${CFLAGS}
 o = o
 mdsrc = i3le.c
diff -ur c/Mf-i3le c2/Mf-i3le
--- c/Mf-i3le	2019-03-22 07:05:24.000000000 +0900
+++ c2/Mf-i3le	2019-08-26 07:34:13.422852525 +0900
@@ -16,7 +16,7 @@
 m = i3le
 Cpu = I386

-mdclib = -lm -ldl -lncurses -lrt -luuid
+mdclib = -lm -ldl -lncurses -lrt -luuid -ltinfo
 C = ${CC} ${CPPFLAGS} -m32 -msse2 -Wpointer-arith -Wall -Wextra -Werror -Wno-implicit-fallthrough -O2 -fno-stack-protector ${CFLAGS}
 o = o
 mdsrc = i3le.c
diff -ur c/Mf-ta6le c2/Mf-ta6le
--- c/Mf-ta6le	2019-03-22 07:05:24.000000000 +0900
+++ c2/Mf-ta6le	2019-08-26 07:32:47.664953137 +0900
@@ -16,7 +16,7 @@
 m = ta6le
 Cpu = X86_64

-mdclib = -lm -ldl -lncurses -lpthread -lrt -luuid
+mdclib = -lm -ldl -lncurses -lpthread -lrt -luuid -ltinfo
 C = ${CC} ${CPPFLAGS} -m64 -msse2 -Wpointer-arith -Wall -Wextra -Werror -Wno-implicit-fallthrough -O2 -D_REENTRANT -pthread ${CFLAGS}
 o = o
 mdsrc = i3le.c
diff -ur c/Mf-ti3le c2/Mf-ti3le
--- c/Mf-ti3le	2019-03-22 07:05:24.000000000 +0900
+++ c2/Mf-ti3le	2019-08-26 07:33:25.493760984 +0900
@@ -16,7 +16,7 @@
 m = ti3le
 Cpu = I386

-mdclib = -lm -ldl -lncurses -lpthread -lrt -luuid
+mdclib = -lm -ldl -lncurses -lpthread -lrt -luuid -ltinfo
 C = ${CC} ${CPPFLAGS} -m32 -msse2 -Wpointer-arith -Wall -Wextra -Werror -Wno-implicit-fallthrough -O2 -D_REENTRANT -pthread ${CFLAGS}
 o = o
 mdsrc = i3le.c'|patch -p0 -d${PF}
}
```


#### 生成签名文件 {#生成签名文件}

```shell
cd /usr/local/portage/dev-scheme/chez-scheme
ebuild ./chez-scheme-9.5.2.ebuild manifest
```

生成 `Manifest` 文件，包括了签名信息

```shell
cat /usr/local/portage/dev-scheme/chez-scheme/Manifest
```

```nil
DIST chez-scheme-9.5.2.tar.gz 30153222 BLAKE2B 3e88cf5d1c633febee7b5c4321e79d3ecc4102ede77975483f8f86d8b28348e699f09d8bceaee014e87541c86c4253201eb952417340378bc2852b72a97c7a28 SHA512 0fa826be21372e101a7d906044ec2798649312c9f7363fd5732dd0cfbe2730526695e40becee423ba06aba5cb8dc2e9c003edbe87cedb37d921022cb84f0d9a2
DIST nanopass-framework-scheme-1.9.tar.gz 3816290 BLAKE2B 2994fa730b8248b292f920eb61ddc2bad3dddd1bc4e27cfbf2edbe0ff33b016e136d2467f0664315b0ebc5429dbd41ce7001854eabc77e1d8da212a6683ff4f5 SHA512 6b11058332b52f6cac08855cd5881ddf7ec12f3c1154ebb2a95e0f44ad4d090f8101f3b68419a65a32a1b6f4ae38f39b204ddfec048bfa30adb4456bae14ab7a
DIST stex-1.2.1.tar.gz 211011 BLAKE2B ad347a28ab6de02d52c8b101e6a042ab128753684b144692404e502338c851ad0f58d8bb6b1cc7a736796d45386093fe834328216e4034508634978d550db3c1 SHA512 29909a05cd813e5b7918d55dc10f1b2de80dec18ae89a1644261decfd79ac8e5468ac3b50f49c70796bde05ddfad5eaacd879dbadc995ce87f6a74c17b947058
DIST zlib-1.2.11.tar.gz 607698 BLAKE2B 6bfc4bca5dcadba8a0d4121a2b3ed0bfe440c261003521862c8e6381f1a6f0a72d3fc037351d30afd7ef321e8e8d2ec817c046ac749f2ca0c97fbdc2f7e840b7 SHA512 73fd3fff4adeccd4894084c15ddac89890cd10ef105dd5e1835e1e9bbb6a49ff229713bd197d203edfa17c2727700fce65a2a235f07568212d820dca88b528ae
EBUILD chez-scheme-9.5.2.ebuild 3215 BLAKE2B 99e324a344d1af0f5464889aac451c38a6d974a256be672eacf164ddf6e9c11220e4387a40ef2dc105ec7f122bf80dcbdfa251c82356b3fe0d72f2727cb9969a SHA512 62e3235a1ea17e96724b3f3bb6be82f014e84a0e660357ab2267ec8d9c60f8c0a024081c9db27417d3c07ac9a4fdb3af67f301e32b3e2ddf01df5e15287592b5
```

然后就可以下载了

```shell
$ sudo emerge -a chez-scheme
密码：

 * IMPORTANT: 10 news items need reading for repository 'gentoo'.
 * Use eselect news read to view new items.

 * Last emerge --sync was 2019年12月23日 星期一 08时35分01秒.

These are the packages that would be merged, in order:

Calculating dependencies... done!
[ebuild   R    ] dev-scheme/chez-scheme-9.5.2

Would you like to merge these packages? [Yes/No]
```
