=====================================
ADO INTELLIGENCE ANALYSIS OUTPUT
=====================================


Story ID: 734893
Title: Buydown Modal When Copying Fees from LE displays a null reference
Timestamp: 20260306_140005
------------ DESCRIPTION ------------
As a user I want the Buydown Split section on the CD Fees screen to correctly display the 'Paid By' fields when copying fees from the LE Fees screen So that I do not encounter a null reference and can ensure accurate split allocation without additional manual steps Issue #2: This seems to be existing. When we copy LE fees to the CD Fees screen, the below is displayed. -Steps to recreate On the LE Fees screen, Buydown Split is incomplete. No values entered in the 'Borrower', 'Lender', or 'Seller' fields. Go to the CD Fees screen and click Copy Fees from LE, ‘Lender’ displays as ‘null’
------ ACCEPTANCE CRITERIA ----------
AC1: Paid By Field Names Displayed When Buydown Split is Incomplete on LE Given the user is on the DIS > LE Fees screen > Interest Rate Details > Temporary Buydown Subsidy AND the Paid By split fields are not completed When the user navigates to the DOCS > CD Fees screen AND clicks "Copy Fees from LE" Then the Buydown Subsidy on the CD Fees screen > Interest Rate Details > Temporary Buydown Subsidy should display the correct 'Paid By' field names without a null reference -UI Mockup When Buydown Product is assigned, the 'Temporary Buydown Subsidy' section appears on the DIS > LE Fees screen Do not enter any values in the 'Paid By' modal Go to the DOCS > CD Fees screen and click Copy Fees from LE and Temporary Buydown Subsidy section should appear displaying the Paid By field names AC2: Regression - Paid By Field Names Displayed When Buydown Split is Complete on LE Given the user is on the DIS > LE Fees screen > Interest Rate Details > Temporary Buydown Subsidy AND the Paid By split fields are completed When the user navigates to the DOCS > CD Fees screen AND clicks "Copy Fees from LE" Then the Buydown Subsidy on the CD Fees screen > Interest Rate Details > Temporary Buydown Subsidy should display the correct 'Paid By' field names without a null reference **Note to Dev** AC2 is existing so no changes should be made for this scenario **Note For Testing** In order to test, loan will need to have a Buydown Product assigned. Some examples would be CF30B3, CF30B2, CF30B1, CHBF30B1, CHRF30B1, etc . 'Paid By' field names appear based on Purpose of Loan . Paid By = Seller would not typically appear on loans where Purpose of Loan = Refinance.

