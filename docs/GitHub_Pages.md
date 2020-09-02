

##  GitHub Pages

GitHub Pages可以存放静态网页代码，可以以此来作为自己的博客。

默认支持markdown格式文件。

官方介绍网站：https://pages.github.com/







## 站点类型

GitHub Pages支持两种用法：个人站点和项目站点。



### 个人站点

每个Github账户有一个，创建一个名为`username.github.io`的新存储库（其中username是您在GitHub上的用户名）。在这个项目中创建的文件，可以通过 https://username.github.io/filename 访问（其中username是您在GitHub上的用户名）。



### 项目站点

公共项目是免费的；私有项目需要收费（2020.09.02）；

在Github的公共项目主页，点击“settings”-“GitHub Pages”可以设置一个目录作为站点主目录，比如“docs”，同时在项目根目录下建一个同名的文件夹，只有这个文件夹及子文件夹里的文件可以被浏览器访问。（私有项目的“GitHub Pages”无设置项）

在这文件夹下创建的文件，可以通过  http://username.github.io/repository/filename 访问（其中username是您在GitHub上的用户名， repository是项目名）。



## 站点使用



### 主题

在Github的项目主页，点击“settings”-“GitHub Pages”可以设置。



### 目录与文件

支持目录：可以创建多层目录树结构。

支持中文。



### 大小写敏感

url中的filename、后面的repository都是大小写敏感的，也就是说/之后的是区分大小写的。（/之前的没有大小写一说）



### 文件与URL的对应

个人站点和项目站点都适用。

index.md文件：默认url，url省略filename时访问这个文件，比如https://username.github.io/repository；

XX.md：markdown文件省略扩展名访问，比如https://username.github.io/repository/XX，加上扩展名访问会显示源文件（无格式的文本文件）；

XX.txt：其他文件类型需要扩展名访问，比如https://username.github.io/repository/XX.txt；











