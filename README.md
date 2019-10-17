# cumulus-blog

### 异地更新指南

#### 环境准备

* 安装node.js

* 克隆博客仓库到本地

  ``````bash
  git clone git@github.com:rainzhop/cumulus-blog.git
  ``````

* 进入仓库目录，安装hexo

  ```bash
  cd cumulus-blog
  npm install hexo --save
  npm install hexo-math --save
  npm install hexo-deployer-git --save
  npm install hexo-generator-sitemap --save
  ```

#### 新建文章

```bash
hexo new <new-post-name>  # 新建新文章

hexo new draft <new-post-name>  # 新建草稿
hexo s --draft  # 预览草稿
hexo p <new-post-name>  # 发布
```

#### 启动本地服务调试

```bash
hexo server
```

#### 远端部署

```bash
hexo clean
hexo generate
hexo deploy
```

