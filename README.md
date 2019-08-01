# Pubmed_Scraper

## Program Goal

Allow users to scrap data from PubMed articles (or other Databases supported by E-utilities although the script is made to be used with PubMed). The data will be output as several sheets in an excel document.

## How to Run

Run `main_internal_args.py` from the terminal to run the process. Variables to control results can be edited within the `main_internal_args.py`.


## Output File

The file that is output will have a filename like `{query_name}_{date}_{number_of_results}res.xlsx`.

The 'Master Table' will have non-flattened data. Most of the data are in lists but there is also a dictionary.

After that, there are a number of flattened tables that are relevant to a specific feature. These features are:
- Author
- Keyword
- Article ID
- Abstract
- Pubtype
- MeSH Keywords


## Program Flow

Scripts run in the following order:

1. `main_internal_args.py`
2. `search_ids.py` - get_article_ids()
3. `pubmed_scraper.py` - pubmed_xml_parse()

#### `main_internal_args.py`

- This script doesn't have a function call within it.
- User can set run parameters in this script.
    - _query_term_
        - Term/phrase that will be used to query PubMed
        - i.e.:
            - translational+AND+microbiome
            - cannabis+AND+(inflammation+OR+nausea)
    - _sort_order_
        - Sort order to return articles in.
        - Not important if pulling ALL articles that match a term.
        - Important when pulling < ALL articles because some need to be left out.
        - Options include :
            - Default value is 'most+recent'
            - Others are 'journal', 'pub+date', 'relevance', 'title', 'author'
    - _results_
        - Number of results to return from the search
        - No upper limit. Script will stall longer with more results.
        - If it returns < _results_ results, that's all the articles on PubMed
    - _id_list_filename_
        - Name of file with list of IDs to run.
        - Leave blank if using a search query.

#### `search_ids.py`

- This script will take in a list of article IDs or a search query, create an .XML file with data on the specific articles found and return some info to be passed to the next function.

- Function : get_article_ids
    - Parameters :
        - query - Query that'll be searched.
        - filename - Filename of an ID list. Okay to leave blank while using a search query.
        - retmax - Number of results to return.
        - sort - Sort order for the results to be sorted using.
        - have_ids - Boolean to tell script whether to use an ID list or not. Defaults to `False`
        - api_key - API key
    - Returns:
        - A list of 2 elements : [file_name_fetch, query_str]
            - _file_name_fetch_
                - Name of the "fetch" .XML file made during the run.
            - _query_str_
                - The query fed into the function at the beginning.

#### `pubmed_scraper.py`

- This file will take in the filename for the fetch .XML file generated by `search_ids.py`, output a .xlsx file and return a string with run information.
    - Parameters:
        - _filename_
            - Filename of the .XML 'fetch' file created by `get_article_ids()`
    - Returns:
        - _return_string_
            - return_string = '\tFile name: ' + xlsx_file_name + '\n\tLength of result: ' + str(len(master_df))
            - String made with the filename and number of results. This will be printed out in the console after a successful run.
