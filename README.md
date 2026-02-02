AC1: Store Vantage scores during Credit Run data processing (Retail and DTC Loans Only)

GIVEN that I am a H2O user,
AND I am in loan, 
WHEN I initiate a credit run from the loan level credit menu (left hand panel),
AND Credit Pull Type is= ANY (Soft, AUS Soft, Full-Tri)
AND that credit completes successfully,
THEN I need to ensure the following is done during credit run data processing:
Use "Xactus360- NRZ" as the credit provider to receive both FICO as well as Vantage scores.
In the Credit response XML, the following tags (as shown below) will confirm if the Vantage scores are returned: "ModelNameType=Other" + "ModelNAmeTYpeOtherDescription=TransUnionVantageScore4.0 OR ExperianVantageScore4.0 OR EquifaxVantageScore4.0"
Note: 
Only certain borrowers can be used to call the 6 score in lower environments (refer to the additional info section below)
"NRZ Xactus 360 - Vantage" is set up for a different purpose and should not be used
Store the Vantage score
The Vantage score can be retrieved from the "_Value" tag from each one of the TransUnion, Experian and Equifax Vantage credit data blocks
This should be stored separately from the FICO scores. 
Note: 
Storing the FICO scores and displaying FICO on the loan remains as is and should not be impacted
Dev resource assistance will be needed to see which tables are being used to dump the vantage scores 
Below loan can be referenced in UAT: 

> Loan: 9714519866 
> Test borrower: Raj Nichelle 
> Credit provider: Xactus360 - NRZ
> XML attached to the story for reference.

Image

AC2: Store Vantage scores during AUS Run and data processing (Retail and DTC Loans Only)

GIVEN that I am in the same loan,
AND I have verified AC1,
WHEN I go to the Left-hand AUS menu to run DU/LPA,
AND I run DU/LPA with the same credit agency and using the pre-populated re-issue number,
AND AUS is successfully completed,
THEN I need to ensure the following:
Take the Vantage scores from the DU/LPA response
Store the Vantage score
This should be stored separately from the FICO scores. 
Note: 
Storing the FICO scores and displaying FICO on the loan remains as is and should not be impacted
Dev resource assistance will be needed to see which tables are being used to dump the vantage scores 


Additional Information:
To get the 6 scores back, test borrowers can be found in the following excel sheet: TRI BUREAU MORTGAGE TEST CASES.xlsx
Refer to Loan numbers and borrower details where Vantage Only score has come back: 9714519866 - Raj, Nichelle, 9711955303 - Altcrdvg, Alice. 
Retail and DTC loans have access to the credit menu (Left hand Panel) in H2O 
WHSL and CL1 loans do not have access to the loan level credit menu from the left-hand panel
Brokers order/re-issue credit from the AUS screen
There is currently code present which ignores the Vantage score through DU and LP for WHSL and CL1 loans - refer to dev analysis task: #745324
