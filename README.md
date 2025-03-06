# Sexism-on-spanish-parliament
This repository will serve as the base to keep up to date my TFG and it's code on the evolution of sexism on the Spanish parliament the last 20 years.

To be able to obtain the segmented interventions of the parliament 

  ## Step 1:
  Run the notebook Data extraction/data_extraction.ipynb. After running succesfully, a new folder called clean_data will apear on the directory Data extraction/. With this notebook you will extract the data from the zenodo repository:  https://doi.org/10.5281/zenodo.11195944 by useing the PyPi library https://pypi.org/project/congreso/. This data will be cleaned so you only keep the plenary sessions from El Congreso De Los Diputados. After that the data will be sabed on clean_data folder.

  ## Step 2:
  Run the notebook Data segmentation/preliminar_segmentation.ipynb. You can segment any document you want by calling the function extract_interventions(doc), for example, if I want to segment by interventions the first doc of 2015:

  ```
  int_20150120 = extract_interventions(ds_2015[4])
  display(int_20150120)
  ```
  ## Step 3:
 To create the dataset and populate it run Data segmentation/populate_database.ipynb, for now only term crawl works, and the database is correctlly created. To visualize the current database, you can run on the terminal:
  ```
    datasette interventions.db
  ```
 
