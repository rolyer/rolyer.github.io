dbyll
=====

Open source stylish, minimalistic theme for jekyll.  
Demo: http://dbtek.github.io/dbyll/

## New
Dbyll for Ghost is now available. Get it [here](https://github.com/dbtek/dbyll-ghost) now.

### Features
- Responsive layout.
- Supports tags and categories.
- Social profile and bio of author.
- Bootstrap based.
- Glyphicon and Font-Awesome Icons.
- Pagination.
- Syntax highlighting with pygments.
- Disqus comments.


### EVN
# 安装版本管理器
brew install rbenv ruby-build
rbenv install 3.1.4
rbenv global 3.1.4

echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(rbenv init -)"' >> ~/.zshrc
source ~/.zshrc

### Build
```shell


# source 'https://rubygems.org'
# source 'https://gems.ruby-china.com'

# timeout 120 bundle install || bundle install --retry 3
bundle install

# 本地构建
bundle exec jekyll build --verbose
# 本地构建 跟踪
bundle exec jekyll build --trace
# 本地预览
bundle exec jekyll serve
```

### Download
* [Download dbyll](https://github.com/dbtek/dbyll/archive/master.zip)

### Screenshots

![dbyll-screenshot](	assets/media/dbyll-ss.png)

### License
- [MIT](http://opensource.org/licenses/MIT)


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/dbtek/dbyll/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

