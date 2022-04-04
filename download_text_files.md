 ---
 layout: wide_default
 ---   

```python
import glob
import os
from time import sleep

import pandas as pd
from sec_edgar_downloader import Downloader
from tqdm import tqdm
```


```python
# places to put files - best practice chapter 2!

os.makedirs("input", exist_ok=True)
os.makedirs("10k_files", exist_ok=True)

dl = Downloader('10k_files')
```


```python
# download sample (somewhat simplistic option!)

sample_csv = "input/sp500_firms.csv"
if not os.path.exists(sample_csv):
    url = "https://en.wikipedia.org/wiki/List_of_S%26P_500_companies"
    sample = pd.read_html(url)[0]
    sample.to_csv(sample_csv, index=False)
else:
    sample = pd.read_csv(sample_csv)
```


```python
for firm in tqdm(sample['Symbol'][:10]):

    # an overly simply option that might be too restrictive on bolder projects
    # just look to see if we have a folder for that firm's 10-K and assume
    # that means we have the 10-k for the right date (we could write a way to
    # verify that)
    firm_folder = "10k_files/sec-edgar-filings/" + firm
    
    html_files = glob.glob(firm_folder  + '/**/*.html', recursive=True)
    if len(html_files) == 0:
        dl.get("10-K", firm, amount=1, after="2019-03-01", before="2020-03-21")

    # if I have downloaded txt files inside firm_folder:
    # get list of .txt in the firms folder
    # print them! once you're printing them, you can just...
    # delete them
    for f in glob.glob(firm_folder  + '/**/*.txt', recursive=True):
        os.remove(f)
```
