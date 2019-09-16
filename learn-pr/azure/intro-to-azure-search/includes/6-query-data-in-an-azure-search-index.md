Importing data into an Azure Search index is the first step to enabling your company to use search. With data loaded into an index, the next step is learning how to query it.

Now that you've been through the basics of Azure Search and how to load documents into an index. You want to understand how to query your new index, you'll look at how to query the data that is stored within an index, work with the results, and understand the basics of query syntax.

In this unit, you'll see how to write simple search queries in the Azure Search Explorer, then append extra parameters to manipulate those results.

## Searching content using Azure Search

Index and query design are closely linked, a crucial component to understand is that the schema of the index sets what queries can be answered.

Azure Search queries work in a similar way to any HTTP or REST API request. They're constructed of two key parts, the request, and the response. Queries take the form of a request specifying a construct that determines what fields are returned, how the index is returned, or how the index should be filtered or sorted. A query that doesn't specify the field to search will execute against all the searchable fields within the index.

All of the methods of searching are made possible by the Azure Search REST API. You can choose to programmatically make GET requests to the API, in Powershell by running `Invoke-RestMethod`, or with tools like PostMan. The Azure Search Explorer is a purpose-built tool provided in the portal, enabling you to easily execute searches and view the JSON results. Or there's a rich SDK available for both .NET and Python, with which you can use the `SearchIndexClient` object to submit searches.

Search is not single subject area in computer science. To be able to understand and write a search solution, knowledge of are several different subject areas are needed. These include lexical analysis, language parsing, data indexing, and search algorithms. Azure Search is built on, and extends, the Apache Lucene project.

Which ever way you choose to search your content, you need to know the following information to write your queries.

### Query request Elements and Parsing

A query request is a list or words (search terms) and query operators (simple of full) of what you would like to be returned in a result set. However you want to search, either by calling the REST API directly, or programmatically, you'll need the following information.

- **The service endpoint and index collection:** Usually the URL such as <https://(your-service-name).search.windows.net/indexes/(your-index-name)/docs>.
- **The API version:** Enables you to choose which version to send the request to.
- **An API Key:** Used to authenticate the request.
- **A query type:** - One of two choices either simple or full, simple syntax is the default option.
- **The query:** - Will contain search terms and then a combination of sorting statements or filtering statements.

Using the Search Explorer you are only concerned with the last two items. Let's look what components make up a search query. Consider this search:

```
calm easy meditation (-"iyengar yoga" + -"hot pilates)"
```

The above query is trying to find any video in your companies catalog that is calm and relaxing, but the person searching wants to exclude iyengar yoga and hot pilates.

Breaking the query into components, it's made up of search terms, (`calm`, `easy`, `meditation`). Two phrases, `"iyengar yoga"` and `"hot pilates"`, and operators (`-`, `+`, and `( )`). The search terms can be matched in the search index in any order or location in the title of your videos. The two phrases will only match with exactly what is specified, so yoga iyengar would not be a match. Finally, a query can contain a number of operators. In the above example, the `-` operator tells the search engine that these phrases should **NOT** be in the results. The parenthesis group terms together and their precedence.

By default, the search engine will match with any of the terms in the query. A title containing just `calm` would be a match. In the above, using `-"iyengar yoga"` would lead to the search results including all the titles that don't have the exact string "iyengar yoga" in it.

### Simple Query Syntax

The simple query syntax in Azure Search excludes some of the more complex features of the full Lucene query syntax, and it's the default search syntax for queries. The example search above is written in this simple query syntax.

**Operators**

`+`: To ask for documents that contain all the search terms. For example, `power + boxing` would return all videos with both power **AND** boxing in the title.

`|`: To ask for documents that contain any of the search terms. For example, `yoga | cardio` would return all the videos with either yoga **OR** cardio in the title.

`-`: To ask for documents that don't contain a term. For example, `-cycling` would return all the videos that do **NOT** have cycling in the title.

> [!IMPORTANT]
> Remember that search queries default to match any of the search terms. You can change this behavior using the `searchMode` option. A query can be changed to match with all the search terms. If you don't pay attention to this, using a combination of terms, with some using the `-` operator will lead to **OR** instead of **AND** searches. Append either `&searchMode=all` or `&searchMode=any` to specify the desired behavior.

```
calm easy meditation (-"iyengar yoga" + -"hot pilates)"
```

Examining the search query again, can you see an error in the above? If you ran this query, the search engine wouldn't return the desired results. As any of the terms are being matched, all videos that don't have `(-"iyengar yoga" + -"hot pilates)` in their titles will be matched. There are two ways to rewrite this query to achieve the desired results, one way using the operators to clarify the query. The other way is to change the search mode.

```
(calm easy meditation) + (-"iyengar yoga" + -"hot pilates)"
```

The above search query groups the first three terms with the precedence operator `( )`, so any of calm, easy, or meditation will be a match in the results. The `+` operator tells the search parser that the tile must have (calm, easy, **OR** meditation) **AND** (neither of the specific terms) in the result.

```
(calm | easy | meditation) (-"iyengar yoga" + -"hot pilates)&searchMode=all"
```

Can you see that using `&searchMode=all` means that the first group of terms now needs to be separated by the **OR** `|` operator. If the query didn't contain the `|` operator, the search results would need to match (calm **AND** easy **AND** meditation) **AND** (neither of the phrases). Setting the search mode to `all` also means that the `+` operator becomes redundant and can be removed.

