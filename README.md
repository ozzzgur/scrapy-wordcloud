Scraping Top 9 Movie’s Reviews of All Time and Visualizing Them on WordCloud 
https://medium.com/@ozzgur.sanli/scraping-top-9-movies-reviews-of-all-time-and-visualizing-them-on-wordcloud-f2e06ba4308a

In this post, using python’s “scrapy” and “wordcloud” libraries, I will first save the audience reviews of the top 9 movies to a json file and then visualize these comments with word cloud. Word cloud is a visualization of word frequency in a given text as a weighted list. (Wikipedia)


Wordcloud of This Post
The operations performed in this article are presented as follows in order of priority;
1.By webscraping, we will save the user reviews of the top 9 movies to a “json” file.
2.After converting this saved file to “Dataframe”, we will convert each reviews of movie to a python list.
3.We will process reviews that we have translated into a list in the wordcloud library and visualize them all in a single image as above.

Web Scraping
I believe that explaining the procedures and scrapy code as someone who prefers to learn rapidly would be more valuable than going into long explanations about this issue. The most important thing to remember is that the scraping processing codes will be on the console, but scrapy will be run on the terminal. So, first and foremost, let us design a project for our scrapy spider.

1. scrapy startproject topwordcloud
2. cd topwordcloud
3. scrapy genspider review_spider imdb.com
The first step launches a scrapy-based project called topwordcloud.
The second step is a DOS procedure, and the CD (Change Directory) command allows you to get to that folder.
The last part, review_spider.py, is also contained in the folder that you have entered.


After all 3 steps
Now we can write down what we want to do to our spider. The name of our spider “review_spider.py ”. Open review_spider.py that we create.

import scrapy
import json


class IMDbReviewsSpider(scrapy.Spider):
    name = "review_spider"
    start_urls = [
        ("The Shawshank Redemption", "https://www.imdb.com/title/tt0111161/reviews?ref_=tt_urv"),
        ("The Godfather", "https://www.imdb.com/title/tt0068646/reviews?ref_=tt_urv"),
        ("The Dark Knight", "https://www.imdb.com/title/tt0468569/reviews?ref_=tt_urv"),
        ("The Godfather: Part II", "https://www.imdb.com/title/tt0071562/reviews?ref_=tt_urv"),
        ("12 Angry Men", "https://www.imdb.com/title/tt0050083/reviews?ref_=tt_urv"),
        ("Schindler's List", "https://www.imdb.com/title/tt0108052/reviews?ref_=tt_urv"),
        ("The Lord of the Rings: The Return of the King", "https://www.imdb.com/title/tt0167260/reviews?ref_=tt_urv"),
        ("Pulp Fiction", "https://www.imdb.com/title/tt0110912/reviews?ref_=tt_urv"),
        ("The Lord of the Rings: The Fellowship of the Ring", "https://www.imdb.com/title/tt0120737/reviews?ref_=tt_urv")

    ]

    def start_requests(self):
        for title, url in self.start_urls:
            yield scrapy.Request(url, callback=self.parse, meta={"title": title})

    def parse(self, response):
        title = response.meta["title"]

        # Extracting review details
        review_blocks = response.css("div.lister-item-content")

        for block in review_blocks:
            review = block.css("div.text.show-more__control::text").get()

            # Creating a dictionary for the extracted data
            review_data = {
                "title": title,
                "review": review
            }

            # Yielding the extracted data
            yield review_data
# Follow pagination link if available
        next_page_url = response.css("a.lister-page-next::attr(href)").get()
        if next_page_url:
            yield response.follow(next_page_url, self.parse, meta={"title": title})
From beginning we import necessary modules, “scrapy” for web scraping and “json” for save it as a json file.

class IMDbReviewsSpider(scrapy.Spider):
    name = "review_spider"
We define our spider as a class and it inherits from scrapy.Spider. We set the name of spider “review_spider”, later we call it from Terminal.

