



##  GitHub Pages

GitHub Pages可以存放静态网页代码，可以以此来作为自己的博客。

支持markdown等多种格式文件。

官方介绍网站：https://pages.github.com/





GitHub Pages支持两种用法：个人站点和项目站点。



## 个人站点

每个Github账户有一个，创建一个名为`username.github.io`的新存储库（其中username是您在GitHub上的用户名）。在这个项目创建的文件，可以通过 https://username.github.io/filename 访问（其中username是您在GitHub上的用户名）。



## 项目站点

在项目根目录下建一个“docs”的文件夹，只有“docs”的文件夹里的文件可以浏览器访问。

在“docs”的文件夹创建的文件，可以通过  http://username.github.io/repository/filename 访问（其中username是您在GitHub上的用户名， repository是项目名）。



## 大小写敏感

url中的filename、后面的repository都是大小写敏感的，也就是说/之后的是区分大小写的。（/之前的没有大小写一说）



## 文件与URL的对应

个人站点和项目站点都适用。

index.md文件：默认url，url省略filename时访问这个文件，比如https://username.github.io/repository；

XX.md：markdown文件省略扩展名访问，比如https://username.github.io/repository/XX，加上扩展名访问会显示源文件（无格式的文本文件）；

XX.txt：其他文件类型需要扩展名访问，比如https://username.github.io/repository/XX.txt；



支持目录：可以创建多层目录树结构。