`*`: To include all matching suffix characters. If you need to search for words that begin with a term. For example, to return all the videos that have variations of crunch in their title use `crunch*`. The search would match with crunch, crunches, and crunched.


### Full Lucene Query Syntax

The full Lucene query syntax in Azure Search includes more complex query functionality including fuzzy search, regular expressions, and proximity searching. To use the full lucene query syntax in the Azure Search explorer append `&queryType=full` to the end of your search term.

Let's write a query to look for videos that could have different spellings for some words, and require those words to be close to each other in the title. You know that you have a group of red colored shirt training videos, but think it may have been misspelled either colour, or colours. You'd also like the returned videos to have an 8 or higher difficulty rating. This type of search is made possible using the Lucene syntax.

```
"red color~2 shirt"~3 && Difficulty:(8 9 10)
```

Braking the query down into its components. The last part uses the Lucene syntaxes ability to specify a field in a document, in this case `Difficulty`, to match a given term. The value can be a single search term or phrase, or in the above example a range of terms.

`~` is an overloaded operator in the syntax. Depending on where it's used, it can represent two different things. The operator is used to specify fuzzy string matching and proximity matching.

The proximity search `"term1 term2 term3"~3` tells the search engine that these terms must be found in a string no more than three words apart from each other.

In the above example `color~2` tells the search engine to look for all words that are up to two characters away from `color`. Running the query would match with the words colored, colors, colour, colon, and colours. Fuzzy matching works be looking for similar strings, either replacing, adding, or removing characters in them.

If you need more specific control of what the strings can be, Lucene also offers the power of matching against regular expressions. Use forward slashes around the regular expression. For example, `/[yt]oga/` would match on all videos with the words yoga or toga in the title.

### Filtering data

Filtering in Azure Search works by you providing criteria to the index to enable it to select documents to be searched. The filtering is executed before your search query is parsed, so your query is running on a subset of the documents in the index. For example, a filter could be used to restrict the exercise videos returned in the catalog based on their difficulty level. You specify your filter criteria using OData expressions.

`&$filter=fieldname operator value`: Both strings and numerical fields are supported. For string fields, `eq` and `ne` are supported. For numeric fields, OData supports `eq`, `ne`, `gt`, or `lt`.

### Result handling

After a search term has been designed, you can choose how the results are returned. If the result set is large, and you want to display them on a webpage, a typical user experience is to show pages of results:

`Showing 1 - 5 or 36 videos` or another way of displaying pagination `Showing Page 3 of 10`.

To support this experience, you limit the returned results with the `&$top=X` parameter. In the above example, you would append `&$top=5` to your search query. If the user then wanted to see the next 5 videos, you would append `&$top=5$skip=5`. The `skip` parameter controls where in the result set you are. The amount that needs to be skipped can be represented as the formula `skip=(page*top)-top`. Using page 5 as an example, the parameter would be `(5*5)-5 = 20` so the value to skip is `20`. The total count of results can be shown in the result set if you append `&$count=true`.

Can you construct the full query needed to return page 3 including the total number of results?

```
search=*&$top=5$skip=10&$count=true
```

The above introduces a new parameter, `search`. The parameter is included here for clarity as the number of options increase. It can be omitted.

A useful feature to your customers is the ability to order the results on the length of exercise videos. They may want to see the shortest videos first, as they don't have much time and what to quickly do some training. If you're familiar with SQL queries, ordering the results works in a similar way. Append an `&$orderby=fieldname asc|desc` parameter to the end of the query string. With this knowledge how would you write a query so sort the results shortest first?

```
&$orderby=Length asc
```

Your marketing team have asked to add more descriptive text for each video. They'd like to have a description field, that is searchable. When the search results are returned, the matching terms need to be highlighted in the results. The Search service offers this functionality by appending this parameter to queries `highligh=fieldname`. This returns a `search.highlights` array with HTML for each document that highlights where term was found.

```json
"@search.highlights": {
    "Description": [
        "Hatha describes any kind of <em>yoga</em> where poses (asanas) are practiced, this would include Ashtanga, Vinyasa and Iyengar <em>yoga</em> to name a few popular styles. The word Hatha is the Sanskrit word for 'Forceful', ..."
    ]
},
```

### Facets

Your web developers would like to provide a sidebar on the search page to allow your customers to filter the results based on their tags. They need to have a list of the tags on videos, and the total number of videos with that tag, to add drill down capabilities.

![An example of faceted browsing of results](../media/facet-results.png)

`&facet=fieldname`: Returns a facet structure in the JSON results for the specified field. For example `&facet=Tags` includes an array in the results. This array could be used by the website, allowing users to select the length of videos.

```JSON
    "@search.facets": {
        "Length": [
            {
                "count": 6,
                "value": "2019-05-01T00:14:12Z"
            },
            {
                "count": 4,
                "value": "2019-05-01T00:15:30Z"
            },
            {
                "count": 3,
                "value": "2019-05-01T00:02:40Z"
            },
            {
                "count": 3,
                "value": "2019-05-01T00:07:11Z"
            },
            {
                "count": 2,
                "value": "2019-05-01T00:04:12Z"
            }
        ]
    }
```

The search results include an array of the length `value` and a `count` of the videos that match that tag.
