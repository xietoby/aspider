## aspider

[![travis](https://travis-ci.org/howie6879/aspider.svg?branch=master)](https://travis-ci.org/howie6879/aspider) [![PyPI - Python Version](https://img.shields.io/pypi/pyversions/aspider.svg)](https://pypi.org/project/aspider/) [![license](https://img.shields.io/github/license/howie6879/aspider.svg)](https://github.com/howie6879/aspider)

A lightweight,asynchronous,distributed scraping micro-framework, written with `asyncio` and `aiohttp`, aims to make crawling url as convenient as possible.

### Installation

``` shell
pip install aspider

pip install git+https://github.com/howie6879/aspider
```

### Usage

#### Item

Let's take a look at a quick example of using `Item`. Start off by adding the following to your demo.py:

``` python
import asyncio

from aspider import AttrField, TextField, Item


class HackerNewsItem(Item):
    target_item = TextField(css_select='tr.athing')
    title = TextField(css_select='a.storylink')
    url = AttrField(css_select='a.storylink', attr='href')

    async def clean_title(self, value):
        return value


items = asyncio.get_event_loop().run_until_complete(HackerNewsItem.get_items(url="https://news.ycombinator.com/"))
for item in items:
    print(item.title, item.url)
```

Run: `python demo.py`

``` shell
Notorious ‘Hijack Factory’ Shunned from Web https://krebsonsecurity.com/2018/07/notorious-hijack-factory-shunned-from-web/
 ......
```

#### Spider

For multiple pages, you can solve this with `Spider`

Create `hacker_news_spider.py`:

``` python
import aiofiles

from aspider import AttrField, TextField, Item, Spider


class HackerNewsItem(Item):
    target_item = TextField(css_select='tr.athing')
    title = TextField(css_select='a.storylink')
    url = AttrField(css_select='a.storylink', attr='href')

    async def clean_title(self, value):
        return value


class HackerNewsSpider(Spider):
    start_urls = ['https://news.ycombinator.com/', 'https://news.ycombinator.com/news?p=2']

    async def parse(self, res):
        items = await HackerNewsItem.get_items(html=res.html)
        for item in items:
            async with aiofiles.open('./hacker_news.txt', 'a') as f:
                await f.write(item.title + '\n')


if __name__ == '__main__':
    HackerNewsSpider.start()
```

Run `hacker_news_spider.py`:

``` shell
[2018-07-11 17:50:12,430]-aspider-INFO  Spider started!
[2018-07-11 17:50:12,430]-Request-INFO  <GET: https://news.ycombinator.com/>
[2018-07-11 17:50:12,456]-Request-INFO  <GET: https://news.ycombinator.com/news?p=2>
[2018-07-11 17:50:14,785]-aspider-INFO  Time usage: 0:00:02.355062
[2018-07-11 17:50:14,785]-aspider-INFO  Spider finished!
```

### TODO

- Custom middleware
- Distributed scraping
- Friendly response

### Contribution

- Pull Request
- Open Issue

### Thanks

- [demiurge](https://github.com/matiasb/demiurge)