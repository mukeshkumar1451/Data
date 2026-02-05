user_story_id = "7123445"

user_story = Part 2:  FHA Case Number Request Screen- Detached Condo Defaults to YES
description = As a user
I want the “Is the Property a Condo?” question on the FHA Case Number > Type of Case section to default to YES when the Type of Property is Detached Condominium
So that the system behavior is consistent for all condominium property types and reduces manual corrections


I noticed the following behavior on Wholesale loans in H2O when navigating to Status Menu > FHA Request Case Number screen:

When the Type of Property on 1003 > Loan Summary > Property Details is set to Condominium or High-Rise Condominium, the “Is the Property a Condo?” question on the FHA Case Number > Type of Case section automatically defaults to YES.
However, when the Type of Property is Detached Condominium, the same question defaults to NO.
 Image




 


ac = AC1: Default "Is the Property a Condo?" to YES for Detached Condominium
Given the loan is a Wholesale loan
AND  FHA Request Case Number screen is available
AND the Type of Property = "Detached Condominium"
When the FHA Case Number > Type of Case section is displayed
Then the "Is the Property a Condo?" question should default to "YES"


-UI Mockup

On 1003 > Loan Summary > Property Details > Type of Property shows Detached Condominium 
Image
Go to Status Menu > FHA Request Case Number screen “Is the Property a Condo?” field should show defaulted to YES
Image

**Note to Dev**
Present day, if type of Property = Condominium or High-Rise Condominium, the “Is the Property a Condo?” field defaults to YES. We want the same behavior for Detached Condominium
