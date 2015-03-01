## News Headline Feeds & Analysis Feeds

Currently the News system breaks down into two sub-systems.  Our News Headlines that we ingest from various sources on the web, all these importers are located inside of the news/ app and the importers are controlled by a series of management commands via upstart.  The other subsystem would be our outbound 'news feed' of our analysis articles written by our content team, these articles and their feeds are contained inside of the articles app.

## News Headlines

Our news headline importers consist of various partners: Yahoo news, Investopedia, Fox Business and etc.  Currently our importers are built
to handle two methdologies.  First being the Yahoo method, in which we iterate over our company symbols and request a feed for a given symbol
that we then get all the news headines for that specified symbol.  The second method is a more 'general' and for a given source feed we just iterate
over each news item which generally contains the 'symbols' for that news article inside of some xml-element or as an attribute.
Each of these importers for this method generally have a custom parse function to extract symbols.

## Analysis Feeds

To access an analysis feed there are specific url structures, all base around /analysis/feed but after that there are defined urls for specific feeds
and partners that route requests to custom feed-creators.  However, if a custom-route isn't defined for the location then it is routed to TaggedEntriesFeed
which will try to interpret the variable_tag.

    // Access the basic feed of analysis articles (default is rss2.0)
    http://ycharts.com/analysis/feed

    // Purl (custom feed structure for some finance companies)
    http://ycharts.com/analysis/feed/purl

    // Custom access pattern
    http://ycharts.com/analysis/feed/investopedia
    http://ycharts.com/analysis/feed/<tag_name>

### Purl

The Purl format is a custom feed by SmartMoney that is a reference/namespace their [DC DublinCore](http://dublincore.org/) initiative.  Shouldn't have to mess with this.

### Custom Article Feeds

To generate custom feeds it is covered briefly with a hand ful of sentences in the [django doc](https://docs.djangoproject.com/en/dev/ref/contrib/syndication/#custom-feed-generators)
there are several custom feeds inside of the article app

    apps/articles/utils/feedgenerator.py

Most of these were created as a means to override xml structures or to bind attributes to elements that you are unable to do by default.

