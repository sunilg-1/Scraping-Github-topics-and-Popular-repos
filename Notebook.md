Here are the steps we'll follow:

- We're going to scrape https://github.com/topics
- We'll get a list of topics. For each topic, we'll get topic title, topic page URL and topic description.
- For each topic, we'll get the top 25 repositories in the topic from the topic page.
- For each repository, we'll grab the repo name, username, stars and repo URL.
- For each topic we'll create a CSV file in the following format:

```
Username,Repo Name,Stars count,Repo URL
mrdoob, three.js, 69700, https://github.com/mrdoob/three.js
libgdx, libgdx, 18300, https://github.com/libgdx/libgdx
```


```python
# Importing required dependencies
```


```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import os
```


```python
URL = 'https://github.com/topics'
response = requests.get(URL)
response.status_code
```




    200




```python
len(response.text)
```




    152245




```python
soup = BeautifulSoup(response.text, 'html.parser')
```


```python
# name of topic
name_class = "f3 lh-condensed mb-0 mt-1 Link--primary"
topic_names_tag = soup.find_all('p', class_ = name_class)
```


```python
topic_names = []
for tag in topic_names_tag:
    topic_names.append(tag.text)
    
print(topic_names)
```

    ['3D', 'Ajax', 'Algorithm', 'Amp', 'Android', 'Angular', 'Ansible', 'API', 'Arduino', 'ASP.NET', 'Atom', 'Awesome Lists', 'Amazon Web Services', 'Azure', 'Babel', 'Bash', 'Bitcoin', 'Bootstrap', 'Bot', 'C', 'Chrome', 'Chrome extension', 'Command line interface', 'Clojure', 'Code quality', 'Code review', 'Compiler', 'Continuous integration', 'COVID-19', 'C++']
    


```python
# Description of topic
desc_class = "f5 color-fg-muted mb-0 mt-1"
topic_desc_tag = soup.find_all('p', class_ = desc_class) 
```


```python
topic_descs = []
for tag in topic_desc_tag:
    topic_descs.append(tag.text.strip())

topic_descs
```




    ['3D modeling is the process of virtually developing the surface and structure of a 3D object.',
     'Ajax is a technique for creating interactive web applications.',
     'Algorithms are self-contained sequences that carry out a variety of tasks.',
     'Amp is a non-blocking concurrency library for PHP.',
     'Android is an operating system built by Google designed for mobile devices.',
     'Angular is an open source web application platform.',
     'Ansible is a simple and powerful automation engine.',
     'An API (Application Programming Interface) is a collection of protocols and subroutines for building software.',
     'Arduino is an open source hardware and software company and maker community.',
     'ASP.NET is a web framework for building modern web apps and services.',
     'Atom is a open source text editor built with web technologies.',
     'An awesome list is a list of awesome things curated by the community.',
     'Amazon Web Services provides on-demand cloud computing platforms on a subscription basis.',
     'Azure is a cloud computing service created by Microsoft.',
     'Babel is a compiler for writing next generation JavaScript, today.',
     'Bash is a shell and command language interpreter for the GNU operating system.',
     'Bitcoin is a cryptocurrency developed by Satoshi Nakamoto.',
     'Bootstrap is an HTML, CSS, and JavaScript framework.',
     'A bot is an application that runs automated tasks over the Internet.',
     'C is a general purpose programming language that first appeared in 1972.',
     'Chrome is a web browser from the tech company Google.',
     'Chrome extensions enable users to customize the Chrome browsing experience.',
     'A CLI, or command-line interface, is a console that helps users issue commands to a program.',
     'Clojure is a dynamic, general-purpose programming language.',
     'Automate your code review with style, quality, security, and test‑coverage checks when you need them.',
     'Ensure your code meets quality standards and ship with confidence.',
     'Compilers are software that translate higher-level programming languages to lower-level languages (e.g. machine code).',
     'Automatically build and test your code as you push it upstream, preventing bugs from being deployed to production.',
     'The coronavirus disease 2019 (COVID-19) is an infectious disease caused by SARS-CoV-2.',
     'C++ is a general purpose and object-oriented programming language.']




```python
# link for topic
link_class = "no-underline flex-1 d-flex flex-column"
topic_link_tags = soup.find_all('a', class_ = link_class)
```


