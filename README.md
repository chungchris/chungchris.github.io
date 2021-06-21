# chungchris.github.io

## backup

```
$ hexo backup
# it will backup to repo `chungchris.github.io` but on `backup` branch
```

### if has modified source of theme

```
$ cd theme/hexo-theme-matery
$ git commit -a -m 'my modification'
$ git push # will push to my own fork of theme
```

## Recover

```
# create workspace
$ mkdir github_pages
$ cd github_pages
$ hexo init

# clone backup source
$ git clone git@github.com:chungchris/chungchris.github.io.git ./backup_source
$ cd backup_source/
$ git checkout origin/backup
$ cp -r ./{_config.yml,scaffolds,source} ../

# clone theme from my own fork
$ cd ..
$ git clone git@github.com:chungchris/hexo-theme-matery.git themes/hexo-theme-matery

# remove default files
$ rm source/_posts/hello-world.md

# install necessary plugin
$ npm i --save hexo-wordcount
```
