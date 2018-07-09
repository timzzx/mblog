# 博客timx.cn备忘录
#### 1.hugo运行  hugo server
#### 2.hugo设置模板环境变量  HUGO_THEME=maupassant-hugo
#### 3.hugo生成html hugo --baseUrl="https://timx.cn/"
#### 4.hugo生成新文章 hugo new post/new.md
#### 5.hugo生成的网站在public下 cd public
#### 6.hugo提交网站 git add -A | git commit -m "add article" | git push

```
hugo server

HUGO_THEME=maupassant-hugo

hugo --baseUrl="https://timx.cn/"

hugo new post/new.md

git add -A

git commit -m "add article"

git push origin master

$ cd public
$ git init
$ git remote add origin https://github.com/coderzh/coderzh.github.io.git
$ git add -A
$ git commit -m "first commit"
$ git push -u origin master
```
