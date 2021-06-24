---
title: electron学习:实现一个文件分类器
image: /images/electron.jpg
date: 2021-03-29 21:45:48
tags: [electron,javascript]
categories: 
- 前端
---

## 项目地址

- [文件分类器](https://github.com/FanLu1994/file_arranger)

## :sunny:动机

因为喜欢看电子书,之前都用多看,但是没有花钱买过多看里面的书,会找很多epub的电子书资源.  (现在用微信读书了,蛮香的,都是正版,而且基本不用花钱)

电子书资源网站现在死的好多,但还是有很多百度网盘的资源. 整理的都很好, 大部分都包含了多种格式的电子书,其中就包括了epub.

曾经一次性下载了很多百度网盘的电子书资源,epub文件都在各自的文件夹内,要传到多看上的话就要一次次的打开所有文件夹.  很烦,当时写了一个小脚本,如下:

```python python提取epub 
import os
import shutil

dir_path = "D:\BaiduNetdiskDownload"
epub_path = "D:\BaiduNetdiskDownload\epub"

for path,dir_list,file_list in os.walk(dir_path):
    for file_name in file_list:
        if file_name.endswith(".epub"):
            print(os.path.join(path, file_name))
            old_file_path = os.path.join(path, file_name)
            new_file_path = os.path.join(epub_path,file_name)
            shutil.move(old_file_path,new_file_path)
```

后来在公司的项目中接触到了vue写前端,也知道了electron这个东西,就决定学习一下,把这个可视化的文件分类器作为一个学习的小项目.  

功能很简陋,实现也不完美,作为一个入门的小程序,勉勉强强.

## 设计思路

​	因为是一个非常简单的项目,设计思路也很简单.

1. 首页用一个输入框点击可以像选择想要处理的文件夹
2. 将文件夹存储到vuex中
3. 选择文件夹后,出现处理按钮,点击跳转到文件列表页面
4. 利用vuex中的文件夹,遍历获取文件夹下面所有文件,构造一个以文件类型为key的map, 作为文件map,也存储到vuex中
5. 文件列表页面分类展示所有文件
6. 每一类文件都有复制和移动功能
7. 复制/移动允许用户选择一个文件夹,在该文件夹下面新建一个文件,以文件类型为文件名, 根据按钮,将相同类型文件复制或者移动到新文件夹
8. 结束

## 框架选择

因为在公司的项目中使用的是vue+elementui来写前端。 因此想要找一个同样支持vue的electron现有框架来写。 在github上找到了https://github.com/SimulatedGREG/electron-vue，感觉很好用。

在UI库方面，用过element-ui，已经是很好用的一个库了。 但是想试试别的，一开始选择了bulma，后来在开发过程中，觉得这个不够“傻瓜式”，于是又引入了iview。

最终选择：

1. [electron-vue](https://github.com/SimulatedGREG/electron-vue)
2. [bulma](https://bulma.zcopy.site/documentation/overview/)
3. [iviewui](https://www.iviewui.com/docs/introduce)

## :loudspeaker:具体实现

### 1 首页实现

​	首页需求很简单，只要一个输入框，用来选择文件夹，然后将文件夹存储到vuex, 并根据文件夹,获取所有文件列表,存储到vuex

​	实现界面如下：

![](/images/首页.png)

### 2 文件夹选择

- remote.dialog.showOpenDialog是electon提供的对话框函数

```js 选择文件夹
//打开文件夹    
openDir(){
    const result = remote.dialog.showOpenDialog({
        properties: ['openFile',"openDirectory"],
    });

    this.dir = result
    this.setPath(result)

},
        
    //将文件夹路径设置为全局状态
    setPath(path){
        console.log(path[0])
        this.$store.dispatch('ChangePath',path[0])
        let ret = this.getAllFile(path[0])  // 获取所有文件
        this.$store.dispatch('saveList',this.filesArray) //存储到vuex
    },
```

### 3 文件目录扫描

​	使用nodejs的fs模块和path模块

- fs.stat()  给定路径,获取文件信息
- path.join()  路径和文件名生成完整路径

```js 文件目录保存
 // 获取文件夹下所有文件
getAllFile(targetPath){
    let that  = this;
    fs.readdir(targetPath,function (err,files) {
        if(err){
            console.error(err)
        }else{
            files.forEach(function (filename) {    // 遍历当前path下所有文件
                let filePath = path.join(targetPath,filename)
                fs.stat(filePath,function (err, stats){  // 判断文件是否是文件夹
                    if(err){
                        console.error(err)
                    }else{
                        let isFile = stats.isFile();
                        let isDir = stats.isDirectory();
                        if(isFile){
                            // console.log(filename)
                            that.filesArray.push(filePath)   //递归处理文件夹
                        }else{
                            that.getAllFile(filePath)
                        }
                    }
                })
            })
        }
    })
    return true;
},
```

### 4 文件字典生成

将vuex中存储的文件列表生成一个文件字典

```js 文件字典
/**
 * 文件分类函数
 */
import store from "../store";
const path = require("path");

export default function Classifier() {
    // 文件分类存储字典
    let fileMap = {}

    store.getters.files.forEach(function (item){
        // console.log(item)
        // TODO 实现文件分类

        // 解析文件路径
        let fileInfo = path.parse(item)
        if (!fileMap.hasOwnProperty(fileInfo.ext)){
            fileMap[fileInfo.ext] = []
        }
        fileMap[fileInfo.ext].push(item)
    })

    store.dispatch('saveMap',fileMap)

    // Object.keys(fileMap).forEach(function (item){
    //     console.log(item)
    //     console.log(fileMap[item])
    // })
}

```

### 5 文件列表页面实现

> 主要需求:  文件分类展示;  文件复制\移动分类

- 文件列表使用iview的 List实现
- 文件类型目录使用iview的Anchor实现

- 使用第4步生成的文件map,v-for来遍历展示
- 每类文件都有一个移动按钮和复制按钮

![](/images/image-20210407222908712.png)

### 6 同类型文件复制/移动

1. 首先选择一个文件夹

2. 根据参数确定处理的文件类型和 处理类型

3. fs.access创建在选择的文件夹下面新建目标路径

   ```js  选择文件夹
   // 打开文件夹移动或复制  opt 1为移动 2为复制
       openDir(type,opt){
         console.log(type)
   
         // 选择文件夹
         const result = remote.dialog.showOpenDialog({
           properties: ['openFile',"openDirectory"],
         });
   
         if(result===undefined){
           return
         }
   
         // 新建文件夹
         console.log(result[0])
         this.targetPath = path.join(result[0],"类型"+type)
         console.log(this.targetPath)
   
   
         let that = this
         fs.access(that.targetPath,function (err){
           if(err){//如果文件不存在，就创建这个文件
             fs.mkdir(that.targetPath,function (err) {
               console.log("创建文件夹成功")
             });
           }else{
             //如果这个文件已经存在，就进入下一个循环
             console.log("文件夹已经存在")
           }
   
           that.moveFile(that.targetPath,type,opt)
         })
       },
   ```

   

4. 遍历复制或者移动每个文件

   ```js 复制文件
   /**
        * 移动或复制文件到新文件夹
        * @param targetPath  目标路径
        * @param type  文件类型
        * @param opt   操作类型 1为移动,2位复制
        */
       moveFile(targetPath,type,opt){
         let that = this
         this.fileMap[type].forEach(item=>{
           let newPath = path.join(targetPath,that.getFileName(item))
           console.log(newPath)
           // 检查文件是否存在
           if(!fs.existsSync(newPath)){
             // 执行复制操作
             fs.writeFileSync(newPath, fs.readFileSync(item));
             // 如果选择的是移动,则把源文件删除
             if(opt===1){
               fs.unlinkSync(item);
               that.successNotify("移动文件成功!")
             }else{
               that.successNotify("复制文件成功!")
             }
   
           }else{
             that.failNotify("文件已经存在了")
           }
         })
   
       },
   ```

## 学到了什么

> - electron-vue的基础用法
>
> - nodejs对于文件处理的几个函数
>
> - iview组件的使用

## 哪里不足

> - electron只用到了初级的用法, 渲染进程和主进程通信都没有用到;
>
> - 功能只能达到勉强能用的地步, 主要原因在于自己的懒惰和规划的垃圾
> - 只用UI组件,很多地方还是达不到自己想要的形式  没有深入学习