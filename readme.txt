Grunt 和 Gruntplugins(grunt 插件) 都是通过npm（https://npmjs.org/）来管理和安装，npm是一个node.js（http://nodejs.org/）的包管理工具。
1.1 安装 Grunt
如果你在之前安装过全局的 Grunt，那么先删除它。

npm uninstall -g grunt

在开始使用 Grunt 之前，你必须先安装一个全局的Grunt命令行接口（CLI）。如果是在OSX、BSD等*nix机器上，你可能需要sudo来运行；如果是在windows上，可能需要管理员权限。

npm install -g grunt-cli

上面这条命令会把 grunt 加入你的系统搜索路径中，所以在任何目录下都可以使用此命令。
需要注意的是，安装 grunt-cli 并不是安装了grunt任务运行器！grunt-cli的任务非常简单：就是运行安装在GruntFile 同目录下的grunt（这里说的grunt是指通过npm安装的一个nodejs包，译者注）。
这样可以允许在同一台机器上运行多个grunt实例。

1.2 CLI 是如何工作的
每当 grunt 命令被执行时，它会通过nodejs的 require 命令在本地寻找已经安装的grunt。正因为如此，你可以在任何子目录下运行grunt 命令。

如果 cli 找到了一个本地安装的grunt，它会加载这个 grunt 库，然后应用你在 GruntFile 中写好的配置， 然后执行相应的任务。
如果你想知道它具体是怎么工作的，可以读这段代码https://github.com/GavinHans/grunt-cli/blob/master/bin/grunt ，很短哦。


1.3 如何使用一个已经配置好的 grunt 项目

假设 Grunt CLI 已经安装好了，并且项目通过 package.json 和GruntFile这两个文件已经正确配置好了。那么非常简单就可以使用 grunt：

进到项目的根目录。
运行 npm install 来安装需要的软件包。
运行 grunt 命令
这就是你需要做的全部事情。

运行 grunt --help 命令可以列出全部的grunt任务，但是最好还是先看下项目文档。


1.4 创建一个新的 grunt 项目
一个典型的安装过程只需要增加两个文件： package.json 和 GruntFile

package.json: 这个文件是npm用来储存发布这个项目所需要的元数据。你需要把 grunt 和相关的 grunt插件都列在这个文件中。

GruntFile：这个文件命名是 GruntFile.js 或者 GruntFile.coffee, 是用来配置或者定义grunt任务并且加载grunt插件用的。

在0.3.x的版本中这个文件命名是 grunt.js。


1.5 package.json 文件
package.json 文件和 GruntFile 文件一样都位于项目的根目录下，都应该加入你的版本控制中。在 package.json 文件所在目录下运行npm install命令可以自动安装此文件中所列出的所有依赖包的正确版本。

有以下几种方式可以创建一个 package.json 文件：

大部分的 grunt-init (http://gruntjs.com/project-scaffolding) 模板会自动创建一个与此项目相关的 package.json 文件
npm init 命令会自动创建一个基本的 package.json 文件
用下面这个例子作为开始，然后在添加自己需要的配置，可以参考这里（https://npmjs.org/doc/json.html）
{
  "name": "my-project-name",
  "version": "0.1.0",
  "devDependencies": {
    "grunt": "~0.4.1",
    "grunt-contrib-jshint": "~0.1.1",
    "grunt-contrib-nodeunit": "~0.1.2"
  }
}

1.6 安装 Grunt 和 grunt插件
把grunt和grunt插件加入一个已经存在的package.json 文件最简单的方式就是使用 npm install <module> --save-dev命令。这个命令不仅会在本地安装 <module>，而且会使用tilde version range（https://npmjs.org/doc/json.html#Tilde-Version-Ranges）自动把它加入到依赖关系中。

安装grunt插件和其他的nodejs插件都可以使用同样的命令，并且会把它加到依赖关系中。注意把更新后的 package.json 文件也提交到你的项目中。

 npm install grunt --save-dev                      --安装grunt插件
 npm install grunt-contrib-watch --save-dev        --实时监控文件变化、调用相应的任务重新执行；
 npm install grunt-contrib-uglify --save-dev       --压缩javascript代码
 npm install grunt-contrib-sass --save-dev         --sass语法支持
 npm install grunt-contrib-jshint --save-dev       --javascript语法错误检查；
 npm install grunt-contrib-clean --save-dev        --清空文件、文件夹；
 npm install grunt-contrib-concat --save-dev       --合并多个文件的代码到一个文件中
 npm install grunt-contrib-copy --save-dev         --复制文件、文件夹
 npm install grunt-contrib-cssmin --save-dev
 npm install grunt-contrib-requirejs --save-dev
 npm install grunt-combine-media-queries --save-dev



1.7 GruntFile 文件
GruntFile.js 或者 GruntFile.coffee 都是合法的文件，它们需要和 package.json 文件一样被存储在项目根目录下并且提交到你的项目代码中。

在 grunt 0.3.x 版本中，这个文件命名是 grunt.js.

GruntFile 文件由如下几部分组成：

wrapper function（包装函数）
项目和任务配置
加载grunt插件和任务
定制任务

1.8 一个 GruntFile 例子

module.exports = function(grunt) {
  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });

  // Load the plugin that provides the "uglify" task.
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // Default task(s).
  grunt.registerTask('default', ['uglify']);

};


