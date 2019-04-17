# Catcheer.github.io

 + 该站点master是部署分支,templeate是写作分支.
 + 当执行hexo deploy时，Hexo会创建或更新另外一个用于部署的分支，这个分支就是_config.yml配置文件中指定的分支(该项目是master).Hexo会将生成的站点文件推送至该分支下，并且完全覆盖该分支下的已有内容
 + hexo deploy并不会对本地或远程的写作分支进行任何操作，因此依旧需要手动推送写作分支的所有改动以实现版本控制.(所以template是手动维护的分支)



```
 hexo new title 
```
新建文章.title是文章名

```
hexo g
```
生产静态文件

```
hexo s
```
允许本地服务 用于测试(预览)

```
hexo d
```

推送到部署分支(master)

