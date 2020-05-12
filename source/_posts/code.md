categories: 测试
	#!/usr/bin/python3
	def main():
	    print("hello world")
	
	if __name__ == "__mian__":
	    main()



# ceshi

	#!/bin/bash
	echo "ceshi"
 
 cehshi

	#!/usr/bin/python3
        def main():
            print("hello world")

        if __name__ == "__mian__":
            main()


hexo 文章插入图片的方法
<br/>设置站点配置_config.yml:将post_asset_folder: false改为post_asset_folder: true<br/>
安装插件:npm install https://github.com/CodeFalling/hexo-asset-image -- save
<br/>运行hexo n "XXXXXX",生成XXXXX.md博文时就会在/source/_posts目录下生成XXXXXX的文件夹，将你想在XXXXX博文中插入的照片放置到这个同名文件夹中即可，图片的命名随意<br/>
添加图片:在想添加的位置写入\!\[\](图片名字.图片格式),例如\!\[\](1.png)
