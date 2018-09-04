## 前言
**Flash的落幕**：基于Flash一直被诟病的安全性问题；加上始作俑者乔帮主在2010年发布了一封公开信，毫不留情地谴责了Flash一番；以及到2016年，谷歌Chrome、微软Edge和苹果Safari陆续宣布停止支持Flash；各种负面的情况直接导致Flash的亲妈Adobe也在2017年宣布，将在2020年年底停止对Flash的维护支持。至此曾经的王者即将落下帷幕，[参考文章](http://36kr.com/p/5085542.html)。

**videojs的选择**：从2015年底开始一直到现在，视频业务都处在一个风口。鉴于上面Flash的“倒台”，各视频相关业务公司都会在Flash真正迎来“终寝”之前找到一款取而代之的产品；而此时H5的势头却正劲，其video标签的各种便利性就不赘述，市面上基于video封装的H5播放器也是比较多的，其中YouTube的H5播放器就在最优秀名单之列，但是人家没有开源；而在开源项目中videojs算是最优秀的，不仅得益其代码的前鉴性，更有其丰富的社区作支持，videojs被更多的开发者所青睐。

**尽管目前国内也有[Chimee](http://chimee.org)和[西瓜视频播放器](http://h5player.bytedance.com/)，但是当开始方案制定的时间（2016.6）都早于这两个项目开源时间，甚至有可能早于这两个项目的起步开发时间。**

**文章的由来**：可能会有人问，既然项目起步时间可能早于国内目前两大开源播放器的起步时间，那为什么这里要介绍videojs，而不是介绍自己的开源播放器项目呢。究其原因可能有以下几点：1. 项目比较紧急（开始时需要一个月替换flash，尽管之后时间不是如此）；2. 人力不足（开始时只有一人）；3. 鄙人技术不够（这可能是最主要的吧）。而这篇介绍由来却是因为鄙人为了离职交接，想来思去还是写一篇简单介绍videojs的文章，便于后人快速接手。尽管[videojs的社区](https://videojs.com/)很健全，但是却没有一个中文社区或比较详细的介绍文章，这也是此文的由来。

`PS：`此文章只是对videojs做相关介绍，如果需要了解详细使用细则可移步[官网文档](https://docs.videojs.com/)，而鄙人的业务播放器是基于源码做自定义修改，这里不涉及。
***
## 正文
正文主要从以下几点剖析videojs源码：     

1. **videojs简介**
    * 简介
    * 快速入手
    * UI自定义
    * 组件化
    * 插件机制
    
2. **代码结构**
    * 代码tree
    * 继承关系
3. **核心模块**
    * videojs
    * Player
    * Componet
***
#### 1. videojs简介
在详细介绍videojs之前，先对其做一个简单的介绍，以便观客对videojs有一个初步的了解，也能之后更好的深入剖析其源码，以及基于其深度定制一个属于自己的播放器。

`ps`：videojs有自己的[官网和社区](https://videojs.com/)，这里除了自己的一些梳理和见解外，其他观客都可以自行在官网和社区中查阅、学习。		

* **简介**
**`videojs`**是一款基于video标签封装的H5播放器开源项目，截止文章发布时间，器在[GitHub](https://github.com/videojs/video.js)上已拥有22375 **star**，5608 **fork**。基于其开发、开源并且已经拥有过千**star**的项目也有好几个了；并且其官网、社区也一直保持在一个很高的活跃度；可以说`videojs`是目前最流行的开源H5播放器也不为过。
其能如此流行，都得益下面几点：使用者可以简单快速入手；可以自定义UI；整个项目都是组件化、插件化，自定义十分方便；社区有丰富的插件可供使用。
***
* **快速入手**
**videojs**使用相当方便，需要感谢项目的所有的贡献者，他们提供了各种便利的服务，可以自行参考。在自己页面初始化一个播放器需要分下面几步，也可[参考](https://videojs.com/getting-started/)。
    1. 源码引入：可以通过`npm`、`GitHub`获取源码到本地并引入，也可以通过`unpkg`、`CDN`服务等线上引入。
    
    ```
    // npm
    $ npm install video.js

    // CDN
    <link href="https://vjs.zencdn.net/7.1.0/video-js.css" rel="stylesheet">
    <script src="https://vjs.zencdn.net/7.1.0/video.js"></script>

    // unpkg 
    <link href="https://unpkg.com/video.js/dist/video-js.css" rel="stylesheet">
    <script src="https://unpkg.com/video.js/dist/video.js"></script>

    // cdnjs 
    <link href="https://cdnjs.cloudflare.com/ajax/libs/video.js/7.1.0/video-js.css" ref="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/video.js/7.1.0/video.js"></script>
    ```
    2. 初始化播放器：初始化一个播放器其实有两种方式，一种是直接通过节点配置`data-setup`属性，`这种源码需要在标签之后引入`；一种是在页面有video标签的同时使用videojs函数初始化，`这种源码需要在初始化js之前引入`。
    
    * data-setup
    
    ```
    // data-setup
    <video
    id="my-player"
    class="video-js"
    controls
    preload="auto"
    poster="//vjs.zencdn.net/v/oceans.png"
    data-setup='{}'>
      <source src="//vjs.zencdn.net/v/oceans.mp4" type="video/mp4">      </source>
      <source src="//vjs.zencdn.net/v/oceans.webm" type="video/webm"></source>
      <source src="//vjs.zencdn.net/v/oceans.ogv" type="video/ogg"></source>
      <p class="vjs-no-js">
    To view this video please enable JavaScript, and consider upgrading to a
    web browser that
        <a href="http://videojs.com/html5-video-support/" target="_blank">
      supports HTML5 video
        </a>
      </p>
  </video>
    ```
    
    * videojs
    
    ```
    // videojs函数：videojs(id, options, ready)返回播放器实例  
    videojs("my-player", {
      width: '500px',
      height: '281px',
      controls: true,
      children: [
        'mediaLoader',
        'posterImage',
        'loadingSpinner',
        'bigPlayButton',
        'controlBar',
        'logo',    // 自定义组件
       ],
      controlBar: {
        children: [
          'playToggle',
          'progressControl',
          'currentTimeDisplay',
          'timeDivider',
          'durationDisplay',
          'fullscreenToggle',
          {
            name: 'volumePanel',
            inline: true,
          }
        ],
      },
      className: 'video-js',
      poster: '//vjs.zencdn.net/v/oceans.png',
      loop: false, 
      autoplay: true, 
      muted: true,  
      sources: [
        {
          src: '//vjs.zencdn.net/v/oceans.mp4',
          type: 'video/mp4'
        },
        {
          src: '//vjs.zencdn.net/v/oceans.webm',
          type: 'video/webm'
        },
        {
          src: '//vjs.zencdn.net/v/oceans.ogv',
          type: 'video/ogg'
        },
      ],
    }, function() {
      // 播放器初始化完成后的回调函数，this就是播放器实例本身
      // 可以在回调函数中做很多业务逻辑，比方对实例监听、自定义方法等
      console.log(this);
    });
    ```
***
* **UI自定义**			
其实不管**videojs**在UI方面做得如何漂亮，对于各具特色的业务来说都不能通吃，还是需要业务方自行定义UI。重新定义UI有下面几种方式：
    * 一种是在原有css后面重新编写相应需要修改的样式名；
    * 一种是fork源码重新编写源码css，庆幸的是**videojs**提供了整套编写、编译方式，可自行学习；
    * 一种就是直接放弃原来的css文件，把样式全部重新按自己的业务要求写一遍，这种方式改变得最彻底，但是要求和风险也最高，需要你熟读videojs源码，不然修改起来就会出现各种bug。
***
* **组件化**			
**videojs**是JavaScript面向对象实现的一种很经典案例。播放器初始化后生成在页面中的一个个DOM节点都有其内部的继承关系（下一节详细介绍），而这些可以看到的DOM控件都是可以按需求自定义使用与否，比方上面`videojs函数`中`options->children`配置，每一项都是一个组件。除了**videojs**自带的组件外，其也提供了强大的功能——自定义组件。

    * es6
    
    ```
    /* es6 */
    import Component from '../component';

    class Logo extends Component {
      createEl() {
        return super.createEl('div', {
          className: 'vjs-logo',
        });
      }
      
      // 演示代码
      handleClick() {
        // this就是播放器实例
        console.log(this);
      }
    }

    Component.registerComponent('Logo', Logo);
    export default Logo;
    ```
    
    * es5
    
    ```
    /* es5 */
    var Componet, Logo, ctrt, createEl, hdlClick;
    Componet = videojs.getComponent('Component');

    // 演示代码
    ctrt = function() {
      console.log(this);
    };

    createEl = function() {
      var el = videojs.dom.createEl('div', {
        className: 'vjs-logo',
      });
      return el;
    };

    // 演示代码
    hdlClick = function() {
      console.log(this);
    };

    Logo = videojs.extend(Componet, {
      constructor: ctrt,
      createEl: createEl,
      handleClick: hdlClick,
    });
    Component.registerComponent('Logo', Logo);
    ```
***
* **插件机制**			
一个完善和强大的框架都会继承插件运行功能，给更多的开发者参与开发的机会进而实现框架功能的补充和延伸。**videojs** 不例外也提供一个插件定义的接口，使插件开发简单易行。而且社区论坛也提供了一些好用的插件供开发者使用。

    * es6
    
    ```
    const Plugin = videojs.getPlugin('plugin');

    class ExamplePlugin extends Plugin {

      constructor(player, options) {
        super(player, options);

        if (options.customClass) {
          player.addClass(options.customClass);
        }

        player.on('playing', function() {
          videojs.log('playback began!');
        });
      }
    }

        videojs.registerPlugin('examplePlugin', examplePlugin);
    ```
    
    * es5
    
    ```
    var Plugin = videojs.getPlugin('plugin');

    var ExamplePlugin = videojs.extend(Plugin, {

      constructor: function(player, options) {
        Plugin.call(this, player, options);

        if (options.customClass) {
          player.addClass(options.customClass);
        }

        player.on('playing', function() {
          videojs.log('playback began!');
        });
      }
    });

    videojs.registerPlugin('examplePlugin', examplePlugin);
    ```
***
#### 2. 代码结构

深入研究一个开源项目，避免不了地要细读源码，以鄙人一贯作风是从两块入手——`从整到单`、`从细到主`。下面就以这两种方法来研究一下**videojs** 的源码。

* 代码tree

    ```
    js
     ├── HD-button.js          清晰度组件
     ├── big-play-button.js          视频播放器的播放按钮组件
     ├── button.js          实现按钮组件的类
     ├── clickable-component.js          实现可点击和键盘事件组件的类
     ├── close-button.js          关闭按钮组件
     ├── component.js          所有类、组件的基类，需要详细阅读
     ├── context-menu-list.js          右键菜单组件
     ├── control-bar          控制条
     │   ├── audio-track-controls          音频音轨控制条组件
     │   │   ├── audio-track-button.js          音频音轨按钮组件
     │   │   └── audio-track-menu-item.js          音频音轨菜单选项组件
     │   ├── control-bar.js           视频控制条组件         
     │   ├── fullscreen-toggle.js          全屏组件
     │   ├── live-display.js          直播显示层组件
     │   ├── mute-toggle.js          音量喇叭组件
     │   ├── play-toggle.js          播放按钮组件
     │   ├── playback-rate-menu          倍速
     │   │   ├── playback-rate-menu-button.js          倍速按钮组件
     │   │   └── playback-rate-menu-item.js          倍速菜单选项组件
     │   ├── progress-control          进度条
     │   │   ├── load-progress-bar.js          加载条组件
     │   │   ├── mouse-time-display.js          鼠标hover时间显示层组件
     │   │   ├── play-progress-bar.js          播放条组件
     │   │   ├── progress-control.js          进度条组件
     │   │   ├── seek-bar.js          拖动按钮组件
     │   │   └── time-tooltip.js          时间提示层组件
     │   ├── spacer-controls          空格
     │   │   ├── custom-control-spacer.js          普通控制空格组件
     │   │   └── spacer.js          空格组件
     │   ├── text-track-controls          字幕相关
     │   │   ├── caption-settings-menu-item.js
     │   │   ├── captions-button.js
     │   │   ├── chapters-button.js
     │   │   ├── chapters-track-menu-item.js
     │   │   ├── descriptions-button.js
     │   │   ├── off-text-track-menu-item.js
     │   │   ├── subs-caps-button.js
     │   │   ├── subs-caps-menu-item.js
     │   │   ├── subtitles-button.js
     │   │   ├── text-track-button.js
     │   │   └── text-track-menu-item.js
     │   ├── time-controls          时间控制
     │   │   ├── control-divider.js          
     │   │   ├── controlDivider.js
     │   │   ├── current-time-display.js          当前时间组件
     │   │   ├── duration-display.js          总时长组件
     │   │   ├── remaining-time-display.js          剩余时长组件
     │   │   └── time-divider.js          时间分割组件
     │   ├── track-button.js          轨道按钮组件
     │   ├── volume-control          音量控制
     │   │   ├── check-volume-support.js          监测音量组件
     │   │   ├── volume-bar.js          音量控制按钮组件
     │   │   ├── volume-control.js          音量控制条组件
     │   │   └── volume-level.js          音量分级组件
     │   └── volume-panel.js          音量组合组件
     ├── end-display.js          播放结束层组件
     ├── error-display.js          播放错误层组件
     ├── event-target.js          事件兼容组件模块
     ├── extend.js          继承封装组件模块
     ├── fullscreen-api.js          全屏兼容API模块
     ├── loading-spinner.js          loading组件
     ├── media-error.js          视频播放错误组件模块
     ├── menu          菜单
     │   ├── menu-button.js          菜单按钮组件
     │   ├── menu-item.js          菜单选项组件
     │   └── menu.js          菜单组件
     ├── mixins          事件绑定系统
     │   ├── evented.js
     │   └── stateful.js
     ├── modal-dialog.js          弹层组件
     ├── next-button.js          下一个组件
     ├── player.js          播放器组件
     ├── plugin.js          插件组件
     ├── popup          弹层
     │   ├── popup-button.js          弹层按钮组件
     │   └── popup.js           弹层组件
     ├── poster-image.js          封面图组件
     ├── setup.js          video标签初始化组件模块
     ├── slider
     │   └── slider.js          拖拽组件
     ├── tech
     │   ├── html5.js
     │   ├── loader.js          视频加载组件
     │   ├── middleware.js          中间件
     │   └── tech.js          视频video封装
     ├── tracks          字幕、音轨等
     │   ├── audio-track-list.js
     │   ├── audio-track.js
     │   ├── html-track-element-list.js
     │   ├── html-track-element.js
     │   ├── text-track-cue-list.js
     │   ├── text-track-display.js
     │   ├── text-track-list-converter.js
     │   ├── text-track-list.js
     │   ├── text-track-settings.js
     │   ├── text-track.js
     │   ├── track-enums.js
     │   ├── track-list.js
     │   ├── track-types.js
     │   ├── track.js
     │   ├── video-track-list.js
     │   └── video-track.js
     ├── utils          工具包
     │   ├── browser.js
     │   ├── buffer.js
     │   ├── computed-style.js
     │   ├── dom-data.js
     │   ├── dom.js
     │   ├── events.js
     │   ├── filter-source.js
     │   ├── fn.js
     │   ├── format-time.js
     │   ├── guid.js
     │   ├── log.js
     │   ├── merge-options.js
     │   ├── obj.js
     │   ├── stylesheet.js
     │   ├── time-ranges.js
     │   ├── to-title-case.js
     │   └── url.js
     └── video.js          videojs外抛主逻辑
    ```
    
***
* 继承关系
在上节 **组件化** 中提到自定义组件的事例，其实`videojs`自带的组件也都是继承于基类`Component`（具体源码解析，在下节介绍），通过阅读源码，把其中所有组件的继承关系罗列出一张表，为了便于更好的梳理关系还拆分出一张核心关系图，如下：
  * 完整版
![完整版](https://upload-images.jianshu.io/upload_images/2317192-b244e4fb1b8a405f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  * 核心版
![核心版](https://upload-images.jianshu.io/upload_images/2317192-db7a07da30db85f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
***
#### 3. 核心模块
基于上面的简介和代码结构的介绍，接下来开始真正进入到源码分析。下面的内容是针对`videojs`的核心模块做代码梳理分析，主要从`API`的罗列、模块中的核心代码的`注释说明`来细说代码，了解其运行机制，这样才能基于其做复杂的业务以及基于其做自定义功能。
* videojs
第一节**快速入手**中提到`videojs`函数，这也是这个框架外抛的唯一一个全局变量，我们先梳理其`API`再对其核心代码作`注释说明`。
  * API
  
  ```
  // 初始化播放器构造函数，详解见源码解析
  videojs(id, options, ready)

  // 生命周期钩子
  videojs.hooks_ = {}
  videojs.hooks(type, fn)
  videojs.hook(type, fn)
  videojs.removeHook(type, fn)
  
  // 执行节点配置初始化播放器的节点data-setup
  setup.autoSetupTimeout(1, videojs)

  // 获取播放器参数和页面已经生成的播放器实例
  videojs.options
  videojs.getPlayers()
  videojs.players

  // 组件相关
  videojs.getComponent = Component.getComponent        // 获取组件
  videojs.registerComponent(name, comp)        // 注册组件
  videojs.getTech = Tech.getTech          // 获取封装
  videojs.registerTech = Tech.registerTech     // 组成封装
  videojs.use = middlewareUse          // 中间件

  videojs.browser = browser          // 浏览器工具
  videojs.TOUCH_ENABLED = browser.TOUCH_ENABLED            // 是否可触摸

  // 函数、参数扩展方法
  videojs.extend = extendFn
  videojs.mergeOptions = mergeOptions
  videojs.bind = Fn.bind

  // 插件相关
  videojs.registerPlugin = Plugin.registerPlugin
  videojs.plugin(name, plugin)
  videojs.getPlugins = Plugin.getPlugins
  videojs.getPlugin = Plugin.getPlugin
  videojs.getPluginVersion = Plugin.getPluginVersion

  // 一些工具方法
  videojs.addLanguage(code, data)
  videojs.log = log
  videojs.createTimeRange = videojs.createTimeRanges = createTimeRanges
  videojs.formatTime = formatTime
  videojs.parseUrl = Url.parseUrl
  videojs.url = Url

  // 事件系统
  videojs.EventTarget = EventTarget
  videojs.on = Events.on
  videojs.one = Events.one
  videojs.off = Events.off
  videojs.trigger = Events.trigger

  // 音轨、字幕相关
  videojs.xhr = xhr
  videojs.TextTrack = TextTrack
  videojs.AudioTrack = AudioTrack
  videojs.VideoTrack = VideoTrack

  videojs.computedStyle = computedStyle          // 样式方法集
  videojs.dom = Dom          // 节点封装工具库
  ```
  
  * 构造函数：除了一些兼容以为，最主要应用了`Player`组件

  ```
  function videojs(id, options, ready) {
    let tag;

    // id可以是节点或字符串ID
    if (typeof id === 'string') {
      const players = videojs.getPlayers();

      // 调整带#号字符串
      if (id.indexOf('#') === 0) {
        id = id.slice(1);
      }

      // 如果播放器已经生成，直接返回这个实例
      if (players[id]) {

        // 如果传了options，就提醒
        if (options) {
          log.warn(`Player "${id}" is already initialised. Options will not be applied.`);
        }
        
        // 如果传了ready就执行
        if (ready) {
          players[id].ready(ready);
        }

        return players[id];
      }

      // 否则获取指定id的节点
      tag = Dom.$('#' + id);

    // 或者id就是一个节点
    } else {
      tag = id;
    }

    // 监测最后tag是否是节点
    if (!tag || !tag.nodeName) {
      throw new TypeError('The element or ID supplied is not valid. (videojs)');
    }

    // 如果相应节点生成过播放器，就会在节点中绑定对应的player并返回其，如果没有生成过就调用Player生成播放器；Player组件
    if (tag.player || Player.players[tag.playerId]) {
      return tag.player || Player.players[tag.playerId];
    }

    options = options || {};

    // 执行设置之前的生命周期函数
    videojs.hooks('beforesetup').forEach(function(hookFunction) {
      const opts = hookFunction(tag, mergeOptions(options));

      if (!isObject(opts) || Array.isArray(opts)) {
        log.error('please return an object in beforesetup hooks');
        return;
      }

      options = mergeOptions(options, opts);
    });

    // 如果没有初始化播放器，这里获取播放器组件并初始化就，Player也就是整个播放器的核心代码
    const PlayerComponent = Component.getComponent('Player');
    
    const player = new PlayerComponent(tag, options, ready);

    videojs.hooks('setup').forEach((hookFunction) => hookFunction(player));

    return player;
  }
  ```
  
***

* Player			
`Player` 就是初始化播放器的主要函数，通过上面**继承关系**可以看到其也是继承于**基类**`Component`，同样我们也是先罗列器`API`（因为其继承于`Component`，所以继承的`API`就先不罗列了）；再对其核心代码作`注释说明`：

    `ps`：如果需要自定义一些播放器的方法，可以在这个文件中作添加修改
  * API：源码中带`_`的方法都是内部调用函数，没有`_`的就是外抛`API`
  
  ```
  // constructor也就是前面videojs中调用Player的构造函数，注释说明中详细说明
  constructor(tag, options, ready)

  // 销毁
  dispose()

  // 获取、设置视频宽高
  width(value)
  height(value)

  // 获取视频原始宽高
  videoWidth()
  videoHeight()

  // 获取、设置视频方向
  dimension(dimension, value)
  fluid(bool)
  aspectRatio(ratio)

  // 设置、获取视频是否开始播放
  hasStarted(hasStarted)
  getCache()

  // 控制播放暂停
  play()
  pause()

  // 判断是否播放暂停
  paused()
  played()

  // 返回设置用户是否点击控制条
  scrubbing(isScrubbing)

  // 设置获取当前时间
  currentTime(seconds)
  // 设置获取视频总时长
  duration(seconds)
  // 获取剩余时间
  remainingTime() 

  // 获取缓存相关
  buffered()
  bufferedPercent()
  bufferedEnd()

  // 设置获取当前音量
  volume(percentAsDecimal)
  // 控制是否静音
  muted(muted)
  defaultMuted(defaultMuted)

  // 全屏相关
  supportsFullScreen()
  isFullscreen(isFS)
  requestFullscreen()
  exitFullscreen()
  enterFullWindow()
  exitFullWindow()
  
  // 判断可播放类型
  canPlayType(type)

  // 切换视频源，视频源相关
  src(source)
  load()
  reset()
  currentSources()
  currentSource()
  currentSrc()
  currentType()
  preload(value)

  // 视频自动播
  autoplay(value)
  // 控制循环播
  loop(value)
  // 封面图
  poster(src)

  // 控制控制条
  controls(bool)
  // 使用原生控制条
  usingNativeControls(bool)
  // 用户主动触发
  reportUserActivity(event)
  // 用户是否活动状态
  userActive(bool)
  
  // 视频播放错误
  error(err)
  // 视频速率
  playbackRate(rate)
  defaultPlaybackRate(rate)

  // 是否是音频
  isAudio(bool)
  // 字幕、语言相关
  addTextTrack(kind, label, language)
  addRemoteTextTrack(options, manualCleanup)
  removeRemoteTextTrack({track = arguments[0]} = {})
  language(code)
  languages()
  createModal(content, options)
  ```
  
  * 构造函数：videojs函数初始化播放器的主函数，此构造函数可以说明整个`videojs`的运行机制
  
  ```
  constructor(tag, options, ready) {
    // tag就是原生video标签，这里确保标签有id属性
    tag.id = tag.id || `vjs_video_${Guid.newGUID()}`;

    // 设置options参数，合并video标签上设置的和传入的options
    // (标签必须在Player构造函数之前存在)
    options = assign(Player.getTagSettings(tag), options);

    // 因为要先设置播放器属性所以需要延迟初始化children；同时在super()之前不能使用this关键字
    options.initChildren = false;

    // 创建节点；原因同上
    options.createEl = false;

    // 播放器不主动触发活动touch，可以看Component中enableTouchActivity
    // 在移动端关掉手势动作监听
    options.reportTouchActivity = false;

    // 如果没有设置语言，就设置最近的lang属性
    if (!options.language) {
      if (typeof tag.closest === 'function') {
        const closest = tag.closest('[lang]');

        if (closest) {
          options.language = closest.getAttribute('lang');
        }
      } else {
        let element = tag;

        while (element && element.nodeType === 1) {
          if (Dom.getAttributes(element).hasOwnProperty('lang')) {
            options.language = element.getAttribute('lang');
            break;
          }
          element = element.parentNode;
        }
      }
    }

    // 这里用最新的options属性调用Component构造函数
    super(null, options, ready);

    // 在加载新tech之前先关闭API
    this.isReady_ = false;

    // 如果全局options被意外丢失，报一错误提醒
    if (!this.options_ ||
        !this.options_.techOrder ||
        !this.options_.techOrder.length) {
      throw new Error('No techOrder specified. Did you overwrite ' +
                      'videojs.options instead of just changing the ' +
                      'properties you want to override?');
    }

    // 缓存原始tag标签
    this.tag = tag;

    // 缓存video标签属性
    this.tagAttributes = tag && Dom.getAttributes(tag);

    // 更新语言
    this.language(this.options_.language);

    // 更新设置支持的语言
    if (options.languages) {
      // Normalise player option languages to lowercase
      const languagesToLower = {};

      Object.getOwnPropertyNames(options.languages).forEach(function(name) {
        languagesToLower[name.toLowerCase()] = options.languages[name];
      });
      this.languages_ = languagesToLower;
    } else {
      this.languages_ = Player.prototype.options_.languages;
    }

    // 缓存video property值
    this.cache_ = {};

    // 设置封面图
    this.poster_ = options.poster || '';

    // 设置控制条
    this.controls_ = !!options.controls;

    // 设置最近默认音量
    this.cache_.lastVolume = 1;

    tag.controls = false;

    // 默认用户没有点击bar
    this.scrubbing_ = false;

    // 这里通过createEl创建并缓存节点el_
    this.el_ = this.createEl();

    // 用el_作为事件驱动节点创建事件对象
    evented(this, {eventBusKey: 'el_'});

    // 缓存一个playerOptions用于传入其他子组件，供子组件访问使用
    const playerOptionsCopy = mergeOptions(this.options_);

    // 加载插件，就是在这里加载了外部插件
    if (options.plugins) {
      const plugins = options.plugins;

      Object.keys(plugins).forEach(function(name) {
        if (typeof this[name] === 'function') {
          this[name](plugins[name]);
        } else {
          throw new Error(`plugin "${name}" does not exist`);
        }
      }, this);
    }

    this.options_.playerOptions = playerOptionsCopy;

    this.middleware_ = [];

    // 初始化子节点，这是这个播放器的继承关系的关键，此部分会在Component中详细介绍
    this.initChildren();

    // 判断是否是音频
    this.isAudio(tag.nodeName.toLowerCase() === 'audio');

    // 更新控制样式名
    if (this.controls()) {
      this.addClass('vjs-controls-enabled');
    } else {
      this.addClass('vjs-controls-disabled');
    }

    // 依据播放器类型设置ARIA和role属性
    this.el_.setAttribute('role', 'region');
    if (this.isAudio()) {
      this.el_.setAttribute('aria-label', this.localize('Audio Player'));
    } else {
      this.el_.setAttribute('aria-label', this.localize('Video Player'));
    }

    if (this.isAudio()) {
      this.addClass('vjs-audio');
    }

    if (this.flexNotSupported_()) {
      this.addClass('vjs-no-flex');
    }

    // iOS Safari 特殊处理
    if (!browser.IS_IOS) {
      this.addClass('vjs-workinghover');
    }

    // 缓存初始化后的播放器，便于通过id快速查找
    Player.players[this.id_] = this;

    // 当播放器首次初始化了，如果需要触发控制条组件显示
    this.userActive(true);
    this.reportUserActivity();
    this.listenForUserActivity_();

    this.on('fullscreenchange', this.handleFullscreenChange_);
    this.on('stageclick', this.handleStageClick_);

    this.changingSrc_ = false;
  }
  ```
  
***

* Componet			
这是整个`videojs`所有组件的**基类**，把他研究透了对整个播放器也就透了。通过其能看到整个`videojs`的继承关系的关键；以及为什么整套代码没有结构模板却生成了页面中的播放器和组件；还有就是自定义UI组件的关键。同样先从`API`开始到核心代码`注释说明`结束：
  * API
  
  ```
  // 构造函数，会在注释说明中详细说明
  constructor(player, options, ready)

  // 销毁，只要继承Component就会有销毁的方法
  dispose()
  
  // 获取播放器实例
  player()
  // 获取、扩展options
  options(obj)
  // 获取组件节点el_
  el()
  
  // 字符串本地化
  localize(string, tokens, defaultValue = string)
  // 获取内容节点contentEl_
  contentEl()
  // 获取id
  id()
  // 获取name
  name()
  // 获取所以children
  children()
  // 通过id获取child
  getChildById(id)
  // 通过name获取child
  getChild(name)
  // 移出child组件
  removeChild(component)
  
  // 添加chilid，以下两个函数就是所有Dom节点、组件继承关系的关键，下面注释说明中详细介绍
  addChild(child, options = {}, index = this.children_.length)
  initChildren()

  // 创建样式属性
  buildCSSClass()
  // 初始化完成的回调函数
  ready(fn, sync = false)
  triggerReady()

  // 以下方法都是通过utils/dom.js扩展生成，可自行参考此文件
  createEl(tagName, properties, attributes)
  $(selector, context)
  $$(selector, context)
  hasClass(classToCheck)
  addClass(classToAdd)
  removeClass(classToRemove)
  toggleClass(classToToggle, predicate)
  getAttribute(attribute)
  setAttribute(attribute, value)
  removeAttribute(attribute)

  // 显示隐藏节点，通过样式名vjs-hidden控制
  show()
  hide()
  // 展示、隐藏节点，通过样式名vjs-lock-showing控制
  lockShowing()
  unlockShowing()
  // 获取节点宽高数值
  dimension(widthOrHeight, num, skipListeners)
  width(num, skipListeners)
  height(num, skipListeners)
  dimensions(width, height)
  currentDimension(widthOrHeight)
  currentDimensions()
  currentWidth()
  currentHeight()

  // 聚焦离开节点
  focus()
  blur()

  // 移动端tap事件
  emitTapEvents()
  // 控制是否可以触发touch事件
  enableTouchActivity()

  // 针对节点创建定时器和原生动画函数
  setTimeout(fn, timeout)
  clearTimeout(timeoutId)
  setInterval(fn, interval)
  clearInterval(intervalId)
  requestAnimationFrame(fn)
  cancelAnimationFrame(id)

  // 两个静态函数，一是注册组件，一个获取组件
  static registerComponent(name, ComponentToRegister)
  static getComponent(name)
  ```
  
  * 构造函数：这是生成组件的关键函数，当然也可在自定义组件时重写此构造函数
  
  ```
  constructor(player, options, ready) {

    // 给组件绑定player_属性，组件有可能是Player本身，所以不能直接传this
    if (!player && this.play) {
      this.player_ = player = this; // eslint-disable-line
    } else {
      this.player_ = player;
    }

    // 给组件扩展自身options_属性
    this.options_ = mergeOptions({}, this.options_);
    options = this.options_ = mergeOptions(this.options_, options);

    // 设置组件id_属性
    this.id_ = options.id || (options.el && options.el.id);

    // 如果没有id_，就创建给组件创建一个
    if (!this.id_) {
      const id = player && player.id && player.id() || 'no_player';

      this.id_ = `${id}_component_${Guid.newGUID()}`;
    }

    this.name_ = options.name || null;

    // 创建并记录节点el_
    if (options.el) {
      this.el_ = options.el;
    } else if (options.createEl !== false) {
      this.el_ = this.createEl();
    }

    // 通过el_为节点创建事件对象
    evented(this, {eventBusKey: this.el_ ? 'el_' : null});
    stateful(this, this.constructor.defaultState);

    this.children_ = [];
    this.childIndex_ = {};
    this.childNameIndex_ = {};

    // 这是Player构造函数中设置initChildren为false的原因
    if (options.initChildren !== false) {
      // 这个initChildren方法是将一个类的子类都实例化，每个类都对应着自己的el_（DOM实例），通过这个方法父类和子类的DOM继承关系也就实现了。初始化children，这就是节点继承的方式函数，后面详细介绍
      this.initChildren();
    }

    this.ready(ready);
   
    if (options.reportTouchActivity !== false) {
      this.enableTouchActivity();
    }
  }
  ```
  
  * 继承关系：`Component`中`initChildren()`和`addChild()`就是整个`videojs`继承关系的关键函数。通过initChildren获取children属性，然后遍历通过addChild将子类实例化并插入到对应的父节点中，实例化的过程会自动重复上述过程从而达到了”继承“的效果。
 
  ```
  //  初始化子节点的initChildren关键函数，通过options中children来生成初始化子节点所需属性，最后通过调用addChild关键函数创建子节点并触发的继承关系
  initChildren() {
    const children = this.options_.children;

    if (children) {
      // `this` is `parent`
      const parentOptions = this.options_;

      // 添加子节点回调函数
      // 每个child对象最后都会包含两个属性，name是组件的名称，opts是组件的属性对象
      const handleAdd = (child) => {
        const name = child.name;
        let opts = child.opts;

        // 以最上层playerOptions为主
        if (parentOptions[name] !== undefined) {
          opts = parentOptions[name];
        }

        // 允许对组件配置false和true
        if (opts === false) {
          return;
        }
        if (opts === true) {
          opts = {};
        }

        // 这就是Player构造函数中创建playerOptions的原因，用于所有子节点的引用
        opts.playerOptions = this.options_.playerOptions;

        // 创建子节点，通过调用addChild函数，下面详细介绍
        const newChild = this.addChild(name, opts);

        // 通过name缓存子节点组件
        if (newChild) {
          this[name] = newChild;
        }
      };

      // children可以以数组和对象的方式传递配置，可以参考第一节中 快速入门 中的传参方式
      let workingChildren;
      const Tech = Component.getComponent('Tech');

      if (Array.isArray(children)) {
        workingChildren = children;
      } else {
        workingChildren = Object.keys(children);
      }
 
      // 筛选child的配置参数
      workingChildren
      .concat(Object.keys(this.options_)
              .filter(function(child) {
                return !workingChildren.some(function(wchild) {
                  if (typeof wchild === 'string') {
                    return child === wchild;
                  }
                  return child === wchild.name;
                });
              }))
      .map((child) => {
        let name;
        let opts;

        if (typeof child === 'string') {
          name = child;
          opts = children[name] || this.options_[name] || {};
        } else {
          name = child.name;
          opts = child;
        }

        return {name, opts};
      })
      .filter((child) => {
        const c = Component.getComponent(child.opts.componentClass ||
                                       toTitleCase(child.name));

        return c && !Tech.isTech(c);
      })
      // 筛选child的配置参数后，调用上面创建child的回调函数
      .forEach(handleAdd);
    }
  }



  // 创建child并产生继承关系的关键函数addChild，此函数是在上面initChildren中回调函数handleAdd中被调用
  addChild(child, options = {}, index = this.children_.length) {
    let component;
    let componentName;

    // 如果child是字符串，通过options创建组件
    if (typeof child === 'string') {
      componentName = toTitleCase(child);

      const componentClassName = options.componentClass || componentName;

      // 设置name
      options.name = componentName;

      // 获取组件，如果不存在或不是函数就报错
      const ComponentClass = Component.getComponent(componentClassName);

      if (!ComponentClass) {
        throw new Error(`Component ${componentClassName} does not exist`);
      }

      if (typeof ComponentClass !== 'function') {
        return null;
      }

      // 通过已注册的组件创建child节点组件实例
      component = new ComponentClass(this.player_ || this, options);

    } else {
      // child是组件实例的情况
      component = child;
    }

    // 通过配置参数在组件children_属性中插入刚创建的子节点组件实例
    this.children_.splice(index, 0, component);

    if (typeof component.id === 'function') {
      this.childIndex_[component.id()] = component;
    }

    // 通过组件名缓存
    componentName = componentName || (component.name && toTitleCase(component.name()));

    if (componentName) {
      this.childNameIndex_[componentName] = component;
    }

    // 这是继承关系的关键代码，其实也很简单，就是把组件节点通过原生insertBefore插入到父节点指定index位置即可
    if (typeof component.el === 'function' && component.el()) {
      const childNodes = this.contentEl().children;
      const refNode = childNodes[index] || null;

      this.contentEl().insertBefore(component.el(), refNode);
    }

    // 返回创建完成的组件
    return component;
  }
  ```

***
## 小结
以上就是`videojs`的简介和核心代码的介绍，当然`videojs`并不止限上面介绍的内容，其还有很多值得学习的地方。通过阅读其源码能感受到作者构思的巧妙；通过分析源码也能总结一些小技巧和感受:

* 通过`从整到单`、`从细到主`两个入手，更能清晰的理清源码结构、继承关系等
* 通过了解`videojs`的运行机制，更能深入理解`Component`，以便更好创建自定义 **UI组件** 来丰富自己播放器		
* 只要是一个好的开源项目，都少不了插件机制，可以供开发者丰富开源项目生态
* 最后一点就是：**还是需要多读优秀的开源项目**
