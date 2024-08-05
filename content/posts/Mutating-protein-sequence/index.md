+++
title = 'Creating mutated protein sequences from a list of amino acid changes'
date = 2024-08-02T16:20:16+09:00
draft = false
tags = ['ensembl', 'protein']
categories = ['bioinformatics']
+++

# *In silico* protein folding

Recent improvements in protein [folding tools](https://github.com/biolists/folding_tools) such as AlphaFold2
have dramatically changed protein structure research. *In silico* predictions have been able to reach comparable accuracy to experimental techniques such as X-ray crystallography and cryo-electron microscopy. 
These tools may be useful to help filter protein structures for experimental studies.

# Missense mutations 


Missense mutations are single nucleotide changes in nucleotide sequence that lead to amino acid change in the
protein sequence. All single nucleotide changes don't lead to amino acid change, but may result in synonymous mutation 
(no amino acid change), early stop codon (translation terminates) or a splicing change if located at splicing site. 

{{< figure src="images/nucleotide_changes.png" title="Single nucleotide changes" >}}

The effect of an amino acid change on protein structure and function is not always clear when only looking at the amino acid sequence. 
Some changes occur at known active sites or post-translational modification sites which may strongly indicate a deleterious effect on the protein function.

But what about the changes located outside of these sites?

Traditionally the effect of the amino acid changes has been studied experimentally: producing protein products with the mutated amino acid,
extracting the protein and solving its protein structure with for example X-ray crystallography.
Experimental protocols are expensive and time-consuming and hence there's a huge interest in pre-filtering the potential structures in cases where there are a large number of mutations of interest.


# Creating mutated sequences for *in silico* analysis

I recently wanted to automate mutating protein FASTA sequences for AlphaFold2 protein folding and made a python script for that.

### Set API connection

The script uses Ensembl [REST API](http://grch37.rest.ensembl.org/) GRCh37 version and first sets a connection to the API:

```python {class="my-class" id="my-codeblock" lineNos=inline height="600"}
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry
from requests_toolbelt import sessions


def set_connection():
	server = "http://grch37.rest.ensembl.org"
	session = sessions.BaseUrlSession(base_url = server)

	retry_strategy = Retry(
		total=3,
		backoff_factor=2,
		status_forcelist=[429,500,502,503,504],
		method_whitelist=['HEAD','GET']
	)

	adapter = HTTPAdapter(max_retries=retry_strategy)
	session.mount('http://',adapter)

	return session


session = set_connection()

```

This creates a lasting connection to the API during which you can do multiple requests without closing and opening the connection each time.

### Read in data

After the connection is set you read in the data, which is a text file with column "Protein_change" and one amino acid change of the format A256T per row (All changes for the SAME protein):

| Protein_change |
|     :---:	 |
| A356T		 |
| V14F		 |


```python {class="my-class" id="my-codeblock2" lineNos=inline height="600"}
def read_data(data):
    try:
        datafile = pd.read_csv(data, sep='\t')
    except FileNotFoundError:
        logging.exception("Data file not found")
        sys.exit()
    else:
        logging.info(f"Datafile {data} read successfully.")
        return datafile

data = read_data(File)
```

Ideally you have all your amino acid changes for the same protein transcript but that is not always the case when gathering mutations from literature. Some proteins may have differing amino acid transcripts; longer/shorter versions, or older/newer versions that have a bit differing mutation locations. 
This script currently takes the default/most common protein sequence from Ensembl server based on Ensembl transcript ENST and checks if the location and amino acid matches. If the mutation belongs to different protein transcript, it is skipped.

### Get protein FASTA sequence

First request the protein sequence (ENSP) from the API, and separate the ENSP name and amino acid sequence in their own variables:

```python {class="my-class" id="my-codeblock3" lineNos=inline height="600"}
def get_protein_seq(ENST,session):
    ext = f"/sequence/id/{ENST}?type=protein"

    p_seq = request_server(ext, session)
    ESP,seq = p_seq.text.split('\n',1)
    seq = seq.replace('\n','')
    logging.info(f"Read protein sequence {ESP} for transcript {ENST}.")
    return(ESP,seq)

ENSP, seq = get_protein_seq(ENST,session)
```

### Make amino acid changes to the sequence

Next, make an amino acid change to the amino acid sequence for the location specified in the 'Protein_change' column of the data file.
Make two lists, first containing the ENSP-code + amino acid change as a name for the sequence and the second one includes the mutated amino acid sequences.

```python {class="my-class" id="my-codeblock3" lineNos=inline height="600"}
def mutate_pseq(changes, ENSP, seq):
    name_list = []
    seq_list = []

    name_list.append(f'{ENSP}_WT')
    seq_list.append(seq)


    for c in changes['Protein_change']:
	#unmutated sequence
        temp = seq 
	#position of mutation eg. A265T -> 265
        k = int(c[5:-3])

	#checking if mutation is a missense mutation
        if seq[k-1] == a_as[c[2:5]]:
            temp2 = temp[:k-1] + a_as[c[-3:]] + temp[k:]
            name_list.append(ENSP+"_"+c)
            seq_list.append(temp2)
            logging.info(f"Mutated protein sequence with change {c}.")
        else:
	    #if not missense mutation
	    #or mutation not on default protein sequence
            logging.info(f'Something wrong with {c}')

    return(name_list, seq_list)

nlist, slist = mutate_pseq(data, ENSP[1:-2], seq)
```

### Write results to a file

Finally, write out the results as FASTA format to a single file:

**NOTE**
 
\>ENSP12344678_A345T \
ADGJRKAJGJRHJRJKDHEUIEJAHK


```python {class="my-class" id="my-codeblock3" lineNos=inline height="600"}
def save_seqs(nlist, slist, outname):
    with open(f"{outname}.fasta","w") as f:
        for i in range(len(slist)):
            f.write(nlist[i] + "\n" + slist[i] + "\n")

    logging.info(f"File {outname}.fasta saved.")

```

# Summary / future to do's

Complete script can be found in [github](https://github.com/jezkoski/MutateProtein/tree/main).


**Future modifications to the script:**

[] Multiple isoform checks

[] Add GRCh38 version API

[] Make an option to write sequences to a single OR separate FASTA files

