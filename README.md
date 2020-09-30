OHDSI Comparative effectiveness and safety of direct ORal Anticoagulants in patients with atrial fibrillation: a standardiZed Observational data Network study (CORAZON)
=============

<img src="https://img.shields.io/badge/Study%20Status-Started-blue.svg" alt="Study Status: Started">

- Analytics use case(s): **Population-Level Estimation**
- Study type: **Clinical Application**
- Tags: **OHDSI, DOAC, AF**
- Study lead: **Wallis CY Lau, PhD, UCL School of Pharmacy, United Kingdom**; 
                  **Carmen Olga Torre, RWS IQVIA, United Kingdom**
- Study lead forums tag: **[wallislau](https://forums.ohdsi.org/u/wallislau), [CarmenOT](https://forums.ohdsi.org/u/carmenot)**
- Study start date: **22/09/2020**
- Study end date: **12/2020**
- Protocol: **[Word Doc](https://github.com/ohdsi-studies/Corazon/blob/master/documents/OHDSI%20Protocol%20CORAZON%20V2.0_clean.docx)**
- Publications: **-**
- Results explorer: **-**

Notice
============
We are seeking collaborators who have access to patient-level data and are interested in providing evidence on the effects of direct oral anticoagulants (DOACs) to the healthcare community through publication. 

Study background 
============
Atrial fibrillation (AF) is the most common cardiac arrhythmia affecting over 33 million people worldwide and is a leading cause of stroke. Current clinical guidelines recommend direct oral anticoagulants (DOACs) as the first-line treatments for stroke prevention in patients with AF. However, there is no further guidance on how to choose between the DOACs, because evidence from head-to-head trials of DOACs is not available.

Objectives
============
We are interested in every pairwise comparison between any two DOACs (dabigatran, rivaroxaban, apixaban, and edoxaban). For each comparison, we are interested in the comparative effect on 1) a composite of ischemic stroke and systemic embolism; 2) intracranial bleeding; 3) gastrointestinal bleeding; and 4) all-cause mortality.
### Primary analyses: 
For each comparison between two DOACs, for each of the outcomes of interest, what is the hazard ratio? 
### Subgroup analyses: 
the primary analyses will be repeated in the following subgroups:
1)	Patients who received a standard dose of DOACs
2)	Patients who received a reduced dose of DOACs
3)	Patients with chronic kidney disease
4)	Aged 80 years or older

Study Requirements 
============
Patient-level data where each DOAC drug cohort (including edoxaban) has more than 1,000 subjects will be eligible for the study. 

Plans for Results Dissemination
============
We intend to publish our findings in a peer-reviewed journal.
Target journal
1.	Lancet (60.392)
2.	JAMA (45.540)
3.	BMJ (30.223)
4.	Circulation (23.603)
5.	Journal of the American College of Cardiology (20.589) 

Timelines
============
- 13th October 2020: Last date of results collation
- 9th November 2020: Circulation of the manuscript for review/comment
- 9th December 2020: Submission to the journal






Requirements
============

