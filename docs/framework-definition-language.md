# Framework Definition Language

## General definition guidelines

Capitalisation is important. 

Use of single quotes to denote strings is important. Quote escaping is not
currently supported; For now, use a right [smart
quote](http://smartquotesforsmartpeople.com/) if you need an apostrophe
(Alt-0146 on a Windows numeric keypad).

Spacing is not important, but it’s recommended for readability that you begin
curly brace sections on the same line as the kind of block you’re writing and
indent lines in blocks by 2 spaces.

## The framework block

The definition MUST begin with `Framework` followed by the framework short name
without quotes and with no spaces, e.g. `Framework RM6666`.

The block of framework content MUST be surrounded with curly braces, e.g. 

    Framework RM6666 {
      ...
    }


## Framework metadata (name and management charge)

The definition MUST contain a full name as the first item in the framework
content block, and it MUST be in single quotes, e.g. 

    Framework RM6666 {
      Name 'Vehicle Purchase'
      ...
    }

The `ManagementCharge` MUST follow the framework name. It can take one of four
forms:

- A flat-rate management charge without a named field will be calculated as a
  percentage of the InvoiceValue field, e.g:

      ManagementCharge 0.5%

- A flat-rate management charge calculated as a percentage of a value in a named
  field:

      ManagementCharge 0.5% of 'Supplier Price'

- A management charge that varies by a value in a named field:

      ManagementCharge varies_by 'Spend Code' {
         'Lease Rental'         -> 0.5%
         'Fleet Management Fee' -> 0.5%
         'Damage'               -> 0%
         'Other Re-charges'     -> 0%
      }


- A management charge that varies by sector (these must always have both
  `CentralGovernment` and `WiderPublicSector`)

      ManagementCharge sector_based {
          CentralGovernment  -> 0.5%
          WiderPublicSector -> 1%
      }

## Lots

Framework Definitions MUST include a list of lot numbers and descriptions
directly after the framework metadata, specified as a list of key/value
mappings, which are always single-quoted strings.

e.g:

    Lots {
      '1a' -> 'Computer Hardware'
      '1b' -> 'Computer Software & Services'
      '2' -> ''
    }

In the last example, the description for lot 2 is a blank string (`''`), but is
still required.

## Invoice and Contract fields

After the lots, the definition MUST contain AT LEAST one of `InvoiceFields` or
`ContractFields`, or both.

Invoice fields and Contract fields are contained in a block surrounded by curly
braces, e.g. 

    Framework RM6666 {
       Name 'Vehicle Purchase'
       ManagementCharge 0.5%
     
       InvoiceFields {
         ...
       }
     
       ContractFields {
         ...
       }
    }

### Field definitions

Field definitions MUST be within the curly braces of the `InvoiceFields` or
`ContractFields` blocks (whichever is relevant).

Field definitions SHOULD be one per line.

Field definitions MUST be in the same order as the Excel template.

#### “Known” fields

Fields which have a [known
destination](https://github.com/dxw/DataSubmissionServiceAPI/blob/master/app/models/framework/definition/data_warehouse/known_fields.rb#L7)
in the data warehouse are always of a type known to the data warehouse. For
example, a `CustomerURN` is always an integer and it’s always validated against
the list of Customer URNs. An `InvoiceDate` is always validated as a date, and
an `InvoiceValue` is always a decimal value and so on. They are written like so:

    <PascalCasedDestination> from '<Origin Column in Template>'

For example:

    LotNumber from 'Lot Number'
    CustomerURN from 'Customer URN'
    InvoiceDate from 'Customer Invoice Date'
    InvoiceNumber from 'Customer Invoice Number'

i.e.

    DataWarehouseDestination from 'Template origin'

Fields are assumed to be mandatory – an error will be raised if no value is
present. If a field is not mandatory, add the `optional` keyword to the beginning
of the line. A validation will only be applied to `optional` fields that are not
blank:

    optional SupplierReferenceNumber from 'Supplier Order Number'
    optional CustomerPostCode from 'Customer PostCode'
    optional CustomerName from 'Customer Organisation'

Known field definitions MUST have a data warehouse destination (e.g.
`CustomerPostCode`) and template origin (e.g. `'Customer PostCode'`).

The field origin MUST be in single quotes.

#### “Additional” fields

FDL provides for up to 24 framework-specific fields per
`InvoiceFields`/`ContractFields` block which are exported to the data warehouse
as `Additional1`, `Additional2`, ... `Additional24`. Because they’re
framework-specific their types vary, so we must supply a type, like so:

    String Additional1 from 'Manufacturers Product Code'
    Decimal Additional2 from 'Unit Quantity'
     
    Date Additional10 from 'Lease Start Date'
    Date Additional11 from 'Lease End Date'
     
    Decimal Additional8 from 'MRP Excluding Options'
    Decimal Additional9 from 'Customer Support Terms'

i.e.

    [optional] <DataType> <AdditionalN> from 'Template origin'

If a field is not mandatory, add the `optional` keyword to the beginning of the line:

    optional Integer Additional10 from 'Lease Period (Months)'

Additional field definitions MUST have a data warehouse destination with the
name `Additional` followed by a number from 1-24 (e.g. `Additional1`) and a
template origin (e.g. `'Manufacturers Product Code'`).

The field origin MUST be in single quotes.

For `DataType`, use any of `String`, `Decimal`, `YesNo`, `Integer`, or `Date`.

Here’s how each of those behaves.

##### Data Types

###### `String`

This is the simplest type, representing a string of data, which can contain
anything, names, addresses, alphanumeric reference numbers etc.

Strings have no special validation applied to them by default – we only check
that the field has some sort of value and isn't blank unless it's `optional`, but
you can validate on length using a range. By example, here’s how you do that:

- `String(8..) from 'Somewhere'`: Validate the column ‘Somewhere’ and fail
  validation if the string is not at least 8 characters in length
- `String(..16) from 'Somewhere'`: Validate the column ‘Somewhere’ and allow
  only up to 16 characters. Fail validation if the string exceeds 16 characters
  in length
- `String(8..16) from 'Somewhere'`: Validate the column ‘Somewhere’, which must
  be at least 8 characters and no more than 16
- `String(8) from 'Somewhere'`: Validate the column ‘Somewhere’, which must be
  exactly 8 characters in length
- `String from 'Somewhere'`: Validate only the presence of a string 'Somewhere'
  – the only constraint is that it cannot be blank; if you need blank to be
  allowable, use optional String.

###### `Decimal`

The field will be validated as a numeric value accepting integers or floating
point types.

###### `Integer`

The field will be validated as a numeric value accepting only integers (whole
numbers).

###### `Date`

The field will be validated as a Date, which must be in a UK date format, or
`dd/mm/yyyy`.

###### `YesNo`

The field must contain either the single character `Y` or the single character
`N`. No other values will be accepted.

#### “Unknown” fields

Fields which are unknown to the data warehouse and hence not exported, but
gathered and validated by CCS are written like so:

    String from 'Cost Centre'
    String from 'Contract Number'

i.e.

    String from 'Template origin'

If a field is not mandatory, add the `optional` keyword to the beginning of the
line: 

    optional String from 'Spend Code'

Unknown fields do not and cannot have a data warehouse destination.

Unknown fields CAN have a Data Type, but the value isn't going anywhere, so we
recommend using `String`. 

Unknown fields MUST have a template origin, expressed in single quotes.

### Lookups (dropdowns)

If a field definition can only have a value from a predetermined set of values
(a dropdown in the template), you can create a Lookup to validate it. 

Lookups have a name and a list of values and can be attached to known fields or
additional fields.

The `Lookups` block follows the last of the `InvoiceFields`/`ContractFields`
blocks, and looks something like this:

    Lookups {
      UnitType [
        'Day'
        'Each'
      ]
     
      BreakfastIngredient [
        'Bacon'
        'Eggs'
        'Sausage'
      ] 
    }

Each lookup has a name (`UnitType` and `BreakfastIngredient` in this example),
and its values are surrounded with square brackets. Each lookup value is on its
own line, delimited with single quotes.

#### Lookups on known fields

For known fields, the lookup name MUST be the same as the field’s data warehouse
destination.

Say we have a known field with a data warehouse destination `UnitType` like so:

    UnitType from 'Unit of Measure'

... and the value of `UnitType` can ONLY be `“Day”` or `“Each”`. Then the
abbreviated framework definition highlighting just this field and the lookup
will be:

    Framework RM1234 {
      ...
      InvoiceFields {
        ... 
        UnitType from 'Unit of Measure'
      }
      Lookups {
        UnitType [
          'Day'
          'Each'
        ]
      }
    }

#### Lookups on additional fields

For lookups on additional fields, the lookup keyword must precede the data
warehouse destination. For example:

    LeasingCompany Additional14 from 'Leasing Company'

The lookup for this field would be something like:

    LeasingCompany [
      'Company 1'
      'Company 2'
      'N/A'
    ]

The lookup values MUST be encapsulated in square brackets.

The lookup values MUST be contained within single quotes.

### Dependent fields (product tables)

A dependent field is a field with a fixed set of possible values (like a
lookup), but the range of acceptable values varies according to the value of
another field.

For example, in `RM3756` (Rail Legal Services - “Real World Examples” at the end
of this document), the value of the field `Primary Specialism` will vary
according to the value of the field `Service Type`.

`Service Type` is a lookup field that accepts any of the values `Core`,
`Non-core` and `Mixture`. 

`Primary Specialism` is the dependent field of `Service Type`. The value you can
pick varies by the value picked in the `Service Type` field (`Core`, `Non-core`,
`Mixture`). 

In this example, we’ll use the `ProductGroup` from `Service Type` field. It has
a dependent field `ProductDescription`, whose valid values change when the
`Service Type` changes.

For these fields, you need to do three things:

1.  Create a lookup for the `Service Type` itself, using the name of the field
    the data is going to in the Data Warehouse – `ProductGroup` and all its
    valid values. For example:

        InvoiceFields {
          ProductGroup from 'Service Type'
        }
     
        Lookups {
          ProductGroup [
            'Core'
            'Non-core'
            'Mixture'
          ]
        }

2.  Add a lookup to this `Lookups` block for each of the values that could occur
    in the `Service Type` field. You can specify valid values as either a
    literal value with quotes like `Specialism 1`, or you can compose the list
    of valid values by referring to the names of other lookups without quotes.
    For example:

        CoreSpecialisms [
          'Specialism 1'
          'Specialism 2'
        ]
       
        NonCoreSpecialisms [
          'Specialism 3'
          'Specialism 4'
        ]
       
        PrimarySpecialisms [
          CoreSpecialisms
          NonCoreSpecialisms
        ]

    In the example above, `PrimarySpecialisms` will have all four values from
    the lists it references.

3.  Finally, when you create the `Primary Specialism` field, add a `depends_on`
    block that tells us which lookup to use for each value that `Primary
    Specialism` might hold. You’ll notice that on the left hand side, this has
    all the values from our original lookup from step 1:

        InvoiceFields {
          ProductGroup from 'Service Type'
          ProductDescription from 'Primary Specialism' depends_on 'Service Type' {
              'Core'     -> CoreSpecialisms
              'Non-core' -> NonCoreSpecialisms
              'Mixture'  -> PrimarySpecialisms
            }
        }

The dependent field MUST contain the `depends_on` keyword followed by the template
origin name of the field it depends on. The template origin name MUST be
surrounded by single quotes.

The dependent field mappings MUST be encapsulated by curly braces.

The dependent field mappings must be the values of the master field in single
quotes, followed by an arrow made up of a minus sign and a greater-than sign ->
followed by the lookup name of the values allowed in the dependent field. 

In the example above:

- If the value of `Service Type` is `Core`, then the value of `Primary
  Specialism` will only validate correctly if it is one of the values from the
  `CoreSpecialisms` lookup – in this case, `Specialism 1` or `Specialism 2`.
- If the value of `Service Type` is `Non-core`, then the value of `Primary
  Specialism` will only validate correctly if it is one of the values from the
  `NonCoreSpecialisms` lookup – in this case, `Specialism 3` or `Specialism 4`.
- If the value of `Service Type` is `Mixture`, then the value of `Primary
  Specialism` will only validate correctly if it is one of the values from the
  `PrimarySpecialisms` lookup – in this case, any of `Specialism 1`, `Specialism
  2`, `Specialism 3`, or `Specialism 4`.

The lookups of the dependent fields can be named anything (but they should not
be the names of data warehouse fields) - the important thing to remember is that
the name of the lookup and the name in the `depends_on` mappings MUST match.

The `depends_on` keyword MAY be followed by multiple template origin names,
indicating that the dependent field depends on the values of more than one other
field. The names of the template origin fields should be given as a
comma-separated list, for example:

    InvoiceFields {
      ProductGroup from 'Service Type'
      ProductValue from 'Service Value'
      ProductDescription from 'Primary Specialism' depends_on 'Service Type', 'Service Value' {
        ...
      }
    }

When using multiple template origin names, the dependent field mappings must
have a corresponding number of values on the left of each arrow, for example:

    InvoiceFields {
      ProductGroup from 'Service Type'
      ProductValue from 'Service Value'
      ProductDescription from 'Primary Specialism' depends_on 'Service Type', 'Service Value' {
        'Core', '10',    -> CoreSpecialisms
        'Non-core', '20' -> NonCoreSpecialisms
        'Mixture', '50'  -> PrimarySpecialisms
      }
    }


This indicates that if `Service Type` has the value `Core` _and_ `Service Value`
has the value `10`, then `Primary Specialism` can take values from
`CoreSpecialisms`, and so on for the other mappings.

Each clause of the mapping MUST have the same number of values on the left of
the arrow, as there are field names following the `depends_on` keyword.

A wildcard match (`*`) may be used in place of any value on the left of the
arrows, to match any value of the corresponding field. For example, to provide a
lookup for the case when `Service Value` has the value `60`, and `Service Type`
has any value:

      ProductDescription from 'Primary Specialism' depends_on 'Service Type', 'Service Value' {
        ...
        *, '60' -> SixtySpecialisms
      }

### Real-world examples

#### Laundry Services - Wave 2 (CM/OSG/05/3565) - a framework with only InvoiceFields and no Lookups.

    Framework CM/OSG/05/3565 {
      Name 'Laundry Services - Wave 2'
      ManagementCharge 0.0%
     
      InvoiceFields {
        optional CustomerPostCode from 'Customer Postcode'
        CustomerName from 'Customer Organisation'
        CustomerURN from 'Customer URN'
        InvoiceDate from 'Customer Invoice Date'
        optional ProductGroup from 'Service Type'
        optional ProductClass from 'Product Group'
        optional ProductSubClass from 'Product Classification'
        optional ProductDescription from 'Item Name or WAPP'
        optional ProductCode from 'Item Code'
     
        optional String Additional1 from 'Manufacturers Product Code'
        optional String Additional2 from 'Unit Quantity'
     
        UnitPrice from 'Price per Item'
        UnitType from 'Unit of Purchase'
     
        optional VATIncluded from 'Vat Included'
        UnitQuantity from 'Quantity'
        InvoiceValue from 'Total Spend'
     
        optional String from 'Cost Centre'
        optional String from 'Contract Number'
      }
    }

#### Laundry & Linen Services Framework (RM849) - a framework with InvoiceFields and ContractFields, but no Lookups.

    Framework RM849 {
      Name 'Laundry & Linen Services Framework'
     
      ManagementCharge 0.5%
     
      InvoiceFields {
        LotNumber from 'Lot Number'
        optional SupplierReferenceNumber from 'Supplier Order Number'
        optional CustomerPostCode from 'Customer PostCode'
        optional CustomerName from 'Customer Organisation'
        CustomerURN from 'Customer URN'
        optional CustomerReferenceNumber from 'Customer Order Number'
        InvoiceDate from 'Customer Invoice Date'
        InvoiceNumber from 'Customer Invoice Number'
        optional String Additional1 from 'Customer Invoice Line Number'
        optional ProductDescription from 'Invoice Line Product / Service Description'
        optional ProductGroup from 'Invoice Line Service Grouping'
        optional UNSPSC from 'UNSPSC'
        optional ProductCode from 'Product Code'
        UnitType from 'Unit of Purchase'
        UnitPrice from 'Price per Unit'
        optional UnitQuantity from 'Invoice Line Quantity'
        InvoiceValue from 'Invoice Line Total Value ex VAT and Expenses'
        VATIncluded from 'VAT Applicable'
        optional VATCharged from 'VAT amount charged'
        optional String from 'Contract Number'
        optional String from 'Cost Centre'
      }
     
      ContractFields {
        LotNumber from 'Lot Number'
        optional CustomerPostCode from 'Customer PostCode'
        CustomerName from 'Customer Organisation'
        CustomerURN from 'Customer URN'
        optional CustomerReferenceNumber from 'Customer Order Number'
        optional String from 'Customer Order Date'
        optional ContractStartDate from 'Customer Contract Start Date'
        optional ContractEndDate from 'Customer Contract End Date'
        optional ProductDescription from 'Project Name'
        optional UNSPSC from 'UNSPSC'
        optional String from 'Number of items'
        ContractValue from 'Customer Order/Contract Value'
        ContractAwardChannel from 'Invoice Line Service Grouping'
        optional String Additional1 from 'Invoice Line Product / Service Description'
      }
    }

#### Vehicle Telematics (RM3754) - a framework with InvoiceFields and Lookups

    Framework RM3754 {
      Name 'Vehicle Telematics'
      ManagementCharge 0.5%
     
      InvoiceFields {
        CustomerURN from 'Customer URN'
        optional CustomerPostCode from 'Customer PostCode'
        CustomerName from 'Customer Organisation'
        InvoiceDate from 'Customer Invoice Date'
        InvoiceNumber from 'Customer Invoice Number'
        ProductDescription from 'Product Description'
        UNSPSC from 'UNSPSC'
        UnitType from 'Unit of Purchase'
        UnitPrice from 'Price per Unit'
        String Additional1 from 'Vehicle Registration No'
        UnitQuantity from 'Total Number of Units'
        InvoiceValue from 'Total Charge (ex VAT)'
        optional PaymentProfile Additional2 from 'Payment Profile'
        VATCharged from 'VAT amount charged'
        optional String Additional3 from 'Subcontractor Supplier Name'
        optional String from 'Cost Centre'
        optional String from 'Contract Number'
      }
     
      Lookups {
        PaymentProfile [
          'Monthly'
          'Quarterly'
          'Annual'
          'One-off'
        ]
      }
    }

#### Rail Legal Services (RM3756) - a framework with InvoiceFields, ContractFields, Lookups and Dependent Fields

    Framework RM3756 {
      Name 'Rail Legal Services'
     
      ManagementCharge 1.5%
     
      InvoiceFields {
        LotNumber from 'Tier Number'
        SupplierReferenceNumber from 'Supplier Reference Number'
        CustomerURN from 'Customer URN'
        CustomerName from 'Customer Organisation Name'
        CustomerPostCode from 'Customer Post Code'
        InvoiceDate from 'Customer Invoice Date'
        InvoiceNumber from 'Customer Invoice Number'
        ProductGroup from 'Service Type'
        ProductDescription from 'Primary Specialism' depends_on 'Service Type' {
          'Core'     -> CoreSpecialism
          'Non-core' -> NonCoreSpecialism
          'Mixture'  -> PrimarySpecialism
        }
        ProductSubClass from 'Practitioner Grade'
        UNSPSC from 'UNSPSC'
        UnitType from 'Unit of Purchase'
        UnitPrice from 'Price per Unit'
        UnitQuantity from 'Quantity'
        InvoiceValue from 'Total Cost (ex VAT)'
        VATCharged from 'VAT Amount Charged'
        optional String from 'Cost Centre'
        optional String from 'Contract Number'
        CustomerReferenceNumber from 'Matter Name'
        PricingMechanism Additional5 from 'Pricing Mechanism'
        Decimal Additional1 from 'Pro-Bono Price per Unit'
        Decimal Additional2 from 'Pro-Bono Quantity'
        Decimal Additional3 from 'Pro-Bono Total Value'
        String Additional4 from 'Sub-Contractor Name (If Applicable)'
      }
     
      ContractFields {
        LotNumber from 'Tier Number'
        SupplierReferenceNumber from 'Supplier Reference Number'
        CustomerURN from 'Customer URN'
        CustomerName from 'Customer Organisation Name'
        CustomerPostCode from 'Customer Post Code'
        CustomerReferenceNumber from 'Matter Name'
        ProductDescription from 'Matter Description'
        ContractStartDate from 'Contract Start Date'
        ContractEndDate from 'Contract End Date'
        ContractAwardChannel from 'Award Procedure'
        ContractValue from 'Expected Total Order Value'
        String Additional1 from 'Sub-Contractor Name'
        String Additional2 from 'Expression Of Interest Used (Y/N)'
        String Additional6 from 'Customer Response Time'
        String Additional3 from 'Call Off Managing Entity'
        String Additional4 from 'Pro-bono work included? (Y/N)'
        Decimal Additional5 from 'Expected Pro-Bono value'
      }
     
      Lookups {
        ProductGroup [
          'Core'
          'Non-core'
          'Mixture'
        ]
     
        CoreSpecialism [
          'Regulatory Law'
          'Company, Commercial and Contract Law'
          'Public procurement law'
        ]
     
        NonCoreSpecialism [
          'EU Law'
          'International Law'
          'Competition Law'
          'Dispute Resolution and Litigation Law'
          'Employment Law'
          'Environmental Law'
          'Health and Safety Law'
          'Information Law including Data Protection Law'
          'Information Technology Law'
          'Intellectual Property Law'
          'Pensions Law'
          'Real Estate Law'
          'Restructuring/Insolvency Law'
          'Tax Law'
          'Insurance Law'
        ]
     
        PrimarySpecialism [
          'Regulatory Law'
          'Company, Commercial and Contract Law'
          'Public procurement law'
          'EU Law'
          'International Law'
          'Competition Law'
          'Dispute Resolution and Litigation Law'
          'Employment Law'
          'Environmental Law'
          'Health and Safety Law'
          'Information Law including Data Protection Law'
          'Information Technology Law'
          'Intellectual Property Law'
          'Pensions Law'
          'Real Estate Law'
          'Restructuring/Insolvency Law'
          'Tax Law'
          'Insurance Law'
        ]
     
        ProductSubClass [
          'Partner'
          'Legal Director/Senior Solicitor'
          'Senior Associate'
          'Junior Solicitor'
          'Trainee / Paralegal'
          'Other Grade / Mix'
        ]
     
        PricingMechanism [
          'Time and Material'
          'Fixed'
          'Risk-Reward'
          'Gain-Share'
          'Pro-Bono'
        ]
     
        UnitType [
          'Hourly'
          'Daily'
          'Monthly'
          'Fixed Price'
        ]
     
      }
    }
