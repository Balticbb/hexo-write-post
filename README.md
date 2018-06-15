# hexo-write-post
使用hexo配合md语法写博客，并通过git推送到gitPage

echo "# hexo-write-post" >> README.md

git init

git add README.md

git commit -m "first commit"

git remote add origin git@github.com:Balticbb/hexo-write-post.git

git push -u origin master


# hexo 常用操作命令
        
        cnpm install 安装相应的包
        hexo new post "name" 创建一篇名为name 的博客
        hexo clean
        hexo s -debug 本地部署
        hexo d -g 生成相关文件并部署到git
---

博客开头加上 
        
        ---
        title: 我的第一篇hexo博客
        ---

博客地址
        
        