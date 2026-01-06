# Python Search Spider, Page Ranker, and Visualizer

A set of programs that emulate core search engine functions: web crawling, page ranking, and link visualization. All data is stored in a SQLite database (`spider.sqlite`), which can be deleted at any time to restart the process.

## Prerequisites

Install the SQLite Browser to view and modify the database:  
[http://sqlitebrowser.org/](http://sqlitebrowser.org/)

### Windows Users

Windows has difficulty displaying UTF-8 characters in the console. Run this command in each console window before executing the scripts:

```
chcp 65001
```

More info: [Unicode characters in Windows command line](http://stackoverflow.com/questions/388490/unicode-characters-in-windows-command-line-how)

---

## Programs Overview

| Script | Purpose |
|--------|---------|
| `spider.py` | Crawls websites and stores pages/links in the database |
| `spdump.py` | Dumps database contents showing page rank data |
| `sprank.py` | Runs the PageRank algorithm on crawled pages |
| `spreset.py` | Resets all page ranks to 1.0 |
| `spjson.py` | Exports data to JSON for visualization |

---

## Usage

### 1. Crawling Web Pages (`spider.py`)

The spider crawls a website and records pages and their links.

```bash
# Clear existing data (optional)
rm spider.sqlite      # Mac/Linux
del spider.sqlite     # Windows

# Run the spider
python3 spider.py     # Mac/Linux
python spider.py      # Windows
```

**Example session:**

```
Enter web url or enter: http://www.dr-chuck.com/
['http://www.dr-chuck.com']
How many pages: 2
1 http://www.dr-chuck.com/ 12
2 http://www.dr-chuck.com/csev-blog/ 57
How many pages:
```

**Key behaviors:**
- The spider won't re-crawl pages already in the database
- On restart, it picks a random non-crawled page to continue from
- Each run is additive—pages accumulate across sessions
- You can have multiple starting URLs ("webs") in the same database

### 2. Viewing Database Contents (`spdump.py`)

Displays pages that have at least one incoming link.

```bash
python3 spdump.py     # Mac/Linux
python spdump.py      # Windows
```

**Output format:**

```
(incoming_links, old_rank, new_rank, page_id, url)
```

**Example output:**

```
(5, None, 1.0, 3, 'http://www.dr-chuck.com/csev-blog')
(3, None, 1.0, 4, 'http://www.dr-chuck.com/dr-chuck/resume/speaking.htm')
(1, None, 1.0, 2, 'http://www.dr-chuck.com/csev-blog/')
(1, None, 1.0, 5, 'http://www.dr-chuck.com/dr-chuck/resume/index.htm')
4 rows.
```

### 3. Running PageRank (`sprank.py`)

Calculates page rank scores based on link structure.

```bash
python3 sprank.py     # Mac/Linux
python sprank.py      # Windows
```

**Example session:**

```
How many iterations: 2
1 0.546848992536
2 0.226714939664
[(1, 0.559), (2, 0.659), (3, 0.985), (4, 2.135), (5, 0.659)]
```

**Tips:**
- Run multiple times to refine rankings—each run builds on previous calculations
- The output shows average change per page for each iteration
- Initial runs show large changes; rankings stabilize over time
- You can alternate between spidering more pages and running PageRank

### 4. Resetting Page Ranks (`spreset.py`)

Resets all page ranks to 1.0 without re-crawling.

```bash
python3 spreset.py    # Mac/Linux
python spreset.py     # Windows
```

```
All pages set to a rank of 1.0
```

### 5. Generating Visualization (`spjson.py`)

Exports the top-ranked pages to JSON for browser visualization.

```bash
python3 spjson.py     # Mac/Linux
python spjson.py      # Windows
```

```
Creating JSON output on spider.js...
How many nodes? 30
Open force.html in a browser to view the visualization
```

---

## Visualization

Open `force.html` in a web browser to see an interactive graph of pages and links.

**Features:**
- Automatic force-directed layout of nodes and links
- Click and drag nodes to reposition them
- Double-click a node to view its URL

**Updating the visualization:**  
After running other utilities, re-run `spjson.py` and refresh your browser to see updated data.

*Visualization powered by [D3.js Force Layout](http://mbostock.github.com/d3/)*

---

## Typical Workflow

1. **Crawl** — Run `spider.py` to collect pages
2. **Rank** — Run `sprank.py` to calculate page ranks (run until values converge)
3. **Visualize** — Run `spjson.py` and open `force.html`
4. **Iterate** — Add more pages with `spider.py`, then re-rank and re-visualize