- A database in [Common Data Model version 5](https://github.com/OHDSI/CommonDataModel) in one of these platforms: SQL Server, Oracle, PostgreSQL, IBM Netezza, Apache Impala, Amazon RedShift, Google BigQuery, or Microsoft APS.
- R version 3.5.0 or newer
- On Windows: [RTools](http://cran.r-project.org/bin/windows/Rtools/)
- [Java](http://java.com)
- 25 GB of free disk space

How to run
==========
1. Follow [these instructions](https://ohdsi.github.io/Hades/rSetup.html) for seting up your R environment, including RTools and Java. 

2. In `R`, use the following code to install the dependencies:

	```r
	# When asked to update packages, select '1' ('update all') (could be multiple times)
	# When asked whether to install from source, select 'No' (could be multiple times)
	install.packages("remotes")
	library(remotes)
	install_github("ohdsi/ParallelLogger", ref = "v2.0.0")
	install_github("ohdsi/SqlRender", ref = "v1.6.6")
	install_github("ohdsi/DatabaseConnector", ref = "v3.0.0")
	install_github("ohdsi/OhdsiSharing", ref = "v0.2.1")
	install_github("ohdsi/FeatureExtraction", ref = "v3.0.0")
	install_github("ohdsi/CohortMethod", ref = "v4.0.0")
	install_github("ohdsi/EmpiricalCalibration", ref = "v2.0.2")
	install_github("ohdsi/MethodEvaluation", ref = "v1.1.0")
	```

3. In `R`, use the following `devtools` command to install the corazon package:

	```r
	install.packages("devtools")
	devtools::install_github("ohdsi-studies/Corazon")
	```
	
3. Once installed, you can execute the study by modifying and using the code below. For your convenience, this code is also provided under `extras/CodeToRun.R`:

	```r
	library(corazon)
	
  # Optional: specify where the temporary files (used by the Andromeda package) will be created:
  options(andromedaTempFolder = "c:/andromedaTemp")
	
	# Maximum number of cores to be used:
	maxCores <- parallel::detectCores()
	
	# Minimum cell count when exporting data:
	minCellCount <- 5
	
	# The folder where the study intermediate and result files will be written:
	outputFolder <- "c:/corazon"
	
	# Details for connecting to the server:
	# See ?DatabaseConnector::createConnectionDetails for help
	connectionDetails <- DatabaseConnector::createConnectionDetails(dbms = "postgresql",
									server = "some.server.com/ohdsi",
									user = "joe",
									password = "secret")
	
	# The name of the database schema where the CDM data can be found:
	cdmDatabaseSchema <- "cdm_synpuf"
	
	# The name of the database schema and table where the study-specific cohorts will be instantiated:
	cohortDatabaseSchema <- "scratch.dbo"
	cohortTable <- "my_study_cohorts"
	
	# Some meta-information that will be used by the export function:
	databaseId <- "Synpuf"
	databaseName <- "Medicare Claims Synthetic Public Use Files (SynPUFs)"
	databaseDescription <- "Medicare Claims Synthetic Public Use Files (SynPUFs) were created to allow interested parties to gain familiarity using Medicare claims data while protecting beneficiary privacy. These files are intended to promote development of software and applications that utilize files in this format, train researchers on the use and complexities of Centers for Medicare and Medicaid Services (CMS) claims, and support safe data mining innovations. The SynPUFs were created by combining randomized information from multiple unique beneficiaries and changing variable values. This randomization and combining of beneficiary information ensures privacy of health information."
	
	# For Oracle: define a schema that can be used to emulate temp tables:
	oracleTempSchema <- NULL
	
	execute(connectionDetails = connectionDetails,
            cdmDatabaseSchema = cdmDatabaseSchema,
            cohortDatabaseSchema = cohortDatabaseSchema,
            cohortTable = cohortTable,
            oracleTempSchema = oracleTempSchema,
            outputFolder = outputFolder,
            databaseId = databaseId,
            databaseName = databaseName,
            databaseDescription = databaseDescription,
            createCohorts = TRUE,
            synthesizePositiveControls = FALSE,
            runAnalyses = TRUE,
            packageResults = TRUE,
            maxCores = maxCores)
	```

4. Send the file ```export/Results_<DatabaseId>.zip``` in the output folder to the study coordinator [Carmen O. Torre](mailto:carmenolga.torre@iqvia.com)
	
	
5. To view the results, use the Shiny app:

	```r
	
	prepareForEvidenceExplorer("Result_<databaseId>.zip", "/shinyData")
	launchEvidenceExplorer("/shinyData", blind = TRUE)	
	```
  
  Note that you can save plots from within the Shiny app. It is possible to view results from more than one database by applying `prepareForEvidenceExplorer` to the Results file from each database, and using the same data folder. Set `blind = FALSE` if you wish to be unblinded to the final results.

License
=======
The corazon package is licensed under Apache License 2.0

Development
===========
corazon was developed in ATLAS and R Studio.

Results
===========
Send the file ```export/Results_<DatabaseId>.zip``` in the output folder to the study coordinator [Carmen O. Torre](mailto:carmenolga.torre@iqvia.com).



