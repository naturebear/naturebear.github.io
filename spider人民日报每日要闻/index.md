# 爬虫 人民日报每日要闻


# <center></center>
## 目标网站
{{< figure src="pics/人民日报每日要闻.png" width="60%" title="人民日报每日要闻" >}}

## 爬取数据
### 1. 安装 firefox 与 geckodriver

[下载 firefox](https://www.firefox.com.cn/)
{{< figure src="pics/下载firefox.png" width="80%" title="下载firefox" >}}


[下载 geckodriver](https://github.com/mozilla/geckodriver/releases)
{{< figure src="pics/下载geckodriver.png" width="80%" title="下载geckodriver" >}}

> firefox 安装目录：```C:\Program Files\Mozilla Firefox\```
> 1. 将下载的 ```geckodriver.exe``` 文件保存至 firefox 安装目录下。
> 2. 将 firefox 的安装目录添加至系统环境变量。
> {{< figure src="pics/设置geckodriver.png" width="80%" title="geckodriver.exe 存放路径" >}}



### 2. 创建虚拟环境
```bash
conda create -n env_spider python=3.10
conda activate env_spider
pip install -r requirements.txt
```

```requirements.txt
requests
pandas
bs4
selenium
```

### 3. 代码
> 通过```start_date```和```end_date```设置需要爬取数据的时间段。
> 爬取的数据将保存为 excel 文件，并且数据会逐日累加。

```python
import time
import requests
import pandas as pd
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.firefox.options import Options

start_date = '20240601'
end_date = '20240622'

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
}

if __name__ == '__main__':

    columns = ['日期', '标题', '内容', '链接']
    df_articles = pd.DataFrame(columns=columns)

    # 打开firefox浏览器
    options = Options()
    options.headless = False
    driver = webdriver.Firefox(options=options)
    driver.maximize_window()
    time.sleep(2)

    dts = pd.date_range(start=start_date, end=end_date, freq="D")
    for dt in dts:
        date_str = dt.strftime('%Y%m%d')
        url = 'http://www.people.com.cn/GB/59476/review/' + date_str + '.html'

        # 访问人民日报今日要闻网页
        driver.get(url)
        driver.implicitly_wait(5)

        td_article = driver.find_element(By.CSS_SELECTOR, '#ta_1 > tbody > tr > td')
        li_article_list = td_article.find_elements(By.TAG_NAME, 'li')
        for li_article in li_article_list:
            article = li_article.find_element(By.TAG_NAME, 'a')
            url = article.get_attribute('href')
            title = article.text
            title = title.replace('\n', '').replace('\t', '').replace('\r', '').replace('？', '').replace('！', '').replace('“', '').replace('”', '')

            print(url, title)

            content = ""
            response = requests.get(url, headers=headers)
            response.encoding = 'utf-8'
            soup = BeautifulSoup(response.content, 'html.parser')
            paragraphs = soup.find_all('p')
            for p in paragraphs:
                content += p.get_text()

            df_articles.loc[len(df_articles)] = [date_str, title, content, url]

            time.sleep(1)

        file_name = './今日要闻-' + date_str + '.xlsx'
        df_articles.to_excel(file_name, index=False)

```

## 基于 FineBI 可视化爬取的数据
{{< figure src="pics/FineBI词云图.png" width="100%" title="FineBI词云图" >}}




