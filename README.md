# pharmaceutical-data-platform-databricks
An example project that shows how to leverage Databricks to organize open-source data for pharmaceutical use.


## The fictional company
We are pretending to be a fictional company called Braincare, a small biotech startup that focuses on treating and curing rare neurological diseases.
The company consists of 4 departments:
- Discovery: Responsible for target discovery and everything ahead of having a candidate cure. Leverages scientific information (Pubmed, ...), open scientific databases (Uniprot, Ensemble, Opentargets, ...) and internal knowledge to identify druggable targets.

- Translational Science: Bridges biology to clinic and helps figure out how to know that a drug is working. It develops biomarkers, designs patient stratification etc.Uses open databases (Human Protein Atlas, GEO, ...), but also outputs from Discovery.

- Translational Medicine: Is responsible for drug development e.g. owning drug candidates, tracking ADMET properties (Absorption, Distribution, Metabolism, Excretion, Toxicology) and owning go/no-go decisions. They often use databases related to chemical properties and binding like ChemBL (what other competitors tried), BindingDb, DrugBank, Google Patents, ...

- Clinical Development: Is responsible for running trials, owning protocol designs etc. They need to remain up to date on the latest recommendations from regulatory agencies like FDA and EMA and especially on the proper way to statistically handle small diverse trial populations. They will also own a lot of sensitive data. 

- Regulatory Affairs and RWE: Needs to make sure drugs get properly approved and need guidance from FDA, EMA, ... on the procedure.  While it is not normally the case, let's assume that this department is also responsible for collecting real-world evidence (i.e. issues that occured after a drug is out in the field).

In reality, these departments have to share information constantly (e.g. Regulatory kills Discovery targets that can't be patented, Discovery uses clinical trial results to assess whether there target is likely to survive Phase II dropoff, ...), which means Braincare needs a flexible open system to share high-quality data.
This project is about building that.