start_urls = [
        ("The Shawshank Redemption", "https://www.imdb.com/title/tt0111161/reviews?ref_=tt_urv"),
        ("The Godfather", "https://www.imdb.com/title/tt0068646/reviews?ref_=tt_urv"),
        ("The Dark Knight", "https://www.imdb.com/title/tt0468569/reviews?ref_=tt_urv"),
        ("The Godfather: Part II", "https://www.imdb.com/title/tt0071562/reviews?ref_=tt_urv"),
        ("12 Angry Men", "https://www.imdb.com/title/tt0050083/reviews?ref_=tt_urv"),
        ("Schindler's List", "https://www.imdb.com/title/tt0108052/reviews?ref_=tt_urv"),
        ("The Lord of the Rings: The Return of the King", "https://www.imdb.com/title/tt0167260/reviews?ref_=tt_urv"),
        ("Pulp Fiction", "https://www.imdb.com/title/tt0110912/reviews?ref_=tt_urv"),
        ("The Lord of the Rings: The Fellowship of the Ring", "https://www.imdb.com/title/tt0120737/reviews?ref_=tt_urv")

    ]
We could have pulled these addresses using scrapy, but we don’t want to violate imdb terms of policy. Instead, we enter the addresses that the spider needs to as a start_urls list in tuple format.

def start_requests(self):
        for title, url in self.start_urls:
            yield scrapy.Request(url, callback=self.parse, meta={"title": title})
To produce the initial requests, the start_requests() method is rewritten. It loops through the start_urls list and generates a scrapy request. For each URL, a request object is created. The meta argument is used to store the movie title as metadata for subsequent usage in the parsing process. Each request is then yielded to start the scraping and callback to the parse() method to handle the response.

def parse(self, response):
        title = response.meta["title"]

        # Extracting review details
        review_blocks = response.css("div.lister-item-content")

        for block in review_blocks:
            review = block.css("div.text.show-more__control::text").get()
The parse() method is assigned in to deal with the responses received from each request. The movie title is extracted from the response’s metadata, which was given via the meta parameter in the start_requests() method. The review_blocks uses a CSS selector (div.lister-item-content) to select the review blocks from the HTML response. It represents the container element that holds each review on the page. For loop uses CSS selectors inside the loop to extract the review content for each review block. The value of the review text is assigned to the review variable.

  review_data = {
                "title": title,
                "review": review
            }

            # Yielding the extracted data
            yield review_data
We create review_data dict, contains “title” and “review”. The extracted data is then returned by yielding the dictionary.

next_page_url = response.css("a.lister-page-next::attr(href)").get()
        if next_page_url:
            yield response.follow(next_page_url, self.parse, meta={"title": title})
By using this code, we select the URL of the next page using a CSS selector (a.lister-page-next::attr(href)). If a next page URL is identified, it uses response to follow the URL.follow() is called with the callback function parse(). The meta option is used to provide the movie title as metadata to the next page request.

After writing this whole code blog, it’s time to start our spider for scraping. We are typing following code into the terminal. By the help of “review_spider.py”, the review we have collected will be saved as a json file.

scrapy crawl review_spider -o reviews.json
With this code we wrote to the terminal, “reviews.json” was created. I will share json file on my github account.

2. WordCloud

Since our “reviews.json” file has been created, we can create our wordclouds from this file, we open new python file named “reviews_cloud.py”. First of all, I’ll be showing the entire code below again, and then I’ll explain the process preformed,line by line


from wordcloud import WordCloud,STOPWORDS
import pandas as pd
import matplotlib.pyplot as plt
from stop_words import get_stop_words
df = pd.read_json("topwordcloud/reviews.json")
top9movies = ["The Shawshank Redemption","The Godfather","The Dark Knight","The Godfather: Part II","12 Angry Men",\
              "Schindler's List","The Lord of the Rings: The Return of the King","Pulp Fiction","The Lord of the Rings: The Fellowship of the Ring"]
word = {}
for i, movie in enumerate(top9movies):
    reviews = df.loc[df["title"] == movie, "review"].tolist()
    word[i] = reviews