```python
topic_links = []
for link in topic_link_tags:
    topic_links.append("https://github.com" + link['href'])
    
topic_links
```




    ['https://github.com/topics/3d',
     'https://github.com/topics/ajax',
     'https://github.com/topics/algorithm',
     'https://github.com/topics/amphp',
     'https://github.com/topics/android',
     'https://github.com/topics/angular',
     'https://github.com/topics/ansible',
     'https://github.com/topics/api',
     'https://github.com/topics/arduino',
     'https://github.com/topics/aspnet',
     'https://github.com/topics/atom',
     'https://github.com/topics/awesome',
     'https://github.com/topics/aws',
     'https://github.com/topics/azure',
     'https://github.com/topics/babel',
     'https://github.com/topics/bash',
     'https://github.com/topics/bitcoin',
     'https://github.com/topics/bootstrap',
     'https://github.com/topics/bot',
     'https://github.com/topics/c',
     'https://github.com/topics/chrome',
     'https://github.com/topics/chrome-extension',
     'https://github.com/topics/cli',
     'https://github.com/topics/clojure',
     'https://github.com/topics/code-quality',
     'https://github.com/topics/code-review',
     'https://github.com/topics/compiler',
     'https://github.com/topics/continuous-integration',
     'https://github.com/topics/covid-19',
     'https://github.com/topics/cpp']




```python
# create a df combining all above
topic_df = {'topic_names': topic_names,
            'topic_descs': topic_descs,
            'topic_links': topic_links}
```


```python
df = pd.DataFrame(topic_df)
```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>topic_names</th>
      <th>topic_descs</th>
      <th>topic_links</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3D</td>
      <td>3D modeling is the process of virtually develo...</td>
      <td>https://github.com/topics/3d</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ajax</td>
      <td>Ajax is a technique for creating interactive w...</td>
      <td>https://github.com/topics/ajax</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algorithm</td>
      <td>Algorithms are self-contained sequences that c...</td>
      <td>https://github.com/topics/algorithm</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amp</td>
      <td>Amp is a non-blocking concurrency library for ...</td>
      <td>https://github.com/topics/amphp</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Android</td>
      <td>Android is an operating system built by Google...</td>
      <td>https://github.com/topics/android</td>
    </tr>
  </tbody>
</table>
</div>



### Popular Repos for particular topics.


```python
# Lets check for a example topic
first_topic = topic_links[0]
```


```python
# loading html page 
response = requests.get(first_topic)
```


```python
len(response.text)
```




    451083




```python
# parsing page
soup2 = BeautifulSoup(response.text, 'html.parser')
```


```python
# print(soup2.prettify())
```

we can pick `p` tags with the `class` ...
which has the repo username, repo_name, and url 


```python
repo_tags = soup2.find_all('h3', {'class':'f3 color-fg-muted text-normal lh-condensed'})
```


```python
user_tags = repo_tags[0].find_all('a')
```


```python
user_tags[0].text.strip()
```




    'mrdoob'




```python
user_tags[1].text.strip()
```




    'three.js'




```python
base_url = "https://github.com"
base_url + user_tags[1]['href']
```




    'https://github.com/mrdoob/three.js'



span and class will get us the total starcount


```python

stars_tag = soup2.find_all('span', {'class':'Counter js-social-count'})
```


```python
def star_tag(stars_tag):
    star_count = stars_tag['title'].replace(',','')
    return int(star_count)
```


```python
star_tag(stars_tag[0])
```




    85695



**Writing a function to combine all of the above**


```python
def repo_info(h3_tags, str_tag):
    a_tags = h3_tags.find_all('a')
    username = a_tags[0].text.strip()
    repo_name = a_tags[1].text.strip()
    stars = star_tag(str_tag)
    repo_url = base_url + a_tags[1]['href']
    return username, repo_name, stars, repo_url
    
```


```python
repo_info(repo_tags[0], stars_tag[0])
```




    ('mrdoob', 'three.js', 85695, 'https://github.com/mrdoob/three.js')




```python
star_tag(stars_tag[0])
```




    85695




```python
# we can get the remaining repositories.
topic_repo_dict = {'username': [],
         'repo name': [],
         'stars count': [],
         'repo url':[]}


for i in range(len(repo_tags)):
    repo = repo_info(repo_tags[i], stars_tag[i])
    topic_repo_dict['username'].append(repo[0])
    topic_repo_dict['repo name'].append(repo[1])
    topic_repo_dict['stars count'].append(repo[2])
    topic_repo_dict['repo url'].append(repo[3])
    
```


