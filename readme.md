# Taxa Information Registry

This repository contains code for an experimental component in the Biogeographic Information System, which handles the processing of discovered taxa of interest across our various systems, does some work to link them with taxonomic authorities, and retrieves other properties from relevant data systems into a cache for use throughout the BIS. We are trying to build this as an entirely code-driven system with occasional need for human intervention to tweak config parameters to make the processing better.

The codes here all interact with still-experimental data platforms we are working with as a component of the BIS. You'll see references to API calls that you can dig around in, but a separate config file is needed to set up API keys and other parameters for use. That file has to go along with the codes when running on a processing engine somewhere.

I started all of these with Jupyter Notebooks. If you see the same names as .py files, those were written with nbconvert to run outside the Jupyter environment. I'm working to make sure everything here will port over to the experimental microservices architecture we are developing where we will run this stuff live.

Keep an eye on issues and projects in this GitHub repo for information about current activities.

## Scripts that are reasonably functional

I'm currently refactoring most of the TIR processes toward what I hope will work as part of a microservices architecture we are developing on the Kafka message queuing system. I'm breaking these down to operate at a very granular level such that they essentially send one specific TIR registration package through a given information processor. It means more total API calls to get and interact with data but makes the whole process pretty resilient to cases where one of the dependencies fails.

Note: Corresponding py scripts are simply an nbconvert of the notebooks that are configured to run all records in the TIR instead of only a limited number for testing. They have been converted and then run on a standalone Python distribution.

* ITIS - This code currently works on registrations of taxa names in the TIR. It looks for matches using [ITIS Solr services](https://www.itis.gov/solr_documentation.html), runs a few modifications to make the returned JSON document more usable, and caches it in the TIR database.
* WoRMS - This code works with registered scientific names but also picks up ITIS names when those are available and checks the [WoRMS REST service](http://www.marinespecies.org/rest/) to find a match. It removes a few things from the returned JSON document for a WoRMS taxon and caches it in the TIR.
* TESS - This code works from cached ITIS TSNs to search the [FWS Threatened and Endangered Species System](https://ecos.fws.gov/ecp/species-query). It caches either a negative result, error, or a transformed and simplified TESS document (XML converted to JSON) for either the discovered TSN or accepted TSN (whichever is discovered in the listing information.
* NatureServe - This code works from cached ITIS names to search the NatureServe [global species lookup service](http://services.natureserve.org/BrowseServices/getSpeciesData/getSpeciesListREST.jsp) and then the [global comprehensive species service](http://services.natureserve.org/BrowseServices/getSpeciesData/getSpeciesREST.jsp) for information to cache in the TIR. We cache a few properties because we need to use them as search facets in systems like our Species of Greatest Conservation Need synthesis. The code takes the XML response from the API, converts the conservation status part of the document to a JSON structure, adds some information from a config file on code descriptions, and caches the documents in the TIR.
* SGCN - The designation of "Species of Greatest Conservation Need" by US States and our synthesis of these lists introduces its own set of specific annotations on the distinct scientific names registered in the TIR. This code works through those and puts information into a JSON structure specific to the SGCN.
* TIR Common Properties - In the course of working through the SGCN and GAP species use cases to produce workable data views and indexes, we were putting a lot of logic into SQL code to pull out the usable parts of cached information in the TIR. We decided to move this logic into code to create a set of most commonly used properties (scientific and common name, etc.) within the TIR table itself.

The other stuff in this repo should be considered experimental until listed here or in the associated documentation for the scripts.

## Provisional Software Disclaimer
Under USGS Software Release Policy, the software codes here are considered preliminary, not released officially, and posted to this repo for informal sharing among colleagues.

This software is preliminary or provisional and is subject to revision. It is being provided to meet the need for timely best science. The software has not received final approval by the U.S. Geological Survey (USGS). No warranty, expressed or implied, is made by the USGS or the U.S. Government as to the functionality of the software and related material nor shall the fact of release constitute any such warranty. The software is provided on the condition that neither the USGS nor the U.S. Government shall be held liable for any damages resulting from the authorized or unauthorized use of the software.