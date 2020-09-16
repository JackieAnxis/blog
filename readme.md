# 博客配置

我对小涛的 aircloud 主题进行了魔改，所以里面有很多东西都改变了

1. 增加了`hexo-generator-author`，需要对其 index.js 做一些修改：

   ```javascript
   hexo.extend.filter.register("template_locals", function (locals) {
     if (typeof locals.site.authors === "undefined") {
       const posts = locals.site.posts;
       locals.site.authors = locals.site.posts
         .map((post) => post.author)
         .unique()
         .map((author) => ({
           name: author,
           posts: posts.find({
             author,
           }),
         }));
     }
   });
   ```

2. 修改了渲染器，修改方法如下：

   1. 第一步： 使用 Kramed 代替 Marked

      `hexo` 默认的渲染引擎是 `marked`，但是 `marked` 不支持 `mathjax`。 `kramed` 是在 `marked` 的基础上进行修改。我们在工程目录下执行以下命令来安装 `kramed`.

      ```
      npm uninstall hexo-renderer-marked --save
      npm install hexo-renderer-kramed --save
      ```

      然后，更改/node_modules/hexo-renderer-kramed/lib/renderer.js，更改：

      ```
      // Change inline math rule
      function formatText(text) {
          // Fit kramed's rule: $$ + \1 + $$
          return text.replace(/`\$(.*?)\$`/g, '$$$$$1$$$$');
      }
      ```

      为：

      ```
      // Change inline math rule
      function formatText(text) {
          return text;
      }
      ```

   2. 第二步: 停止使用 hexo-math

      首先，如果你已经安装 `hexo-math`, 请卸载它：

      ```
      npm uninstall hexo-math --save
      ```

      然后安装 [hexo-renderer-mathjax](https://github.com/phoenixcw/hexo-renderer-mathjax) 包：

      ```
      npm install hexo-renderer-mathjax --save
      ```

   3. 第三步: 更新 Mathjax 的 CDN 链接

      首先，打开/node_modules/hexo-renderer-mathjax/mathjax.html

      然后，把`<script>`更改为：

      ```
      <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML"></script>
      ```

   4. 第四步: 更改默认转义规则

      因为 `hexo` 默认的转义规则会将一些字符进行转义，比如 `_` 转为 `<em>`, 所以我们需要对默认的规则进行修改.
      首先， 打开<path-to-your-project/node_modules/kramed/lib/rules/inline.js,

      然后，把:

      ```
      escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
      ```

      更改为:

      ```
      escape: /^\\([`*\[\]()# +\-.!_>])/,
      ```

      把

      ```
      em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
      ```

      更改为:

      ```
      em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
      ```

   5. 第五步: 开启 mathjax

      在主题 `_config.yml` 中开启 Mathjax， 找到 `mathjax` 字段添加如下代码：

      ```
      mathjax:
          enable: true
      ```

      这一步可选，在博客中开启 `Mathjax`，， 添加以下内容：

      ```
      ---
      title: Testing Mathjax with Hexo
      category: Uncategorized
      date: 2017/05/03
      mathjax: true
      ---
      ```

      通过以上步骤，我们就可以在 `hexo` 中使用 `Mathjax` 来书写数学公式。

3. 其他魔改的内容都在 theme 文件夹下，pull 下来应该就包括了，不再多说；
