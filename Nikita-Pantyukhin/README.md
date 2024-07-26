# Reason
A graph needs to be created based on the text from the submitted documents for use in RAG.

[Download NLP](https://drive.google.com/drive/folders/19Lbb60iLRvEUkTrWRSz8tNDQ1mgTT-NG?usp=sharing)

**Objective**: Create a graph based on the provided documents.

**Main task**: Use the LangChain framework (`langchain_experimental.graph_transformers`). Correctly pass text from documents through LLM, fitting into the constraints of the model context window.

# Setting up the environment

For the project to work correctly, you must install the following components:
1) [Miniconda (Python 3.12)](https://docs.anaconda.com/miniconda/miniconda-other-installer-links/);
2) Go to the installed `Anaconda Prompt (miniconda3)` and use the command: `conda env create -f path/to/ChatOpsEnvironment.yml`;
3) Activate environment `conda activate ChatOps`.

# General problems
1) Identifying documents;
2) Documents have a lot of noise that needs to be removed;
3) It is important to properly chunk the text without losing too much context.

# Idea of the script `creating_a_graph`
## Document identification:
1) The document text is separated from the table of contents;
2) The document is identified by the table of contents.

## Splitting text into chunks:
1) The document is split into headings, if they exist, paragraphs and subparagraphs;
2) The text of each subparagraph is summarized:
   1) If the size of the summarized text is larger than the allowed chunk size:
      - The text is split into sentences;
      - If the sentence is still larger than the allowed chunk size, the sentence is additionally summarized.
   2) If the summarized text is one large sentence that exceeds the allowed chunk size:
      - The sentence is divided in half taking into account punctuation until the parts satisfy the set chunk size.
3) The resulting text is sent to LLM to create a graph.

**Warning:**

**The text summarization model runs locally. To run, you need a video card with CUDA support and install the following components:**
1) [CUDA Toolkit 12.4 (Version 11)](https://developer.nvidia.com/cuda-downloads?target_os=Windows&target_arch=x86_64&target_version=11&target_type=exe_network);
2) [cuDNN 9.0.0 (Version 10)](https://developer.nvidia.com/cudnn-downloads?target_os=Windows&target_arch=x86_64&target_version=10&target_type=exe_local).

## Adding data to the graph:

1) The "CONTENT" relation is created:
[Organization] => [Document_number]

2) If the text has headings, they are linked to the [Document_number] node (the "CONTENT" relation);

3) All heading items and subitems, if any, are linked to them; if there are no headings, the link is with [Document_number] (the "CONTENT" relation);

4) All nodes from the graph received from LLM are linked to the item by the "CONTENT" relation. Relationships between nodes specified at the output from LLM are also created.