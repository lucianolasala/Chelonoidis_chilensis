## Modeling Workflow

A Maximum Entropy approach (https://biodiversityinformatics.amnh.org/open_source/maxent/) method was used inside the R programing environment (https://www.r-project.org/).  

Se crearon modelos en ciclos secuenciales (i.e., ciclo 1, ciclo 2, etc.), cada uno de los cuales consistió en tres pasos:  

1. Calibración de modelos candidatos: se utilizó la función *kuenm_cal*, la cual genera un set de modelos candidatos con el objetivo de poner a prueba combinaciones múltiples de parámetros, incluyendo coeficientes de regularización (RM), clases de características (FC), y diferentes variables ambientales.     

2. Evaluation of candidate models: we used the function *kuenm_ceval*, which evaluates candidate models in terms of statistical significance (partial ROC), prediction ability (omission rates), and model complexity (AICc). After evaluation, this function selects the best models based on user-defined criteria.  
3. Creation of final models with selected parameters: we used the function *kuenm_mod*, which generates models using parameters previously selected with the *kuenm_ceval* function.  
At the end of Step 2 in Cycle 1, the selected model/s were evaluated. If only one model passed the evaluation criteria, this was the only model evaluated. Alternatively, of more than one model was selected, the one that ranked as best was evaluated. The model was evaluated in terms of analysis of omission / commission, area under the receiver operating characteristic (ROC) curve (AUC), response curves for variables, analysis of variable contributions (percent contribution and permutation importance), and jackknife test of variable importance.
Variables were removed from subsequent modeling cycles if the regularized trainign gain did not decrease considerably when excluded during the jackknife process.      
Cycles  were repeate until no model improvement could be observed.  

Finally, the best model selected through the above cycles was processed as follows:  

4. Model projection: we used the function *kuenm_mod* with *project* argument set to TRUE, which projects the final model to new environmental scenarios in a different geographic area.   
5. Evaluation of extrapolation risks: we used the function *kuenm_mop*, which calculates a mobility-oriented parity layer by comparing environmental values between the calibration area and the area or scenario to which an ecological niche model is transferred.  
6. Model averaging: we used the function *kuenm_modstats* to calculate descriptive statistics of models using model replicates across multiple parameter settings.

