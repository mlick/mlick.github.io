# mlick.github.io
**利用的是[Hexo](https://github.com/hexojs/hexo)这个框架，主题使用的是[nexT](http://theme-next.iissnan.com/)**
## hexo.io

### 安装

```shell
npm install hexo-cli -g
```

详细的文档，请参考操作手册

[操作手册](https://hexo.io/zh-cn/docs/)


## 编译
从github上克隆到本地
```
git clone https://github.com/mlick/mlick.github.io.git
```
切换至source分支 
```
git checkout source
```
安装所需要的库 
```
npm install
```
## 运行
    hexo server
## 结果
    http://localhost:4000/
## 更新next主题库
```
cd themes/next
git init
git remote add origin https://github.com/iissnan/hexo-theme-next
git pull
```

## next 使用文档
https://theme-next.js.org/docs/getting-started/

## Generate new SSH key
https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent)"
ssh-add ~/.ssh/id_ed25519

```

## Add a new SSH key
https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account
```shell
clip < ~/.ssh/id_ed25519.pub
```
github->profile photo->settings->add ssh and GPG keys