1.8.1 wrapper function（包装函数）
每一个GruntFile 文件都会使用下面这个基本的格式， 所有的 Grunt 代码都会在这个函数内。
module.exports = function(grunt) {
  // Do grunt-related things in here
};

1.8.2 项目和任务的配置
大部分的 Grunt 任务都依赖于通过 grunt.initConfig(http://gruntjs.com/grunt#grunt.initconfig) 方法定义的配置。

在这个例子中， grunt.file.readJSON('package.json') 把存储在 package.json 文件中的元数据导入到 GruntFile 中。因为<% %>字符串模板可以在任何地方引用，所以像文件路径和文件列表这种配置数据可以通过这种方式来避免到处复制粘贴。

你可以在 config 对象中存储任意数据，只要不和你配置任务时需要用的参数冲突就可以，否则会被忽略掉。并且，因为这是 javascript，你不止可以配置json，任何合法的js代码都可以写在这。你甚至可以用js代码来自动生成某些配置。

和大多数的任务一样， grunt-contrib-uglify(http://github.com/gruntjs/grunt-contrib-uglify) 插件的 uglify 任务需要一个同名的配置。这里定义了一个 banner 配置；同时也定义了一个 简单的uglify目标叫 build，把一个源文件压缩成另一个目标文件。

// Project configuration.
grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  uglify: {
    options: {
      banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
    },
    build: {
      src: 'src/<%= pkg.name %>.js',
      dest: 'build/<%= pkg.name %>.min.js'
    }
  }
});


1.8.3 加载grunt插件和任务
最常见的任务比如 concatenation(https://github.com/gruntjs/grunt-contrib-concat)、
minification(http://github.com/gruntjs/grunt-contrib-uglify)和
linting(https://github.com/gruntjs/grunt-contrib-jshint) 可以找到对应的grunt插件（https://github.com/gruntjs）。
只要一个插件在 package.json 中定义了依赖关系，并且已经通过 npm install 安装好了，就可以在 GruntFile 文件中通过下面这个命令来启用。

// Load the plugin that provides the "uglify" task.
grunt.loadNpmTasks('grunt-contrib-uglify');

注意：grunt --help 命令可以列出所有可用的任务。

1.8.4 定制任务
你可以通过定义一个 default 任务来让grunt默认运行某些任务。
下面这个例子中，运行 grunt 命令不指定任何任务的话，会默认运行 uglify 任务。运行 grunt 和 运行 grunt uglify 或者 grunt default 在功能上是完全等价的。你可以在数组中定义任意数量的任务。

// Default task(s).
grunt.registerTask('default', ['uglify']);


如果你有某些任务没有找到对应的 grunt 插件，可以在 GruntFile 中定制自己的个性化任务。比如，下面这段代码定义了一个甚至不需要配置的完全自定义的任务。

module.exports = function(grunt) {
  // A very basic default task.
  grunt.registerTask('default', 'Log some stuff.', function() {
    grunt.log.write('Logging some stuff...').ok();
  });

};
定制项目相关的任务不一定非要在 GruntFile 文件中， 它们可以被定义在任意的外部 js 文件中，然后通过 grunt.loadTasks(http://gruntjs.com/grunt#grunt.loadtasks) 来加载任务。





