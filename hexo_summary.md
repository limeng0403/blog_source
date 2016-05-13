## 目录结构

.

|--_config.yml	//网站的配置信息

|--package.json	//应用程序的信息

|--scaffolds		//模板文件夹。以此建立文件

|--source		//资源文件夹，存放用户资源

|	|--_drafts

|	|--_posts

|--themes		//主题文件夹。以此生成静态页面



##  _config.yml



| 参数                | 描述                                       | 默认值                       |
| :---------------- | :--------------------------------------- | :------------------------ |
| title             | 网站标题                                     |                           |
| subtitle          | 网站副标题                                    |                           |
| description       | 网站描述                                     |                           |
| author            | 作者                                       |                           |
| language          | 网站使用的语言                                  |                           |
| timezone          | 网站时区。[时区列表](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) |                           |
| url               | 网址                                       |                           |
| root              | 网站根目录                                    |                           |
| permalink         | [永久链接](https://hexo.io/zh-cn/docs/permalinks.html) | :year/:month/:day/:title/ |
| permalink_default | 永久链接中各部分的默认值                             |                           |
| source_dir        | 资源文件夹                                    | source/                   |
| public_dir        | 公共文件夹                                    | public/                   |
| tag_dir           | 标签文件夹                                    | tags/                     |
| archive_dir       | 归档文件夹                                    | archives/                 |
| category_dir      | 分类文件夹                                    | categories/               |
| code_dir          | incluce code文件夹                          | downloads/code/           |
| i18n_dir          | 国际化文件夹                                   | :lang                     |
| skip_render       | 跳过指定文件的渲染                                |                           |
| new_post_name     | 新文章的文件名称                                 | :title.md                 |
| default_layout    | 预设布局                                     | post                      |
| auto_spacing      | 在中文和英文之间加入空格                             | false                     |
| titlecase         | 把标题转换为 title case                        | false                     |
| external_link     | 在新标签中打开链接                                | true                      |
| filename_case     | 把文件名称转换为 (1) 小写或 (2) 大写                  | 0                         |
| render_drafts     | 显示草稿                                     | false                     |
| post_asset_folder | 启动 [Asset 文件夹](https://hexo.io/zh-cn/docs/asset-folders.html) | false                     |
| relative_link     | 把链接改为与根目录的相对位址                           | false                     |
| future            | 显示未来的文章                                  | true                      |
| highlight         | 代码块的设置                                   |                           |
| default_category  | 默认分类                                     | uncategorized             |
| category_map      | 分类别名                                     |                           |
| tag_map           | 标签别名                                     |                           |
| date_format       | 日期格式[Moment.js](http://momentjs.com/)    | MMM D YYYY                |
| titme_format      | 时间格式[Moment.js](http://momentjs.com/)    | H:mm:ss                   |
| per_page          | 每页显示的文章量 (0 = 关闭分页功能)                    | 10                        |
| pagination_dir    | 分页目录                                     | page                      |
| theme             | 当前主题名称。值为`false`时禁用主题                    |                           |
| deploy            | 部署部分的设置                                  |                           |



## 指令

### init

```
$ hexo init [folder]
```

新建一个网站。如果没有设置 `folder` ，Hexo 默认在目前的文件夹建立网站。

### new

```
$ hexo new [layout] <title>

```

新建一篇文章。如果没有设置 `layout` 的话，默认使用 [_config.yml](https://hexo.io/zh-cn/docs/configuration.html) 中的 `default_layout` 参数代替。如果标题包含空格的话，请使用引号括起来。

###  generate

```
$ hexo generate

```

生成静态文件。

| 选项               | 描述          |
| ---------------- | ----------- |
| `-d`, `--deploy` | 文件生成后立即部署网站 |
| `-w`, `--watch`  | 监视文件变动      |

### publish

```
$ hexo publish [layout] <filename>

```

发表草稿。

### clean

```
$ hexo clean

```

清除缓存文件 (`db.json`) 和已生成的静态文件 (`public`)。

