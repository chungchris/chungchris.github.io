# Backup

``` bash
# backup to the same repo as github pages but on `backup` branch (specified in _config.yml `backup` section)
hexo backup
```

# Recover

``` bash

# create workspace
mkdir github_pages
cd github_pages
hexo init

# download source backup
git clone git@github.com:chungchris/chungchris.github.io.git ./backup
cd backup
git checkout origin/backup
cp -r ./{_config.yml,scaffolds,source,themes} ../
cd ..

# remove files
rm source/_posts/hello-world.md
rm -rf backup

# install necessary plugins
npm install hexo-deployer-git --save
npm install hexo-wordcount --save
npm install hexo-git-backup --save
npm install hexo-generator-sitemap --save
npm install hexo-filter-github-emojis --save
npm install hexo-generator-feed --save

# try build
hexo g

```
