这是我博客的源文件，使用hexo实现的博客，感兴趣的欢迎fork,共同完善。

# 使用方法

基于Linux

## Clone
~~~
git clone https://github.com/czlitony/czlitony.github.io.source.git
cd czlitony.github.io.source
git clone https://github.com/czlitony/hexo-theme-next themes/next
~~~

## 安装依赖包
~~~
npm install
~~~

> 可以复制下面的代码段，省去多次复制的麻烦
~~~
git clone https://github.com/czlitony/czlitony.github.io.source.git;
cd czlitony.github.io.source; 
git clone https://github.com/czlitony/hexo-theme-next themes/next;
npm install
~~~

**注意:** 当修改了next主题文件之后，要同步到github上面。具体操作为：
~~~
cd theme/next
git add .
git commit -m "modify the theme"
git push origin master
~~~

## 使用