custom_stopwords = ["film","movie","best","films"]
stopwords = set(STOPWORDS)
stopwords.update(get_stop_words('en'))

for movie in top9movies:
    words = movie.split()
    custom_stopwords.extend(words)

stopwords.update(custom_stopwords)

fig, axes = plt.subplots(3, 3, figsize=(12, 12))
fig.suptitle('Word Clouds for Top 9 Movies', fontsize=16)

for i, movie in enumerate(top9movies):
    plainwords = " ".join(word[i])

    word_cloud = WordCloud(width=800, height=600, stopwords=stopwords, collocations=False,
                           max_words=200, min_word_length=4, background_color='black').generate(plainwords)

    ax = axes[i // 3, i % 3]
    ax.imshow(word_cloud, interpolation='bilinear')
    ax.axis("off")
    ax.set_title(movie, fontsize=10, fontweight='bold')
plt.subplots_adjust(wspace=0.1, hspace=0.3)
plt.savefig('wordclouds.jpg', dpi=300)

plt.show()
We import necessary modules to generate word cloud. Stop_words library is used for cleaning reviews from “the, an, and etc.” words. It is called stopwords. By using read_json() function we create a DataFrame called df. “top9movies” contains titles of top 9 movies we scrapt, and we use this list for loops.

In [5]: example = df.loc[df["title"] == "The Shawshank Redemption","review"].tolist()
In [6]: example[0]
Out[6]: 'The Shawshank Redemption is written and directed by Frank Darabont. It is an adaptation of the Stephen King novella Rita Hayworth and Shawshank Redemption. Starring Tim Robbins and Morgan Freeman, the film portrays the story of Andy Dufresne (Robbins), a banker who is sentenced to two life sentences at Shawshank State Prison for apparently murdering his wife and her lover. Andy finds it tough going but finds solace in the friendship he forms with fellow inmate Ellis "Red" Redding (Freeman). While things start to pick up when the warden finds Andy a prison job more befitting his talents as a banker. However, the arrival of another inmate is going to vastly change things for all of them.'
When we want to do the above code for all movie reviews in a loop. It will turn into the following code.

word = {}
for i, movie in enumerate(top9movies):
    reviews = df.loc[df["title"] == movie, "review"].tolist()
    word[i] = reviews
We are updating stopwords because we don’t want some of the frequently used words in reviews.Also English stopwords words and top 9 movie names to be in the word cloud of movie reviews.

custom_stopwords = ["film","movie","best","films"]
stopwords = set(STOPWORDS)
stopwords.update(get_stop_words('en'))

for movie in top9movies:
    words = movie.split()
    custom_stopwords.extend(words)

stopwords.update(custom_stopwords)
We will create the wordcloud image in 3x3 format.

fig, axes = plt.subplots(3, 3, figsize=(12, 12))
fig.suptitle('Word Clouds for Top 9 Movies', fontsize=16)

for i, movie in enumerate(top9movies):
    plainwords = " ".join(word[i])

    word_cloud = WordCloud(width=800, height=600, stopwords=stopwords, collocations=False,
                           max_words=200, min_word_length=4, background_color='black').generate(plainwords)

    ax = axes[i // 3, i % 3]
    ax.imshow(word_cloud, interpolation='bilinear')
    ax.axis("off")
    ax.set_title(movie, fontsize=10, fontweight='bold')
plt.subplots_adjust(wspace=0.1, hspace=0.3)
plt.savefig('wordclouds.jpg', dpi=300)

plt.show()
This loop iterates through each movie title in top9movies, producing a word cloud for each review. The reviews are combined into a single string (plainwords), and a WordCloud object with various characteristics such as width, height, stopwords, collocations, max_words, min_word_length, and background_color is constructed. In our cloud we want to use maximum 200 words and we eliminate the word shorter than 4. We use subplots_adjust function to lower white space areas. We save the wordcloud as wordclouds.jpg.

