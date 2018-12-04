# Validations

The following are the rules in the current validator that runs as a lambda function.

## Column names
Checks the column names in the file are the same as those in the template. If there are extra columns or the name doesn’t match this would fail.

## Missing
All fields expect a value, this will check there is a value supplied in the submission.

## URN Number
Confirms the URN is one from the list of valid URNs. No further checks for if the URN is valid for a given framework.

## Postcode
Checks the value passes a basic test for UK Postcodes.

One or Two capital letters, followed by a number, followed by a number or a capital letter, followed by a single space, followed by one numbers, followed by one or two capital letters.

This validation is case sensitive.

## Date
Expects a date value separated by forward slashes ‘/’. Checks that there are three values. Assigns the first value to month, the second to day and the third to year.

Each value must be a number or the validation will fail.

Checks the day and month value are not less than one and not more than thirty one.

## Currency
Checks that the value can be converted to a valid number, otherwise checks that the value is a recognised ‘not applicable’.

This validation is not case sensitive.

## Value in range
Checks that the value is in a known list of allowed values.

This validation is not case sensitive.
