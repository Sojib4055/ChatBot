# COVID-19 Data Chatbot with Retrieval-Augmented Generation (RAG)

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)

## Project Overview

The **COVID-19 Data Chatbot** is an interactive tool that answers user queries about global COVID-19 statistics, such as total cases, deaths, or population by country. Powered by **Retrieval-Augmented Generation (RAG)**, the project combines web scraping and natural language processing to deliver accurate, data-driven responses. It consists of two main components:

1. **Scraper**: Extracts COVID-19 data from the [Worldometers](https://www.worldometers.info/coronavirus/) website and saves it to a CSV file.
2. **Chatbot**: Uses RAG to retrieve relevant data from the CSV and generate human-like responses via the Groq API with the LLaMA 3 model.

This project demonstrates the integration of web scraping, text retrieval, and advanced language models to create a user-friendly conversational interface.

### Scraper
The scraper script (`scraper.py`) automates data collection from the Worldometers COVID-19 page. It:
- Uses **Selenium** to navigate the website and extract data from the main table (`main_table_countries_today`).
- Collects data for the `World` row and individual countries, excluding continent totals (hidden rows).
- Saves the data to `worldometers_covid1.csv` with columns: `Rank`, `Country`, `Total Cases`, `Total Deaths`, `Total Recovered`, `Tot Cases/1M pop`, `Deaths/1M pop`, `Population`, and `Continent`.
- Ensures robust error handling, such as checking for file access issues and closing the browser properly.

### Chatbot
The chatbot script (`chatbot.py`) provides an interactive interface for querying the scraped data. It employs RAG by:
- **Retrieval**: Uses **scikit-learn**’s `TfidfVectorizer` and `cosine_similarity` to find the top 5 relevant rows based on user queries (e.g., country-specific or comparison questions).
- **Generation**: Sends the retrieved data and query to the **Groq API** (LLaMA 3 model: `llama3-70b-8192`) to generate precise, natural language responses.
- Supports queries like:
  - “Total cases in USA?”
  - “Which country has the highest deaths?”
  - “Compare cases in USA and India.”
- Handles edge cases (e.g., unavailable continent totals, `N/A` values) with clear feedback.
- Offers a verbose mode to debug retrieved context.

## Requirements

- **Python**: 3.8 or higher
- **Dependencies**:
  - `pandas==2.2.2`: For data manipulation and CSV handling.
  - `scikit-learn==1.5.2`: For text vectorization and similarity scoring in the chatbot’s retrieval step.
  - `selenium==4.25.0`: For web scraping with browser automation.
  - `groq==0.11.0`: For interfacing with the Groq API and LLaMA 3 model.
- **ChromeDriver**: Required for Selenium. Must match your Chrome browser version (e.g., Chrome 126 requires ChromeDriver 126).
- **Groq API Key**: Obtain from [Groq Console](https://console.groq.com/).

## Installation

Follow these steps to set up and run the project:

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/covid19-data-chatbot.git
   cd covid19-data-chatbot
   ```

2. **Create a Virtual Environment** (recommended):
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install Dependencies**:
   ```bash
   pip install pandas==2.2.2 scikit-learn==1.5.2 selenium==4.25.0 groq==0.11.0
   ```

4. **Install ChromeDriver**:
   - Download ChromeDriver matching your Chrome browser version from [chromedriver.chromium.org](https://chromedriver.chromium.org/downloads).
   - Place it at `C:\Users\UseR\Intern Task\Task\chromedriver-win64\chromedriver.exe` (or update the path in `scraper.py`).
   - Ensure Chrome is installed and updated.

5. **Set Up Groq API Key**:
   - Sign up at [Groq](https://console.groq.com/) and obtain an API key.
   - Replace `"your_groq_api_key_here"` in `chatbot.py` with your key (line ~75):
     ```python
     client = Groq(api_key="your_groq_api_key_here")
     ```

## Usage

1. **Run the Scraper**:
   ```bash
   python scraper.py
   ```
   - This scrapes data from Worldometers and saves it to `worldometers_covid1.csv`.
   - Output example:
     ```
     🌐 Opened website, waiting for table to load...
     ✅ Data saved to worldometers_covid1.csv
     🚪 Browser closed.
     ```

2. **Run the Chatbot**:
   ```bash
   python chatbot.py
   ```
   - Enter queries interactively. Type `exit` to quit.
   - Example interaction:
     ```
     RAG Chatbot for COVID-19 Data (type 'exit' to quit)
     You: Total cases in USA?
     Answer: The USA has 111,820,082 total COVID-19 cases.
     You: Compare deaths in USA and India.
     Answer: USA: 1,219,487 deaths | India: 533,570 deaths.
     You: Total cases in Europe?
     Answer: The total COVID-19 cases in Europe cannot be directly calculated from country-level data alone, as continent totals are not provided. Please specify a country or another metric.
     You: exit
     ```

3. **Verbose Mode**:
   - To debug retrieved context, set `verbose=True` in `chatbot.py` (line ~200):
     ```python
     rag_chatbot(verbose=True)
     ```

## Project Structure

```
covid19-data-chatbot/
├── scraper.py              # Scrapes data from Worldometers
├── chatbot.py              # RAG-based chatbot for querying data
├── worldometers_covid1.csv # Output CSV (generated by scraper)
├── README.md               # Project documentation
└── requirements.txt        # Dependency list
```

Create a `requirements.txt` file with:
```
pandas==2.2.2
scikit-learn==1.5.2
selenium==4.25.0
groq==0.11.0
```

## Limitations

- **Continent Totals**: The scraper excludes continent-level totals, limiting direct answers for queries like “Total cases in Europe.” Users must specify countries or use individual country data.
- **Static Data**: The CSV is static once generated. Re-run the scraper for updated data.
- **Retrieval**: TF-IDF may miss nuanced queries. Future improvements could use advanced embeddings (e.g., `sentence-transformers`).

## Future Enhancements

- Scrape continent totals by navigating continent tabs on Worldometers.
- Implement advanced text embeddings for improved retrieval.
- Add a graphical user interface (e.g., using Streamlit).
- Schedule periodic scraping for real-time data updates.
