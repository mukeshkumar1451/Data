=====================================
ADO INTELLIGENCE ANALYSIS OUTPUT
=====================================


Story ID: 734893
Title: Buydown Modal When Copying Fees from LE displays a null reference
Timestamp: 20260306_153814
------------ DESCRIPTION ------------
As a user
I want the Buydown Split section on the CD Fees screen to correctly display the 'Paid By' fields when copying fees from the LE Fees screen
So that I do not encounter a null reference and can ensure accurate split allocation without additional manual steps

Issue #2: This seems to be existing. When we copy LE fees to the CD Fees screen, the below is displayed. 

Steps to recreate On the LE Fees screen, Buydown Split is incomplete. No values entered in the 'Borrower', 'Lender', or 'Seller' fields. Go to the CD Fees screen and click Copy Fees from LE, ‘Lender’ displays as ‘null’
------ ACCEPTANCE CRITERIA ----------
AC1: Paid By Field Names Displayed When Buydown Split is Incomplete on LE

Giventhe user is on the DIS > LE Fees screen > Interest Rate Details > Temporary Buydown SubsidyANDthe Paid By split fields are not completedWhenthe user navigates to the DOCS > CD Fees screenANDclicks "Copy Fees from LE"Thenthe Buydown Subsidy on the CD Fees screen> Interest Rate Details > Temporary Buydown Subsidyshould display the correct 'Paid By' field names without a null reference-UI MockupWhen Buydown Product is assigned, the 'Temporary Buydown Subsidy' section appears on the DIS > LE Fees screenDo not enter any values in the 'Paid By' modalGo to the DOCS > CD Fees screen and click Copy Fees from LE and Temporary Buydown Subsidy section should appear displaying the Paid By field names

Giventhe user is on the DIS > LE Fees screen > Interest Rate Details > Temporary Buydown Subsidy

ANDthe Paid By split fields are not completed

Whenthe user navigates to the DOCS > CD Fees screen

ANDclicks "Copy Fees from LE"

Thenthe Buydown Subsidy on the CD Fees screen> Interest Rate Details > Temporary Buydown Subsidyshould display the correct 'Paid By' field names without a null reference

> Interest Rate Details > Temporary Buydown Subsidy

-UI Mockup

When Buydown Product is assigned, the 'Temporary Buydown Subsidy' section appears on the DIS > LE Fees screenDo not enter any values in the 'Paid By' modalGo to the DOCS > CD Fees screen and click Copy Fees from LE and Temporary Buydown Subsidy section should appear displaying the Paid By field names

When Buydown Product is assigned, the 'Temporary Buydown Subsidy' section appears on the DIS > LE Fees screen

Do not enter any values in the 'Paid By' modal

[Image Analysis]
Section:  
Loan Estimate Fee Quote  
Interest Rate Details  
Temporary Buydown Subsidy  
Lender Credit Information  
Product Assignment  

Fields:  
Rate Information: Borrower will receive, Borrower will pay a discount fee of, Borrower will lock at par with int. rate of, Lender Credit for the interest rate of  
Temporary Buydown Subsidy: Total Subsidy Amount, Paid By: Borrower, Lender, Seller  
Lender Credit Information: Lock Credit ($), Additional Lender Credit ($), Total Credit ($), Additional Lender Credit Applied ($), Total Credit Applied ($)  
Product Assignment: Quick Assign  

Buttons:  
Appraisal Fee Adjustments  
Fee Quote  
Fee Quote History  
Pricing Detail  

Values:  
NOTE: Running the Fee Quote service is required in order to generate the Newrez LE package.  
Borrower will pay a discount fee of: 0.570% / $2,280.00 to lower their interest rate to 7.750%  
Temporary Buydown Subsidy: Total Subsidy Amount: $18,980.16  
Paid By: Borrower: $0.00, Lender: $0.00, Seller: $0.00  
Lender Credit Information: Lock Credit ($): 0.00, Additional Lender Credit ($): 0.00, Total Credit ($): 0.00, Additional Lender Credit Applied ($): 0.00, Total Credit Applied ($): 0.00  
Product Assignment: CF30B3  

Go to the DOCS > CD Fees screen and click Copy Fees from LE and Temporary Buydown Subsidy section should appear displaying the Paid By field names

[Image Analysis]
Section:  
CD Fees  

Fields:  
Copy Fees From Loan Estimate  

Buttons:  
- Copy Fees From LE  
- Bypass Copy Fees From LE  
- Compliance Check  
- Save & Next  

Values:  
No values displayed.  

[Image Analysis]
Section:  
CD Fees  

Fields:  
- Borrower will receive: % / $  
- Lender Credit for the interest rate of: %  
- Borrower will pay a discount fee of: % / $  
- To lower their interest rate to: %  
- Borrower will lock at par for the interest rate of: %  
- Total Subsidy Amount  
- Paid By  

Buttons:  
- Compliance Check  
- Save & Next  
- Buydown Subsidy COC Breakdown  

Values:  
- Borrower will pay a discount fee of: 0.570% / $2,280.00  
- To lower their interest rate to: 7.750%  
- Total Subsidy Amount: $18,980.16  
- Paid By: Borrower: $0.00, Lender: $0.00  

AC2: Regression - Paid By Field Names Displayed When Buydown Split is Complete on LE

Giventhe user ison the DIS > LE Fees screen > Interest Rate Details > Temporary Buydown Subsidy

on the DIS > LE Fees screen > Interest Rate Details > Temporary Buydown Subsidy

ANDthe Paid By split fields are completed

Whenthe user navigates to theDOCS > CD Fees screen

DOCS > CD Fees screen

ANDclicks "Copy Fees from LE"

Thenthe Buydown Subsidy on the CD Fees screen> Interest Rate Details > Temporary Buydown Subsidy shoulddisplay the correct 'Paid By' field names without a null reference

the Buydown Subsidy on the CD Fees screen

> Interest Rate Details > Temporary Buydown Subsidy should

**Note to Dev**

**Note to Dev**

AC2 is existing so no changes should be made for this scenario

AC2 is existing so no changes should be made for this scenario

**Note For Testing**

**Note For Testing**

In order to test, loan will need to have a Buydown Product assigned. Some examples would be CF30B3,CF30B2,CF30B1,CHBF30B1,CHRF30B1, etc. 'Paid By' field names appear based onPurpose of Loan. Paid By = Seller would not typically appear on loans where Purpose of Loan = Refinance.

In order to test, loan will need to have a Buydown Product assigned. Some examples would be CF30B3,

CF30B2,

CF30B1,

CHBF30B1,

CHRF30B1, etc

. 'Paid By' field names appear based on

Purpose of Loan

. Paid By = Seller would not typically appear on loans where Purpose of Loan = Refinance.