```python
topic_df = pd.DataFrame(topic_repo_dict)
```


```python
topic_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>username</th>
      <th>repo name</th>
      <th>stars count</th>
      <th>repo url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>mrdoob</td>
      <td>three.js</td>
      <td>85695</td>
      <td>https://github.com/mrdoob/three.js</td>
    </tr>
    <tr>
      <th>1</th>
      <td>libgdx</td>
      <td>libgdx</td>
      <td>20533</td>
      <td>https://github.com/libgdx/libgdx</td>
    </tr>
    <tr>
      <th>2</th>
      <td>pmndrs</td>
      <td>react-three-fiber</td>
      <td>19727</td>
      <td>https://github.com/pmndrs/react-three-fiber</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BabylonJS</td>
      <td>Babylon.js</td>
      <td>18442</td>
      <td>https://github.com/BabylonJS/Babylon.js</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ssloy</td>
      <td>tinyrenderer</td>
      <td>14763</td>
      <td>https://github.com/ssloy/tinyrenderer</td>
    </tr>
    <tr>
      <th>5</th>
      <td>aframevr</td>
      <td>aframe</td>
      <td>14584</td>
      <td>https://github.com/aframevr/aframe</td>
    </tr>
    <tr>
      <th>6</th>
      <td>lettier</td>
      <td>3d-game-shaders-for-beginners</td>
      <td>13750</td>
      <td>https://github.com/lettier/3d-game-shaders-for...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>FreeCAD</td>
      <td>FreeCAD</td>
      <td>12261</td>
      <td>https://github.com/FreeCAD/FreeCAD</td>
    </tr>
    <tr>
      <th>8</th>
      <td>metafizzy</td>
      <td>zdog</td>
      <td>9395</td>
      <td>https://github.com/metafizzy/zdog</td>
    </tr>
    <tr>
      <th>9</th>
      <td>CesiumGS</td>
      <td>cesium</td>
      <td>9299</td>
      <td>https://github.com/CesiumGS/cesium</td>
    </tr>
    <tr>
      <th>10</th>
      <td>timzhang642</td>
      <td>3D-Machine-Learning</td>
      <td>8359</td>
      <td>https://github.com/timzhang642/3D-Machine-Lear...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>isl-org</td>
      <td>Open3D</td>
      <td>7454</td>
      <td>https://github.com/isl-org/Open3D</td>
    </tr>
    <tr>
      <th>12</th>
      <td>a1studmuffin</td>
      <td>SpaceshipGenerator</td>
      <td>7213</td>
      <td>https://github.com/a1studmuffin/SpaceshipGener...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>blender</td>
      <td>blender</td>
      <td>6662</td>
      <td>https://github.com/blender/blender</td>
    </tr>
    <tr>
      <th>14</th>
      <td>domlysz</td>
      <td>BlenderGIS</td>
      <td>5641</td>
      <td>https://github.com/domlysz/BlenderGIS</td>
    </tr>
    <tr>
      <th>15</th>
      <td>openscad</td>
      <td>openscad</td>
      <td>5039</td>
      <td>https://github.com/openscad/openscad</td>
    </tr>
    <tr>
      <th>16</th>
      <td>spritejs</td>
      <td>spritejs</td>
      <td>4982</td>
      <td>https://github.com/spritejs/spritejs</td>
    </tr>
    <tr>
      <th>17</th>
      <td>google</td>
      <td>model-viewer</td>
      <td>4781</td>
      <td>https://github.com/google/model-viewer</td>
    </tr>
    <tr>
      <th>18</th>
      <td>jagenjo</td>
      <td>webglstudio.js</td>
      <td>4734</td>
      <td>https://github.com/jagenjo/webglstudio.js</td>
    </tr>
    <tr>
      <th>19</th>
      <td>tensorspace-team</td>
      <td>tensorspace</td>
      <td>4716</td>
      <td>https://github.com/tensorspace-team/tensorspace</td>
    </tr>
  </tbody>
</table>
</div>



We got the popular repositories for first topic (3D).

### Now we can get top repositories for each topic

Lets write some more functions to get repos for different topics and store them in a file.


```python

def topic_page(topic_url):
    # request page content
    response = requests.get(topic_url)
    # parseusing bs4
    topic_doc = BeautifulSoup(response.text, 'html.parser')
    
    return topic_doc

