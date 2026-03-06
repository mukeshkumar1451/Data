=====================================
ADO INTELLIGENCE ANALYSIS OUTPUT
=====================================


Story ID: 734893
Title: Buydown Modal When Copying Fees from LE displays a null reference
Timestamp: 20260306_140614
------------ DESCRIPTION ------------
As a user
I want the Buydown Split section on the CD Fees screen to correctly display the 'Paid By' fields when copying fees from the LE Fees screen
So that I do not encounter a null reference and can ensure accurate split allocation without additional manual steps Issue #2: This seems to be existing. When we copy LE fees to the CD Fees screen, the below is displayed. -Steps to recreate On the LE Fees screen, Buydown Split is incomplete. No values entered in the 'Borrower', 'Lender', or 'Seller' fields. Go to the CD Fees screen and click Copy Fees from LE, ‘Lender’ displays as ‘null’
------ ACCEPTANCE CRITERIA ----------
[Image Analysis]
• **UI section name**: Loan Estimate Fee Quote  
• **Field names**:  
  - Borrower  
  - Lender  
  - Seller  
  - Total Subsidy Amount  
  - Paid By  
  - Additional Lender Credit ($)  
  - Total Credit ($)  
  - Additional Lender Credit Applied ($)  
  - Total Credit Applied ($)  
  - Product Assignment  
• **Button names**:  
  - Appraisal Fee Adjustments  
  - Fee Quote  
  - Fee Quote History  
  - Pricing Detail  
  - Quick Assign  
• **Values shown**:  
  - Borrower: $0.00  
  - Lender: $0.00  
  - Seller: $0.00  
  - Total Subsidy Amount: $18,980.16  
  - Product Assignment: CF30B3  

[Image Analysis]
• UI section name: CD Fees  
• Field names: Copy Fees From Loan Estimate  
• Button names: Copy Fees From LE, Bypass Copy Fees From LE  
• Values shown: None

[Image Analysis]
• **UI section name**: CD Fees  
• **Field names**:  
  - Borrower will receive  
  - Lender Credit for the interest rate of  
  - Borrower will pay a discount fee of  
  - Borrower will lock at par for the interest rate of  
  - Total Subsidy Amount  
  - Borrower  
  - Lender  

• **Button names**:  
  - Paid By  
  - Buydown Subsidy COC Breakdown  

• **Values shown**:  
  - Borrower will pay a discount fee of: 0.570% / $2,280.00 to lower their interest rate to 7.750%  
  - Total Subsidy Amount: $18,980.16  
  - Borrower: $0.00  
  - Lender: $0.00  

