Our marketing team needed to gauge customer response to our new shoe. We wanted to monitor social media, determine customer reaction, and route the data to a database or to customer service based on sentiment.

Azure Logic Apps let us automate the process. Each step of our workflow was mapped to one of the built-in components. We used the Twitter trigger to detect mentions of our product and launch our app. The Azure Cognitive Services action let us analyze whether the tweets were positive or negative. A control action helped us decide where to route the tweet based on sentiment. Finally, we used actions to insert a row in SQL Server and send an Outlook email.

Imagine how much work it would be to build this app from scratch. We'd have to write code to access each of the services' APIs. We'd need a polling infrastructure to monitor Twitter and trigger our app when new tweets were available. Once all the code was ready, we'd need servers to host the app.

Logic Apps made it easy. The standard connectors did the hard work of integrating systems that were never designed to work together. Building the app took us less than an hour and we didn't have to write any code or set up any servers. We can now analyze every tweet about our product and our business analytics can use the data to shape future products.

[!include[](../../../includes/azure-sandbox-cleanup.md)]