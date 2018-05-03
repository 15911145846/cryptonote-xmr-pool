门罗币矿池程序
====================

高性能 Node.js 挖掘的基于 CryptoNote 的硬币，如Bytecoin, DuckNote, Monero, QuazarCoin, Boolberry, Dashcoin等。
附带了轻量级的示例前端脚本，该脚本使用了池的AJAX API。

加入我的星球，提供全方位的技术指导，与服务。少走弯路，直达终点。
----
<img width="400" src="http://octk4wj3v.bkt.clouddn.com/get-money-xingqiu.jpg">


## 门罗老算法山寨币矿池请使用
https://github.com/cyyinfo/old-xmr-pool

#### 目录列表
* [产品特点](#features)
* [社区支持](#community--support)
* [矿池](#pools-using-this-software)
* [使用](#usage)
  * [需求](#requirements)
  * [下载和安装](#1-downloading--installing)
  * [配置](#2-configuration)
  * [配置挖矿配置](#3-optional-configure-cryptonote-easy-miner-for-your-pool)
  * [启动矿池](#4-start-the-pool)
  * [Host the front-end](#5-host-the-front-end)
  * [定制矿池前端](#6-customize-your-website)
  * [升级](#upgrading)
* [设置测试网络](#setting-up-testnet)
* [JSON-RPC 命令行接口](#json-rpc-commands-from-cli)
* [监控矿池](#monitoring-your-pool)
* [捐款](#donations)
* [Credits](#credits)
* [License](#license)


#### 基本特点

* 基于服务器的 TCP (层状) 基本任务
  * 与旧的HTTP协议相比，这有更高的哈希速率，更低的网络/CPU服务器负载，更低的孤子。块百分比，且容易出错。

* 绑定 IP 防止很低基本的网络攻击
* Socket 嗅探
* 支付处理
  * 拆分事务处理最大事务大小。
  * 最低付款门槛
  * 最小面额的截断付款金额精度，以减少区块交易的规模/复杂性。
* 详细的日志记录
* 能够配置多个端口——每个端口都有自己的难度。
* 可变难度/共享限制。
* 共享信任算法，减少共享验证哈希CPU负载。
* 垂直扩展
* 水平扩展模块组件(池服务器、数据库、统计/API、支付处理、前端)
* 实时统计API(使用带有CORS的AJAX长轮询)
  * 货币网络/块 难度
  * 当前快的高度
  * 全网算力
  * 矿池算力
  * 每个矿工的个人统计数据(hashrate，提交的哈希，未支付余额，总支付等)
  * 发现的块(等待、确认和无效)
* 一个易于扩展，响应性，轻量级前端使用API来显示数据。
* 支持在池代码中使用tls (https)来支持web前端的配置。
* 可以在命令行上启动多个模块，而不是一个一个的启动。
* Onishin's keepalive 函数 https://github.com/perl5577/cpuminer-multi/commit/0c8aedb



#### 其他功能

* 管理员页面
  * 聚合池统计
  * 守护进程 和 钱包RPC服务的稳定性监控。
  * 日志文件数据访问
  * 用户列表中有详细的统计信息。
* 历史图表的池的哈希率和矿工数，挖矿难度，利率和币的盈利能力。
* 用户的哈希率和支付的历史图表。
* 矿机登录(钱包地址)验证。
* 五个可配置的CSS主题
* 普通的交易区块数据。[chainradar.com](http://chainradar.com)
* 在2016年3月23日之后，FantomCoin的支持并没有在修复后得到节点模块的工作。
* 通过“地址”与“地址”匹配，为矿工客户设置固定难度。(困难)”后缀
* 防止“交易太大”的错误与“付款”。maxTransactionAmount”选项


### 社区/支持

* [CryptoNote Technology](https://cryptonote.org)
* [CryptoNote Forum](https://forum.cryptonote.org/)
* [CryptoNote Universal Pool Forum](https://bitcointalk.org/index.php?topic=705509)

====


使用
===

#### 需求
* 官方钱包（守护程序 ）(找到硬币的repo并从源代码构建最新版本。)
* [Node.js](http://nodejs.org/) v0.10+ ([follow these installation instructions](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager))
* [Redis](http://redis.io/) 基于内存的Key-value 数据存储 v2.6+ ([follow these instructions](http://redis.io/topics/quickstart))
* node- multihashing模块需要libssl。
  * Ubuntu 系统中使用命令: `sudo apt-get install libssl-dev`


##### 注意
如果使用老版本的 Node.js 或 Redis 可能会遇到一些问题，按照要求获取最新版本就能解决


[**Redis 安全警告**](http://redis.io/topics/security): 关于 Redis 的一些安全设置，包括 `绑定本机IP 127.0.0.1` 在`redis.conf` 文件上修改。

##### Ubuntu 14 LTS 上轻松安装
在不同的Linux发行版上安装池是不同的，因为它依赖于系统默认的组件和版本。目前，安装池的最简单方法是使用Ubuntu 14 LTS。因此，为了准备Ubunty 14进行池安装，您所要做的就是运行:

```bash
sudo apt-get install git redis-server libboost1.55-all-dev nodejs-dev nodejs-legacy npm cmake libssl-dev
```


#### 1) 下载和安装

Clone 本项目 并且运行 `npm update` 安装所有依赖库

```bash
git clone https://github.com/clintar/cryptonote-xmr-pool.git pool
cd pool
npm update
```

#### 2) 配置


配置项说明:
```javascript
"coin": "ducknote",
"symbol": "XDN",
"coinUnits": 100000000,
/* see DIFFICULTY_TARGET constant in DAEMON_CODE/src/cryptonote_config.h */
"coinDifficultyTarget": 240,
"logging": {
    "files": {
        /* Specifies the level of log output verbosity. This level and anything
           more severe will be logged. Options are: info, warn, or error. */
        "level": "info",

        /* Directory where to write log files. */
        "directory": "logs",

        /* How often (in seconds) to append/flush data to the log files. */
        "flushInterval": 5
    },

    "console": {
        "level": "info",
        /* Gives console output useful colors. If you direct that output to a log file
           then disable this feature to avoid nasty characters in the file. */
        "colors": true
    }
},

/* Modular Pool Server */
"poolServer": {
    "enabled": true,

    /* Set to "auto" by default which will spawn one process/fork/worker for each CPU
       core in your system. Each of these workers will run a separate instance of your
       pool(s), and the kernel will load balance miners using these forks. Optionally,
       the 'forks' field can be a number for how many forks will be spawned. */
    "clusterForks": "auto",

    /* Address where block rewards go, and miner payments come from. */
    "poolAddress": "ddehi53dwGSBEXdhTYtga2R3fS4y9hRz4YHAsLABJpH75yUd5EDQmuL3yDBj1mG6MMeDfydY9vp4zFVVNQ99FTYq2PpsFJP2y"

    /* Poll RPC daemons for new blocks every this many milliseconds. */
    "blockRefreshInterval": 1000,

    /* How many seconds until we consider a miner disconnected. */
    "minerTimeout": 900,

    "ports": [
        {
            "port": 3333, //Port for mining apps to connect to
            "difficulty": 100, //Initial difficulty miners are set to
            "desc": "Low end hardware" //Description of port
        },
        {
            "port": 5555,
            "difficulty": 2000,
            "desc": "Mid range hardware"
        },
        {
            "port": 7777,
            "difficulty": 10000,
            "desc": "High end hardware"
        }
    ],

    /* Variable difficulty is a feature that will automatically adjust difficulty for
       individual miners based on their hashrate in order to lower networking and CPU
       overhead. */
    "varDiff": {
        "minDiff": 2, //Minimum difficulty
        "maxDiff": 100000,
        "targetTime": 100, //Try to get 1 share per this many seconds
        "retargetTime": 30, //Check to see if we should retarget every this many seconds
        "variancePercent": 30, //Allow time to very this % from target without retargeting
        "maxJump": 100 //Limit diff percent increase/decrease in a single retargetting
    },

    /* Set difficulty on miner client side by passing <address> param with .<difficulty> postfix
       minerd -u 4AsBy39rpUMTmgTUARGq2bFQWhDhdQNekK5v4uaLU699NPAnx9CubEJ82AkvD5ScoAZNYRwBxybayainhyThHAZWCdKmPYn.5000 */
    "fixedDiff": {
        "enabled": true,
        "separator": ".", // character separator between <address> and <difficulty>
    },

    /* Feature to trust share difficulties from miners which can
       significantly reduce CPU load. */
    "shareTrust": {
        "enabled": true,
        "min": 10, //Minimum percent probability for share hashing
        "stepDown": 3, //Increase trust probability % this much with each valid share
        "threshold": 10, //Amount of valid shares required before trusting begins
        "penalty": 30 //Upon breaking trust require this many valid share before trusting
    },

    /* If under low-diff share attack we can ban their IP to reduce system/network load. */
    "banning": {
        "enabled": true,
        "time": 600, //How many seconds to ban worker for
        "invalidPercent": 25, //What percent of invalid shares triggers ban
        "checkThreshold": 30 //Perform check when this many shares have been submitted
    }
},

/* Module that sends payments to miners according to their submitted shares. */
"payments": {
    "enabled": true,
    "interval": 600, //how often to run in seconds
    "maxAddresses": 50, //split up payments if sending to more than this many addresses
    "mixin": 3, //number of transactions yours is indistinguishable from
    "transferFee": 5000000000, //fee to pay for each transaction
    "minPayment": 100000000000, //miner balance required before sending payment
    "maxTransactionAmount": 0, //split transactions by this amount(to prevent "too big transaction" error)
    "denomination": 100000000000 //truncate to this precision and store remainder
},

/* Module that monitors the submitted block maturities and manages rounds. Confirmed
   blocks mark the end of a round where workers' balances are increased in proportion
   to their shares. */
"blockUnlocker": {
    "enabled": true,
    "interval": 30, //how often to check block statuses in seconds

    /* Block depth required for a block to unlocked/mature. Found in daemon source as
       the variable CRYPTONOTE_MINED_MONEY_UNLOCK_WINDOW */
    "depth": 60,
    "poolFee": 1.8, //1.8% pool fee (2% total fee total including donations)
    "devDonation": 0.1, //0.1% donation to send to pool dev - only works with Monero
    "coreDevDonation": 0.1 //0.1% donation to send to core devs - works with Bytecoin, Monero, Dashcoin, QuarazCoin, Fantoncoin, AEON and OneEvilCoin
},

/* AJAX API used for front-end website. */
"api": {
    "enabled": true,
    "hashrateWindow": 600, //how many second worth of shares used to estimate hash rate
    "updateInterval": 3, //gather stats and broadcast every this many seconds
    "port": 8117,
    "blocks": 30, //amount of blocks to send at a time
    "payments": 30, //amount of payments to send at a time
    "password": "test" //password required for admin stats
},

/* Coin daemon connection details. */
"daemon": {
    "host": "127.0.0.1",
    "port": 18081
},

/* Wallet daemon connection details. */
"wallet": {
    "host": "127.0.0.1",
    "port": 8082
},

/* Redis connection into. */
"redis": {
    "host": "127.0.0.1",
    "port": 6379
}

/* Monitoring RPC services. Statistics will be displayed in Admin panel */
"monitoring": {
    "daemon": {
        "checkInterval": 60, //interval of sending rpcMethod request
        "rpcMethod": "getblockcount" //RPC method name
    },
    "wallet": {
        "checkInterval": 60,
        "rpcMethod": "getbalance"
    }

/* Collect pool statistics to display in frontend charts  */
"charts": {
    "pool": {
        "hashrate": {
            "enabled": true, //enable data collection and chart displaying in frontend
            "updateInterval": 60, //how often to get current value
            "stepInterval": 1800, //chart step interval calculated as average of all updated values
            "maximumPeriod": 86400 //chart maximum periods (chart points number = maximumPeriod / stepInterval = 48)
        },
        "workers": {
            "enabled": true,
            "updateInterval": 60,
            "stepInterval": 1800, //chart step interval calculated as maximum of all updated values
            "maximumPeriod": 86400
        },
        "difficulty": {
            "enabled": true,
            "updateInterval": 1800,
            "stepInterval": 10800,
            "maximumPeriod": 604800
        },
        "price": { //USD price of one currency coin received from cryptonator.com/api
            "enabled": true,
            "updateInterval": 1800,
            "stepInterval": 10800,
            "maximumPeriod": 604800
        },
        "profit": { //Reward * Rate / Difficulty
            "enabled": true,
            "updateInterval": 1800,
            "stepInterval": 10800,
            "maximumPeriod": 604800
        }
    },
    "user": { //chart data displayed in user stats block
        "hashrate": {
            "enabled": true,
            "updateInterval": 180,
            "stepInterval": 1800,
            "maximumPeriod": 86400
        },
        "payments": { //payment chart uses all user payments data stored in DB
            "enabled": true
        }
    }
```

#### 3) 旷工使用该池配置
旷工可以选择一个挖矿程序，然后配置下矿池的地址，和端口，就可以开始挖矿
```ini
pool_host=example.com
pool_port=5555
```

#### 4) 启动矿池

```bash
node init.js
```

可以使用 `-config=file` 参数来指定配置文件,例如:

```bash
node init.js -config=config_backup.json
```

项目包含4个不同的模块:
* `pool` - 为矿工开通连接和处理提供的算力端口
* `api` - 网站api用来显示 旷工，网络的数据
* `unlocker` - 解决挖矿失败，给旷工增加收入的bug
* `payments` - 根据储存在redis的余额向矿工发送付款。


默认情况下，运行 init.js 的脚本将启动所有四个模块。您可以选择启动脚本。
通过“-module=name” 命令参数来启动一个特定的模块，例如:
```bash
node init.js -module=api
```


#### 5) 前端网站

搭建一个简单的网站服务代理下 `website_example` 目录下的静态文件


配置下 `website_example/config.js` 配置文件

```javascript

/* Must point to the API setup in your config.json file. */
var api = "http://poolhost:8117";

/* Pool server host to instruct your miners to point to.  */
var poolHost = "poolhost.com";

/* IRC Server and room used for embedded KiwiIRC chat. */
var irc = "irc.freenode.net/#ducknote";

/* Contact email address. */
var email = "support@poolhost.com";

/* Market stat display params from https://www.cryptonator.com/widget */
var cryptonatorWidget = ["XDN-BTC", "XDN-USD", "XDN-EUR"];

/* Download link to cryptonote-easy-miner for Windows users. */
var easyminerDownload = "https://github.com/zone117x/cryptonote-easy-miner/releases/";

/* Used for front-end block links. */
var blockchainExplorer = "http://chainradar.com/{symbol}/block/{id}";

/* Used by front-end transaction links. */
var transactionExplorer = "http://chainradar.com/{symbol}/transaction/{id}";

/* Any custom CSS theme for pool frontend */
var themeCss = "themes/default-theme.css";

```

#### 6) 定制网站

修改一下这些文件就可以自定义您自己的矿池网站。而不必要大量修改了
* `index.html`
* `custom.css`
* `custom.js`


然后只需通过 nginx、Apache、或任何可以代理静态内容的内容来服务软


#### 更新
更新只需要只需 `git pull` 拉取最新的代码就行
* 进入到代码目录执行 `git pull` 拉取最新代码.
* 删除 node.js 依赖 `node_modules` 目录，使用 `rm -r node_modules` 命令就行。
* 运行 `npm update` 重新下载依赖。
* 对比 `config.json` 配置文件，调整参数


### 用户指南
http://www.xmr-zh.com/user-guides.html

#### 门罗币中文网
http://www.xmr-zh.com/


### 矿磁监控

* redis 建议使用 [redis-commander](https://github.com/joeferner/redis-commander)
* 为了监视CPU、网络、IO等的服务器负载，我建议使用。 [New Relic](http://newrelic.com/)
* 保持你的池节点脚本在后台运行，记录到文件，如果它崩溃就自动重新启动-我建议使用。 [forever](https://github.com/nodejitsu/forever)


捐献
---------
如果你觉得对你有用你可以赏作者一杯咖啡。
---
<img width="200" src="http://octk4wj3v.bkt.clouddn.com/get-money-weixin.jpg"/>
---
<img width="200" src="http://octk4wj3v.bkt.clouddn.com/get-money-zhifubao.jpg"/>


### 如果有什么不明白的可以加QQ群讨论
<img width="200" src="http://p5kq4rn7t.bkt.clouddn.com/static/image/my-qqgroup.png"/>
或者加我个人QQ：719591157 微信:cy719591157






License
-------
Released under the GNU General Public License v2

http://www.gnu.org/licenses/gpl-2.0.html
