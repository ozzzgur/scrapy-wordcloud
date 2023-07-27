# Scraping Top 9 Movie’s Reviews of All Time and Visualizing Them on WordCloud

In this post, using Python's "scrapy" and "wordcloud" libraries, I will first save the audience reviews of the top 9 movies to a JSON file and then visualize these comments with a word cloud. A word cloud is a visualization of word frequency in a given text as a weighted list. (Wikipedia)

## WordCloud of This Post

The operations performed in this article are presented as follows in order of priority:
1. By webscraping, we will save the user reviews of the top 9 movies to a "json" file.
2. After converting this saved file to a "Dataframe", we will convert each review of a movie into a Python list.
3. We will process reviews that we have translated into a list in the wordcloud library and visualize them all in a single image as above.

## Web Scraping

I believe that explaining the procedures and scrapy code as someone who prefers to learn rapidly would be more valuable than going into long explanations about this issue. The most important thing to remember is that the scraping processing codes will be on the console, but scrapy will be run on the terminal. So, first and foremost, let us design a project for our scrapy spider.

1. `scrapy startproject topwordcloud`
2. `cd topwordcloud`
3. `scrapy genspider review_spider imdb.com`

The first step launches a scrapy-based project called topwordcloud.
The second step is a DOS procedure, and the CD (Change Directory) command allows you to get to that folder.
The last part, review_spider.py, is also contained in the folder that you have entered.

[Link to the medium post](https://medium.com/@ozzgur.sanli/scraping-top-9-movies-reviews-of-all-time-and-visualizing-them-on-wordcloud-f2e06ba4308a)


