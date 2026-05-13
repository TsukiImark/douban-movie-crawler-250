# 豆瓣电影Top250 爬虫数据分析系统

## 项目简介

一个完整的Python网络爬虫数据采集与分析系统，以豆瓣电影Top250为基础，实现列表页+详情页+短评的多层级爬虫，包含数据存储、清洗、分析与可视化，形成可演示的"电影数据智能分析平台"原型。

## 环境配置

### 1. Python环境
```bash
Python 3.8+
```

### 2. 安装依赖
```bash
cd douban-movie-crawler-250
pip install -r requirements.txt
```

### 3. ChromeDriver (Selenium需要)
1. 确认已安装Chrome浏览器
2. 下载与Chrome版本匹配的[ChromeDriver](https://chromedriver.chromium.org/)
3. 将chromedriver.exe放到项目根目录或添加到系统PATH
   - 或者修改 `config.py` 中的 `CHROMEDRIVER_PATH` 指定路径

### 4. 数据库 (无需额外配置)
默认使用SQLite，无需安装任何数据库软件。

如需使用MySQL：
1. 安装MySQL并创建数据库
2. 修改 `config.py`:
   - `DB_TYPE = "mysql"`
   - 填写 `MYSQL_CONFIG` 中的连接信息
3. 执行 `database/schema_mysql.sql` 建表

## 快速开始

### 一键运行（完整流程）
```bash
python main.py
```
执行：爬取 → 存储 → 清洗 → 分析 → 可视化

### 仅爬取数据
```bash
python main.py --crawl-only
```

### 仅分析已有数据
```bash
python main.py --analyze-only
```

### 使用Scrapy框架版本
```bash
python main.py --use-scrapy
```

### 跳过Selenium（仅requests爬取）
```bash
python main.py --no-selenium
```

## 项目结构

```
douban-movie-crawler-250/
├── main.py                    # 一键运行入口
├── config.py                  # 全局配置
├── requirements.txt           # 依赖清单
│
├── crawlers/                  # 【成员A】爬虫模块
│   ├── request_crawler.py     #   requests+BS4静态爬虫
│   ├── selenium_crawler.py    #   Selenium动态爬虫
│   ├── image_downloader.py    #   海报下载（断点续传）
│   └── anti_spider.py         #   反爬策略（UA池/代理/重试）
│
├── scrapy_version/            # 【成员B】Scrapy重构
│   ├── scrapy.cfg
│   ├── run.py                 #   独立运行入口
│   └── douban_scrapy/
│       ├── items.py           #   Item定义
│       ├── settings.py        #   Scrapy设置
│       ├── pipelines.py       #   SQLite/CSV/JSON Pipeline
│       ├── middlewares.py     #   UA/延时/重试中间件
│       └── spiders/
│           └── douban_spider.py  # Spider逻辑
│
├── database/                  # 【成员B】数据库
│   ├── db_manager.py          #   数据库管理器
│   ├── schema_sqlite.sql      #   SQLite建表脚本
│   └── schema_mysql.sql       #   MySQL建表脚本
│
├── data_processing/           # 【成员B】数据清洗
│   ├── cleaner.py             #   pandas清洗
│   └── exporter.py            #   CSV/JSON导出
│
├── analysis/                  # 【成员C】数据分析
│   ├── analyzer.py            #   统计分析
│   ├── visualizer.py          #   图表生成（5+张）
│   └── sentiment.py           #   情感分析+词云
│
├── utils/                     # 工具模块
│   ├── logger.py              #   日志管理
│   └── helpers.py             #   通用函数
│
├── output/                    # 输出目录
│   ├── data/                  #   CSV/JSON备份
│   ├── charts/                #   可视化图表
│   └── logs/                  #   日志文件
│
└── posters/                   # 电影海报
```

## 功能特性

### 1. 基础爬取 (requests + BeautifulSoup)
- 10页全量列表页爬取
- 提取: 排名、中英标题、评分、评价人数、导演/主演、简介
- 智能分页、随机UA池(20+个)、延时1-4s、异常重试

### 2. 动态爬取 (Selenium)
- 无头Chrome处理JS动态加载
- 短评"加载更多"自动点击
- 提取: 年份、片长、类型、IMDb评分、热门短评

### 3. 海报下载
- 按规范命名: `{排名}_{中文片名}.jpg`
- 支持断点续传

### 4. 数据存储
- SQLite数据库 (2张关联表: movies + comments, 外键约束)
- CSV/JSON双格式备份

### 5. Scrapy完整重构
- Item / Spider / Pipeline / Middleware全部实现
- 与requests版本性能对比

### 6. 数据分析与可视化 (5+张图表)
- 评分分布直方图
- 类型分布饼图
- 评分vs评价人数散点图
- 导演分布柱状图
- 时间趋势线图
- 短评星级分布饼图
- 短评词云

### 7. 情感分析
- jieba中文分词
- SnowNLP情感分析 (正面/中性/负面)
- 词云生成

### 8. 反爬虫与工程实践
- robots.txt检查与遵守
- 随机User-Agent池
- 免费代理IP池轮换
- Cookie管理
- 异常重试 (403/429/5xx)
- logging日志记录
- tqdm进度条

## 输出说明

### 数据文件 (output/data/)
- `movies.csv` / `movies.json` - 250部电影完整数据
- `comments.csv` / `comments.json` - 短评数据

### 图表 (output/charts/)
- `01_rating_histogram.png` - 评分分布直方图
- `02_genre_pie.png` - 类型分布饼图
- `03_rating_vs_count.png` - 评分与人数散点图
- `04_director_bar.png` - 导演分布柱状图
- `05_year_trend.png` - 时间趋势线图
- `06_comment_stars.png` - 短评星级分布
- `07_wordcloud.png` - 短评词云

### 日志 (output/logs/)
- 爬虫运行日志 (含robots.txt检查、请求重试记录)

## 注意事项

1. 严格遵守robots.txt，礼貌爬取 (延时1-4秒)
2. 仅爬取公开、无需登录的页面
3. 仅供学习研究使用，请勿用于商业目的
4. 如网站结构变化，需要调整解析规则
5. 推荐使用venv虚拟环境进行开发

## 环境要求

- Python >= 3.8
- Chrome浏览器 (Selenium需要)
- 网络连接 (爬取豆瓣需要)

## 许可证

本项目仅供学习研究使用。
