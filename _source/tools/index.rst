sphinx 笔记
============

在笔记记录方面，sphinx 可能是比 markdown 更好的选择 —— 更加丰富的格式，适于笔记系统的主题等等。

系统构建
--------------
sphinx 利用 reStructuredText 作为标记语言，并且提供了语法增强、文档之间关系的组织以及与其它文件格式的转换、部署以及各种主题等。  

笔记站点的整体层级关系是以笔记的分类来确定的，基本上是一致的，看起来就像是自己的 ``印象笔记`` 或者 ``oneNote`` 之类的记录的层级关系。用 sphinx 配上特定的主题，站点天然的就是类似于标准的API文档之类的格式。  

著名的 read the docs 网站就是用的 sphinx ，这个网站有很多大家托管的自己的 sphinx 站点。  

这里我将站点托管在 github pages 中的博客下，作为原本博客的一个相对独立的子区。  

工具如下：

*  vim/Sublime 编写 reST（即 reStructuredText 格式） 文件
*  sphinx 套件 + read the docs 的主题
*  sphinx-autobuild 做 reLiveload
*  github pages 托管生成的 html 文件

reStructuredText 语法
----------------------
类似于 markdown 的标记语言


sphinx 
-----------------------
主要说明 sphinx 对 reST 语法的增强