def repo_info(h3_tags, str_tag):
    a_tags = h3_tags.find_all('a')
    username = a_tags[0].text.strip()
    repo_name = a_tags[1].text.strip()
    stars = star_tag(str_tag)
    repo_url = base_url + a_tags[1]['href']
    return username, repo_name, stars, repo_url
    

def topic_content(topic_doc):

    repo_tags = topic_doc.find_all('h3', {'class':'f3 color-fg-muted text-normal lh-condensed'})
    
    stars_tag = topic_doc.find_all('span', {'class':'Counter js-social-count'})
    
    
    topic_repo_dict = {'username': [],
             'repo name': [],
             'stars count': [],
             'repo url':[]
    }


    for i in range(len(repo_tags)):
        repo = repo_info(repo_tags[i], stars_tag[i])
        topic_repo_dict['username'].append(repo[0])
        topic_repo_dict['repo name'].append(repo[1])
        topic_repo_dict['stars count'].append(repo[2])
        topic_repo_dict['repo url'].append(repo[3])
        
    return pd.DataFrame(topic_repo_dict)

def scrape_topic(topic_url, path):
    if os.path.exists(path):
        print("The file {} already exists. Skipping...".format(path))
        return
    topic_df = topic_content(topic_page(topic_url))
    topic_df.to_csv(path, index=None)
```


```python
def get_topic_titles(soup):
    name_class = "f3 lh-condensed mb-0 mt-1 Link--primary"
    topic_names_tag = soup.find_all('p', class_ = name_class)
    topic_titles = []
    for tag in topic_names_tag:
        topic_titles.append(tag.text)
    return topic_titles

def get_topic_descs(soup):
    # Description of topic
    desc_class = "f5 color-fg-muted mb-0 mt-1"
    topic_desc_tag = soup.find_all('p', class_ = desc_class) 
    topic_descs = []
    for tag in topic_desc_tag:
        topic_descs.append(tag.text.strip())
    return topic_descs

def get_topic_urls(soup):
    # link for topic
    link_class = "no-underline flex-1 d-flex flex-column"
    topic_link_tags = soup.find_all('a', class_ = link_class)
    topic_links = []
    for link in topic_link_tags:
        topic_links.append("https://github.com" + link['href'])

    return topic_links
    

def scrape_topics():
    topics_url = 'https://github.com/topics'
    response = requests.get(topics_url)
    if response.status_code != 200:
        raise Exception('Failed to load page {}'.format(topic_url))
    topics_dict = {
        'title': get_topic_titles(soup),
        'description': get_topic_descs(soup),
        'url': get_topic_urls(soup)
    }
    return pd.DataFrame(topics_dict)
```


```python
def scrape_topics_repos():
    print('Scraping list of topics')
    topics_df = scrape_topics()
    
    os.makedirs('data', exist_ok=True)
    for index, row in topics_df.iterrows():
        print('Scraping top repositories for "{}"'.format(row['title']))
        scrape_topic(row['url'], 'data/{}.csv'.format(row['title']))
```


```python
scrape_topics_repos()
```

    Scraping list of topics
    Scraping top repositories for "3D"
    Scraping top repositories for "Ajax"
    Scraping top repositories for "Algorithm"
    Scraping top repositories for "Amp"
    Scraping top repositories for "Android"
    Scraping top repositories for "Angular"
    Scraping top repositories for "Ansible"
    Scraping top repositories for "API"
    Scraping top repositories for "Arduino"
    Scraping top repositories for "ASP.NET"
    Scraping top repositories for "Atom"
    Scraping top repositories for "Awesome Lists"
    Scraping top repositories for "Amazon Web Services"
    Scraping top repositories for "Azure"
    Scraping top repositories for "Babel"
    Scraping top repositories for "Bash"
    Scraping top repositories for "Bitcoin"
    Scraping top repositories for "Bootstrap"
    Scraping top repositories for "Bot"
    Scraping top repositories for "C"
    Scraping top repositories for "Chrome"
    Scraping top repositories for "Chrome extension"
    Scraping top repositories for "Command line interface"
    Scraping top repositories for "Clojure"
    Scraping top repositories for "Code quality"
    Scraping top repositories for "Code review"
    Scraping top repositories for "Compiler"
    Scraping top repositories for "Continuous integration"
    Scraping top repositories for "COVID-19"
    Scraping top repositories for "C++"
    


```python

```
