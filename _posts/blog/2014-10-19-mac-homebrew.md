---
layout:		post
title:		Mac OS X 不可或缺的套件管理器 -- Homebrew
category:	blog
description:	又提示缺少套件啦？别担心，Homebrew 随时守候。

---

Homebrew 会将套件安装到独立目录，并将文件软链接至 /usr/local 。

	$ cd /usr/local
	$ find Cellar
	Cellar/wget/1.15
	Cellar/wget/1.15/bin/wget
	Cellar/wget/1.15/share/man/man1/wget.1

	$ ls -l bin
	bin/wget -> ../Cellar/wget/1.15/bin/wget

Homebrew 的所有文件均会被安装到预定义目录下，所以您无需担心 Homebrew 的安装位置。

轻松创建您的 Homebrew 程式。

以 git、 ruby 为其筋骨，所以借助您的相关知识，自由修改，并且可以简单撤回您的调改或者合并上游更新。

	$ brew edit wget # opens in $EDITOR!

Homebrew 的程式都是简单的 Ruby 脚本：

	require "formula"

	class Wget < Formula
	  homepage "http://www.gnu.org/software/wget/"
	  url "http://ftp.gnu.org/gnu/wget/wget-1.15.tar.gz"
	  sha1 "f3c925f19dfe5ed386daae4f339175c108c50574"

	  def install
	    system "./configure", "--prefix=#{prefix}"
	    system "make", "install"
	  end
	end

Homebrew 使 OS X 更完美。使用 gem 来安装 gems、用 brew 来搞定那些依赖包。

##安装 Homebrew

	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

注意：千万不要在root用户下运行此命令。如提示 'Don't run this as root!' 运行logout退出root用户

##使用 Homebrew 

更新 formulae，保证每次安装的文件源是最新的。
	
	brew update

查看寻找自己需要的软件，例如想寻找jpeg相关的软件。

	brew search jpeg

出来结果有三个
	
	jpeg jpegoptim openjpeg

然后安装自己需要的软件
	
	brew install jpegoptim

Homebrew更新快速，操作简单安装方便，建议大家使用。如果formulae中的源代码下载地址被天朝墙掉，可以直接修改rb脚本中的源代码下载地址。
