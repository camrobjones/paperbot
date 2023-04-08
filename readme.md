# Paperbot

Paperbot is a simple command-line tool that answers questions by retrieving relevant sources from a collection of PDFs and (hopefully) citing them in its answer. While vanilla LLMs are very good at providing plausible text, they sometimes hallucinate ciations. In academic contexts this can be very frustrating or problematic.

We use [langchain](https://python.langchain.com/en/latest/index.html) to retrieve actual sources and a prompt that encouraging the LLM to cite these sources. You create the source collection yourself from a directory of PDFs, so you can choose which papers the model can refer to. You can also return the raw sources to see the information the model used to construct its response.

You can choose between Hugging Face embeddings (default) or OpenAI ada-text-002 embeddings (which could be costly for large collections). By default the QA LLM is gpt-3.5-turbo, but you can specify any openai model.

You have to supply your own OPENAI_API_KEY. With the default settings it should cost around $0.005 per query. Most of this is for the model to process the retrieved source text, so increasing `chunk_size` and `n_sources` could increase cost significantly.

This was just a fun project to try out using langchain, but it returns pretty good results a lot of the time! I have some ideas below to try to improve it but please do reach out or feel free to contribute if you have others.

## Setup Instructions

1. Clone this repository to your local machine.

```bash
git clone https://github.com/camrobjones/paperbot.git
```

2. Navigate to the repository folder and install the required Python packages.

```bash
cd paperbot
pip install -r requirements.txt
```

3. Set your OpenAI [API key](https://platform.openai.com/account/api-keys) as an environment variable

```bash
export OPENAI_API_KEY=sk-xxxx
```

## Example Usage

### Create an Index

Create a new index from a directory of PDF files. The model will use this to find sources:

```bash
python paperbot.py create_index --input_dir /path/to/pdfs --index_name my_index
```

Arguments:
- `-d` `--input_dir`: The directory containing the PDF files.
- `-i` `--index_name`: The name of the index.
- `-s` `--chunk_size`: The size of each chunk to be indexed in chars (default: 1000).
- `-o` `--chunk_overlap`: The number of overlapping characters between each chunk in chars (default: 500)
- `-e` `--embeddings`: The type of embeddings to use, either `huggingface` (default)  or `openai`. 
- `-l` `--loglevel`: Level of detail in logs (default: INFO)

## Query

Ask a question that the model will answer with info from the index.

```bash
$ python paperbot.py query_model --question "What is Theory of Mind?" --index_name my_index
Theory of Mind (ToM) refers to the cognitive ability to attribute mental states to others and predict their behavior based on these mental states, such as intentions, beliefs, desires, and other psychological phenomena (Premack & Woodruff, 1978; van de Pol, 2015, p. 15). This ability allows humans to navigate and understand social situations ranging from simple everyday interactions to complex negotiations (Sap et al., 2022, p. 2). ToM is essential for explaining intentional and unintentional behaviors and making inferences about the causal relationships between mental states and actions (van de Pol, 2015, p. 15).

References:

Premack, D., & Woodruff, G. (1978). Does the chimpanzee have a theory of mind? Behavioral and Brain Sciences, 1(4), 515-526.

Sap, M., Gella, S., Costa-jussà, M. R., & Iyyer, M. (2022). Neural Theory-of-Mind: Enabling the Understanding of Commonsense Reasoning in Artificial Agents. Retrieved from /Users/cameron/Documents/Papers/Theory of Mind/Sap et al_2022_Neural Theory-of-Mind.pdf

van de Pol, I. (2015). MSc Thesis (Afstudeerscriptie): Measuring Relativistic Effects in Humans: A New Model-Theoretic Approach. Retrieved from /Users/cameron/Documents/Papers/Theory of Mind/van de Pol_MSc Thesis (Afstudeerscriptie).pdf
```

Arguments:
- `-q` `--question`: The question to ask the model.
- `-i` `--index_name`: The name of the index.
- `-n` `--n_sources`: The number of sources to retrieve (default: 5).
- `-m` `--model`: The OpenAI model to use for question answering (default: `gpt-3.5-turbo`).
- `-s` `--return_sources`: Flag to return the sources retrieved from the index.
- `-l` `--loglevel`: Level of detail in logs (default: INFO)

## Roadmap

- Document loader
    - Improve PDF parsing [ ]
        - Use SCIPDF parser or GROBID? [ ]
    - Add full references to metadata [ ]

- Text splitter
    - Intelligent chunks (title, abstract, keywords, paragraphs)

- Search
    - MMR? [ ]
    - Incorporate authority of paper [ ]
    - Relevance cutoff [ ]

- QA
    - Chat mode [ ]
    - Other models (GPT-J? LLaMa?) [ ]