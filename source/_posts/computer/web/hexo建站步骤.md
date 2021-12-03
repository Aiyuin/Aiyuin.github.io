---
title: hexo建站步骤 
date: 2021-12-03 12:00:00
updated: 2020-12-03 24:00:00
email: 956954649@qq.com
tags:
    - 学习
    - 生活
categories:
    - 艾小艾的建站历程
---

1. 安装node.js
   
   [Node.js](https://nodejs.org/zh-cn/)

2. 切换镜像源
   
   ```shell
   npm config set registry https://registry.npm.taobao.org
   ```
   
   > [yarn](https://www.yarnpkg.com/zh-Hans/) 是 Facebook 出品的另一个包管理工具，同样可以管理 npm 中的包，安装已缓存的包时速度更快，你也可以使用它来替代 npm。
   
   > [npm & yarn 常用包与命令](https://www.yuque.com/yunyoujun/notes/npm-and-yarn#nrm)
   
   ```shell
   npm install -g yarn
   ```

3. 必备软件
   
   > [Git](https://git-scm.com/)
   > 
   >  [VS Code](https://code.visualstudio.com/)
   
   可以参考Git命令：
   
   > [猴子都能懂的GIT入门 | 贝格乐（Backlog） (navisec.it)](http://git.wiki.navisec.it/)

4. 安装hexo
   
   [Hexo](https://hexo.io/)
   
   - GitHub: [GitHub - hexojs/hexo: A fast, simple &amp; powerful blog framework, powered by Node.js.](https://github.com/hexojs/hexo)
   - [官方文档](https://hexo.io/zh-cn/docs/index.html)（直接参考文档也是一个不错的选择）
   
   > **为嘛使用 Hexo ？**  
   > Hexo 是一个快速、简洁而强大的博客框架，基于 Node.js，同样托管于 GitHub 之上。生态中拥有众多插件主题。你可以基于它快速生成一些静态页面。  
   > 你可以使用别人的各种主题与插件，也可以自己定制开发想要的功能。  
   > **为什么不是…?**  
   > 其他常用的博客框架还有 [WordPress](https://wordpress.org/)，[Typecho](https://typecho.org/)，[Ghost](https://ghost.org/) 等，但这些往往都需要购置自己的服务器，而无法静态化地部署到 GitHub Pages 上。（当然，相应的功能和灵活性也大大提升。）静态化站点还有一个优势就是访问速度往往更快。  
   > 静态网站生成器还有 [Vuepress](https://vuepress.vuejs.org/)，[Gatsby](https://www.gatsbyjs.org/) 等。但这些多是为了写文档而量身定制的，你也可以使用它们，但是相较 Hexo 的博客定位，它们关于博客的插件和主题以及解决办法会少得多。  
   > Hexo 提供的功能与 [Hugo](https://gohugo.io/) 几乎都有，（生成大量文件时，甚至比 Hexo 更快）不过它是基于 GO 语言。日后你想对自己的网站进行自定义，即便是 Hugo，你编写前端的交互仍旧需要使用 JavaScript，所以选择基于 JavaScript 的 Hexo 可以降低学习成本。（你若对 GO 有兴趣，仍然可以尝试使用 Hugo，但本教程将不会针对 Hugo 进行展开。）  
   > 所以对于新手来说，使用 Hexo 作为起始点，不失为一个好选择。（当然如果你有钱租服务器，并希望快速上手的话，就可以考虑考虑 WordPress 或者 Typecho）  
   > 这里顺带推荐一下小伙伴 [染川瞳](https://asuhe.jp/) 的 WordPress 主题 [Sakurairo](https://github.com/mirai-mamori/Sakurairo)，[阔落](https://guhub.cn/) 的 Typecho 主题 [miracles](https://github.com/BigCoke233/miracles)，都是很漂亮的主题。
   
   在终端中输入以下命令：
   
   ```shell
   npm install hexo-cli -g
   # 如果安装失败，可能是没有权限，可以尝试头部加上 sudo 重新执行
   # sudo npm install hexo-cli -g
   ```
   
   > `install` 自然是安装。  
   > `hexo-cli` 则是 `hexo` 的终端工具，可以帮助你生成一些模版文件，之后再用到。  
   > `-g` 代表的是全局安装。也就是在任何地方都可以使用，否则会只能在安装的目录下使用。
   
   此时，请先通过 `cd` 进入你本地电脑打算存储网站代码的文件夹目录。（或者右键文件夹 Git Bash Here）
   
   > [cd | DOS 命令](https://baike.baidu.com/item/cd/3516393)  
   > [cd （LINUXSHELL 命令）](https://baike.baidu.com/item/cd/3516411)
   
   譬如：
   
   > 注意：这里是你自定义的目录，请不要复制粘贴
   
   ```shell
   # '#' 字符后的文字代表注释，不需要输入
   # Windows
   cd C:\Users\YunYou\Documents\GitHub\
   # macOS
   # cd /Users/yunyou/github/
   ```
   
   接下来输入：
   
   ```shell
   hexo init 你的名字.github.io
   ```
   
   > `hexo` 正是因为我们之前安装了 `hexo-cli` 这一个包，所以我们可以在终端中使用 `hexo` 这一命令。  
   > `init` 初始化博客的模版文件。后面跟的是你要新建的文件夹，最好和你此前新建的仓库名一致。
   
   ```shell
   # 进入你的博客文件夹
   cd 你的名字.github.io
   # 默认安装所有 `package.json` 文件中提到的包
   npm install
   # 你也可以缩写成 hexo s
   hexo server
   ```
   
   `server` 代表开启本地的 Hexo 服务器，这时你就可以打开浏览器，在地址栏中输入 `localhost:4000` 就可以看到本地的网页了。
   
   按 `Ctrl + C` 中断服务器的运行。
   
   至此，基础的模版页面便已经搭建好了。
   
   #### 使用 Hexo 主题
   
   Hexo 默认提供的是 [hexo-theme-landscape](https://github.com/hexojs/hexo-theme-landscape) 主题。  
   默认主题样式简单，功能较少。所以大多数人并不会使用默认主题。

5. 利用yun主题做我的个人博客站点
   
   参考文档：
   
   https://www.yunyoujun.cn/share/how-to-build-your-site/
   
   - [hexo-theme-yun - GitHub](https://github.com/YunYouJun/hexo-theme-yun)
   - [hexo-theme-yun 使用文档](https://yun.yunyoujun.cn/)：更详细的配置进阶指南。
   - [示例效果](https://www.yunyoujun.cn/)
   
   ##### 下载 Hexo 主题
   
   进入终端（确保路径处于你此前使用 Hexo 初始化好的文件夹目录下，即 `xxx.github.io`，后简称为 `Hexo 目录`），输入以下命令。
   
   > 实际上你也可以直接在 VS Code 中使用终端。
   
   ```shell
   git clone https://github.com/YunYouJun/hexo-theme-yun themes/yun
   ```
   
   > 这里便使用到了我们此前安装的 Git，`git clone` 即代表克隆（也就是复制的作用）我的主题（托管于 GitHub，链接便是主题所在的地址），`themes/yun` 则代表放在你 Hexo 文件夹下的 `themes/yun` 文件夹里（没有该文件夹会自动新建）。
   
   编辑 Hexo 配置
   
   > 右键文件夹使用 VS Code 打开，或者进入 VS Code 打开你存储网站的文件夹。此后操作都将默认你已处于该工作目录下。
   
   在你此前通过 Hexo 初始化生成的文件目录下，会存在一个 `_config.yml` 文件。
   
   > `yml` 是 [YAML](https://baike.baidu.com/item/YAML/1067697) 文件的后缀名，YAML 是 “YAML Ain’t a Markup Language”（YAML 不是一种标记语言） 的缩写，但它实际上还是一种标记语言。你可以将其理解为存储数据的一种文本格式，这也是其诞生的目的。 如果你听说过 JSON，那你就更能明白它是干什么的了。
   
   它是 Hexo 的配置文件，关于各配置选项的意义你可以查看 [配置 | Hexo](https://hexo.io/zh-cn/docs/configuration)。
   
   在 `_config.yml` 中找到 `theme` 这个字段，将其后的 `landscape` 修改为 `yun`。
   
   ```yaml
   theme: yun
   ```
   
   > pug 是一种模板引擎，可以渲染为 HTML 字符串。类似的还有 ejs，swig 等，语法和设计理念有所不同。  
   > stylus 是一种 CSS 预处理器，可以渲染为 CSS。类似的还有 scss，less，同样只是语法和设计理念有所差异。
   
   由于我的主题使用了 pug 和 stylus，而 Hexo 自带的一般是 ejs 与 stylus，所以你可能还需要输入以下命令安装渲染器。
   
   ```shell
   npm install hexo-render-pug hexo-renderer-stylus
   # 如果出问题，可以换 yarn 安装试试。
   ```
   
   这时再像此前那般使用 `hexo server` 重新启动服务器，你就可以看到一个不一样的主题风格的页面了。
   
   ### 生成静态文件
   
   至今我们的工作都是在本地进行，想必你也很想放到线上与小伙伴们分享。  
   这便轮到了 GitHub Pages 的出场，不过 GitHub Pages 只支持纯静态文件。
   
   所以我们需要使用以下命令先来生成站点的静态文件。
   
   ```shell
   # 如果进行多次生成，为了避免受错误缓存影响，最好使用 hexo clean 先清除一遍。
   hexo generate
   # 缩写为 hexo g
   ```
   
   此时你的文件夹目录下会出现 `public` 这个文件夹，里面存放的就是你站点的静态文件。
   
   ### 与远程仓库建立关联
   
   接下来我们将本地的仓库与此前在 GitHub 上建立的仓库建立关联。
   
   ```shell
   git init # 初始化 Git 仓库，只需要执行一次即可
   ```
   
   在将其部署到 GitHub Pages 上之前，我们最好先建立一个分支。
   
   > 什么是分支？  
   > Git 提供了版本管理功能，其中还有一个分支功能，你现在可以简单地将其理解为平行世界。
   
   `你的名字.github.io` 部署后，GitHub Pages 将默认使用你的 master 分支作为静态文件部署。  
   所以我们最好新建一个 hexo 分支（命名无所谓）用来存储 Hexo 地源代码，master 分支则用来存储部署后的静态文件。
   
   ```shell
   git checkout -b hexo
   ```
   
   这时便成功建立了一个 hexo 分支。（此后的工作都将在 hexo 分支下进行）
   
   你可以通过 `git branch -v` 来查看当前有哪些分支，使用 `git checkout 分支名` 来切换到对应的分支。
   
   > [Git 学习笔记](https://www.yunyoujun.cn/note/git-learn-note/)
   
   ### 部署
   
   为了更方便的部署到 GitHub Pages，Hexo 提供了 `hexo-deployer-git` 插件。
   
   老规矩，安装。
   
   ```shell
   npm install hexo-deployer-git
   ```
   
   在 `_config.yml` 中配置。
   
   ```yaml
   deploy:
     type: git
     repo: 你此前新建的仓库的链接 # 比如：https://github.com/YunYouJun/yunyoujun.github.io
     branch: master # 默认使用 master 分支
     message: Update Hexo Static Content # 你可以自定义此次部署更新的说明
   ```
   
   保存，部署！
   
   > 第一次可能需要你输入用户名与密码。  
   > 密码输入的时候不会出现 ***，不要害怕，已经输入进去了。
   
   ```bash
   hexo deploy
   ```
   
   等待完成后，打开网址 `https://你的名字.github.io` 就能看到你的线上网站了。
   
   > 使用 https，http 可能无法正常打开。HTTPS 是多了安全加密的 HTTP，Chrome 浏览器已经默认会显示 `http` 链接为不安全。  
   > 为了安全，建议开启强制 https 跳转。`项目地址页面 -> Settings -> Options -> GitHub Pages -> Enforce HTTPS`。（翻到下面）  
   > 此时，http 网址会自动重定向到 https
   
   ### 备份与自动部署
   
   我们当前只是将生成的静态文件部署到了云端。
   
   为了以防万一，我们应该将网站的源代码文件也推送到 GitHub 仓库备份。
   
   ```bash
   # 与远程 Git 仓库建立连接，只此一次即可
   git remote add origin https://github.com/你的用户名/你的名字.github.io
   ```
   
   接下来准备提交，这几句命令将是你以后每次备份所需要输入。
   
   ```bash
   # 添加到缓存区
   git add -A
   git commit -m "这次做了什么更改，简单描述下即可"
   # 推送至远程仓库
   git push
   # 第一次提交，你可能需设置一下默认提交分支
   # git push --set-upstream origin hexo
   ```
   
   每次推送都要输入这三条命令，你可能觉得有些麻烦。  
   那么你可以编写 bash 脚本。
   
   譬如，在根目录下新建 `update.sh`。
   
   ```bash
   # 如果没有消息后缀，默认提交信息为 `:pencil: update content`
   info=$1
   if ["$info" = ""];
   then info=":pencil: update content"
   fi
   git add -A
   git commit -m "$info"
   git push origin hexo
   ```
   
   此后更新的话，只需要在终端执行 `sh update.sh` 即可。
   
   更新麻烦，每次部署也很麻烦，可以使用持续集成进行自动部署。
   
   > 什么是持续集成？  
   > 持续集成是一种软件开发实践。对软件进行自动化构建，以此来发现错误。  
   > Travis CI 就是一个线上持续集成服务的提供商。它可以拉取你每次推送到 GitHub 上的代码，然后根据你的要求对其进行构建。  
   > 我们可以趁机让它自动生成网站静态文件，然后自动帮我们部署。  
   > 除此之外，你还可以使用 [GitHub Actions](https://help.github.com/en/actions/getting-started-with-github-actions/about-github-actions) ，[Netlify](https://www.netlify.com/) 等服务。  
   > GitHub Actions 相比 Travis 等，自身便拥有仓库的 Token，不再需要额外设置，可以直接使用 `secrets.GITHUB_TOKEN`。  
   > 推荐一个专门用来部署 gh-pages 的 Actions [actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)
   
   关于更多更具体的自动化部署方案和操作步骤，你可以参考我的小伙伴 ChrAlpha 的 [初探无后端静态博客自动化部署方案](https://blog.ichr.me/post/automated-deployment-of-serverless-static-blog/)。
   
   他在文章中详细介绍了 Netlify 、GitHub Actions 和 Travis CI 的部署方法。
   
   你也可以参考 Hexo 的官方文档 [将 Hexo 部署到 GitHub Pages](https://hexo.io/zh-cn/docs/github-pages)
   
   我就不在此画蛇添足。
   
   ---
   
   当然还有个更简便的方法，直接参考我的 [gh-pages.yml](https://github.com/YunYouJun/yunyoujun.github.io/blob/hexo/.github/workflows/gh-pages.yml)。
   
   去掉夹在 `yarn install` 与 `yarn build` 之间的 `algolia` 部分，直接粘贴到你的 `.github/workflows/` 文件夹（自己新建）下 `xxx.yml` 文件里即可。
   
   推送后便可直接自动部署。
   
   至此，你的站点便基本搭建完成，此后继续对主题进行自定义吧。
   
   > [Yun 主题文档](https://yun.yunyoujun.cn/)
   
   <mark>！注意！</mark>
   
   1. 配置后的gh-pages.yml
      
      ```yaml
      name: GitHub Pages
      on:
        push:
          branches:
            - hexo
      jobs:
        deploy:
          runs-on: ubuntu-latest
          steps:
            - uses: actions/checkout@v2
              with:
                submodules: true
      
            - name: Setup Node
              uses: actions/setup-node@v2
              with:
                node-version: "14.x"
      
            - name: Install Dependencies
              run: yarn
            - run: yarn build
      
            - name: Deploy
              uses: peaceiris/actions-gh-pages@v3
              with:
                github_token: ${{ secrets.GITHUB_TOKEN }}
                publish_dir: ./public
                publish_branch: master
                force_orphan: true
      ```
   
   2. 需要在根目录配置<mark>.gitmodules</mark>文件
      
      > 当站点文件被git上传到github时，themes目录下的主题文件是github下克隆下来的文件，所以不能一并提交上传到github上，所以需要建立该文件，当自动部署时需要themes目录中的主题与github的主题仓库相关联。
      
      将本次配置的主题写入该文件
      
      ```
      [submodule "hexo-theme-yun"]
      path = themes/yun
      url = https://github.com/YunYouJun/hexo-theme-yun
      ```
   
   3. git中屏蔽.bak文件
      
      > 在.gitignore加入忽略文件名
      
      在根目录的.gitignorew文件中尾部加入
      
      ```
      *.bak
      ```
   
   4. [Hexo 博客备份与恢复 | Mupceet](https://mupceet.com/2019/09/backup-hexo-blog/)

6. 
