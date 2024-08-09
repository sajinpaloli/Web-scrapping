<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>README</title>
</head>
<body>

<h1>Dow Jones Industrial Average Data Analysis</h1>

<p>This project involves extracting and processing data related to the Dow Jones Industrial Average (DJIA) from Wikipedia and Yahoo Finance. The code is written in Python and uses libraries such as <code>pandas</code>, <code>yfinance</code>, and <code>unicodedata</code>. The end goal is to retrieve historical stock prices for the DJIA constituents and the DJIA index itself, and save the data to CSV files.</p>

<h2>Requirements</h2>

<p>To run the code, you need to have the following Python packages installed:</p>

<ul>
    <li><code>pandas</code></li>
    <li><code>yfinance</code></li>
    <li><code>unicodedata</code></li>
</ul>

<p>You can install the required packages using pip:</p>

<pre><code>pip install pandas yfinance
</code></pre>

<h2>Code Explanation</h2>

<h3>Step 1: Extract DJIA Constituents from Wikipedia</h3>

<ol>
    <li><p><strong>Read the HTML table from Wikipedia:</strong></p>
    <pre><code>pd.read_html("https://en.wikipedia.org/wiki/Dow_Jones_Industrial_Average")
</code></pre></li>

    <li><p><strong>Select the second table (which contains the current constituents):</strong></p>
    <pre><code>const = pd.read_html("https://en.wikipedia.org/wiki/Dow_Jones_Industrial_Average")[1]
</code></pre></li>

    <li><p><strong>Trim the table to keep only the first five columns:</strong></p>
    <pre><code>const = const.iloc[:, :5].copy()
</code></pre></li>

    <li><p><strong>Rename the "Date added" column to "Date_added" for easier manipulation:</strong></p>
    <pre><code>const.rename(columns={"Date added": "Date_added"}, inplace=True)
</code></pre></li>

    <li><p><strong>Convert the "Date_added" column to datetime format:</strong></p>
    <pre><code>const.Date_added = pd.to_datetime(const.Date_added)
</code></pre></li>

    <li><p><strong>Normalize the "Symbol" column to ensure consistent text encoding:</strong></p>
    <pre><code>const.Symbol = const.Symbol.apply(lambda x: unicodedata.normalize("NFKD", x))
</code></pre></li>

    <li><p><strong>Extract the ticker symbol for each company:</strong></p>
    <pre><code>const["Ticker"] = const.Symbol.str.split(": ").apply(lambda x: x[-1])
</code></pre></li>

    <li><p><strong>Save the processed data to a CSV file named <code>const.csv</code>:</strong></p>
    <pre><code>const.to_csv("const.csv", index=False)
</code></pre></li>
</ol>

<h3>Step 2: Retrieve Historical Stock Prices</h3>

<ol>
    <li><p><strong>Import the necessary packages and retrieve the list of ticker symbols:</strong></p>
    <pre><code>import yfinance as yf
ticker_list = const.Ticker.to_list()
</code></pre></li>

    <li><p><strong>Download historical stock prices for the DJIA constituents from Yahoo Finance:</strong></p>
    <pre><code>prices = yf.download(ticker_list, start="2007-01-01", end="2020-03-31")
</code></pre></li>

    <li><p><strong>Keep only the closing prices and save them to <code>const_prices.csv</code>:</strong></p>
    <pre><code>prices = prices.loc[:, "Close"].copy()
prices.to_csv("const_prices.csv")
</code></pre></li>

    <li><p><strong>Download historical prices for the DJIA index:</strong></p>
    <pre><code>dji = yf.download("^DJI", start="2007-01-01", end="2020-03-31")
</code></pre></li>

    <li><p><strong>Save the DJIA index data to <code>dji.csv</code>:</strong></p>
    <pre><code>dji.to_csv("dji.csv")
</code></pre></li>
</ol>

<h2>Output Files</h2>

<ul>
    <li><code>const.csv</code>: Contains the list of DJIA constituents with their ticker symbols and other details.</li>
    <li><code>const_prices.csv</code>: Contains the historical closing prices for the DJIA constituents from 2007-01-01 to 2020-03-31.</li>
    <li><code>dji.csv</code>: Contains the historical data for the DJIA index for the same period.</li>
</ul>

<h2>Notes</h2>

<p>The code assumes that the Wikipedia page structure and the table layout remain consistent. If the structure changes, the code may need adjustments. The data extracted from Yahoo Finance is subject to the availability of the API and the historical data.</p>


</body>
</html>
