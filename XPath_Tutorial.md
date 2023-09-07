# DreamLab23

# XPath Web Scraping Activity

## Essential Resources

[w3schools HTML overview](https://www.w3schools.com/html/)

[w3schools XPath Tutorial](https://www.w3schools.com/xml/xpath_intro.asp)

XPath is a query language used to select HTML / XML information. We can use this to select information on a webpage, such as hyperlinks, and copy/paste that information into a datasheet. You can later build on your XPath knowledge to use programming languages, like Python or R, to automatically scrape a website. 

## How does HTML work?

XML and HTML are nested data structures. This structure allows for more complex data than can be stored in spreadsheets, since we can establish a hierarchy. 

Using this as an example:

```html
<a href='https://library.udel.edu'>Library, Museums and Press</a>
```

This is what a hyperlink looks like in HTML. You can right click on links on a webpage and select "Inspect Element" (or similar, depending on the browser) to see something like this. 

### Vocabulary

element name: the ```a``` is the element. Between the ```<>``` is all the information you need about this element on the page. 

node: everything inside the element. This is also called a root element. 

opening and closing tags: ```<a``` and ```</a>```

attributes: usually come in key/value pairs like ```name="value"```. here, the ```href='https://library.udel.edu'``` is our attribute. 

Because of the nested structure, we will be dealing with a ```parent/child``` structure. Everything in HTML must a single 'root' element -- you can see this in the ```<html>``` tag on a webpage. For example: 

```html
<cats>
	<calicos>
    	<name>Phoebe</name>
    </calicos>
    <tabbys>
    	<name>Zeus</name>
    </tabbys>
</cats>
```

The **root** element is ```<cats>```. ```<calicos>``` and ```<tabbys>``` are both the **children** elements of ```<cats>```. The **values** of the ```<name>``` element are Phoebe and Zeus. 

Together this structure is called a **tree**. 

## Using XPath to select information

We'll use XPath to describe a location in the tree, in order to select that information. Queries are formulated using ```/```. In the previous example, I could select ```<calicos>``` using: 

```/cats/calicos```

If you want to select multiple elements, you don't have to specify the full path. You can use ```//``` to have the query search at any level of the tree (instead of starting at the root). 

```//name```

This would select both "Phoebe" and "Zeus". 

| Expression                        | Description                                                  |
| :-------------------------------- | :----------------------------------------------------------- |
| *nodename*                        | Selects all nodes with the name "*nodename*"                 |
| /                                 | Selects from the root node                                   |
| //                                | Selects nodes in the document from the current node that match the selection no matter where they are |
| .                                 | Selects the current node                                     |
| ..                                | Selects the parent of the current node                       |
| @                                 | Selects attributes                                           |
| element[@attribute = 'something'] | Select an attribute with a specific value.                   |
| *                                 | Wildcard -- selects any node                                 |



### Using XPath to extract information

For this activity, you'll need the [XPath Helper Chrome extension](https://chrome.google.com/webstore/detail/xpath-helper/hgimnogjllphhhkhlmebbmlgjoejdpjl) and a Chromium-based browser like Brave or Google Chrome. Take a minute to get that installed. XPath Helper lets you test out queries on a webpage, and you can use it to select what you want to copy / paste into a spreadsheet. Go ahead and open a spreadsheet to add your data to as well. 

As an example, let's look at the [Wine Spectator Top 100 list](https://top100.winespectator.com/lists/) from 2021.  

This page is actually well-formatted into a table, so we could potentially copy / paste the information, but that might cause problems when putting it into a spreadsheet format. So we'll use XPath to extract the information on the page for easy import into a spreadsheet. 

Right click on the first wine name (**Dominus Estate** Napa Valley) and select "Inspect Element" or "Inspect" (depends on your browser).

You'll see: 

```html
<span class="wineName">
    <span class="sort-text">Dominus Estate</span> 
    " Napa Valley "
    <span class="vintageNumber">2019</span> 
    <span class="plus">+</span>
    <span class="minus">-</span>          
</span>
```

The element name is ```span```. We can specify which ```class``` we want using the following query: 

```xquery
//span[@class='wineName']
```

This will give us everything in that tree, including the + and - for the interactive nature of the webpage. We could clean that up in OpenRefine or something, but we can also clean up our query to select just what we want. 

```xquery
//span[@class='sort-text']
```

"sort-text" is the attribute where the name of the vineyard is stored. We can use this query to select that separately, to paste into a separate column on our spreadsheet. 

So now we have a spreadsheet that looks like this: 

| Vineyard                           | Type | Year |
| ---------------------------------- | ---- | ---- |
| Dominus Estate                     |      |      |
| Château Pichon Longueville Lalande |      |      |
| Heitz                              |      |      |

So how can we extract the region / wine type? For the first wine, 'Napa Valley' is just kind of hanging out inside the wineName element. You can tell because there are no <> </> enclosing that value. We can use the ```text()``` function in XPath to extract that information.

```xquery
//span[@class='wineName']/text()
```

*Note: You may have an issue wherein there are extra carriage returns in your results, making the wine type not paste cleanly. You can fix this in Excel by selecting the two types of cell that contain a carriage return, using Find & Replace to replace that sequence of spaces with nothingness, then using Find & Select > Go to Special to select all the blanks in the range. Then you can delete them.*

Now we have in our spreadsheet: 

| Vineyard                           | Type                                          | Year |
| ---------------------------------- | --------------------------------------------- | ---- |
| Dominus Estate                     | Napa Valley                                   |      |
| Château Pichon Longueville Lalande | Pauillac                                      |      |
| Heitz                              | Cabernet Sauvignon Oakville Martha's Vineyard |      |

See if you can select and add the "Year / Vintage" values on your own, and add them to your spreadsheet.

### Next Steps

Here are a few webpages we can practice scraping from using XPath. See if you can get some data out of these using XPath! 

- [Best Fantasy Movies from Rotten Tomatoes](https://editorial.rottentomatoes.com/guide/best-fantasy-movies-of-all-time/) (but come on how is HP higher than LOTR??)
- Some stuff from the [Jeopardy Archive](https://j-archive.com/listseasons.php)
- List of [Pokemon](https://pokemon.fandom.com/wiki/List_of_Pok%C3%A9mon) from Wikipedia
- [Middle Earth Recipes](http://www.lotrscrapbook.bookloaf.net/other/recipes.html)

If you're ever lost or aren't sure how to formulate a query, check out the [w3schools XPath Tutorial](https://www.w3schools.com/xml/xpath_intro.asp). 

You can use what you just learned to extract information from webpages using XPath, or you can integrate XPath queries into a script to programmatically access data on a webpage. Some challenges with this method include "anti-crawler" technologies that certain websites have, as well as some basic knowledge of a programming language. 

This tutorial for [Python and XPath](https://medium.com/analytics-vidhya/web-scraping-using-python-and-selenium-xpath-f315f63ac229) may help, as well as lessons on web scraping from the [Programming Historian](https://programminghistorian.org/en/lessons/?topic=web-scraping). [Data Miner](https://dataminer.io/) is a relatively new extension that operates similarly to XPath but with a GUI. 

### Credits

This tutorial was adapted from [Elizabeth Wickes' XPath tutorial](https://github.com/elliewix/IS-452-Spring2018/blob/master/Lectures/Week-12-XPath-A.ipynb) and the [w3schools XPath Tutorial](https://www.w3schools.com/xml/xpath_intro.asp). 
